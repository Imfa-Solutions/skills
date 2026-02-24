# Animations

## Overview

Seamless integration with **Reanimated v3+** — animations run on UI thread at 60/120fps. Shared values can be passed directly as Skia component props with near-zero FFI cost. No `createAnimatedComponent` needed.

## Setup

```bash
npm install react-native-reanimated react-native-gesture-handler
```

```js
// babel.config.js
module.exports = {
  presets: ['module:metro-react-native-babel-preset'],
  plugins: ['react-native-reanimated/plugin'], // Must be last
};
```

## Basic Animation

```tsx
import { Canvas, Circle, Group } from "@shopify/react-native-skia";
import { useSharedValue, useDerivedValue, withRepeat, withTiming } from "react-native-reanimated";

export const HelloWorldAnimation = () => {
  const size = 256;
  const r = useSharedValue(0);
  const c = useDerivedValue(() => size - r.value);

  useEffect(() => {
    r.value = withRepeat(withTiming(size * 0.33, { duration: 1000 }), -1);
  }, [r, size]);

  return (
    <Canvas style={{ flex: 1 }}>
      <Group blendMode="multiply">
        <Circle cx={r} cy={r} r={r} color="cyan" />
        <Circle cx={c} cy={r} r={r} color="magenta" />
        <Circle cx={size / 2} cy={c} r={r} color="yellow" />
      </Group>
    </Canvas>
  );
};
```

## Animation Types

### Timing

```tsx
progress.value = withTiming(1, { duration: 1000 });
progress.value = withTiming(1, { duration: 1000, easing: Easing.bezier(0.25, 0.1, 0.25, 1) });
// Easings: Easing.linear, .ease, .in(.ease), .out(.ease), .inOut(.ease), .bounce, .elastic(1)
```

### Spring

```tsx
scale.value = withSpring(2);
scale.value = withSpring(2, {
  damping: 10, stiffness: 100, mass: 1,
  overshootClamping: false, restDisplacementThreshold: 0.01, restSpeedThreshold: 2,
});
```

### Decay

```tsx
translateX.value = withDecay({ velocity: 500, clamp: [0, screenWidth], deceleration: 0.997 });
```

### Repeat

```tsx
// Infinite
rotation.value = withRepeat(withTiming(2 * Math.PI, { duration: 2000 }), -1);
// Finite with reverse
rotation.value = withRepeat(withTiming(2 * Math.PI, { duration: 2000 }), 3, true);
```

### Sequence

```tsx
opacity.value = withSequence(
  withTiming(1, { duration: 500 }),
  withDelay(1000, withTiming(0, { duration: 500 }))
);
```

## Shared Values in Skia

### Direct Property Binding

```tsx
const cx = useSharedValue(100);
const cy = useSharedValue(100);
const r = useSharedValue(50);

<Circle cx={cx} cy={cy} r={r} color="red" />  // Direct — no conversion!
```

### Derived Values

```tsx
const progress = useSharedValue(0);
const x = useDerivedValue(() => progress.value * 200);
const y = useDerivedValue(() => Math.sin(progress.value * Math.PI) * 100);
const opacity = useDerivedValue(() => 1 - progress.value * 0.5);
```

### Transform Arrays

```tsx
const transform = useDerivedValue(() => [
  { translateX: x.value }, { translateY: y.value },
  { scale: scale.value }, { rotate: rotation.value },
]);
<Group transform={transform}><Circle cx={0} cy={0} r={50} /></Group>
```

## Skia Animation Hooks

### usePathInterpolation

Smoothly interpolate between paths (must have same number of commands):

```tsx
import { usePathInterpolation } from "@shopify/react-native-skia";

const path = usePathInterpolation(progress, [0, 0.5, 1], [angryPath, normalPath, goodPath]);
<Path path={path} style="stroke" strokeWidth={5} />
```

### usePathValue

Animate paths efficiently:

```tsx
import { usePathValue } from "@shopify/react-native-skia";

const clip = usePathValue((path) => {
  "worklet";
  const transform = processTransform3d([{ perspective: 300 }, { rotateY: rotateY.value }]);
  path.transform(transform);
}, Skia.Path.Make());
```

### useClock

Continuously updating time value:

```tsx
import { useClock } from "@shopify/react-native-skia";

const clock = useClock(); // Milliseconds since start
const rotation = useDerivedValue(() => (clock.value / 1000) * Math.PI);
```

### useRectBuffer

Animate multiple rectangles efficiently:

```tsx
import { useRectBuffer } from "@shopify/react-native-skia";

const rects = useRectBuffer(100, (i, rect) => {
  "worklet";
  rect.x = Math.random() * 300;
  rect.y = Math.random() * 300;
  rect.width = 10; rect.height = 10;
});
```

### useRSXformBuffer

Animate Atlas transforms efficiently:

