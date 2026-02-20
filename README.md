# AI Agent Skills

A collection of open source skills for AI coding agents by **[IMFA Solutions](https://www.imfa.solutions/)**.

Works with all major AI agents that support the [skills standard](https://github.com/vercel-labs/skills):

## Available Skills

| Skill | Description |
|-------|-------------|
| [rn-reanimated](react-native/rn-reanimated/) | React Native Reanimated 4.x — 60fps animation best practices, code review, bug fixing, gestures, layout animations, worklets |
| [uniwind](react-native/uniwind/) | Uniwind (Tailwind CSS v4 for React Native) — best practices, setup diagnostics, theming, component styling, HeroUI Native integration, Pro features, NativeWind migration |

## Installation

### Using the Skills CLI (Recommended)

Install any skill directly using the [skills CLI](https://github.com/vercel-labs/skills) — works with all supported agents, no setup required:

```bash
npx skills add imfa-solutions/skills
```

### Quick Install from `.skill` File

Download the `.skill` file from the skill folder and install it with your agent:

```bash
# Claude Code
claude install-skill <skill-name>.skill

# Other agents — copy to your agent's skills directory
```

### Install from Source

Copy the skill folder to your agent's skills directory:

```bash
# Claude Code (macOS / Linux)
cp -r <skill-name> ~/.claude/skills/<skill-name>

# Claude Code (Windows)
xcopy /E /I <skill-name> %USERPROFILE%\.claude\skills\<skill-name>

# Cursor / Windsurf / Other agents
# Copy to your agent's configured skills directory
```

## About the Skills CLI

The [skills CLI](https://github.com/vercel-labs/skills) is the universal standard for discovering, installing, and managing skills across AI coding agents.

```bash
# Install a skill
npx skills add <owner>/<skill-name>

# Example
npx skills add imfa-solutions/skills
```

Browse the [skills leaderboard](https://skills.dev) to discover more skills.

### Telemetry

The CLI collects anonymous telemetry to help rank skills on the [leaderboard](https://skills.dev). Only skill name and timestamp are collected — no personal data. To opt out:

```bash
DISABLE_TELEMETRY=1 npx skills add <skill-name>
```

## Created By

**[IMFA Solutions](https://www.imfa.solutions/)**

## License

MIT
