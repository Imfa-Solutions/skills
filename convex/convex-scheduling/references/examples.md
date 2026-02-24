# Convex Scheduling — Patterns & Examples

## Table of Contents

- [Self-Destructing Messages](#self-destructing-messages)
- [Payment Reminder System](#payment-reminder-system)
- [Background Job Queue](#background-job-queue)
- [Rate Limiting with Scheduled Reset](#rate-limiting-with-scheduled-reset)
- [Complete Cron Jobs File](#complete-cron-jobs-file)
- [Cron Syntax Reference](#cron-syntax-reference)

---

## Self-Destructing Messages

```typescript
import { mutation, internalMutation } from "./_generated/server";
import { internal } from "./_generated/api";
import { v } from "convex/values";

export const sendExpiringMessage = mutation({
  args: {
    channelId: v.id("channels"),
    body: v.string(),
    author: v.string(),
    expiresInSeconds: v.number(),
  },
  handler: async (ctx, args) => {
    const messageId = await ctx.db.insert("messages", {
      channelId: args.channelId,
      body: args.body,
      author: args.author,
      expiresAt: Date.now() + args.expiresInSeconds * 1000,
    });

    await ctx.scheduler.runAfter(
      args.expiresInSeconds * 1000,
      internal.messages.destruct,
      { messageId }
    );

    return messageId;
  },
});

export const destruct = internalMutation({
  args: { messageId: v.id("messages") },
  handler: async (ctx, args) => {
    await ctx.db.delete(args.messageId);
  },
});
```

---

## Payment Reminder System

Recurring reminder that re-schedules itself after each execution:

```typescript
export const createSubscription = mutation({
  args: { userId: v.id("users"), plan: v.string() },
  handler: async (ctx, args) => {
    const subscriptionId = await ctx.db.insert("subscriptions", {
      userId: args.userId,
      plan: args.plan,
      startDate: Date.now(),
      status: "active",
    });

    // Schedule reminder 3 days before next billing (30 days)
    const nextBilling = Date.now() + 30 * 24 * 60 * 60 * 1000;
    const reminderTime = nextBilling - 3 * 24 * 60 * 60 * 1000;

    const scheduledId = await ctx.scheduler.runAt(
      reminderTime,
      internal.billing.sendReminder,
      { subscriptionId }
    );

    await ctx.db.patch(subscriptionId, { scheduledReminderId: scheduledId });
    return subscriptionId;
  },
});

export const sendReminder = internalAction({
  args: { subscriptionId: v.id("subscriptions") },
  handler: async (ctx, args) => {
    const subscription = await ctx.runQuery(
      internal.billing.getSubscription,
      { id: args.subscriptionId }
    );
    if (!subscription || subscription.status !== "active") return;

    // Send email via external service
    await fetch("https://email-service.com/send", {
      method: "POST",
      body: JSON.stringify({
        to: subscription.userEmail,
        subject: "Payment Reminder",
        body: `Your ${subscription.plan} plan will renew in 3 days.`,
      }),
    });

    // Re-schedule for next month
    const nextReminder = Date.now() + 27 * 24 * 60 * 60 * 1000;
    await ctx.scheduler.runAt(
      nextReminder,
      internal.billing.sendReminder,
      { subscriptionId: args.subscriptionId }
    );
  },
});
```

---

## Background Job Queue

Generic job processor with status tracking:

```typescript
export const queueJob = mutation({
  args: {
    type: v.string(),
    data: v.any(),
    priority: v.optional(v.number()),
  },
  handler: async (ctx, args) => {
    const jobId = await ctx.db.insert("jobs", {
      type: args.type,
      data: args.data,
      priority: args.priority ?? 0,
      status: "queued",
      createdAt: Date.now(),
    });

    // Process immediately (atomic — only if insert succeeds)
    await ctx.scheduler.runAfter(0, internal.jobs.process, { jobId });
    return jobId;
  },
});

export const process = internalAction({
  args: { jobId: v.id("jobs") },
  handler: async (ctx, args) => {
    await ctx.runMutation(internal.jobs.updateStatus, {
      jobId: args.jobId,
      status: "processing",
    });

    try {
      const job = await ctx.runQuery(internal.jobs.get, { id: args.jobId });

      let result;
      switch (job.type) {
        case "generateReport":
          result = await generateReport(job.data);
          break;
        case "processVideo":
          result = await processVideo(job.data);
          break;
        default:
          throw new Error(`Unknown job type: ${job.type}`);
      }

      await ctx.runMutation(internal.jobs.complete, {
        jobId: args.jobId,
        result,
      });
    } catch (error) {
      await ctx.runMutation(internal.jobs.fail, {
        jobId: args.jobId,
        error: error.message,
      });
    }
  },
});
```

---

## Rate Limiting with Scheduled Reset

```typescript
export const recordAPICall = mutation({
  args: { userId: v.id("users") },
  handler: async (ctx, args) => {
    const limit = await ctx.db
      .query("rateLimits")
      .withIndex("by_user", (q) => q.eq("userId", args.userId))
      .first();

    if (!limit) {
      const limitId = await ctx.db.insert("rateLimits", {
        userId: args.userId,
        count: 1,
        resetAt: Date.now() + 60 * 60 * 1000,
      });

      // Schedule reset in 1 hour
      await ctx.scheduler.runAfter(
        60 * 60 * 1000,
        internal.rateLimit.reset,
        { limitId }
      );

      return { allowed: true, remaining: 99 };
    }

    if (limit.count >= 100) {
      return { allowed: false, remaining: 0, resetAt: limit.resetAt };
    }

    await ctx.db.patch(limit._id, { count: limit.count + 1 });
    return { allowed: true, remaining: 100 - (limit.count + 1) };
  },
});

export const reset = internalMutation({
  args: { limitId: v.id("rateLimits") },
  handler: async (ctx, args) => {
    await ctx.db.delete(args.limitId);
  },
});
```

---

## Complete Cron Jobs File

```typescript
// convex/crons.ts
import { cronJobs } from "convex/server";
import { internal } from "./_generated/api";

const crons = cronJobs();

// Health checks every 30 seconds
crons.interval("health check", { seconds: 30 }, internal.monitoring.healthCheck);

// Process queue every minute
crons.interval("process queue", { minutes: 1 }, internal.queue.process);

// Clean up old data every hour
crons.hourly("cleanup old data", { minuteUTC: 0 }, internal.cleanup.removeOldData);

// Send daily digest at 8am UTC
crons.daily(
  "daily digest",
  { hourUTC: 8, minuteUTC: 0 },
  internal.emails.sendDailyDigest
);

// Weekly backup on Sundays at 2am UTC
crons.weekly(
  "weekly backup",
  { dayOfWeek: "sunday", hourUTC: 2, minuteUTC: 0 },
  internal.backup.weekly
);

// Monthly billing on the 1st at 9am UTC
crons.monthly(
  "monthly billing",
  { day: 1, hourUTC: 9, minuteUTC: 0 },
  internal.billing.processMonthly,
  { sendEmail: true } // arguments passed to function
);

// Custom cron syntax — every weekday at 5pm UTC
crons.cron("weekday reminder", "0 17 * * 1-5", internal.reminders.sendWeekdayReminder);

export default crons;
```

---

## Cron Syntax Reference

```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6) (Sunday = 0)
│ │ │ │ │
* * * * *
```

| Expression | Meaning |
|---|---|
| `* * * * *` | Every minute |
| `0 * * * *` | Every hour on the hour |
| `0 0 * * *` | Every day at midnight UTC |
| `0 9 * * 1` | Every Monday at 9am UTC |
| `0 17 * * 1-5` | Every weekday at 5pm UTC |
| `0 15 1 * *` | First day of month at 3pm UTC |
| `*/5 * * * *` | Every 5 minutes |

All cron times are **UTC**. Use [crontab.guru](https://crontab.guru/) for help.
