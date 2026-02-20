# React Native Reanimated - Best Practices & Performance

> **Complete Guide for LLMs** | Version: Reanimated 4.x | Last Updated: 2026-02-20

---

## Table of Contents

1. [Performance Principles](#performance-principles)
2. [Shared Value Best Practices](#shared-value-best-practices)
3. [Animation Optimization](#animation-optimization)
4. [Render Optimization](#render-optimization)
5. [Memory Management](#memory-management)
6. [Common Anti-Patterns](#common-anti-patterns)
7. [Debugging & Profiling](#debugging--profiling)
8. [Platform-Specific Considerations](#platform-specific-considerations)
9. [Checklist](#checklist)

---

## Performance Principles

### The Golden Rules

| Rule | Impact | Priority |
|------|--------|----------|
| Keep worklets small | Reduces UI thread overhead | Critical |
| Minimize JS thread work | Prevents frame drops | Critical |
| Use shared values, not state | Avoids re-renders | Critical |
| Batch style updates | Reduces native calls | High |
| Avoid closures in render | Prevents re-workletization | High |

### Performance Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    PERFORMANCE TARGETS                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Frame Rate:        60 FPS (16.67ms per frame)             │
│  JS Thread:         < 8ms per frame                        │
│  UI Thread:         < 10ms per frame                       │
│  Bridge Calls:      Minimize / Eliminate                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Shared Value Best Practices

### DO's

✅ **Initialize with correct types**
```typescript
// Good - explicit types
const offset = useSharedValue<number>(0);
const color = useSharedValue<string>('#000');
const position = useSharedValue<{ x: number; y: number }>({ x: 0, y: 0 });
```

✅ **Use shared values for animation state**
```typescript
// Good - animation state in shared values
const isAnimating = useSharedValue(false);
const progress = useSharedValue(0);

const startAnimation = () => {
  isAnimating.value = true;
  progress.value = withTiming(1, {}, (finished) => {
    if (finished) {
      isAnimating.value = false;
    }
  });
};
```

✅ **Derive values instead of duplicating**
```typescript
// Good - derived value
const width = useSharedValue(100);
const height = useSharedValue(200);

const aspectRatio = useDerivedValue(() => {
  return width.value / height.value;
});
```

✅ **Batch related updates**
```typescript
// Good - update related values together
const animateTransition = () => {
  'worklet';
  opacity.value = withTiming(0, { duration: 150 }, () => {
    // Update position while invisible
    position.value = newPosition;
    opacity.value = withTiming(1, { duration: 150 });
  });
};
```

### DON'Ts

❌ **Don't use shared values for React state**
```typescript
// Bad - using shared value as React state
const [count, setCount] = useState(0);
const countSv = useSharedValue(0);

// Update both - unnecessary!
const increment = () => {
  setCount(c => c + 1);
  countSv.value = countSv.value + 1;
};

// Good - use one or the other
const [count, setCount] = useState(0); // For React rendering
// OR
const countSv = useSharedValue(0); // For animations
```

❌ **Don't create shared values conditionally**
```typescript
// Bad - conditional hook
function Component({ animate }) {
  if (animate) {
    const offset = useSharedValue(0); // ❌ Hook called conditionally!
  }
}

// Good - always create
function Component({ animate }) {
  const offset = useSharedValue(0); // ✅ Always created
  
  // Use conditionally
  const animatedStyle = useAnimatedStyle(() => ({
    transform: animate ? [{ translateX: offset.value }] : [],
  }), [animate]);
}
```

❌ **Don't read shared values in render**
```typescript
// Bad - reading in render
function Component() {
  const offset = useSharedValue(0);
  
  return (
    <View style={{ marginLeft: offset.value }}> {/* ❌ Won't update! */}
      {/* Content */}
    </View>
  );
}

// Good - use animated style
function Component() {
  const offset = useSharedValue(0);
  
  const animatedStyle = useAnimatedStyle(() => ({
    marginLeft: offset.value, // ✅ Updates automatically
  }));
  
  return (
    <Animated.View style={animatedStyle}>
      {/* Content */}
    </Animated.View>
  );
}
```

---

## Animation Optimization

### Animation Function Selection

| Scenario | Recommended | Why |
|----------|-------------|-----|
| User-driven (gesture) | Direct value | Follows finger |
| Gesture release | `withSpring` | Natural physics |
| Fixed duration | `withTiming` | Predictable |
| Momentum | `withDecay` | Natural deceleration |
| Toggle states | `withSpring` | Bouncy feedback |

### Optimizing Timing Animations

```typescript
// Good - appropriate duration
opacity.value = withTiming(1, { duration: 200 }); // Quick feedback

// Good - appropriate easing
transform.value = withTiming(100, {
  duration: 300,
  easing: Easing.out(Easing.ease), // Smooth deceleration
});
```

### Optimizing Spring Animations

```typescript
// Good - appropriate spring config
const config = {
  gentle: { damping: 15, stiffness: 50 },    // Slow, smooth
  normal: { damping: 10, stiffness: 100 },   // Balanced
  snappy: { damping: 20, stiffness: 300 },   // Quick response
};

// Use velocity for natural feel
translateX.value = withSpring(target, {
  velocity: event.velocityX,
  damping: 15,
});
```

### Animation Batching

```typescript
// Bad - multiple separate animations
const animateBad = () => {
  opacity.value = withTiming(1);
  scale.value = withSpring(1.2);
  translateX.value = withTiming(100);
};

// Good - sequence related animations
const animateGood = () => {
  opacity.value = withTiming(1);
  
  scale.value = withSequence(
    withSpring(1.2),
    withSpring(1)
  );
  
  translateX.value = withDelay(
    100,
    withTiming(100)
  );
};
```

### Cancelling Animations

```typescript
// Good - cancel before starting new animation
const gesture = Gesture.Pan()
  .onBegin(() => {
    cancelAnimation(translateX);
    cancelAnimation(translateY);
  })
  .onUpdate((event) => {
    translateX.value = event.translationX;
    translateY.value = event.translationY;
  });
```

---

## Render Optimization

### Minimizing Re-renders

```typescript
// Bad - causes re-render on every animation frame
function BadComponent() {
  const [position, setPosition] = useState(0);
  
  const gesture = Gesture.Pan()
    .onUpdate((event) => {
      runOnJS(setPosition)(event.translationX); // ❌ Re-renders every frame!
    });
  
  return <View style={{ transform: [{ translateX: position }] }} />;
}

// Good - no re-renders during animation
function GoodComponent() {
  const position = useSharedValue(0);
  
  const gesture = Gesture.Pan()
    .onUpdate((event) => {
      position.value = event.translationX; // ✅ No re-render!
    });
  
  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: position.value }],
  }));
  
  return <Animated.View style={animatedStyle} />;
}
```

### Stable References

```typescript
// Bad - new function every render
function Component() {
  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: offset.value }],
  })); // ❌ New function reference every render
}

// Good - stable reference with dependencies
function Component({ externalValue }) {
  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ 
      translateX: offset.value + externalValue 
    }],
  }), [externalValue]); // ✅ Only re-create when externalValue changes
}
```

### Memoization

```typescript
// Good - memoize expensive calculations
const expensiveValue = useDerivedValue(() => {
  // This only runs when dependencies change
  return heavyComputation(position.value);
}, [/* dependencies */]);

// Good - memoize gesture handlers
const gesture = useMemo(() => {
  return Gesture.Pan()
    .onUpdate((event) => {
      offset.value = event.translationX;
    });
}, []);
```

### Component Splitting

```typescript
// Bad - entire component re-renders
function ParentComponent() {
  const [state, setState] = useState(0);
  
  return (
    <View>
      <Animated.View style={animatedStyle} /> {/* Re-renders with parent */}
      <Button onPress={() => setState(s => s + 1)} />
    </View>
  );
}

// Good - isolate animated component
const AnimatedPart = memo(() => {
  const offset = useSharedValue(0);
  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: offset.value }],
  }));
  
  return <Animated.View style={animatedStyle} />;
});

function ParentComponent() {
  const [state, setState] = useState(0);
  
  return (
    <View>
      <AnimatedPart /> {/* Doesn't re-render with parent */}
      <Button onPress={() => setState(s => s + 1)} />
    </View>
  );
}
```

---

## Memory Management

### Cleaning Up

```typescript
function Component() {
  const offset = useSharedValue(0);
  
  useEffect(() => {
    return () => {
      // Cancel running animations on unmount
      cancelAnimation(offset);
    };
  }, []);
}
```

### Avoiding Memory Leaks

```typescript
// Bad - callback may fire after unmount
useAnimatedReaction(
  () => progress.value,
  (current) => {
    if (current >= 1) {
      runOnJS(setState)('done'); // ❌ May set state after unmount!
    }
  }
);

// Good - check if component is still mounted
const isMounted = useSharedValue(true);

useEffect(() => {
  return () => {
    isMounted.value = false;
  };
}, []);

useAnimatedReaction(
  () => progress.value,
  (current) => {
    if (current >= 1 && isMounted.value) {
      runOnJS(setState)('done');
    }
  }
);
```

### Limiting Concurrent Animations

```typescript
// Bad - too many simultaneous animations
function ManyAnimations() {
  return (
    <View>
      {Array(100).fill(0).map((_, i) => (
        <Animated.View
          key={i}
          entering={FadeIn.delay(i * 10)} // ❌ 100 simultaneous animations!
        />
      ))}
    </View>
  );
}

// Good - stagger animations
function StaggeredAnimations() {
  return (
    <View>
      {Array(100).fill(0).map((_, i) => (
        <Animated.View
          key={i}
          entering={FadeIn.delay(Math.min(i * 50, 1000))} // ✅ Max 1s delay
        />
      ))}
    </View>
  );
}
```

---

## Common Anti-Patterns

### Anti-Pattern 1: Reading Shared Values in JS

```typescript
// ❌ Anti-pattern
function Component() {
  const offset = useSharedValue(0);
  
  const handlePress = () => {
    console.log(offset.value); // Works but not reactive
    if (offset.value > 100) { // Stale value!
      // ...
    }
  };
}

// ✅ Solution - use derived value or reaction
function Component() {
  const offset = useSharedValue(0);
  const isBeyondThreshold = useDerivedValue(() => {
    return offset.value > 100;
  });
  
  useAnimatedReaction(
    () => isBeyondThreshold.value,
    (current, previous) => {
      if (current && !previous) {
        runOnJS(handleThresholdCrossed)();
      }
    }
  );
}
```

### Anti-Pattern 2: Mixing JS and UI State

```typescript
// ❌ Anti-pattern
function Component() {
  const [isOpen, setIsOpen] = useState(false);
  const progress = useSharedValue(0);
  
  const toggle = () => {
    setIsOpen(!isOpen); // React state
    progress.value = withTiming(isOpen ? 0 : 1); // Shared value
  };
  // State can get out of sync!
}

// ✅ Solution - single source of truth
function Component() {
  const isOpen = useSharedValue(false);
  const progress = useDerivedValue(() => {
    return isOpen.value ? 1 : 0;
  });
  
  const toggle = () => {
    isOpen.value = !isOpen.value;
    // progress updates automatically
  };
}
```

### Anti-Pattern 3: Creating Values in Render

```typescript
// ❌ Anti-pattern
function Component() {
  return (
    <Animated.View
      style={{
        transform: [{ 
          translateX: withTiming(targetValue) // ❌ Creates animation every render!
        }]
      }}
    />
  );
}

// ✅ Solution - use shared values
function Component() {
  const offset = useSharedValue(0);
  
  const animate = () => {
    offset.value = withTiming(targetValue);
  };
  
  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: offset.value }],
  }));
  
  return <Animated.View style={animatedStyle} />;
}
```

### Anti-Pattern 4: Over-animating

```typescript
// ❌ Anti-pattern - animating too many properties
const animatedStyle = useAnimatedStyle(() => ({
  opacity: progress.value,
  backgroundColor: interpolateColor(/* ... */),
  borderRadius: interpolate(/* ... */),
  borderWidth: interpolate(/* ... */),
  borderColor: interpolateColor(/* ... */),
  shadowOpacity: interpolate(/* ... */),
  shadowRadius: interpolate(/* ... */),
  shadowOffset: { /* ... */ },
  transform: [
    { translateX: /* ... */ },
    { translateY: /* ... */ },
    { scale: /* ... */ },
    { rotate: /* ... */ },
    { rotateX: /* ... */ },
    { rotateY: /* ... */ },
    { perspective: /* ... */ },
  ],
}));

// ✅ Solution - animate only what's needed
const animatedStyle = useAnimatedStyle(() => ({
  opacity: progress.value,
  transform: [
    { translateX: offsetX.value },
    { scale: scale.value },
  ],
}));
```

---

## Debugging & Profiling

### Console Logging

```typescript
// Log from worklets
const worklet = () => {
  'worklet';
  console.log('Worklet value:', offset.value);
  console.warn('Warning from worklet');
};

// Log animation progress
const animate = () => {
  offset.value = withTiming(100, {}, (finished) => {
    console.log('Animation finished:', finished);
  });
};
```

### Performance Monitoring

```typescript
// Measure frame time
const frameCallback = useFrameCallback((frameInfo) => {
  const frameTime = frameInfo.timeSincePreviousFrame;
  if (frameTime && frameTime > 20) {
    console.warn('Slow frame:', frameTime, 'ms');
  }
});

// Track animation performance
const startTime = useSharedValue(0);

const animate = () => {
  startTime.value = performance.now();
  offset.value = withTiming(100, {}, (finished) => {
    const duration = performance.now() - startTime.value;
    console.log('Animation took:', duration, 'ms');
  });
};
```

### React DevTools

```typescript
// Name components for DevTools
const AnimatedCard = memo(function AnimatedCard({ item }) {
  // Component code
});

// Profile re-renders
import { Profiler } from 'react';

<Profiler
  id="AnimationComponent"
  onRender={(id, phase, actualDuration) => {
    console.log(id, phase, actualDuration);
  }}
>
  <AnimationComponent />
</Profiler>
```

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Janky animations | JS thread blocked | Move work to UI thread |
| Delayed response | Bridge communication | Use worklets directly |
| Memory leaks | Uncancelled animations | Cancel on unmount |
| Stale values | Reading in render | Use animated styles |
| Gesture lag | React state updates | Use shared values |

---

## Platform-Specific Considerations

### iOS

```typescript
// Enable 120fps on ProMotion displays
import { enableLayoutAnimations } from 'react-native-reanimated';

// Layout animations are enabled by default on iOS
enableLayoutAnimations(true);
```

### Android

```typescript
// Android-specific optimizations
// - Use hardware acceleration
// - Avoid shadow animations
// - Limit simultaneous animations

const androidOptimizedStyle = useAnimatedStyle(() => ({
  // Avoid shadow properties on Android
  // elevation is more performant
  elevation: interpolate(
    progress.value,
    [0, 1],
    [0, 8]
  ),
}));
```

### Web

```typescript
// Web-specific considerations
// - Use CSS transforms when possible
// - Limit filter animations
// - Test on various browsers

const webOptimizedStyle = useAnimatedStyle(() => ({
  // Use transform instead of position
  transform: [{ translateX: offset.value }],
  // Instead of left: offset.value
}));
```

---

## Checklist

### Before Shipping

- [ ] Animations run at 60fps
- [ ] No frame drops during gestures
- [ ] Memory usage is stable
- [ ] Animations cancel on unmount
- [ ] Reduced motion is handled
- [ ] Works on low-end devices
- [ ] Tested on both platforms

### Code Review

- [ ] Shared values used for animation state
- [ ] No React state updates during animations
- [ ] Worklets are small and focused
- [ ] Dependencies arrays are correct
- [ ] No memory leaks from callbacks
- [ ] Animations are cancelable

### Performance Checklist

- [ ] Profile with React DevTools
- [ ] Check for unnecessary re-renders
- [ ] Verify worklet execution
- [ ] Test with slow animations enabled
- [ ] Monitor memory usage
- [ ] Test on older devices

---

## Quick Reference Card

```typescript
// Performance patterns
const offset = useSharedValue(0);
const animatedStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: offset.value }],
}));

// Cancel animations
cancelAnimation(offset);

// Optimize springs
withSpring(target, { damping: 15, stiffness: 150 });

// Batch updates
withSequence(
  withTiming(value1),
  withTiming(value2)
);

// Avoid re-renders
useDerivedValue(() => compute(value));
useAnimatedReaction(
  () => value.value,
  (current, previous) => { }
);

// Memory cleanup
useEffect(() => {
  return () => cancelAnimation(offset);
}, []);

// Debug
console.log('Value:', offset.value);
useFrameCallback((info) => {
  console.log('Frame time:', info.timeSincePreviousFrame);
});
```
