# Performance Optimization

## Rendering Pipeline

```
JS Thread (React) → UI Thread (Skia) → GPU (Render)
Component Updates → Display List Updates → Frame Buffer
```

**Key Principles:**
1. Minimize JS thread work — keep animations on UI thread
2. Reduce FFI calls — batch operations
3. Optimize display lists — structure components efficiently
4. Use GPU effectively — leverage hardware acceleration

## Frame Budgets

| Target | Budget | Use Case |
|--------|--------|----------|
| 60fps | 16.67ms | Standard animations |
| 120fps | 8.33ms | High-end devices, smooth scrolling |

## Performance Targets

| Metric | Target | Excellent |
|--------|--------|-----------|
| Frame Rate | 60fps | 120fps |
| Frame Time | <16ms | <8ms |
| Memory | <100MB | <50MB |
| Startup | <2s | <1s |
| JS Thread | <50% | <30% |

## Rendering Mode Performance

### Retained Mode (Recommended for Most Cases)

Best for: UI animations, charts, interactive elements, property-based animations.

- Near-zero FFI cost for property updates
- Display list cached on native side
- Perfect for 60/120fps
- Structure changes are expensive

```tsx
// Excellent performance
const x = useSharedValue(0);
const y = useSharedValue(0);
const scale = useSharedValue(1);
const rotation = useSharedValue(0);

useEffect(() => {
  x.value = withSpring(100);
  y.value = withSpring(100);
  scale.value = withSpring(1.5);
  rotation.value = withSpring(Math.PI);
}, []);

<Canvas style={{ flex: 1 }}>
  <Group transform={[{ translateX: x }, { translateY: y }, { scale }, { rotate: rotation }]}>
    <Circle cx={0} cy={0} r={50} color="red" />
  </Group>
</Canvas>
```

### Immediate Mode (Picture API)

Best for: Particle systems, games, generative art, variable drawing commands.

- Dynamic command lists
- Full control over rendering
- Higher FFI cost
- More complex to optimize

```tsx
const recorder = useMemo(() => Skia.PictureRecorder(), []);
const paint = useMemo(() => Skia.Paint(), []);

const picture = useDerivedValue(() => {
  "worklet";
  const canvas = recorder.beginRecording(bounds);
  const count = Math.floor(progress.value * 100);
  for (let i = 0; i < count; i++) {
    canvas.drawCircle(Math.random() * 256, Math.random() * 256, 5, paint);
  }
  return recorder.finishRecordingAsPicture();
});

<Picture picture={picture} />
```

## Animation Performance

### 1. Use Shared Values Correctly

```tsx
// GOOD: Direct binding
const x = useSharedValue(0);
<Circle cx={x} cy={100} r={50} />

// BAD: Reading .value in render — breaks reactivity
<Circle cx={x.value} cy={100} r={50} />
```

### 2. Batch Related Animations

```tsx
// GOOD: Animate together
x.value = withSpring(targetX);
y.value = withSpring(targetY);
scale.value = withSpring(targetScale);

// BAD: Sequential with delays
x.value = withTiming(targetX, { duration: 300 });
setTimeout(() => { y.value = withTiming(targetY, { duration: 300 }); }, 300);
```

### 3. Use Derived Values

```tsx
// GOOD: Derive from single source
const progress = useSharedValue(0);
const x = useDerivedValue(() => progress.value * 200);
const y = useDerivedValue(() => Math.sin(progress.value * Math.PI) * 100);

// BAD: Multiple independent shared values updated via setInterval
```

### 4. Mark Worklets Properly

```tsx
// GOOD
const derived = useDerivedValue(() => { "worklet"; return progress.value * 100; });

// BAD: Missing worklet — runs on JS thread!
const bad = useDerivedValue(() => { return progress.value * 100; });
```

## Component Structure Optimization

### 1. Minimize Tree Depth

```tsx
// GOOD: Flat
<Canvas><Group><Circle /><Rect /><Path /></Group></Canvas>

// BAD: Deep nesting
<Canvas><Group><Group><Group><Circle /></Group></Group></Group></Canvas>
```

### 2. Use Groups Wisely

```tsx
// GOOD: Group shared properties
<Group opacity={0.5} transform={[{ scale: 2 }]}>
  <Circle cx={50} cy={50} r={25} />
  <Circle cx={100} cy={50} r={25} />
  <Circle cx={150} cy={50} r={25} />
</Group>

// BAD: Repeat on each
<Circle cx={50} cy={50} r={25} opacity={0.5} transform={[{ scale: 2 }]} />
<Circle cx={100} cy={50} r={25} opacity={0.5} transform={[{ scale: 2 }]} />
```

### 3. Cache Static Content as Pictures

```tsx
const staticPicture = useMemo(() => {
  const recorder = Skia.PictureRecorder();
  const canvas = recorder.beginRecording(bounds);
  // Draw static content
  return recorder.finishRecordingAsPicture();
}, []);

<Canvas>
  <Picture picture={staticPicture} />
  {/* Animated content here */}
</Canvas>
```