```tsx
import { useRSXformBuffer, Atlas } from "@shopify/react-native-skia";

const transforms = useRSXformBuffer(1000, (i, transform) => {
  "worklet";
  const x = (i % 10) * 30;
  const y = Math.floor(i / 10) * 30;
  transform.set(1, 0, x, y); // scaleX, skewY, transX, transY
});
<Atlas image={spriteImage} sprites={sprites} transforms={transforms} />
```

## Color Animations

Use `interpolateColors` (Skia uses different color format than Reanimated):

```tsx
import { interpolateColors, Fill } from "@shopify/react-native-skia";

const color = useDerivedValue(() =>
  interpolateColors(progress.value, [0, 0.5, 1], ["red", "green", "blue"])
);
<Fill color={color} />
```

### Gradient Color Animation

```tsx
const colors = useDerivedValue(() => [
  interpolateColors(progress.value, [0, 1], ["#FF008C", "#7928CA"]),
  interpolateColors(progress.value, [0, 1], ["#7928CA", "#FF008C"]),
]);
<Fill>
  <LinearGradient start={vec(0, 0)} end={vec(256, 256)} colors={colors} />
</Fill>
```

## Gesture Integration

### Pan Gesture

```tsx
import { GestureDetector, Gesture } from "react-native-gesture-handler";
import { useSharedValue, withDecay } from "react-native-reanimated";

const translateX = useSharedValue(100);
const gesture = Gesture.Pan()
  .onChange((e) => { translateX.value += e.changeX; })
  .onEnd((e) => {
    translateX.value = withDecay({ velocity: e.velocityX, clamp: [0, 400] });
  });

<GestureDetector gesture={gesture}>
  <Canvas style={{ flex: 1 }}>
    <Fill color="white" />
    <Circle cx={translateX} cy={100} r={20} color="blue" />
  </Canvas>
</GestureDetector>
```

### Element Tracking

```tsx
const x = useSharedValue(100);
const y = useSharedValue(100);
const gesture = Gesture.Pan().onChange((e) => {
  x.value += e.changeX;
  y.value += e.changeY;
});

const animatedStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: x.value - radius }, { translateY: y.value - radius }],
}));

<GestureDetector gesture={gesture}>
  <View style={{ flex: 1 }}>
    <Canvas style={{ flex: 1 }}>
      <Circle cx={x} cy={y} r={radius} color="blue" />
    </Canvas>
    <Animated.View style={[styles.tracker, animatedStyle]} />
  </View>
</GestureDetector>
```

### Multi-Touch

```tsx
const scale = useSharedValue(1);
const rotation = useSharedValue(0);

const pinch = Gesture.Pinch().onChange((e) => { scale.value = e.scale; });
const rotate = Gesture.Rotation().onChange((e) => { rotation.value = e.rotation; });
const combined = Gesture.Simultaneous(pinch, rotate);

<GestureDetector gesture={combined}>
  <Canvas>
    <Group transform={[{ scale: scale.value }, { rotate: rotation.value }]}>
      <Rect x={-50} y={-50} width={100} height={100} color="red" />
    </Group>
  </Canvas>
</GestureDetector>
```

## Best Practices

1. **Mark worklets**: `useDerivedValue(() => { "worklet"; return progress.value * 100; })`
2. **Derive from single source**: Use `useDerivedValue` chains, not multiple independent `useSharedValue`.
3. **Batch related animations**: Set all `.value` together, don't use setTimeout between them.
4. **Cleanup animations**: `useEffect(() => { ... return () => cancelAnimation(progress); }, [])`.
5. **Use `interpolateColors`** for Skia color animations, not Reanimated's `interpolateColor`.

## Complete Examples

### Pulsing Button

```tsx
const scale = useSharedValue(1);
const opacity = useSharedValue(1);
useEffect(() => {
  scale.value = withRepeat(withSequence(withTiming(1.1, { duration: 500 }), withTiming(1, { duration: 500 })), -1);
  opacity.value = withRepeat(withSequence(withTiming(0.7, { duration: 500 }), withTiming(1, { duration: 500 })), -1);
}, []);

<Canvas style={{ width: 100, height: 100 }}>
  <Group transform={[{ scale }]} origin={{ x: 50, y: 50 }}>
    <Circle cx={50} cy={50} r={40} color="#007AFF" opacity={opacity} />
  </Group>
</Canvas>
```

### Loading Spinner

```tsx
const rotation = useSharedValue(0);
useEffect(() => {
  rotation.value = withRepeat(withTiming(2 * Math.PI, { duration: 1000, easing: Easing.linear }), -1);
}, []);

<Canvas style={{ width: 50, height: 50 }}>
  <Group transform={[{ rotate: rotation }]} origin={{ x: 25, y: 25 }}>
    <Arc rect={{ x: 5, y: 5, width: 40, height: 40 }} start={0} end={Math.PI * 1.5}
      strokeWidth={4} style="stroke" color="#007AFF" />
  </Group>
</Canvas>
```
