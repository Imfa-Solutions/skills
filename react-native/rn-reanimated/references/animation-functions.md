# React Native Reanimated - Animation Functions

> **Complete Guide for LLMs** | Version: Reanimated 4.x | Last Updated: 2026-02-20

---

## Table of Contents

1. [Overview](#overview)
2. [withTiming](#withtiming)
3. [withSpring](#withspring)
4. [withDecay](#withdecay)
5. [Animation Modifiers](#animation-modifiers)
6. [Easing Functions](#easing-functions)
7. [Spring Configurations](#spring-configurations)
8. [Animation Callbacks](#animation-callbacks)
9. [Common Patterns](#common-patterns)

---

## Overview

Animation functions are the core building blocks of Reanimated. They define **how** values transition from one state to another.

```typescript
import {
  withTiming,    // Duration-based animation
  withSpring,    // Physics-based animation
  withDecay,     // Deceleration animation
  withDelay,     // Delay modifier
  withRepeat,    // Repeat modifier
  withSequence,  // Sequence modifier
  withClamp,     // Clamp modifier
} from 'react-native-reanimated';
```

### Animation Function Signature

```typescript
type AnimationFunction = <T extends AnimatableValue>(
  toValue: T,
  config?: AnimationConfig,
  callback?: (finished?: boolean, current?: AnimatableValue) => void
) => T;
```

---

## withTiming

Duration-based animation with customizable easing.

### Basic Usage

```typescript
import { withTiming, Easing } from 'react-native-reanimated';

// Simple timing animation
offset.value = withTiming(100, {
  duration: 500,
  easing: Easing.inOut(Easing.ease),
});
```

### Configuration Options

```typescript
interface WithTimingConfig {
  duration?: number;        // Animation duration in ms (default: 300)
  easing?: EasingFunction;  // Easing function (default: Easing.inOut(Easing.ease))
  reduceMotion?: ReduceMotion; // Accessibility option
}

enum ReduceMotion {
  System = 'system',    // Follow system settings
  Always = 'always',    // Always reduce motion
  Never = 'never',      // Never reduce motion
}
```

### Complete Example

```typescript
import React from 'react';
import { View, Button } from 'react-native';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withTiming,
  Easing,
} from 'react-native-reanimated';

function TimingExample() {
  const progress = useSharedValue(0);
  const opacity = useSharedValue(1);

  const animatedStyles = useAnimatedStyle(() => ({
    transform: [{ translateX: progress.value }],
    opacity: opacity.value,
  }));

  const animate = () => {
    // Reset
    progress.value = 0;
    opacity.value = 1;
    
    // Animate with timing
    progress.value = withTiming(200, {
      duration: 1000,
      easing: Easing.bezier(0.25, 0.1, 0.25, 1),
    }, (finished) => {
      if (finished) {
        console.log('Animation completed!');
      }
    });
    
    // Fade out
    opacity.value = withTiming(0.5, { duration: 1000 });
  };

  return (
    <View>
      <Animated.View style={[styles.box, animatedStyles]} />
      <Button title="Animate" onPress={animate} />
    </View>
  );
}
```

### Supported Value Types

```typescript
// Numbers
const width = useSharedValue(100);
width.value = withTiming(200);

// Strings with units
const widthPercent = useSharedValue('0%');
widthPercent.value = withTiming('100%');

// Colors (hex, rgb, rgba, hsl, named)
const color = useSharedValue('#FF0000');
color.value = withTiming('#00FF00');

// Arrays
const position = useSharedValue([0, 0]);
position.value = withTiming([100, 100]);

// Objects
const box = useSharedValue({ width: 100, height: 100 });
box.value = withTiming({ width: 200, height: 200 });
```

---

## withSpring

Physics-based spring animation for natural motion.

### Basic Usage

```typescript
import { withSpring } from 'react-native-reanimated';

// Simple spring
offset.value = withSpring(100);

// Custom spring configuration
offset.value = withSpring(100, {
  damping: 10,
  stiffness: 100,
  mass: 1,
});
```

### Configuration Options

```typescript
interface WithSpringConfig {
  damping?: number;           // Damping coefficient (default: 10)
  stiffness?: number;         // Spring stiffness (default: 100)
  mass?: number;              // Mass of the object (default: 1)
  overshootClamping?: boolean; // Clamp overshoot (default: false)
  restDisplacementThreshold?: number; // Rest threshold for position (default: 0.01)
  restSpeedThreshold?: number; // Rest threshold for speed (default: 2)
  reduceMotion?: ReduceMotion;
}
```

### Spring Presets

```typescript
import { withSpring } from 'react-native-reanimated';

// Gentle spring - slow, smooth
withSpring(targetValue, { damping: 15, stiffness: 50 });

// Wobbly spring - bouncy
withSpring(targetValue, { damping: 5, stiffness: 200 });

// Stiff spring - quick, snappy
withSpring(targetValue, { damping: 20, stiffness: 300 });

// No bounce - critically damped
withSpring(targetValue, { damping: 30, stiffness: 200 });
```

### Complete Example

```typescript
import React from 'react';
import { View, Gesture, GestureDetector } from 'react-native';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
} from 'react-native-reanimated';

function SpringExample() {
  const translateX = useSharedValue(0);
  const translateY = useSharedValue(0);
  const scale = useSharedValue(1);

  const animatedStyles = useAnimatedStyle(() => ({
    transform: [
      { translateX: translateX.value },
      { translateY: translateY.value },
      { scale: scale.value },
    ],
  }));

  const panGesture = Gesture.Pan()
    .onBegin(() => {
      scale.value = withSpring(1.2);
    })
    .onUpdate((event) => {
      translateX.value = event.translationX;
      translateY.value = event.translationY;
    })
    .onEnd((event) => {
      // Spring back to center with velocity
      translateX.value = withSpring(0, {
        velocity: event.velocityX,
        damping: 15,
        stiffness: 150,
      });
      translateY.value = withSpring(0, {
        velocity: event.velocityY,
        damping: 15,
        stiffness: 150,
      });
      scale.value = withSpring(1);
    });

  return (
    <GestureDetector gesture={panGesture}>
      <Animated.View style={[styles.box, animatedStyles]} />
    </GestureDetector>
  );
}
```

### Understanding Spring Physics

```
┌─────────────────────────────────────────────────────────────┐
│                    SPRING PHYSICS                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  stiffness: Higher = stronger spring = faster animation    │
│                                                             │
│  damping: Higher = more friction = less bounce             │
│                                                             │
│  mass: Higher = heavier object = slower animation          │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Low damping (5)  │  High damping (30)              │   │
│  │     ╱╲            │        ────                     │   │
│  │    ╱  ╲           │       ╱                         │   │
│  │   ╱    ╲          │      ╱                          │   │
│  │  ╱      ╲         │     ╱                           │   │
│  │ ╱        ╲        │    ╱                            │   │
│  │╱          ╲       │   ╱                             │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

## withDecay

Deceleration animation that simulates friction.

### Basic Usage

```typescript
import { withDecay } from 'react-native-reanimated';

// Decay with velocity
offset.value = withDecay({
  velocity: 500,  // Initial velocity
  deceleration: 0.997, // Deceleration rate
});
```

### Configuration Options

```typescript
interface WithDecayConfig {
  velocity: number;           // Initial velocity (required)
  deceleration?: number;      // Deceleration rate 0-1 (default: 0.997)
  clamp?: [number, number];   // Clamp values [min, max]
  velocityFactor?: number;    // Velocity multiplier (default: 1)
  rubberBandEffect?: boolean; // Enable rubber band at clamps
  rubberBandFactor?: number;  // Rubber band strength (default: 0.6)
  reduceMotion?: ReduceMotion;
}
```

### Complete Example

```typescript
import React from 'react';
import { View } from 'react-native';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withDecay,
} from 'react-native-reanimated';

function DecayExample() {
  const translateX = useSharedValue(0);
  const context = useSharedValue(0);

  const animatedStyles = useAnimatedStyle(() => ({
    transform: [{ translateX: translateX.value }],
  }));

  const panGesture = Gesture.Pan()
    .onBegin(() => {
      context.value = translateX.value;
    })
    .onUpdate((event) => {
      translateX.value = context.value + event.translationX;
    })
    .onEnd((event) => {
      translateX.value = withDecay({
        velocity: event.velocityX,
        deceleration: 0.995,
        clamp: [-200, 200], // Limit movement
      });
    });

  return (
    <GestureDetector gesture={panGesture}>
      <Animated.View style={[styles.box, animatedStyles]} />
    </GestureDetector>
  );
}
```

---

## Animation Modifiers

Modifiers wrap animation functions to add behavior.

### withDelay

Delays the start of an animation.

```typescript
import { withDelay, withTiming } from 'react-native-reanimated';

// Wait 500ms before starting animation
offset.value = withDelay(500, withTiming(100, { duration: 300 }));
```

### withRepeat

Repeats an animation.

```typescript
import { withRepeat, withTiming } from 'react-native-reanimated';

interface WithRepeatConfig {
  count: number;           // Number of repetitions (-1 for infinite)
  reverse?: boolean;       // Reverse on each repeat (default: false)
  reduceMotion?: ReduceMotion;
}

// Repeat 3 times
offset.value = withRepeat(
  withTiming(100, { duration: 500 }),
  3,
  true // Reverse direction each time
);

// Infinite repeat
offset.value = withRepeat(
  withTiming(100, { duration: 500 }),
  -1, // Infinite
  true
);
```

### withSequence

Runs animations in sequence.

```typescript
import { withSequence, withTiming, withSpring } from 'react-native-reanimated';

// Move right, then up, then scale
offset.value = withSequence(
  withTiming(100, { duration: 300 }),
  withSpring(200),
  withTiming(0, { duration: 500 })
);
```

### withClamp

Clamps animation values within a range.

```typescript
import { withClamp, withSpring } from 'react-native-reanimated';

// Clamp between 0 and 100
offset.value = withClamp(
  [0, 100],
  withSpring(targetValue)
);
```

### Combining Modifiers

```typescript
// Delayed, repeating, spring animation
offset.value = withDelay(
  500,
  withRepeat(
    withSpring(100, { damping: 10 }),
    3,
    true
  )
);

// Sequence with delays
offset.value = withSequence(
  withDelay(100, withTiming(50, { duration: 200 })),
  withDelay(100, withTiming(100, { duration: 200 })),
  withDelay(100, withTiming(0, { duration: 200 }))
);
```

---

## Easing Functions

Easing functions control the acceleration curve of timing animations.

### Built-in Easings

```typescript
import { Easing } from 'react-native-reanimated';

// Linear
Easing.linear

// Ease in (start slow, end fast)
Easing.in(Easing.ease)
Easing.in(Easing.quad)
Easing.in(Easing.cubic)

// Ease out (start fast, end slow)
Easing.out(Easing.ease)
Easing.out(Easing.quad)
Easing.out(Easing.cubic)

// Ease in-out (slow start and end)
Easing.inOut(Easing.ease)
Easing.inOut(Easing.quad)
Easing.inOut(Easing.cubic)

// Back (overshoot)
Easing.back(1.70158) // overshoot amount

// Elastic (bounce)
Easing.elastic(1) // bounciness

// Bounce
Easing.bounce
```

### Custom Bezier Curves

```typescript
import { Easing } from 'react-native-reanimated';

// Custom cubic bezier
const customEasing = Easing.bezier(0.4, 0.0, 0.2, 1);

// Common curves
const easeInOut = Easing.bezier(0.42, 0, 0.58, 1);
const easeOut = Easing.bezier(0, 0, 0.58, 1);
const easeIn = Easing.bezier(0.42, 0, 1, 1);
```

### Visual Reference

```
Linear:      ════════════════════════
             
Ease In:                    ═══════
                        ════
                    ═══
                ══
             ═

Ease Out:    ═
                ══
                    ═══
                        ════
                            ═══════

Ease In-Out:           ═════════
                    ════
                ═══
                    ════
                        ═════════
```

---

## Spring Configurations

### Preset Configurations

```typescript
// Gentle - smooth, slow
const gentle = {
  damping: 15,
  stiffness: 50,
  mass: 1,
};

// Wobbly - bouncy
const wobbly = {
  damping: 5,
  stiffness: 200,
  mass: 1,
};

// Stiff - quick, snappy
const stiff = {
  damping: 20,
  stiffness: 300,
  mass: 1,
};

// No bounce - critically damped
const noBounce = {
  damping: 30,
  stiffness: 200,
  mass: 1,
};
```

### Calculating Spring Parameters

```typescript
// For a specific duration and bounce
function calculateSpringConfig(
  duration: number, // in seconds
  bounce: number   // 0-1, where 0 is no bounce
) {
  const stiffness = Math.pow(Math.PI / duration, 2);
  const damping = 2 * (1 - bounce) * Math.sqrt(stiffness);
  
  return { stiffness, damping, mass: 1 };
}
```

---

## Animation Callbacks

Callbacks execute when animations complete or are interrupted.

```typescript
// Basic callback
offset.value = withTiming(100, {}, (finished) => {
  if (finished) {
    console.log('Animation completed successfully');
  } else {
    console.log('Animation was interrupted');
  }
});

// Chaining animations with callbacks
function chainAnimations() {
  offset.value = withTiming(100, {}, (finished) => {
    if (finished) {
      opacity.value = withTiming(0, {}, (finished) => {
        if (finished) {
          scale.value = withSpring(1.5);
        }
      });
    }
  });
}

// Run JS code from callback (use runOnJS)
import { runOnJS } from 'react-native-reanimated';

function onAnimationComplete() {
  // This runs on JS thread
  console.log('Done!');
  setState('completed');
}

offset.value = withTiming(100, {}, (finished) => {
  if (finished) {
    runOnJS(onAnimationComplete)();
  }
});
```

---

## Common Patterns

### Pattern 1: Fade In/Out

```typescript
const opacity = useSharedValue(0);

const fadeIn = () => {
  opacity.value = withTiming(1, { duration: 300 });
};

const fadeOut = () => {
  opacity.value = withTiming(0, { duration: 300 });
};
```

### Pattern 2: Scale Animation

```typescript
const scale = useSharedValue(1);

const pulse = () => {
  scale.value = withSequence(
    withSpring(1.2, { damping: 5 }),
    withSpring(1, { damping: 5 })
  );
};
```

### Pattern 3: Slide Animation

```typescript
const translateX = useSharedValue(-300);

const slideIn = () => {
  translateX.value = withSpring(0, { damping: 15 });
};

const slideOut = () => {
  translateX.value = withSpring(-300, { damping: 15 });
};
```

### Pattern 4: Rotation Animation

```typescript
const rotation = useSharedValue(0);

const rotate = () => {
  rotation.value = withRepeat(
    withTiming(360, { duration: 2000, easing: Easing.linear }),
    -1
  );
};
```

### Pattern 5: Color Interpolation

```typescript
const progress = useSharedValue(0);

const animateColor = () => {
  progress.value = withTiming(1, { duration: 1000 });
};

const animatedStyles = useAnimatedStyle(() => ({
  backgroundColor: interpolateColor(
    progress.value,
    [0, 1],
    ['#FF0000', '#00FF00']
  ),
}));
```

### Pattern 6: Staggered Animations

```typescript
const items = [0, 1, 2, 3, 4];
const animations = items.map(() => useSharedValue(0));

const staggerIn = () => {
  animations.forEach((anim, index) => {
    anim.value = withDelay(
      index * 100,
      withSpring(1, { damping: 12 })
    );
  });
};
```

### Pattern 7: Gesture with Spring

```typescript
const panGesture = Gesture.Pan()
  .onUpdate((event) => {
    translateX.value = event.translationX;
  })
  .onEnd((event) => {
    // Spring to nearest snap point
    const snapPoints = [0, 100, 200];
    const nearest = snapPoints.reduce((prev, curr) =>
      Math.abs(curr - translateX.value) < Math.abs(prev - translateX.value)
        ? curr
        : prev
    );
    
    translateX.value = withSpring(nearest, {
      velocity: event.velocityX,
    });
  });
```

---

## Best Practices

### DO's

✅ **Use springs for interactive animations**
```typescript
// Good - spring follows gesture naturally
translateX.value = withSpring(0, { velocity: event.velocityX });
```

✅ **Set appropriate durations**
```typescript
// Good - 200-300ms for UI feedback
opacity.value = withTiming(1, { duration: 200 });
```

✅ **Use callbacks for sequencing**
```typescript
// Good - chain animations properly
offset.value = withTiming(100, {}, () => {
  opacity.value = withTiming(0);
});
```

✅ **Handle reduced motion**
```typescript
// Good - respect accessibility settings
offset.value = withTiming(100, {
  reduceMotion: ReduceMotion.System,
});
```

### DON'Ts

❌ **Don't use timing for gesture-driven animations**
```typescript
// Bad - feels unnatural
translateX.value = withTiming(event.translationX);

// Good - follows finger naturally
translateX.value = event.translationX;
```

❌ **Don't create animations in render**
```typescript
// Bad - creates new animation every render
<Animated.View style={{
  transform: [{ translateX: withTiming(value) }]
}} />

// Good - use shared values and animated styles
const offset = useSharedValue(0);
const style = useAnimatedStyle(() => ({
  transform: [{ translateX: offset.value }]
}));
```

❌ **Don't forget to handle interruptions**
```typescript
// Bad - callback may not fire
offset.value = withTiming(100, {}, () => {
  // May never execute if interrupted
  doSomething();
});

// Good - check finished parameter
offset.value = withTiming(100, {}, (finished) => {
  if (finished) {
    doSomething();
  }
});
```

---

## Quick Reference Card

```typescript
// Timing - duration-based
withTiming(toValue, {
  duration: 300,
  easing: Easing.inOut(Easing.ease),
});

// Spring - physics-based
withSpring(toValue, {
  damping: 10,
  stiffness: 100,
  mass: 1,
});

// Decay - friction-based
withDecay({
  velocity: 500,
  deceleration: 0.997,
});

// Modifiers
withDelay(500, withSpring(100));
withRepeat(withTiming(100), 3, true);
withSequence(withTiming(50), withTiming(100));
withClamp([0, 100], withSpring(200));
```