## Memory Optimization

### 1. Reuse Objects

```tsx
// GOOD
const paint = useMemo(() => { const p = Skia.Paint(); p.setColor(Skia.Color("red")); return p; }, []);

// BAD
const paint = Skia.Paint(); // New every render
```

### 2. Dispose Large Images

```tsx
useEffect(() => {
  return () => { largeImage.dispose(); };
}, []);
```

### 3. Use Appropriate Image Sizes

```tsx
// GOOD: Resize to display size
const displayImage = useMemo(() => largeImage.resize({ width: 200, height: 200 }), [largeImage]);

// BAD: Use 4K image for 200x200 display
<Image image={largeImage} width={200} height={200} />
```

## Atlas for High-Performance Rendering

1000+ particles at 60fps with single draw call:

```tsx
const spriteImage = useMemo(
  () => drawAsImage(<Circle cx={5} cy={5} r={5} color="blue" />, { width: 10, height: 10 }),
  []
);

const transforms = useRSXformBuffer(particleCount, (i, transform) => {
  "worklet";
  const time = progress.value * Math.PI * 2;
  const angle = (i / particleCount) * Math.PI * 2;
  const radius = 100 + Math.sin(time + i * 0.1) * 50;
  const x = 150 + Math.cos(angle + time) * radius;
  const y = 150 + Math.sin(angle + time) * radius;
  const rotation = angle + time;
  transform.set(Math.cos(rotation), Math.sin(rotation), x, y);
});

<Atlas image={spriteImage} sprites={[rect(0, 0, 10, 10)]} transforms={transforms} />
```

## Filter Cost Hierarchy

| Filter | Cost | Use For |
|--------|------|---------|
| `MatrixColorFilter` | Low | Color adjustments |
| `Offset` | Low | Shadows |
| `BlurMask` | Medium | Shape glow |
| `Blur` | Medium | Image blur |
| `BackdropBlur` | High | Glass effects |
| `DisplacementMap` | High | Distortion |
| `RuntimeShader` | High | Custom effects |

**Tips:**
- Order cheapest-first: `MatrixColorFilter` → `Blur`
- Use `BlurMask` for simple shapes (cheaper than `Blur`)
- Limit blur radius: 5 fast, 50 slow

## Platform-Specific Optimization

### iOS
- Metal rendering by default
- Leverage tile-based rendering
- Minimize offscreen render passes
- Retained mode gives best performance

### Android
```tsx
<Canvas androidWarmup={true}>{/* Static icon or logo */}</Canvas>
```
- OpenGL rendering
- Use `androidWarmup` for static content
- Consider TextureView for complex scenes
- API 26+ for video

### Web
- CanvasKit (WASM) — larger bundle
- Load asynchronously
- Good for prototyping

## Profiling and Debugging

### FPS Monitor

```tsx
import { useFrameCallback } from "@shopify/react-native-skia";

const frameCount = useRef(0);
const lastTime = useRef(Date.now());

useFrameCallback(() => {
  frameCount.current++;
  const now = Date.now();
  if (now - lastTime.current >= 1000) {
    console.log(`FPS: ${frameCount.current}`);
    frameCount.current = 0;
    lastTime.current = now;
  }
});
```

### React DevTools
Look for: unnecessary re-renders, long mount times, deep component trees.

### Native Profiling
- **iOS**: Instruments (Time Profiler, GPU Driver), Metal Frame Capture
- **Android**: Android Profiler, GPU rendering profile

## Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| `cx={x.value}` in JSX | Pass shared value directly: `cx={x}` |
| `Skia.Paint()` in render | Wrap in `useMemo` |
| Dynamic component lists | Use Atlas with `useRSXformBuffer` |
| `BackdropBlur` on everything | Limit area or use `BlurMask` |
| Missing worklet directive | Add `"worklet"` as first statement |
| No animation cleanup | `cancelAnimation(sv)` in effect cleanup |

## Operation Speed Reference

```
Fastest (use freely):      Circle, Rect, Line, Group transforms, MatrixColorFilter
Fast (use moderately):     Path (simple), BlurMask, LinearGradient
Slow (use sparingly):      BackdropBlur, RuntimeShader, DisplacementMap, Blur (large radius)
```

## Pre-Release Checklist

- [ ] Test on low-end devices
- [ ] Verify 60fps on target devices
- [ ] Profile memory usage
- [ ] Check for memory leaks
- [ ] Optimize large images
- [ ] Minimize filter usage
- [ ] Use Atlas for particles
- [ ] Cache static content
- [ ] Worklet directives present
- [ ] Shared values used correctly (not `.value` in render)
- [ ] Objects reused with `useMemo`
- [ ] Derived values for computations
- [ ] Proper cleanup in `useEffect`
