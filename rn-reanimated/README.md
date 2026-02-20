# rn-reanimated

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill for **React Native Reanimated 4.x** — best practices, 60fps animation patterns, bug fixing, and code enhancement for Android and iOS.

## What This Skill Does

When installed, Claude automatically applies Reanimated best practices when you:

- Write or review animation code (`useSharedValue`, `useAnimatedStyle`, `withSpring`, `withTiming`, etc.)
- Build gesture-driven interactions (`Gesture.Pan`, `Gesture.Pinch`, etc.)
- Work with layout animations, shared element transitions, or worklets
- Debug janky animations or performance issues
- Ask to **review/audit your code** — Claude scans all Reanimated files, reports issues by severity (CRITICAL/HIGH/MEDIUM/LOW), and offers to fix them automatically

## Coverage

| Topic | Reference File |
|-------|---------------|
| Installation & Core Concepts | `references/fundamentals.md` |
| `withTiming`, `withSpring`, `withDecay`, Modifiers | `references/animation-functions.md` |
| All Hooks & Utility Functions | `references/hooks-utilities.md` |
| Threading, `'worklet'` Directive, Closures | `references/worklets.md` |
| Entering/Exiting/Layout/Keyframe Animations | `references/layout-animations.md` |
| Gesture Types, Composition, Patterns | `references/gestures.md` |
| 60fps Optimization, Anti-Patterns, Debugging | `references/best-practices.md` |
| Installation/Build/Runtime/Performance Issues | `references/troubleshooting.md` |

## Installation

### Option 1: Install from `.skill` file

1. Download `rn-reanimated.skill` from [Releases](../../releases)
2. Run in your terminal:

```bash
claude install-skill rn-reanimated.skill
```

### Option 2: Install from source

1. Clone this repo:

```bash
git clone https://github.com/YOUR_USERNAME/rn-reanimated.git
```

2. Copy the skill to your Claude skills directory:

```bash
# macOS / Linux
cp -r rn-reanimated ~/.claude/skills/rn-reanimated

# Windows
xcopy /E /I rn-reanimated %USERPROFILE%\.claude\skills\rn-reanimated
```

## Usage Examples

Once installed, the skill activates automatically. Just ask Claude:

- "Create a bottom sheet with pan gesture"
- "Add a spring animation to this card"
- "Review my reanimated code for performance issues"
- "Fix the janky animation in my FlatList"
- "Why is my gesture not tracking at 60fps?"

## Requirements

- React Native 0.80+ (New Architecture / Fabric)
- react-native-reanimated 4.x
- react-native-worklets 0.7.x
- react-native-gesture-handler 2.x

## Created By

**[IMFA Solutions](https://www.imfa.solutions/)**

## License

MIT
