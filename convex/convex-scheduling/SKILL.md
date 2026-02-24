---
name: convex-scheduling
description: >
  Convex scheduling and cron jobs — schedule functions to run in the future with runAfter/runAt,
  define recurring cron jobs (interval, hourly, daily, weekly, monthly, cron syntax), cancel
  scheduled functions, track status via _scheduled_functions system table, handle errors and
  retries for actions, and build durable workflows without external queues. Use when scheduling
  delayed execution, building reminder systems, self-destructing data, background job queues,
  recurring tasks, payment reminders, or any time-based automation in Convex. Triggers on:
  ctx.scheduler, runAfter, runAt, scheduler.cancel, cronJobs, crons.interval, crons.daily,
  crons.weekly, crons.monthly, crons.hourly, crons.cron, _scheduled_functions, "schedule a
  function", "run later", "cron job", "recurring task", "delayed execution in Convex".
---

# Convex Scheduling — Delayed Functions & Cron Jobs

> Schedule functions for future execution and define recurring cron jobs — durable, no external infrastructure needed.

## Scheduling API

### runAfter — delay in milliseconds

```typescript
import { internal } from "./_generated/api";

// Schedule deletion in 5 seconds
const scheduledId = await ctx.scheduler.runAfter(5000, internal.messages.destruct, {
  messageId: id,
});
```

### runAt — specific timestamp (ms since epoch)

```typescript
await ctx.scheduler.runAt(args.remindAt, internal.reminders.send, { reminderId });
```

### runAfter(0) — immediate, conditional on mutation success

Like `setTimeout(fn, 0)`. Use to trigger an action from a mutation — only runs if mutation succeeds.

```typescript
export const createUser = mutation({
  handler: async (ctx, args) => {
    const userId = await ctx.db.insert("users", args);
    // Only runs if insert succeeds (atomic with mutation)
    await ctx.scheduler.runAfter(0, internal.users.generateAIProfile, { userId });
    return userId;
  },
});
```

### cancel

```typescript
await ctx.scheduler.cancel(scheduledFunctionId);
```

| State when canceled | Behavior |
|---|---|
| Not started | Won't run |
| Already started | Continues, but its scheduled children won't run |

## Mutation vs Action Scheduling

| Scheduling from | Atomicity | On failure |
|---|---|---|
| **Mutation** | Atomic with the rest of the mutation | Nothing scheduled if mutation fails |
| **Action** | NOT atomic | Scheduled functions still execute even if action throws |

**Rule**: Prefer scheduling from mutations for guaranteed consistency. If scheduling from an action, be aware that scheduled children survive parent failure.

## Tracking Status

`runAfter`/`runAt` return a `Id<"_scheduled_functions">`. Query the system table:

```typescript
// Get all scheduled functions
const all = await ctx.db.system.query("_scheduled_functions").collect();

// Get specific one
const fn = await ctx.db.system.get(scheduledId);
// fn.state.kind: "pending" | "inProgress" | "success" | "failed" | "canceled"
// fn.scheduledTime, fn.completedTime, fn.name, fn.args
```

Results available for **7 days** after completion.

## Cancellable Pattern

Store the scheduled ID to allow cancellation later:

```typescript
export const createReminder = mutation({
  handler: async (ctx, args) => {
    const scheduledId = await ctx.scheduler.runAfter(
      args.delayMs, internal.reminders.send, { message: args.message }
    );
    return await ctx.db.insert("reminders", {
      message: args.message,
      scheduledFunctionId: scheduledId,
      status: "scheduled",
    });
  },
});

export const cancelReminder = mutation({
  args: { reminderId: v.id("reminders") },
  handler: async (ctx, { reminderId }) => {
    const reminder = await ctx.db.get(reminderId);
    if (!reminder) throw new Error("Not found");
    await ctx.scheduler.cancel(reminder.scheduledFunctionId);
    await ctx.db.patch(reminderId, { status: "canceled" });
  },
});
```

