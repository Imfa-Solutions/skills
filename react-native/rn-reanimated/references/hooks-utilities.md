# React Native Reanimated - Hooks & Utilities

> **Complete Guide for LLMs** | Version: Reanimated 4.x | Last Updated: 2026-02-20

---

## Table of Contents

1. [Overview](#overview)
2. [useSharedValue](#usesharedvalue)
3. [useAnimatedStyle](#useanimatedstyle)
4. [useAnimatedProps](#useanimatedprops)
5. [useDerivedValue](#usederivedvalue)
6. [useAnimatedReaction](#useanimatedreaction)
7. [useAnimatedRef](#useanimatedref)
8. [useAnimatedScrollHandler](#useanimatedscrollhandler)
9. [useScrollViewOffset](#usescrollviewoffset)
10. [useAnimatedKeyboard](#useanimatedkeyboard)
11. [useAnimatedSensor](#useanimatedsensor)
12. [useFrameCallback](#useframecallback)
13. [Utility Functions](#utility-functions)

---

## Overview

Reanimated provides a rich set of hooks and utilities for creating animations. These hooks are designed to work seamlessly with the UI thread.

```typescript
// Essential hooks
import {
  useSharedValue,        // Create reactive values
  useAnimatedStyle,      // Create animated styles
  useAnimatedProps,      // Create animated props
  useDerivedValue,       // Compute derived values
  useAnimatedReaction,   // React to value changes
  useAnimatedRef,        // Get component ref
  useAnimatedScrollHandler, // Handle scroll events
  useScrollViewOffset,   // Track scroll offset
  useAnimatedKeyboard,   // Track keyboard
  useAnimatedSensor,     // Access device sensors
  useFrameCallback,      // Run on each frame
} from 'react-native-reanimated';

// Utilities
import {
  runOnJS,               // Run JS from UI thread
  runOnUI,               // Run on UI thread
  cancelAnimation,       // Cancel animation
  interpolate,           // Map ranges
  interpolateColor,      // Interpolate colors
  clamp,                 // Clamp values
} from 'react-native-reanimated';
```

---

## useSharedValue

Creates a reactive value synchronized between JS and UI threads.

### Basic Usage

```typescript
const value = useSharedValue(initialValue);
```

### Examples

```typescript
// Numbers
const opacity = useSharedValue(1);
const scale = useSharedValue(1);
const translateX = useSharedValue(0);

// Strings
const color = useSharedValue('#FF0000');
const width = useSharedValue('100%');

// Arrays
const position = useSharedValue([0, 0]);
const matrix = useSharedValue([1, 0, 0, 1, 0, 0]);

// Objects
const box = useSharedValue({ width: 100, height: 100 });
const transform = useSharedValue([
  { translateX: 0 },
  { scale: 1 },
]);
```

### Reading and Writing

```typescript
function Example() {
  const offset = useSharedValue(0);

  // Read value (in JS or worklet)
  console.log(offset.value); // 0

  // Write value (in JS or worklet)
  offset.value = 100;

  // Write with animation
  offset.value = withTiming(100);

  return (
    <Animated.View
      style={useAnimatedStyle(() => ({
        transform: [{ translateX: offset.value }],
      }))}
    />
  );
}
```

### Best Practices

```typescript
// ✅ Initialize with correct type
const offset = useSharedValue<number>(0);
const color = useSharedValue<string>('#000');

// ✅ Use for animation state
const isAnimating = useSharedValue(false);

// ✅ Store complex objects
const gestureState = useSharedValue({
  startX: 0,
  startY: 0,
  translationX: 0,
  translationY: 0,
});

// ❌ Don't use for React state
const [count, setCount] = useState(0); // Use useState instead
```

---

## useAnimatedStyle

Creates a style object that automatically updates when shared values change.

### Basic Usage

```typescript
const animatedStyle = useAnimatedStyle(() => {
  return {
    // style properties based on shared values
  };
}, dependencies);
```

### Examples

```typescript
// Simple transform
const offset = useSharedValue(0);

const animatedStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: offset.value }],
}));
```

```typescript
// Multiple transforms
const translateX = useSharedValue(0);
const translateY = useSharedValue(0);
const scale = useSharedValue(1);
const rotation = useSharedValue(0);

const animatedStyle = useAnimatedStyle(() => ({
  transform: [
    { translateX: translateX.value },
    { translateY: translateY.value },
    { scale: scale.value },
    { rotate: `${rotation.value}rad` },
  ],
}));
```

```typescript
// Multiple properties
const progress = useSharedValue(0);

const animatedStyle = useAnimatedStyle(() => ({
  opacity: interpolate(progress.value, [0, 1], [0.5, 1]),
  backgroundColor: interpolateColor(
    progress.value,
    [0, 1],
    ['#FF0000', '#00FF00']
  ),
  borderRadius: interpolate(progress.value, [0, 1], [0, 20]),
  transform: [
    { scale: interpolate(progress.value, [0, 1], [0.8, 1]) },
  ],
}));
```

### Conditional Styles

```typescript
const isActive = useSharedValue(false);

const animatedStyle = useAnimatedStyle(() => ({
  backgroundColor: isActive.value ? '#00FF00' : '#FF0000',
  transform: [
    { scale: withSpring(isActive.value ? 1.2 : 1) },
  ],
}));
```

### Dependencies Array

```typescript
// Re-create style when external value changes
const animatedStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: offset.value + externalValue }],
}), [externalValue]); // Re-create when externalValue changes
```

### Complete Example

```typescript
import React from 'react';
import { View, Button } from 'react-native';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  withTiming,
  interpolate,
  interpolateColor,
} from 'react-native-reanimated';

function AnimatedCard() {
  const progress = useSharedValue(0);

  const animatedStyle = useAnimatedStyle(() => ({
    // Opacity from 0.5 to 1
    opacity: interpolate(progress.value, [0, 1], [0.5, 1]),
    
    // Background color transition
    backgroundColor: interpolateColor(
      progress.value,
      [0, 0.5, 1],
      ['#FF6B6B', '#FFE66D', '#4ECDC4']
    ),
    
    // Border radius animation
    borderRadius: interpolate(progress.value, [0, 1], [0, 24]),
    
    // Scale and rotation
    transform: [
      { scale: interpolate(progress.value, [0, 1], [0.8, 1]) },
      { rotate: `${interpolate(progress.value, [0, 1], [0, 10])}deg` },
    ],
    
    // Shadow
    shadowOpacity: interpolate(progress.value, [0, 1], [0, 0.3]),
    shadowRadius: interpolate(progress.value, [0, 1], [0, 10]),
    elevation: interpolate(progress.value, [0, 1], [0, 8]),
  }));

  const animate = () => {
    progress.value = withTiming(1, { duration: 1000 });
  };

  const reset = () => {
    progress.value = withSpring(0);
  };

  return (
    <View>
      <Animated.View style={[styles.card, animatedStyle]}>
        <View style={styles.content}>
          <Text>Animated Card</Text>
        </View>
      </Animated.View>
      <Button title="Animate" onPress={animate} />
      <Button title="Reset" onPress={reset} />
    </View>
  );
}

const styles = StyleSheet.create({
  card: {
    width: 200,
    height: 120,
    padding: 16,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 4 },
  },
  content: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});
```

---

## useAnimatedProps

Animates props of native components (like SVG paths, text inputs).

### Basic Usage

```typescript
const animatedProps = useAnimatedProps(() => {
  return {
    // props based on shared values
  };
});
```

### SVG Example

```typescript
import Svg, { Circle } from 'react-native-svg';
import Animated from 'react-native-reanimated';

const AnimatedCircle = Animated.createAnimatedComponent(Circle);

function PulsingCircle() {
  const radius = useSharedValue(50);

  const animatedProps = useAnimatedProps(() => ({
    r: radius.value,
    fillOpacity: interpolate(radius.value, [50, 100], [1, 0.5]),
  }));

  React.useEffect(() => {
    radius.value = withRepeat(
      withTiming(100, { duration: 1000 }),
      -1,
      true
    );
  }, []);

  return (
    <Svg width={200} height={200}>
      <AnimatedCircle
        cx={100}
        cy={100}
        animatedProps={animatedProps}
        fill="red"
      />
    </Svg>
  );
}
```

### TextInput Example

```typescript
import { TextInput } from 'react-native';

const AnimatedTextInput = Animated.createAnimatedComponent(TextInput);

function AnimatedPlaceholder() {
  const focusProgress = useSharedValue(0);

  const animatedProps = useAnimatedProps(() => ({
    placeholderTextColor: interpolateColor(
      focusProgress.value,
      [0, 1],
      ['#999', '#333']
    ),
  }));

  return (
    <AnimatedTextInput
      animatedProps={animatedProps}
      placeholder="Enter text..."
      onFocus={() => { focusProgress.value = withTiming(1); }}
      onBlur={() => { focusProgress.value = withTiming(0); }}
    />
  );
}
```

---

## useDerivedValue

Creates a computed value that updates automatically when dependencies change.

### Basic Usage

```typescript
const derivedValue = useDerivedValue(() => {
  return computation;
}, dependencies);
```

### Examples

```typescript
// Simple derivation
const width = useSharedValue(100);
const height = useSharedValue(100);

const area = useDerivedValue(() => {
  return width.value * height.value;
});
```

```typescript
// Position calculation
const progress = useSharedValue(0);

const position = useDerivedValue(() => {
  return {
    x: interpolate(progress.value, [0, 1], [0, 200]),
    y: interpolate(progress.value, [0, 1], [0, 100]),
  };
});

// Use in style
const animatedStyle = useAnimatedStyle(() => ({
  transform: [
    { translateX: position.value.x },
    { translateY: position.value.y },
  ],
}));
```

```typescript
// Complex derivation
const gestureX = useSharedValue(0);
const gestureY = useSharedValue(0);
const baseX = useSharedValue(100);
const baseY = useSharedValue(100);

const finalPosition = useDerivedValue(() => {
  const distance = Math.sqrt(
    gestureX.value ** 2 + gestureY.value ** 2
  );
  
  return {
    x: baseX.value + gestureX.value,
    y: baseY.value + gestureY.value,
    distance,
    isFar: distance > 100,
  };
});
```

### With Dependencies

```typescript
const offset = useSharedValue(0);
const multiplier = useSharedValue(1);

// Re-computes when multiplier changes
const scaledOffset = useDerivedValue(() => {
  return offset.value * multiplier.value;
}, [multiplier]); // Dependency
```

---

## useAnimatedReaction

Reacts to changes in shared values and runs a side effect.

### Basic Usage

```typescript
useAnimatedReaction(
  () => {
    // Prepare data (runs on UI thread)
    return data;
  },
  (data, previousData) => {
    // React to changes (runs on UI thread)
  },
  dependencies
);
```

### Examples

```typescript
// Track value changes
const offset = useSharedValue(0);

useAnimatedReaction(
  () => offset.value,
  (currentValue, previousValue) => {
    if (currentValue > 100 && previousValue <= 100) {
      console.log('Crossed threshold!');
    }
  }
);
```

```typescript
// Sync values
const translateX = useSharedValue(0);
const translateY = useSharedValue(0);
const distance = useSharedValue(0);

useAnimatedReaction(
  () => ({
    x: translateX.value,
    y: translateY.value,
  }),
  (current, previous) => {
    distance.value = Math.sqrt(
      current.x ** 2 + current.y ** 2
    );
  }
);
```

```typescript
// Trigger JS callback
const progress = useSharedValue(0);

const onProgressComplete = () => {
  // Runs on JS thread
  console.log('Progress complete!');
  setState('done');
};

useAnimatedReaction(
  () => progress.value,
  (current) => {
    if (current >= 1) {
      runOnJS(onProgressComplete)();
    }
  }
);
```

### Complete Example

```typescript
function ScrollProgressTracker() {
  const scrollY = useSharedValue(0);
  const progress = useSharedValue(0);
  const [jsProgress, setJsProgress] = useState(0);

  // Calculate progress on UI thread
  useAnimatedReaction(
    () => scrollY.value,
    (current) => {
      const contentHeight = 1000;
      const containerHeight = 300;
      const maxScroll = contentHeight - containerHeight;
      
      progress.value = Math.min(Math.max(current / maxScroll, 0), 1);
      
      // Sync to JS state (throttled)
      if (Math.abs(progress.value - jsProgress) > 0.05) {
        runOnJS(setJsProgress)(progress.value);
      }
    }
  );

  const scrollHandler = useAnimatedScrollHandler({
    onScroll: (event) => {
      scrollY.value = event.contentOffset.y;
    },
  });

  return (
    <View>
      <ProgressBar progress={jsProgress} />
      <Animated.ScrollView onScroll={scrollHandler}>
        {/* Content */}
      </Animated.ScrollView>
    </View>
  );
}
```

---

## useAnimatedRef

Gets a reference to an animated component for measuring and commands.

### Basic Usage

```typescript
const animatedRef = useAnimatedRef<Animated.View>();
```

### Examples

```typescript
// Measure component
const viewRef = useAnimatedRef<Animated.View>();
const layout = useSharedValue({ x: 0, y: 0, width: 0, height: 0 });

const measureView = () => {
  const measurement = measure(viewRef);
  if (measurement) {
    layout.value = measurement;
  }
};
```

```typescript
// Scroll to position
const scrollRef = useAnimatedRef<Animated.ScrollView>();

const scrollToTop = () => {
  scrollTo(scrollRef, 0, 0, true);
};

const scrollToPosition = (y: number) => {
  scrollTo(scrollRef, 0, y, true);
};
```

### Complete Example

```typescript
function MeasurableView() {
  const viewRef = useAnimatedRef<Animated.View>();
  const position = useSharedValue({ x: 0, y: 0 });
  const size = useSharedValue({ width: 0, height: 0 });

  const handleMeasure = () => {
    const measurement = measure(viewRef);
    if (measurement) {
      position.value = { x: measurement.pageX, y: measurement.pageY };
      size.value = { width: measurement.width, height: measurement.height };
    }
  };

  return (
    <View>
      <Animated.View ref={viewRef} style={styles.box}>
        <Text>Measure me!</Text>
      </Animated.View>
      <Button title="Measure" onPress={handleMeasure} />
      <Text>
        Position: {position.value.x}, {position.value.y}
      </Text>
      <Text>
        Size: {size.value.width} x {size.value.height}
      </Text>
    </View>
  );
}
```

---

## useAnimatedScrollHandler

Handles scroll events with worklet support.

### Basic Usage

```typescript
const scrollHandler = useAnimatedScrollHandler({
  onScroll: (event) => {
    // Handle scroll
  },
  onBeginDrag: (event) => {
    // Handle drag start
  },
  onEndDrag: (event) => {
    // Handle drag end
  },
  onMomentumBegin: (event) => {
    // Handle momentum start
  },
  onMomentumEnd: (event) => {
    // Handle momentum end
  },
});
```

### Examples

```typescript
// Basic scroll tracking
const scrollY = useSharedValue(0);

const scrollHandler = useAnimatedScrollHandler({
  onScroll: (event) => {
    scrollY.value = event.contentOffset.y;
  },
});

// Use in component
<Animated.ScrollView onScroll={scrollHandler}>
  {/* Content */}
</Animated.ScrollView>
```

```typescript
// Parallax header effect
const scrollY = useSharedValue(0);

const scrollHandler = useAnimatedScrollHandler({
  onScroll: (event) => {
    scrollY.value = event.contentOffset.y;
  },
});

const headerStyle = useAnimatedStyle(() => ({
  transform: [
    { translateY: scrollY.value * 0.5 },
  ],
  opacity: interpolate(
    scrollY.value,
    [0, 200],
    [1, 0],
    Extrapolation.CLAMP
  ),
}));
```

```typescript
// Scroll with context
const scrollHandler = useAnimatedScrollHandler(
  {
    onScroll: (event, context) => {
      // Access context
      const delta = event.contentOffset.y - context.startY;
    },
    onBeginDrag: (event, context) => {
      context.startY = event.contentOffset.y;
    },
  },
  [] // Dependencies
);
```

### Complete Example

```typescript
function CollapsibleHeader() {
  const scrollY = useSharedValue(0);
  const headerHeight = 200;

  const scrollHandler = useAnimatedScrollHandler({
    onScroll: (event) => {
      scrollY.value = event.contentOffset.y;
    },
  });

  const headerStyle = useAnimatedStyle(() => ({
    height: interpolate(
      scrollY.value,
      [0, headerHeight],
      [headerHeight, 60],
      Extrapolation.CLAMP
    ),
    opacity: interpolate(
      scrollY.value,
      [0, headerHeight / 2],
      [1, 0],
      Extrapolation.CLAMP
    ),
  }));

  const titleStyle = useAnimatedStyle(() => ({
    transform: [
      {
        translateY: interpolate(
          scrollY.value,
          [0, headerHeight],
          [0, -headerHeight + 60],
          Extrapolation.CLAMP
        ),
      },
    ],
    fontSize: interpolate(
      scrollY.value,
      [0, headerHeight],
      [32, 18],
      Extrapolation.CLAMP
    ),
  }));

  return (
    <View style={styles.container}>
      <Animated.View style={[styles.header, headerStyle]}>
        <Animated.Text style={[styles.title, titleStyle]}>
          Header Title
        </Animated.Text>
      </Animated.View>
      <Animated.ScrollView
        onScroll={scrollHandler}
        scrollEventThrottle={16}
        style={styles.scrollView}
      >
        {/* Scroll content */}
      </Animated.ScrollView>
    </View>
  );
}
```

---

## useScrollViewOffset

Simplified hook for tracking scroll offset.

### Basic Usage

```typescript
const scrollRef = useAnimatedRef<Animated.ScrollView>();
const offset = useScrollViewOffset(scrollRef);
```

### Example

```typescript
function SimpleScrollTracker() {
  const scrollRef = useAnimatedRef<Animated.ScrollView>();
  const scrollY = useScrollViewOffset(scrollRef);

  const progressStyle = useAnimatedStyle(() => ({
    width: `${(scrollY.value / 1000) * 100}%`,
  }));

  return (
    <View>
      <Animated.View style={[styles.progressBar, progressStyle]} />
      <Animated.ScrollView ref={scrollRef}>
        {/* Content */}
      </Animated.ScrollView>
    </View>
  );
}
```

---

## useAnimatedKeyboard

Tracks keyboard state and dimensions.

### Basic Usage

```typescript
const { height, state } = useAnimatedKeyboard();
```

### Configuration

```typescript
const keyboard = useAnimatedKeyboard({
  isStatusBarTranslucentAndroid: false,
  isNavigationBarTranslucentAndroid: false,
});
```

### Example

```typescript
function KeyboardAwareView() {
  const { height: keyboardHeight } = useAnimatedKeyboard();

  const animatedStyle = useAnimatedStyle(() => ({
    paddingBottom: keyboardHeight.value,
  }));

  return (
    <Animated.View style={[styles.container, animatedStyle]}>
      <TextInput style={styles.input} />
    </Animated.View>
  );
}
```

### Complete Example

```typescript
function ChatScreen() {
  const { height: keyboardHeight } = useAnimatedKeyboard();
  const scrollRef = useAnimatedRef<Animated.ScrollView>();

  const containerStyle = useAnimatedStyle(() => ({
    paddingBottom: keyboardHeight.value + 60,
  }));

  useAnimatedReaction(
    () => keyboardHeight.value,
    (current, previous) => {
      if (current > 0 && previous === 0) {
        // Keyboard opened - scroll to bottom
        scrollTo(scrollRef, 0, 99999, true);
      }
    }
  );

  return (
    <View style={styles.container}>
      <Animated.ScrollView
        ref={scrollRef}
        style={containerStyle}
      >
        {/* Messages */}
      </Animated.ScrollView>
      <View style={styles.inputContainer}>
        <TextInput style={styles.input} />
        <Button title="Send" />
      </View>
    </View>
  );
}
```

---

## useAnimatedSensor

Accesses device sensors for motion-based animations.

### Sensor Types

```typescript
enum SensorType {
  ACCELEROMETER = 1,
  GYROSCOPE = 2,
  GRAVITY = 3,
  MAGNETIC_FIELD = 4,
  ROTATION = 5,
}
```

### Basic Usage

```typescript
const sensor = useAnimatedSensor(SensorType.GYROSCOPE);
```

### Configuration

```typescript
const sensor = useAnimatedSensor(SensorType.GYROSCOPE, {
  interval: 16, // Update interval in ms (default: 16 = ~60fps)
  adjustToInterfaceOrientation: true,
});
```

### Example

```typescript
function GyroscopeCard() {
  const sensor = useAnimatedSensor(SensorType.GYROSCOPE);

  const animatedStyle = useAnimatedStyle(() => {
    const { x, y } = sensor.sensor.value;
    
    return {
      transform: [
        { rotateX: `${-y * 10}deg` },
        { rotateY: `${x * 10}deg` },
      ],
    };
  });

  return (
    <View style={styles.container}>
      <Animated.View style={[styles.card, animatedStyle]}>
        <Text>Tilt your device!</Text>
      </Animated.View>
    </View>
  );
}
```

### Complete Example

```typescript
function ParallaxBackground() {
  const accelerometer = useAnimatedSensor(SensorType.ACCELEROMETER);
  const gyroscope = useAnimatedSensor(SensorType.GYROSCOPE);

  const backgroundStyle = useAnimatedStyle(() => {
    const accel = accelerometer.sensor.value;
    const gyro = gyroscope.sensor.value;
    
    return {
      transform: [
        { translateX: accel.x * 20 },
        { translateY: accel.y * 20 },
        { rotate: `${gyro.z * 5}deg` },
      ],
    };
  });

  const foregroundStyle = useAnimatedStyle(() => {
    const accel = accelerometer.sensor.value;
    
    return {
      transform: [
        { translateX: accel.x * 10 },
        { translateY: accel.y * 10 },
      ],
    };
  });

  return (
    <View style={styles.container}>
      <Animated.Image
        source={require('./background.png')}
        style={[StyleSheet.absoluteFill, backgroundStyle]}
      />
      <Animated.View style={foregroundStyle}>
        {/* Foreground content */}
      </Animated.View>
    </View>
  );
}
```

---

## useFrameCallback

Runs a callback on every animation frame.

### Basic Usage

```typescript
const frameCallback = useFrameCallback((frameInfo) => {
  // Runs every frame
  // frameInfo.timestamp - current time
  // frameInfo.timeSincePreviousFrame - delta time
  // frameInfo.timeSinceFirstFrame - total elapsed time
});
```

### Configuration

```typescript
const frameCallback = useFrameCallback((frameInfo) => {
  // Frame logic
}, false); // false = don't auto-start
```

### Control Methods

```typescript
frameCallback.setActive(true);   // Start
frameCallback.setActive(false);  // Stop
frameCallback.isActive;          // Check if running
```

### Example

```typescript
function GameLoop() {
  const position = useSharedValue({ x: 0, y: 0 });
  const velocity = useSharedValue({ x: 2, y: 1 });

  const frameCallback = useFrameCallback((frameInfo) => {
    const dt = frameInfo.timeSincePreviousFrame ?? 0;
    
    position.value = {
      x: position.value.x + velocity.value.x * dt * 0.1,
      y: position.value.y + velocity.value.y * dt * 0.1,
    };
    
    // Bounce off edges
    if (position.value.x > 300 || position.value.x < 0) {
      velocity.value.x *= -1;
    }
    if (position.value.y > 500 || position.value.y < 0) {
      velocity.value.y *= -1;
    }
  });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [
      { translateX: position.value.x },
      { translateY: position.value.y },
    ],
  }));

  return (
    <View>
      <Button
        title={frameCallback.isActive ? 'Stop' : 'Start'}
        onPress={() => frameCallback.setActive(!frameCallback.isActive)}
      />
      <Animated.View style={[styles.ball, animatedStyle]} />
    </View>
  );
}
```

---

## Utility Functions

### runOnJS

Runs JavaScript code from the UI thread.

```typescript
import { runOnJS } from 'react-native-reanimated';

const jsFunction = (value: number) => {
  // Runs on JS thread
  console.log('JS:', value);
  setState(value);
};

// In worklet
someWorklet() {
  'worklet';
  runOnJS(jsFunction)(42);
}
```

### runOnUI

Runs worklet on the UI thread.

```typescript
import { runOnUI } from 'react-native-reanimated';

const worklet = (value: number) => {
  'worklet';
  // Runs on UI thread
  offset.value = withSpring(value);
};

// From JS thread
runOnUI(worklet)(100);
```

### cancelAnimation

Cancels a running animation.

```typescript
import { cancelAnimation } from 'react-native-reanimated';

// Stop animation
offset.value = withTiming(100);
cancelAnimation(offset);

// Reset value
offset.value = 0;
```

### interpolate

Maps values from one range to another.

```typescript
import { interpolate, Extrapolation } from 'react-native-reanimated';

const progress = useSharedValue(0);

// Basic interpolation
const output = interpolate(
  progress.value,
  [0, 1],           // Input range
  [0, 100]          // Output range
);

// With extrapolation
const clamped = interpolate(
  progress.value,
  [0, 1],
  [0, 100],
  Extrapolation.CLAMP  // Prevents values outside range
);

// Multiple output ranges
const complex = interpolate(
  progress.value,
  [0, 0.5, 1],
  [0, 50, 100]
);
```

### interpolateColor

Interpolates between colors.

```typescript
import { interpolateColor, ColorSpace } from 'react-native-reanimated';

const color = interpolateColor(
  progress.value,
  [0, 0.5, 1],
  ['#FF0000', '#FFFF00', '#00FF00'],
  ColorSpace.RGB  // or ColorSpace.HSV
);
```

### clamp

Clamps a value between min and max.

```typescript
import { clamp } from 'react-native-reanimated';

const clamped = clamp(value, 0, 100); // 0 <= clamped <= 100
```

---

## Quick Reference Card

```typescript
// Shared values
const value = useSharedValue(0);

// Animated styles
const style = useAnimatedStyle(() => ({
  transform: [{ translateX: value.value }],
}));

// Animated props
const props = useAnimatedProps(() => ({
  opacity: value.value,
}));

// Derived values
const derived = useDerivedValue(() => value.value * 2);

// Reactions
useAnimatedReaction(
  () => value.value,
  (current, previous) => { /* ... */ }
);

// Refs
const ref = useAnimatedRef();
const measurement = measure(ref);
scrollTo(ref, x, y, animated);

// Scroll handling
const scrollHandler = useAnimatedScrollHandler({
  onScroll: (event) => { scrollY.value = event.contentOffset.y; },
});

// Keyboard
const { height } = useAnimatedKeyboard();

// Sensors
const sensor = useAnimatedSensor(SensorType.GYROSCOPE);

// Frame callback
const frame = useFrameCallback((info) => { /* ... */ });

// Utilities
runOnJS(fn)(args);
runOnUI(worklet)(args);
cancelAnimation(sharedValue);
interpolate(value, input, output);
interpolateColor(value, input, colors);
clamp(value, min, max);
```
