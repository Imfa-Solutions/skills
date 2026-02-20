# React Native Reanimated - Gestures Integration

> **Complete Guide for LLMs** | Version: Reanimated 4.x | Last Updated: 2026-02-20

---

## Table of Contents

1. [Overview](#overview)
2. [Setup & Installation](#setup--installation)
3. [Gesture Types](#gesture-types)
4. [Basic Gesture Patterns](#basic-gesture-patterns)
5. [Gesture Compositions](#gesture-compositions)
6. [Common Gesture Patterns](#common-gesture-patterns)
7. [Advanced Techniques](#advanced-techniques)
8. [Best Practices](#best-practices)

---

## Overview

Reanimated integrates seamlessly with **React Native Gesture Handler** to create smooth, gesture-driven animations. Gesture callbacks are automatically workletized, allowing direct manipulation of shared values.

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    GESTURE HANDLER                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Touch       │  │  Gesture     │  │  State       │      │
│  │  Events      │  │  Recognition │  │  Machine     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ Worklet Callbacks
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      REANIMATED UI THREAD                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Shared      │  │  Animation   │  │  Style       │      │
│  │  Values      │  │  Functions   │  │  Updates     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

---

## Setup & Installation

### Install Dependencies

```bash
npm install react-native-gesture-handler react-native-reanimated
```

### Wrap with GestureHandlerRootView

```typescript
import { GestureHandlerRootView } from 'react-native-gesture-handler';

function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      {/* Your app */}
    </GestureHandlerRootView>
  );
}
```

> **Important**: Place `GestureHandlerRootView` as close to the root as possible for proper gesture handling.

---

## Gesture Types

### Available Gestures

```typescript
import { Gesture } from 'react-native-gesture-handler';

Gesture.Tap()        // Single or multiple taps
Gesture.Pan()        // Drag/pan gesture
Gesture.Pinch()      // Two-finger pinch
Gesture.Rotation()   // Two-finger rotation
Gesture.Fling()      // Quick swipe
Gesture.LongPress()  // Press and hold
Gesture.ForceTouch() // Pressure-sensitive (iOS)
Gesture.Hover()      // Mouse/trackpad hover
Gesture.Native()     // Native gesture (ScrollView, etc.)
```

### Gesture Configuration

```typescript
// Tap gesture
Gesture.Tap()
  .maxDuration(250)
  .numberOfTaps(2)
  .maxDelay(100)

// Pan gesture
Gesture.Pan()
  .activeOffsetX([-20, 20])
  .activeOffsetY([-20, 20])
  .failOffsetY([-100, 100])
  .minPointers(1)
  .maxPointers(2)

// Pinch gesture
Gesture.Pinch()
  .minSpan(10)

// Rotation gesture
Gesture.Rotation()

// Long press gesture
Gesture.LongPress()
  .minDuration(500)
  .maxDistance(10)
```

---

## Basic Gesture Patterns

### Tap Gesture

```typescript
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useSharedValue, withSpring } from 'react-native-reanimated';

function TapExample() {
  const pressed = useSharedValue(false);
  const scale = useSharedValue(1);

  const tapGesture = Gesture.Tap()
    .onBegin(() => {
      pressed.value = true;
      scale.value = withSpring(0.9);
    })
    .onFinalize(() => {
      pressed.value = false;
      scale.value = withSpring(1);
    });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
    backgroundColor: pressed.value ? '#FFE04B' : '#B58DF1',
  }));

  return (
    <GestureDetector gesture={tapGesture}>
      <Animated.View style={[styles.box, animatedStyle]}>
        <Text>Tap Me</Text>
      </Animated.View>
    </GestureDetector>
  );
}
```

### Pan Gesture

```typescript
function PanExample() {
  const offsetX = useSharedValue(0);
  const offsetY = useSharedValue(0);
  const startX = useSharedValue(0);
  const startY = useSharedValue(0);

  const panGesture = Gesture.Pan()
    .onBegin(() => {
      startX.value = offsetX.value;
      startY.value = offsetY.value;
    })
    .onUpdate((event) => {
      offsetX.value = startX.value + event.translationX;
      offsetY.value = startY.value + event.translationY;
    })
    .onEnd((event) => {
      // Spring back to center with velocity
      offsetX.value = withSpring(0, { velocity: event.velocityX });
      offsetY.value = withSpring(0, { velocity: event.velocityY });
    });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [
      { translateX: offsetX.value },
      { translateY: offsetY.value },
    ],
  }));

  return (
    <GestureDetector gesture={panGesture}>
      <Animated.View style={[styles.box, animatedStyle]}>
        <Text>Drag Me</Text>
      </Animated.View>
    </GestureDetector>
  );
}
```

### Pinch Gesture

```typescript
function PinchExample() {
  const scale = useSharedValue(1);
  const savedScale = useSharedValue(1);

  const pinchGesture = Gesture.Pinch()
    .onUpdate((event) => {
      scale.value = savedScale.value * event.scale;
    })
    .onEnd(() => {
      savedScale.value = scale.value;
      // Optional: reset if scale is too small
      if (scale.value < 0.5) {
        scale.value = withSpring(1);
        savedScale.value = 1;
      }
    });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
  }));

  return (
    <GestureDetector gesture={pinchGesture}>
      <Animated.View style={[styles.box, animatedStyle]}>
        <Text>Pinch Me</Text>
      </Animated.View>
    </GestureDetector>
  );
}
```

### Rotation Gesture

```typescript
function RotationExample() {
  const rotation = useSharedValue(0);
  const savedRotation = useSharedValue(0);

  const rotationGesture = Gesture.Rotation()
    .onUpdate((event) => {
      rotation.value = savedRotation.value + event.rotation;
    })
    .onEnd(() => {
      savedRotation.value = rotation.value;
    });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ rotate: `${rotation.value}rad` }],
  }));

  return (
    <GestureDetector gesture={rotationGesture}>
      <Animated.View style={[styles.box, animatedStyle]}>
        <Text>Rotate Me</Text>
      </Animated.View>
    </GestureDetector>
  );
}
```

### Long Press Gesture

```typescript
function LongPressExample() {
  const pressed = useSharedValue(false);
  const scale = useSharedValue(1);

  const longPressGesture = Gesture.LongPress()
    .minDuration(500)
    .onBegin(() => {
      scale.value = withSpring(1.2);
    })
    .onStart(() => {
      pressed.value = true;
      // Haptic feedback
      // runOnJS(Haptics.impactAsync)(Haptics.ImpactFeedbackStyle.Heavy);
    })
    .onEnd(() => {
      pressed.value = false;
      scale.value = withSpring(1);
    });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
    backgroundColor: pressed.value ? '#FF6B6B' : '#B58DF1',
  }));

  return (
    <GestureDetector gesture={longPressGesture}>
      <Animated.View style={[styles.box, animatedStyle]}>
        <Text>Long Press Me</Text>
      </Animated.View>
    </GestureDetector>
  );
}
```

---

## Gesture Compositions

### Simultaneous Gestures

```typescript
const panGesture = Gesture.Pan().onUpdate(/* ... */);
const pinchGesture = Gesture.Pinch().onUpdate(/* ... */);
const rotationGesture = Gesture.Rotation().onUpdate(/* ... */);

// All gestures work simultaneously
const composed = Gesture.Simultaneous(
  panGesture,
  pinchGesture,
  rotationGesture
);
```

### Exclusive Gestures

```typescript
const tapGesture = Gesture.Tap().onEnd(/* ... */);
const longPressGesture = Gesture.LongPress().onStart(/* ... */);

// Only one gesture can activate
const composed = Gesture.Exclusive(longPressGesture, tapGesture);
```

### Race Gestures

```typescript
const flingGesture = Gesture.Fling().onStart(/* ... */);
const panGesture = Gesture.Pan().onUpdate(/* ... */);

// First to activate wins
const composed = Gesture.Race(flingGesture, panGesture);
```

### Complete Example: Photo Viewer

```typescript
function PhotoViewer({ source }) {
  const scale = useSharedValue(1);
  const savedScale = useSharedValue(1);
  const rotation = useSharedValue(0);
  const savedRotation = useSharedValue(0);
  const offsetX = useSharedValue(0);
  const offsetY = useSharedValue(0);
  const startX = useSharedValue(0);
  const startY = useSharedValue(0);

  const pinchGesture = Gesture.Pinch()
    .onUpdate((event) => {
      scale.value = savedScale.value * event.scale;
    })
    .onEnd(() => {
      savedScale.value = scale.value;
    });

  const rotationGesture = Gesture.Rotation()
    .onUpdate((event) => {
      rotation.value = savedRotation.value + event.rotation;
    })
    .onEnd(() => {
      savedRotation.value = rotation.value;
    });

  const panGesture = Gesture.Pan()
    .onBegin(() => {
      startX.value = offsetX.value;
      startY.value = offsetY.value;
    })
    .onUpdate((event) => {
      offsetX.value = startX.value + event.translationX;
      offsetY.value = startY.value + event.translationY;
    });

  const doubleTapGesture = Gesture.Tap()
    .numberOfTaps(2)
    .onEnd(() => {
      if (scale.value > 1) {
        // Reset
        scale.value = withSpring(1);
        savedScale.value = 1;
        rotation.value = withSpring(0);
        savedRotation.value = 0;
        offsetX.value = withSpring(0);
        offsetY.value = withSpring(0);
      } else {
        // Zoom in
        scale.value = withSpring(2);
        savedScale.value = 2;
      }
    });

  const composed = Gesture.Simultaneous(
    panGesture,
    Gesture.Simultaneous(pinchGesture, rotationGesture)
  );

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [
      { translateX: offsetX.value },
      { translateY: offsetY.value },
      { scale: scale.value },
      { rotate: `${rotation.value}rad` },
    ],
  }));

  return (
    <GestureDetector gesture={composed}>
      <GestureDetector gesture={doubleTapGesture}>
        <Animated.Image
          source={source}
          style={[styles.image, animatedStyle]}
          resizeMode="contain"
        />
      </GestureDetector>
    </GestureDetector>
  );
}
```

---

## Common Gesture Patterns

### Swipeable List Item

```typescript
function SwipeableItem({ children, onDelete }) {
  const translateX = useSharedValue(0);
  const context = useSharedValue(0);
  const itemHeight = useSharedValue(70);

  const panGesture = Gesture.Pan()
    .activeOffsetX([-10, 10])
    .onBegin(() => {
      context.value = translateX.value;
    })
    .onUpdate((event) => {
      // Only allow swiping left
      const newValue = context.value + event.translationX;
      translateX.value = Math.min(0, newValue);
    })
    .onEnd((event) => {
      // Snap to delete or back
      if (translateX.value < -100) {
        translateX.value = withTiming(-150);
      } else {
        translateX.value = withSpring(0);
      }
    });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: translateX.value }],
    height: itemHeight.value,
  }));

  const deleteStyle = useAnimatedStyle(() => ({
    opacity: translateX.value < -50 ? 1 : 0,
  }));

  return (
    <View style={styles.container}>
      <Animated.View style={[styles.deleteBackground, deleteStyle]}>
        <Text style={styles.deleteText}>Delete</Text>
      </Animated.View>
      <GestureDetector gesture={panGesture}>
        <Animated.View style={[styles.item, animatedStyle]}>
          {children}
        </Animated.View>
      </GestureDetector>
    </View>
  );
}
```

### Bottom Sheet

```typescript
function BottomSheet({ children }) {
  const translateY = useSharedValue(SCREEN_HEIGHT);
  const context = useSharedValue(0);
  const active = useSharedValue(false);

  const panGesture = Gesture.Pan()
    .onBegin(() => {
      context.value = translateY.value;
    })
    .onUpdate((event) => {
      const newValue = context.value + event.translationY;
      // Don't drag above top
      translateY.value = Math.max(0, newValue);
    })
    .onEnd((event) => {
      // Snap to open or closed
      if (translateY.value > 200 || event.velocityY > 500) {
        translateY.value = withSpring(SCREEN_HEIGHT);
        active.value = false;
      } else {
        translateY.value = withSpring(0);
        active.value = true;
      }
    });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateY: translateY.value }],
  }));

  const open = () => {
    translateY.value = withSpring(0);
    active.value = true;
  };

  const close = () => {
    translateY.value = withSpring(SCREEN_HEIGHT);
    active.value = false;
  };

  return (
    <>
      <TouchableOpacity onPress={open}>
        <Text>Open Sheet</Text>
      </TouchableOpacity>
      <GestureDetector gesture={panGesture}>
        <Animated.View style={[styles.sheet, animatedStyle]}>
          <View style={styles.handle} />
          {children}
        </Animated.View>
      </GestureDetector>
    </>
  );
}
```

### Pull to Refresh

```typescript
function PullToRefresh({ onRefresh, children }) {
  const pullProgress = useSharedValue(0);
  const isRefreshing = useSharedValue(false);

  const panGesture = Gesture.Pan()
    .activeOffsetY([-10, 10])
    .onUpdate((event) => {
      if (event.translationY > 0 && !isRefreshing.value) {
        pullProgress.value = Math.min(event.translationY / 100, 1);
      }
    })
    .onEnd(() => {
      if (pullProgress.value >= 1 && !isRefreshing.value) {
        isRefreshing.value = true;
        pullProgress.value = withSpring(1);
        runOnJS(onRefresh)();
      } else {
        pullProgress.value = withSpring(0);
      }
    });

  const contentStyle = useAnimatedStyle(() => ({
    transform: [{ translateY: pullProgress.value * 80 }],
  }));

  const spinnerStyle = useAnimatedStyle(() => ({
    opacity: pullProgress.value,
    transform: [{ rotate: `${pullProgress.value * 360}deg` }],
  }));

  return (
    <GestureDetector gesture={panGesture}>
      <View>
        <Animated.View style={[styles.spinner, spinnerStyle]}>
          <ActivityIndicator />
        </Animated.View>
        <Animated.View style={contentStyle}>
          {children}
        </Animated.View>
      </View>
    </GestureDetector>
  );
}
```

### Carousel

```typescript
function Carousel({ items }) {
  const translateX = useSharedValue(0);
  const context = useSharedValue(0);
  const currentIndex = useSharedValue(0);
  const ITEM_WIDTH = SCREEN_WIDTH * 0.8;

  const panGesture = Gesture.Pan()
    .activeOffsetX([-10, 10])
    .onBegin(() => {
      context.value = translateX.value;
    })
    .onUpdate((event) => {
      translateX.value = context.value + event.translationX;
    })
    .onEnd((event) => {
      const velocity = event.velocityX;
      const translation = event.translationX;
      
      // Determine direction based on velocity and translation
      let newIndex = currentIndex.value;
      
      if (Math.abs(velocity) > 500 || Math.abs(translation) > ITEM_WIDTH / 2) {
        if (velocity < 0 || translation < 0) {
          newIndex = Math.min(currentIndex.value + 1, items.length - 1);
        } else {
          newIndex = Math.max(currentIndex.value - 1, 0);
        }
      }
      
      currentIndex.value = newIndex;
      translateX.value = withSpring(-newIndex * ITEM_WIDTH, {
        velocity: event.velocityX,
      });
    });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: translateX.value }],
  }));

  return (
    <GestureDetector gesture={panGesture}>
      <Animated.View style={[styles.carousel, animatedStyle]}>
        {items.map((item, index) => (
          <View key={index} style={{ width: ITEM_WIDTH }}>
            {item}
          </View>
        ))}
      </Animated.View>
    </GestureDetector>
  );
}
```

---

## Advanced Techniques

### Gesture with Decay

```typescript
const panGesture = Gesture.Pan()
  .onBegin(() => {
    context.value = translateX.value;
    cancelAnimation(translateX);
  })
  .onUpdate((event) => {
    translateX.value = context.value + event.translationX;
  })
  .onEnd((event) => {
    // Decay with velocity
    translateX.value = withDecay({
      velocity: event.velocityX,
      deceleration: 0.995,
      clamp: [-300, 300],
    });
  });
```

### Rubber Band Effect

```typescript
const panGesture = Gesture.Pan()
  .onUpdate((event) => {
    const newValue = context.value + event.translationX;
    
    // Apply rubber band effect when beyond limits
    if (newValue > MAX) {
      const overshoot = newValue - MAX;
      translateX.value = MAX + overshoot * 0.3;
    } else if (newValue < MIN) {
      const overshoot = MIN - newValue;
      translateX.value = MIN - overshoot * 0.3;
    } else {
      translateX.value = newValue;
    }
  })
  .onEnd(() => {
    // Spring back if beyond limits
    if (translateX.value > MAX) {
      translateX.value = withSpring(MAX);
    } else if (translateX.value < MIN) {
      translateX.value = withSpring(MIN);
    }
  });
```

### Snap Points

```typescript
const SNAP_POINTS = [0, 100, 200, 300];

const panGesture = Gesture.Pan()
  .onEnd((event) => {
    // Find nearest snap point
    const target = translateX.value + event.velocityX * 0.2;
    
    const nearest = SNAP_POINTS.reduce((prev, curr) =>
      Math.abs(curr - target) < Math.abs(prev - target) ? curr : prev
    );
    
    translateX.value = withSpring(nearest, {
      velocity: event.velocityX,
    });
  });
```

### Gesture with ScrollView

```typescript
function ScrollableList() {
  const scrollEnabled = useSharedValue(true);

  const panGesture = Gesture.Pan()
    .onBegin(() => {
      scrollEnabled.value = false;
    })
    .onEnd(() => {
      scrollEnabled.value = true;
    });

  return (
    <GestureDetector gesture={panGesture}>
      <Animated.ScrollView scrollEnabled={scrollEnabled.value}>
        {/* Items */}
      </Animated.ScrollView>
    </GestureDetector>
  );
}
```

---

## Best Practices

### DO's

✅ **Use shared values for gesture state**
```typescript
// Good - shared values update immediately
const translateX = useSharedValue(0);

const gesture = Gesture.Pan()
  .onUpdate((event) => {
    translateX.value = event.translationX;
  });
```

✅ **Cancel animations before starting new ones**
```typescript
const gesture = Gesture.Pan()
  .onBegin(() => {
    cancelAnimation(translateX);
  });
```

✅ **Use velocity for natural feel**
```typescript
.onEnd((event) => {
  translateX.value = withSpring(target, {
    velocity: event.velocityX,
  });
});
```

✅ **Handle edge cases**
```typescript
.onEnd((event) => {
  // Don't exceed bounds
  const target = Math.max(MIN, Math.min(MAX, newValue));
  translateX.value = withSpring(target);
});
```

### DON'Ts

❌ **Don't use React state in gesture callbacks**
```typescript
// Bad - causes lag
const [position, setPosition] = useState(0);

const gesture = Gesture.Pan()
  .onUpdate((event) => {
    setPosition(event.translationX); // ❌ Don't do this!
  });

// Good - use shared values
const position = useSharedValue(0);

const gesture = Gesture.Pan()
  .onUpdate((event) => {
    position.value = event.translationX; // ✅ Good!
  });
```

❌ **Don't forget to handle gesture conflicts**
```typescript
// Bad - ScrollView and Pan gesture conflict
<ScrollView>
  <GestureDetector gesture={panGesture}>
    {/* Content */}
  </GestureDetector>
</ScrollView>

// Good - use waitFor or simultaneousWith
const panGesture = Gesture.Pan()
  .waitFor(scrollRef) // Wait for scroll to fail
  .onUpdate(/* ... */);
```

❌ **Don't ignore velocity**
```typescript
// Bad - ignores user's swipe momentum
.onEnd(() => {
  translateX.value = withSpring(target);
});

// Good - incorporates velocity
.onEnd((event) => {
  translateX.value = withSpring(target, {
    velocity: event.velocityX,
  });
});
```

---

## Quick Reference Card

```typescript
// Gesture creation
Gesture.Tap()
Gesture.Pan()
Gesture.Pinch()
Gesture.Rotation()
Gesture.LongPress()
Gesture.Fling()

// Gesture configuration
Gesture.Pan()
  .activeOffsetX([-20, 20])
  .failOffsetY([-100, 100])
  .minPointers(1)
  .maxPointers(2)

// Gesture callbacks
Gesture.Pan()
  .onBegin((event) => { })
  .onStart((event) => { })
  .onUpdate((event) => { })
  .onEnd((event) => { })
  .onFinalize((event) => { })

// Gesture composition
Gesture.Simultaneous(gesture1, gesture2)
Gesture.Exclusive(gesture1, gesture2)
Gesture.Race(gesture1, gesture2)

// Common patterns
const panGesture = Gesture.Pan()
  .onBegin(() => {
    context.value = translateX.value;
    cancelAnimation(translateX);
  })
  .onUpdate((event) => {
    translateX.value = context.value + event.translationX;
  })
  .onEnd((event) => {
    translateX.value = withSpring(target, {
      velocity: event.velocityX,
    });
  });
```