## Error Handling

| Function type | Execution guarantee | Auto-retry |
|---|---|---|
| **Scheduled mutation** | Exactly once | Yes (internal errors) |
| **Scheduled action** | At most once | No — permanently fails on transient errors/timeout |

### Retry pattern for actions

```typescript
export const reliableAction = internalAction({
  args: { taskId: v.id("tasks") },
  handler: async (ctx, args) => {
    try {
      await fetch("https://api.example.com/process");
      await ctx.runMutation(internal.tasks.markComplete, { taskId: args.taskId });
    } catch (error) {
      // Schedule retry through a mutation (atomic retry count check)
      await ctx.scheduler.runAfter(60000, internal.tasks.retryAction, {
        taskId: args.taskId,
      });
    }
  },
});

export const retryAction = internalMutation({
  args: { taskId: v.id("tasks") },
  handler: async (ctx, { taskId }) => {
    const task = await ctx.db.get(taskId);
    if (task?.completed) return;
    if ((task?.retries ?? 0) >= 3) {
      await ctx.db.patch(taskId, { status: "failed" });
      return;
    }
    await ctx.db.patch(taskId, { retries: (task?.retries ?? 0) + 1 });
    await ctx.scheduler.runAfter(0, internal.tasks.reliableAction, { taskId });
  },
});
```

## Authentication

**Auth is NOT propagated** from the scheduling function to the scheduled function. Pass user info explicitly:

```typescript
export const scheduleUserTask = mutation({
  handler: async (ctx, args) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) throw new Error("Not authenticated");
    await ctx.scheduler.runAfter(5000, internal.tasks.process, {
      userId: identity.subject, // pass explicitly
      taskData: args.taskData,
    });
  },
});
```

## Cron Jobs

Define in `convex/crons.ts`:

```typescript
import { cronJobs } from "convex/server";
import { internal } from "./_generated/api";

const crons = cronJobs();

// Interval (first run on deploy)
crons.interval("check queue", { seconds: 30 }, internal.queue.process);
crons.interval("cleanup", { minutes: 5 }, internal.files.cleanTemp);
crons.interval("sync", { hours: 2 }, internal.sync.fetchExternal);

// Hourly
crons.hourly("metrics", { minuteUTC: 0 }, internal.metrics.collect);

// Daily
crons.daily("digest", { hourUTC: 8, minuteUTC: 0 }, internal.emails.sendDigest);

// Weekly (dayOfWeek: "monday" | "tuesday" | ... | "sunday")
crons.weekly("backup", { dayOfWeek: "sunday", hourUTC: 2, minuteUTC: 0 }, internal.backup.run);

// Monthly
crons.monthly("billing", { day: 1, hourUTC: 9, minuteUTC: 0 }, internal.billing.process);

// Traditional cron syntax (UTC) — "minute hour day-of-month month day-of-week"
crons.cron("weekday reminder", "0 17 * * 1-5", internal.reminders.send);

// With arguments
crons.daily("report", { hourUTC: 8, minuteUTC: 0 }, internal.reports.generate, {
  type: "daily",
});

export default crons;
```

**Cron rules**:
- At most ONE run executing at a time per cron job
- If a run takes too long, following runs may be skipped (logged in dashboard)
- Same error guarantees as scheduled functions (mutations: exactly once, actions: at most once)

## Limits & Rules

| Limit | Value |
|---|---|
| Max scheduled per mutation/action | 1000 |
| Max total argument size | 8 MB |
| Results retention | 7 days |
| Auth propagation | None — pass userId explicitly |
| Mutation scheduling | Atomic (all-or-nothing) |
| Action scheduling | Non-atomic (survives failure) |
| Cron concurrency | At most 1 concurrent run per job |

## Reference Files

- **Full examples**: Self-destructing messages, payment reminders, background job queue, rate limiting, complete cron file → See [references/examples.md](references/examples.md)
