# React Native Reanimated - Troubleshooting & Common Issues

> **Complete Guide for LLMs** | Version: Reanimated 4.x | Last Updated: 2026-02-20

---

## Table of Contents

1. [Installation Issues](#installation-issues)
2. [Build Errors](#build-errors)
3. [Runtime Errors](#runtime-errors)
4. [Animation Issues](#animation-issues)
5. [Gesture Issues](#gesture-issues)
6. [Performance Issues](#performance-issues)
7. [Platform-Specific Issues](#platform-specific-issues)
8. [Debugging Techniques](#debugging-techniques)

---

## Installation Issues

### Issue: Plugin Not Applied

**Error:**
```
ReanimatedError: Tried to synchronously call function {assign} from a different thread.
```

**Cause:** Babel plugin not configured correctly.

**Solution:**
```javascript
// babel.config.js
module.exports = {
  presets: ['module:metro-react-native-babel-preset'],
  plugins: [
    // Other plugins...
    'react-native-reanimated/plugin', // MUST be last!
  ],
};
```

**Verification:**
```bash
# Clear all caches
npx react-native start --reset-cache
# or
expo start --clear
```

### Issue: Metro Resolution Error

**Error:**
```
Unable to resolve module `react-native-reanimated`
```

**Solution:**
```bash
# Clean install
rm -rf node_modules
rm package-lock.json
npm install

# iOS
cd ios && pod deintegrate && pod install

# Android
cd android && ./gradlew clean
```

### Issue: Worklets Not Found

**Error:**
```
Error: The 'react-native-worklets' package was not found
```

**Solution:**
```bash
npm install react-native-worklets

# For iOS
cd ios && pod install
```

---

## Build Errors

### Issue: iOS Build Fails

**Error:**
```
'RNReanimated/REAAnimationsManager.h' file not found
```

**Solution:**
```bash
# Clean build
cd ios
rm -rf Pods Podfile.lock
pod deintegrate
pod install

# In Xcode
# Product > Clean Build Folder (Cmd+Shift+K)
# Product > Build (Cmd+B)
```

### Issue: Android Build Fails

**Error:**
```
Could not find com.swmansion.reanimated:react-native-reanimated
```

**Solution:**
```bash
# Clean and rebuild
cd android
./gradlew clean
./gradlew assembleDebug

# Or with Android Studio
# Build > Clean Project
# Build > Rebuild Project
```

### Issue: New Architecture Required

**Error:**
```
Reanimated 4.x requires React Native New Architecture (Fabric)
```

**Solution:**
```bash
# Enable New Architecture
export RCT_NEW_ARCH_ENABLED=1

# iOS
cd ios && pod install

# Android
cd android && ./gradlew assembleDebug
```

### Issue: Hermes Required

**Error:**
```
Reanimated requires Hermes JavaScript engine
```

**Solution:**
```javascript
// android/app/build.gradle
project.ext.react = [
    enableHermes: true
]

// ios/Podfile
use_react_native!(
  :hermes_enabled => true
)
```

---

## Runtime Errors

### Issue: Worklet Not Running on UI Thread

**Error:**
```
Tried to synchronously call function {x} from a different thread
```

**Cause:** Missing `'worklet'` directive.

**Solution:**
```typescript
// ❌ Wrong
const myFunction = () => {
  offset.value = 100;
};

// ✅ Correct
const myFunction = () => {
  'worklet';
  offset.value = 100;
};
```

### Issue: Hook Called Outside Component

**Error:**
```
Invalid hook call. Hooks can only be called inside of the body of a function component
```

**Cause:** Using hooks in worklets or outside components.

**Solution:**
```typescript
// ❌ Wrong - hook in worklet
const worklet = () => {
  'worklet';
  const [state, setState] = useState(0); // ❌
};

// ✅ Correct - use shared values
const Component = () => {
  const state = useSharedValue(0);
  
  const worklet = () => {
    'worklet';
    state.value = 100; // ✅
  };
};
```

### Issue: Reading Shared Value in Render

**Error:**
```
Value is not defined
```

**Cause:** Trying to read shared value directly in JSX.

**Solution:**
```typescript
// ❌ Wrong
function Component() {
  const offset = useSharedValue(0);
  return (
    <View style={{ left: offset.value }} /> // ❌ Won't update
  );
}

// ✅ Correct
function Component() {
  const offset = useSharedValue(0);
  const animatedStyle = useAnimatedStyle(() => ({
    left: offset.value, // ✅ Updates automatically
  }));
  return <Animated.View style={animatedStyle} />;
}
```

### Issue: Cannot Read Property of Undefined

**Error:**
```
TypeError: Cannot read property 'value' of undefined
```

**Cause:** Shared value not initialized or accessed incorrectly.

**Solution:**
```typescript
// ❌ Wrong
const Component = () => {
  let offset; // ❌ Not initialized
  
  useEffect(() => {
    offset = useSharedValue(0); // ❌ Hook in useEffect!
  }, []);
};

// ✅ Correct
const Component = () => {
  const offset = useSharedValue(0); // ✅ Initialize at top level
  
  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: offset?.value ?? 0 }], // ✅ Safe access
  }));
};
```

### Issue: runOnJS Error

**Error:**
```
runOnJS can only be used on the UI thread
```

**Cause:** Calling `runOnJS` from JS thread.

**Solution:**
```typescript
// ❌ Wrong
const jsFunction = () => {
  console.log('JS function');
};

// Called from JS thread
runOnJS(jsFunction)(); // ❌

// ✅ Correct - only call from worklet
const worklet = () => {
  'worklet';
  runOnJS(jsFunction)(); // ✅ Called from UI thread
};
```

---

## Animation Issues

### Issue: Animation Not Starting

**Symptom:** Animation doesn't run when triggered.

**Possible Causes & Solutions:**

```typescript
// 1. Value not being set
// ❌ Wrong
const animate = () => {
  withTiming(100); // ❌ Not assigned to anything!
};

// ✅ Correct
const animate = () => {
  offset.value = withTiming(100); // ✅ Assign to shared value
};

// 2. Component not animated
// ❌ Wrong
<Animated.View style={styles.box} /> // ❌ No animated style

// ✅ Correct
<Animated.View style={[styles.box, animatedStyle]} /> // ✅ Has animated style

// 3. Style not using shared value
// ❌ Wrong
const animatedStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: 100 }], // ❌ Static value
}));

// ✅ Correct
const animatedStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: offset.value }], // ✅ Uses shared value
}));
```

### Issue: Animation Janky/Laggy

**Symptom:** Animation doesn't run at 60fps.

**Solutions:**

```typescript
// 1. Use appropriate animation function
// ❌ Wrong - timing for gesture
const gesture = Gesture.Pan()
  .onUpdate((event) => {
    offset.value = withTiming(event.translationX); // ❌ Laggy!
  });

// ✅ Correct - direct assignment
const gesture = Gesture.Pan()
  .onUpdate((event) => {
    offset.value = event.translationX; // ✅ Smooth!
  });

// 2. Cancel previous animations
// ❌ Wrong
const gesture = Gesture.Pan()
  .onBegin(() => {
    // Animation may still be running
  });

// ✅ Correct
const gesture = Gesture.Pan()
  .onBegin(() => {
    cancelAnimation(offset); // ✅ Cancel first
  });

// 3. Reduce style complexity
// ❌ Wrong - too many properties
const animatedStyle = useAnimatedStyle(() => ({
  opacity: progress.value,
  backgroundColor: interpolateColor(/* ... */),
  borderRadius: interpolate(/* ... */),
  borderWidth: interpolate(/* ... */),
  shadowOpacity: interpolate(/* ... */),
  shadowRadius: interpolate(/* ... */),
  transform: [
    { translateX: /* ... */ },
    { translateY: /* ... */ },
    { scale: /* ... */ },
    { rotate: /* ... */ },
    { rotateX: /* ... */ },
    { rotateY: /* ... */ },
  ],
}));

// ✅ Correct - only what's needed
const animatedStyle = useAnimatedStyle(() => ({
  opacity: progress.value,
  transform: [
    { translateX: offsetX.value },
    { scale: scale.value },
  ],
}));
```

### Issue: Animation Not Completing

**Symptom:** Animation stops before reaching target.

**Causes & Solutions:**

```typescript
// 1. Value overwritten
// ❌ Wrong
offset.value = withTiming(100);
offset.value = withTiming(200); // ❌ First animation cancelled!

// ✅ Correct - sequence or wait
offset.value = withSequence(
  withTiming(100),
  withTiming(200)
);

// 2. Component unmounted
useEffect(() => {
  offset.value = withTiming(100);
  
  return () => {
    cancelAnimation(offset); // ✅ Cancel on unmount
  };
}, []);

// 3. Spring threshold not reached
// ❌ Wrong - spring may never settle
offset.value = withSpring(100, {
  damping: 1, // Too low
  stiffness: 10, // Too low
});

// ✅ Correct - reasonable values
offset.value = withSpring(100, {
  damping: 10,
  stiffness: 100,
});
```

### Issue: Spring Animation Too Bouncy

**Symptom:** Spring oscillates too much.

**Solution:**
```typescript
// Too bouncy
offset.value = withSpring(target, {
  damping: 2, // Too low
  stiffness: 50,
});

// Just right
offset.value = withSpring(target, {
  damping: 10, // Higher = less bounce
  stiffness: 100,
});

// No bounce (critically damped)
offset.value = withSpring(target, {
  damping: 30,
  stiffness: 200,
});
```

---

## Gesture Issues

### Issue: Gesture Not Recognized

**Symptom:** Gesture doesn't trigger.

**Solutions:**

```typescript
// 1. Missing GestureHandlerRootView
// ❌ Wrong
function App() {
  return <MyComponent />; // ❌ No root view
}

// ✅ Correct
function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <MyComponent />
    </GestureHandlerRootView>
  );
}

// 2. Gesture conflicts
// ❌ Wrong - ScrollView blocks Pan
<ScrollView>
  <GestureDetector gesture={panGesture}>
    {/* Content */}
  </GestureDetector>
</ScrollView>

// ✅ Correct - use waitFor or simultaneous
const panGesture = Gesture.Pan()
  .waitFor(scrollRef) // Wait for scroll to fail
  .onUpdate(/* ... */);

// 3. Active offset not set
// ❌ Wrong - gesture activates immediately
const panGesture = Gesture.Pan()
  .onUpdate(/* ... */);

// ✅ Correct - set activation threshold
const panGesture = Gesture.Pan()
  .activeOffsetX([-20, 20]) // Activate after 20px horizontal
  .activeOffsetY([-20, 20]) // Activate after 20px vertical
  .onUpdate(/* ... */);
```

### Issue: Gesture Laggy

**Symptom:** Gesture doesn't follow finger smoothly.

**Solutions:**

```typescript
// 1. Using React state instead of shared values
// ❌ Wrong
const [position, setPosition] = useState(0);

const gesture = Gesture.Pan()
  .onUpdate((event) => {
    runOnJS(setPosition)(event.translationX); // ❌ Laggy!
  });

// ✅ Correct
const position = useSharedValue(0);

const gesture = Gesture.Pan()
  .onUpdate((event) => {
    position.value = event.translationX; // ✅ Smooth!
  });

// 2. Not using velocity on release
// ❌ Wrong
.onEnd(() => {
  position.value = withSpring(0); // ❌ No momentum
});

// ✅ Correct
.onEnd((event) => {
  position.value = withSpring(0, {
    velocity: event.velocityX, // ✅ Natural momentum
  });
});
```

### Issue: Multiple Gestures Conflict

**Symptom:** Multiple gestures don't work together.

**Solution:**
```typescript
// Use Simultaneous for gestures that should work together
const panGesture = Gesture.Pan().onUpdate(/* ... */);
const pinchGesture = Gesture.Pinch().onUpdate(/* ... */);
const rotationGesture = Gesture.Rotation().onUpdate(/* ... */);

const composed = Gesture.Simultaneous(
  panGesture,
  Gesture.Simultaneous(pinchGesture, rotationGesture)
);

// Use Exclusive for mutually exclusive gestures
const tapGesture = Gesture.Tap().onEnd(/* ... */);
const longPressGesture = Gesture.LongPress().onStart(/* ... */);

const exclusive = Gesture.Exclusive(longPressGesture, tapGesture);
```

---

## Performance Issues

### Issue: Low FPS During Animation

**Diagnosis:**
```typescript
// Add frame callback to monitor
const frameCallback = useFrameCallback((frameInfo) => {
  const frameTime = frameInfo.timeSincePreviousFrame;
  if (frameTime && frameTime > 20) {
    console.warn('Slow frame:', frameTime.toFixed(2), 'ms');
  }
});
```

**Solutions:**

```typescript
// 1. Simplify animated styles
// ❌ Wrong - too complex
const animatedStyle = useAnimatedStyle(() => ({
  opacity: progress.value,
  backgroundColor: interpolateColor(/* 3 colors */),
  borderRadius: interpolate(/* complex */),
  borderWidth: interpolate(/* complex */),
  shadowOpacity: interpolate(/* complex */),
  shadowRadius: interpolate(/* complex */),
  shadowOffset: { width: 0, height: interpolate(/* ... */) },
  transform: [
    { translateX: interpolate(/* ... */) },
    { translateY: interpolate(/* ... */) },
    { scale: interpolate(/* ... */) },
    { rotate: interpolate(/* ... */) },
    { rotateX: interpolate(/* ... */) },
    { rotateY: interpolate(/* ... */) },
    { perspective: 1000 },
  ],
}));

// ✅ Correct - simplify
const animatedStyle = useAnimatedStyle(() => ({
  opacity: progress.value,
  transform: [
    { translateX: offsetX.value },
    { scale: scale.value },
  ],
}));

// 2. Use derived values for complex calculations
// ❌ Wrong - complex calculation in style
const animatedStyle = useAnimatedStyle(() => ({
  transform: [
    { 
      translateX: Math.sin(progress.value * Math.PI) * 100 
    },
  ],
}));

// ✅ Correct - pre-calculate
const derivedX = useDerivedValue(() => {
  return Math.sin(progress.value * Math.PI) * 100;
});

const animatedStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: derivedX.value }],
}));

// 3. Reduce re-renders
// ❌ Wrong - parent re-renders cause child re-render
function Parent() {
  const [count, setCount] = useState(0);
  return (
    <View>
      <AnimatedChild /> {/* Re-renders with parent */}
      <Button onPress={() => setCount(c => c + 1)} />
    </View>
  );
}

// ✅ Correct - isolate animated components
const AnimatedChild = memo(() => {
  const offset = useSharedValue(0);
  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: offset.value }],
  }));
  return <Animated.View style={animatedStyle} />;
});
```

### Issue: Memory Leaks

**Symptom:** App memory usage grows over time.

**Solutions:**

```typescript
// 1. Cancel animations on unmount
useEffect(() => {
  return () => {
    cancelAnimation(offset);
    cancelAnimation(scale);
    cancelAnimation(opacity);
  };
}, []);

// 2. Clean up reactions
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
      runOnJS(callback)();
    }
  }
);

// 3. Limit concurrent animations
// ❌ Wrong - too many animations
{items.map((item, i) => (
  <Animated.View
    key={item.id}
    entering={FadeIn.delay(i * 10)} // 1000 items = 1000 animations!
  />
))}

// ✅ Correct - stagger with max delay
{items.map((item, i) => (
  <Animated.View
    key={item.id}
    entering={FadeIn.delay(Math.min(i * 50, 1000))} // Max 1s delay
  />
))}
```

---

## Platform-Specific Issues

### iOS Issues

#### Issue: Shadow Animation Not Working

**Solution:**
```typescript
// iOS shadow properties are animatable
const animatedStyle = useAnimatedStyle(() => ({
  shadowOpacity: interpolate(progress.value, [0, 1], [0, 0.5]),
  shadowRadius: interpolate(progress.value, [0, 1], [0, 10]),
  shadowOffset: {
    width: 0,
    height: interpolate(progress.value, [0, 1], [0, 5]),
  },
}));
```

#### Issue: Blur Animation Not Working

**Solution:**
```typescript
// Use opacity for blur transitions
const animatedStyle = useAnimatedStyle(() => ({
  opacity: interpolate(progress.value, [0, 1], [0, 1]),
}));
```

### Android Issues

#### Issue: Shadow Animation Not Working

**Solution:**
```typescript
// Use elevation instead of shadow on Android
const animatedStyle = useAnimatedStyle(() => ({
  elevation: interpolate(progress.value, [0, 1], [0, 8]),
}));
```

#### Issue: Border Radius Animation Janky

**Solution:**
```typescript
// Use overflow: 'hidden' on parent for smooth border radius
<Animated.View style={{ borderRadius: animatedRadius, overflow: 'hidden' }}>
  <Image />
</Animated.View>
```

#### Issue: Performance on Low-End Devices

**Solution:**
```typescript
// Reduce animation complexity on Android
const isAndroid = Platform.OS === 'android';

const animatedStyle = useAnimatedStyle(() => ({
  transform: [
    { translateX: offsetX.value },
    // Skip rotation on Android for better performance
    ...(!isAndroid ? [{ rotate: `${rotation.value}rad` }] : []),
  ],
}));
```

---

## Debugging Techniques

### Console Logging

```typescript
// Log from worklets
const worklet = () => {
  'worklet';
  console.log('Value:', offset.value);
  console.warn('Warning!');
};

// Log animation progress
offset.value = withTiming(100, {}, (finished) => {
  console.log('Finished:', finished);
});

// Log gesture events
const gesture = Gesture.Pan()
  .onUpdate((event) => {
    console.log('Translation:', event.translationX);
  });
```

### Runtime Checks

```typescript
// Check which runtime you're on
import { isUIRuntime, isRNRuntime } from 'react-native-worklets';

const worklet = () => {
  'worklet';
  console.log('UI Runtime:', isUIRuntime());
  console.log('RN Runtime:', isRNRuntime());
};
```

### Performance Profiling

```typescript
// Measure animation duration
const startTime = useSharedValue(0);

const animate = () => {
  startTime.value = performance.now();
  offset.value = withTiming(100, {}, (finished) => {
    const duration = performance.now() - startTime.value;
    console.log('Animation took:', duration.toFixed(2), 'ms');
  });
};

// Monitor frame times
const frameCallback = useFrameCallback((frameInfo) => {
  const frameTime = frameInfo.timeSincePreviousFrame;
  if (frameTime && frameTime > 16.67) {
    console.warn(
      'Dropped frame:',
      frameTime.toFixed(2),
      'ms (target: 16.67ms)'
    );
  }
});
```

### React DevTools

```typescript
// Name components for easier debugging
const AnimatedCard = memo(function AnimatedCard({ item }) {
  // Component code
});

// Use displayName
AnimatedCard.displayName = 'AnimatedCard';
```

### Common Debug Patterns

```typescript
// Check if value is updating
const DebugComponent = () => {
  const offset = useSharedValue(0);
  
  // Visual indicator
  const animatedStyle = useAnimatedStyle(() => {
    console.log('Style re-evaluated, offset:', offset.value);
    return {
      transform: [{ translateX: offset.value }],
    };
  });
  
  return (
    <Animated.View style={animatedStyle}>
      <Text>Debug: {offset.value}</Text> {/* Won't update! */}
    </Animated.View>
  );
};

// Sync to React state for debugging
const [debugValue, setDebugValue] = useState(0);

useAnimatedReaction(
  () => offset.value,
  (current) => {
    runOnJS(setDebugValue)(current);
  }
);

return (
  <View>
    <Text>Debug: {debugValue}</Text> {/* Updates! */}
    <Animated.View style={animatedStyle} />
  </View>
);
```

---

## Quick Reference: Error Messages

| Error | Cause | Solution |
|-------|-------|----------|
| `Tried to synchronously call function from a different thread` | Missing `'worklet'` directive | Add `'worklet'` to function |
| `Invalid hook call` | Using hooks in worklets | Use shared values instead |
| `Value is not defined` | Reading shared value in render | Use animated styles |
| `Cannot read property 'value' of undefined` | Shared value not initialized | Initialize at component top |
| `runOnJS can only be used on the UI thread` | Calling runOnJS from JS | Only call from worklets |
| `Reanimated 4.x requires New Architecture` | Old architecture enabled | Enable Fabric/TurboModules |
| `Unable to resolve module` | Installation issue | Clean install dependencies |
| `Animation not starting` | Value not assigned | Assign to shared value |
| `Janky animation` | JS thread blocked | Use direct value assignment |

---

## Quick Reference: Debug Checklist

- [ ] Babel plugin is last in plugins array
- [ ] Metro cache cleared (`--reset-cache`)
- [ ] Native code rebuilt (`pod install` / `gradlew clean`)
- [ ] `'worklet'` directive present in custom worklets
- [ ] Hooks only used at component top level
- [ ] Shared values used for animation state
- [ ] Animated styles used for dynamic values
- [ ] Gestures wrapped in GestureHandlerRootView
- [ ] Animation functions assigned to shared values
- [ ] Previous animations cancelled before new ones
