# React Native Reanimated - Fundamentals & Getting Started

> **Complete Guide for LLMs** | Version: Reanimated 4.x | Last Updated: 2026-02-20

---

## Table of Contents

1. [What is React Native Reanimated?](#what-is-react-native-reanimated)
2. [Prerequisites & Installation](#prerequisites--installation)
3. [Core Concepts](#core-concepts)
4. [Your First Animation](#your-first-animation)
5. [Architecture Overview](#architecture-overview)
6. [Key Terminology (Glossary)](#key-terminology-glossary)

---

## What is React Native Reanimated?

React Native Reanimated is a powerful animation library built by **Software Mansion**. It enables you to create smooth, 60fps animations and interactions that run on the **UI thread** (also called the **Reanimated thread**), completely separate from the JavaScript thread.

### Key Features

- **UI Thread Execution**: Animations run on a separate thread, ensuring 60fps even when JS thread is busy
- **Worklet-Based**: Uses worklets - small JavaScript functions that run synchronously on the UI thread
- **Gesture Integration**: Tight integration with React Native Gesture Handler
- **Layout Animations**: Animate layout changes automatically
- **Shared Element Transitions**: Smooth transitions between screens
- **Cross-Platform**: Supports iOS, Android, macOS, tvOS, and Web

---

## Prerequisites & Installation

### Requirements

| Package | Version | Notes |
|---------|---------|-------|
| React Native | 0.80+ | New Architecture (Fabric) required for v4.x |
| react-native-worklets | ^0.7.0 | Required peer dependency |
| react-native-reanimated | ^4.0.0 | Main package |

### Step 1: Install Packages

```bash
# Using npm
npm install react-native-reanimated react-native-worklets

# Using yarn
yarn add react-native-reanimated react-native-worklets
```

### Step 2: Configure Babel

Add the Reanimated plugin to your `babel.config.js`:

```javascript
module.exports = {
  presets: ['module:metro-react-native-babel-preset'],
  plugins: [
    'react-native-reanimated/plugin',  // Must be listed last
  ],
};
```

> **CRITICAL**: The Reanimated plugin MUST be the last plugin in the array.

### Step 3: Rebuild Native Code

```bash
# For Expo
npx expo prebuild

# For React Native CLI
cd ios && pod install && cd ..
npx react-native run-android  # or run-ios
```

### Step 4: Clear Cache (Important!)

```bash
# Metro cache
npx react-native start --reset-cache

# Or for Expo
expo start --clear
```

---

## Core Concepts

### 1. Shared Values

Shared values are the **driving factor** of all animations. They are synchronized between the JavaScript thread and the UI thread.

```typescript
import { useSharedValue } from 'react-native-reanimated';

function App() {
  // Create a shared value with initial value of 0
  const progress = useSharedValue(0);
  const opacity = useSharedValue(1);
  const scale = useSharedValue(1);
  
  // Can store any JS value: number, string, boolean, array, object
  const position = useSharedValue({ x: 0, y: 0 });
  const colors = useSharedValue(['red', 'blue', 'green']);
  
  return (
    // Component JSX
  );
}
```

**Key Points:**
- Access with `.value` property: `progress.value`
- Changes trigger animations when used in `useAnimatedStyle`
- Automatically synchronized between JS and UI threads
- Can hold any JavaScript value type

### 2. Animated Components

Reanimated provides pre-wrapped components that can be animated:

```typescript
import Animated from 'react-native-reanimated';

// Built-in animated components
<Animated.View />
<Animated.Text />
<Animated.Image />
<Animated.ScrollView />
<Animated.FlatList />
```

**Creating Custom Animated Components:**

```typescript
import Animated from 'react-native-reanimated';
import { Circle } from 'react-native-svg';

// Wrap any component to make it animatable
const AnimatedCircle = Animated.createAnimatedComponent(Circle);

// Usage
<AnimatedCircle cx={50} cy={50} r={30} fill="red" />
```

### 3. Worklets

Worklets are JavaScript functions that run synchronously on the UI thread.

```typescript
import { runOnUI } from 'react-native-reanimated';

// A worklet function - runs on UI thread
function myWorklet() {
  'worklet';  // Directive that marks this as a worklet
  
  // This code runs on the UI thread
  console.log('Running on UI thread');
}

// Execute worklet on UI thread
runOnUI(myWorklet)();
```

**The `'worklet'` Directive:**
- Must be the first statement in the function
- Tells the Babel plugin to process this function
- Automatically captures closure variables

---

## Your First Animation

### Basic Animation Example

```typescript
import React from 'react';
import { View, Button, StyleSheet } from 'react-native';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withTiming,
  withSpring,
} from 'react-native-reanimated';

export default function FirstAnimation() {
  // 1. Create shared value
  const offset = useSharedValue(0);

  // 2. Create animated style
  const animatedStyles = useAnimatedStyle(() => {
    return {
      transform: [{ translateX: offset.value }],
    };
  });

  // 3. Animation handlers
  const moveRight = () => {
    offset.value = withTiming(100, { duration: 500 });
  };

  const moveLeft = () => {
    offset.value = withSpring(-100, { damping: 10, stiffness: 100 });
  };

  const reset = () => {
    offset.value = withTiming(0, { duration: 300 });
  };

  return (
    <View style={styles.container}>
      {/* 4. Apply animated style to Animated component */}
      <Animated.View style={[styles.box, animatedStyles]} />
      
      <View style={styles.buttonContainer}>
        <Button title="Move Right" onPress={moveRight} />
        <Button title="Move Left" onPress={moveLeft} />
        <Button title="Reset" onPress={reset} />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  box: {
    width: 100,
    height: 100,
    backgroundColor: '#B58DF1',
    borderRadius: 16,
  },
  buttonContainer: {
    marginTop: 50,
    gap: 10,
  },
});
```

### How It Works

1. **Shared Value**: `offset` holds the animation state
2. **Animated Style**: `useAnimatedStyle` creates a style object that reacts to shared value changes
3. **Animation Functions**: `withTiming` and `withSpring` animate the value change
4. **Animated Component**: `Animated.View` receives the animated styles

---

## Architecture Overview

### Thread Model

```
┌─────────────────────────────────────────────────────────────┐
│                    JAVASCRIPT THREAD                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  React       │  │  Shared      │  │  Worklet     │      │
│  │  Components  │  │  Values      │  │  Definitions │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ Babel Plugin (Workletization)
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      UI THREAD (REANIMATED)                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Animation   │  │  Gesture     │  │  Style       │      │
│  │  Engine      │  │  Handlers    │  │  Updates     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      NATIVE PLATFORM                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  iOS Views   │  │  Android     │  │  Shadow      │      │
│  │  (UIKit)     │  │  Views       │  │  Tree        │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

### How Animations Flow

```
1. JS Thread: User triggers animation
   ↓
2. Shared Value: Value updated with animation function
   ↓
3. Worklet: Animation calculation runs on UI thread
   ↓
4. Native: Style updates applied to native views
   ↓
5. Screen: Frame rendered at 60fps
```

---

## Key Terminology (Glossary)

### Animated Component
A React Native component wrapped to accept animated styles and props.

```typescript
const AnimatedView = Animated.createAnimatedComponent(View);
```

### Shared Value
A reactive value synchronized between JS and UI threads.

```typescript
const sv = useSharedValue(0);  // Initial value: 0
sv.value = 100;  // Update value
```

### Animatable Value
A value type that can be animated: `number`, `string`, or `number[]`.

### Animation Function
Functions that animate value changes: `withTiming`, `withSpring`, `withDecay`.

### Animation Modifier
Functions that modify animations: `withDelay`, `withRepeat`, `withSequence`.

### Worklet
A JavaScript function marked with `'worklet'` directive that runs on the UI thread.

```typescript
function myWorklet() {
  'worklet';
  // UI thread code
}
```

### Workletization
The process of converting a JS function to a worklet via Babel plugin.

### JavaScript Thread
The main React Native thread where React components run.

### UI Thread
The separate thread where Reanimated animations execute.

### React Native New Architecture
The modern React Native architecture (Fabric renderer) required for Reanimated 4.x.

---

## Quick Reference: Essential Imports

```typescript
// Core
import Animated, {
  // Shared Values
  useSharedValue,
  
  // Styles & Props
  useAnimatedStyle,
  useAnimatedProps,
  
  // Animation Functions
  withTiming,
  withSpring,
  withDecay,
  
  // Modifiers
  withDelay,
  withRepeat,
  withSequence,
  withClamp,
  
  // Hooks
  useAnimatedRef,
  useDerivedValue,
  useAnimatedReaction,
  useAnimatedScrollHandler,
  
  // Utilities
  runOnJS,
  runOnUI,
  cancelAnimation,
  interpolate,
  interpolateColor,
} from 'react-native-reanimated';
```

---

## Common Patterns

### Pattern 1: Toggle Animation

```typescript
const isActive = useSharedValue(false);

const toggle = () => {
  isActive.value = !isActive.value;
};

const animatedStyles = useAnimatedStyle(() => ({
  transform: [{ scale: isActive.value ? 1.2 : 1 }],
  backgroundColor: isActive.value ? 'red' : 'blue',
}));
```

### Pattern 2: Value-Driven Animation

```typescript
const progress = useSharedValue(0);

const animatedStyles = useAnimatedStyle(() => ({
  opacity: progress.value,
  transform: [{ 
    translateX: interpolate(
      progress.value, 
      [0, 1], 
      [0, 100]
    ) 
  }],
}));
```

### Pattern 3: Gesture-Driven Animation

```typescript
const translateX = useSharedValue(0);
const translateY = useSharedValue(0);

const panGesture = Gesture.Pan()
  .onUpdate((event) => {
    translateX.value = event.translationX;
    translateY.value = event.translationY;
  })
  .onEnd(() => {
    translateX.value = withSpring(0);
    translateY.value = withSpring(0);
  });
```

---

## Next Steps

1. **[Animation Functions](./02-animation-functions.md)** - Learn `withTiming`, `withSpring`, `withDecay`
2. **[Hooks & Utilities](./03-hooks-utilities.md)** - Master `useAnimatedStyle`, `useDerivedValue`, etc.
3. **[Worklets Deep Dive](./04-worklets-deep-dive.md)** - Understand worklets thoroughly
4. **[Layout Animations](./05-layout-animations.md)** - Animate layout changes
5. **[Gestures Integration](./06-gestures-integration.md)** - Combine with Gesture Handler
6. **[Best Practices](./07-best-practices.md)** - Performance optimization and patterns

---

## Resources

- [Official Documentation](https://docs.swmansion.com/react-native-reanimated/)
- [GitHub Repository](https://github.com/software-mansion/react-native-reanimated)
- [React Native Gesture Handler](https://docs.swmansion.com/react-native-gesture-handler/)
- [Worklets Documentation](https://docs.swmansion.com/react-native-worklets/)
