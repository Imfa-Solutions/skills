# Skills

A collection of open source [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills by **[IMFA Solutions](https://www.imfa.solutions/)**.

## Available Skills

| Skill | Description |
|-------|-------------|
| [rn-reanimated](react-native/rn-reanimated/) | React Native Reanimated 4.x — 60fps animation best practices, code review, bug fixing, gestures, layout animations, worklets |
| [uniwind](react-native/uniwind/) | Uniwind (Tailwind CSS v4 for React Native) — best practices, setup diagnostics, theming, component styling, HeroUI Native integration, Pro features, NativeWind migration |

## Installation

### Using the Skills CLI (Recommended)

Install any skill directly using the [skills CLI](https://github.com/vercel-labs/skills) — no setup required:

```bash
npx skills add imfa-solutions/skills 
```

### Quick Install from `.skill` File

Download the `.skill` file from the skill folder and run:

```bash
claude install-skill <skill-name>.skill
```

### Install from Source

Copy the skill folder to your Claude skills directory:

```bash
# macOS / Linux
cp -r <skill-name> ~/.claude/skills/<skill-name>

# Windows
xcopy /E /I <skill-name> %USERPROFILE%\.claude\skills\<skill-name>
```

## About the Skills CLI

The [skills CLI](https://github.com/vercel-labs/skills) is the standard way to discover, install, and manage skills for AI agents.

```bash
# Install a skill
npx skills add <owner>/<skill-name>

# Example
npx skills add imfa-solutions/skills
```

### Telemetry

The CLI collects anonymous telemetry to help rank skills on the [leaderboard](https://skills.dev). Only skill name and timestamp are collected — no personal data. To opt out:

```bash
DISABLE_TELEMETRY=1 npx skills add <skill-name>
```

## Created By

**[IMFA Solutions](https://www.imfa.solutions/)**

## License

MIT
