# Claude Code Skills

A collection of open source [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills by **[IMFA Solutions](https://www.imfa.solutions/)**.

## Available Skills

| Skill | Description |
|-------|-------------|
| [rn-reanimated](rn-reanimated/) | React Native Reanimated 4.x — 60fps animation best practices, code review, bug fixing, gestures, layout animations, worklets |

## Installation

Each skill folder contains a `.skill` file and a `README.md` with install instructions.

### Quick Install

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

## Created By

**[IMFA Solutions](https://www.imfa.solutions/)**

## License

MIT
