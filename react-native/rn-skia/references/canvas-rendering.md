# Canvas & Rendering Modes

## Two Rendering Paradigms

1. **Retained Mode** (Default) — Declarative React component tree → display list → GPU
2. **Immediate Mode** — Direct drawing commands via Picture API

## Retained Mode

React components → optimized display list (C++) → GPU rendering. Animations update properties with near-zero FFI cost.

**When to use:** UI components, interactive graphics, animations with static structure, 60/120fps smooth animations.

```tsx
import { Canvas, Circle, Group } from "@shopify/react-native-skia";
import { useSharedValue, withSpring } from "react-native-reanimated";

export const RetainedModeExample = () => {
  const radius = useSharedValue(50);
  useEffect(() => { radius.value = withSpring(100); }, []);

  return (
    <Canvas style={{ flex: 1 }}>
      <Group>
        <Circle cx={128} cy={128} r={radius} color="cyan" />
      </Group>
    </Canvas>
  );
};
```

### Best Practices

```tsx
// GOOD: Static structure, animated properties
const cx = useSharedValue(100);
<Circle cx={cx} cy={100} r={50} color="red" />

// BAD: Dynamic structure changing every frame
{items.map((item, i) => (
  <Circle key={i} cx={item.x} cy={item.y} r={10} />
))}
```

### Performance for Retained Mode

```tsx
// GOOD: Animate properties
const scale = useSharedValue(1);
const opacity = useSharedValue(1);
<Group transform={[{ scale }]}>
  <Circle cx={100} cy={100} r={50} opacity={opacity} />
</Group>

// BAD: setState driving animation
const [count, setCount] = useState(0);
useEffect(() => {
  const interval = setInterval(() => setCount(c => c + 1), 16);
  return () => clearInterval(interval);
}, []);
<Circle cx={count} cy={100} r={50} /> // Creates new Circle every frame!
```

## Immediate Mode (Picture API)

Issue drawing commands directly per frame. Full control but manual management.

**When to use:** Variable draw commands per frame, games, particle systems, generative art, unpredictable scenes.

```tsx
import { Canvas, Picture, Skia } from "@shopify/react-native-skia";
import { useDerivedValue, useSharedValue, withRepeat, withTiming } from "react-native-reanimated";

const size = 256;

export const ImmediateModeExample = () => {
  const progress = useSharedValue(0);
  const recorder = Skia.PictureRecorder();
  const paint = Skia.Paint();

  useEffect(() => {
    progress.value = withRepeat(withTiming(1, { duration: 2000 }), -1, true);
  }, []);

  const picture = useDerivedValue(() => {
    "worklet";
    const canvas = recorder.beginRecording(Skia.XYWHRect(0, 0, size, size));
    const numberOfCircles = Math.floor(progress.value * 10);
    for (let i = 0; i < numberOfCircles; i++) {
      const alpha = ((i + 1) / 10) * 255;
      const r = ((i + 1) / 10) * (size / 2);
      paint.setColor(Skia.Color(`rgba(0, 122, 255, ${alpha / 255})`));
      canvas.drawCircle(size / 2, size / 2, r, paint);
    }
    return recorder.finishRecordingAsPicture();
  });

  return (
    <Canvas style={{ flex: 1 }}>
      <Picture picture={picture} />
    </Canvas>
  );
};
```

### Picture API Reference

```tsx
const recorder = Skia.PictureRecorder();
const canvas = recorder.beginRecording(bounds);
canvas.drawCircle(x, y, r, paint);
canvas.drawRect(rect, paint);
canvas.drawPath(path, paint);
const picture = recorder.finishRecordingAsPicture();
```

### Performance for Immediate Mode

```tsx
// GOOD: Reuse paint and recorder
const paint = useMemo(() => Skia.Paint(), []);
const recorder = useMemo(() => Skia.PictureRecorder(), []);

// BAD: Create new objects every frame
const picture = useDerivedValue(() => {
  "worklet";
  const paint = Skia.Paint(); // new every frame!
  const recorder = Skia.PictureRecorder(); // new every frame!
  ...
});
```

## Combining Both Modes

```tsx
<Canvas style={{ flex: 1 }}>
  {/* Immediate mode for particles */}
  <Picture picture={particlePicture} />
  {/* Retained mode for UI */}
  <Group>
    <Circle cx={50} cy={50} r={uiCircle} color="red" />
  </Group>
</Canvas>
```

## Offscreen Rendering

```tsx
const surface = Skia.Surface.MakeOffscreen(256, 256);
const canvas = surface.getCanvas();
canvas.drawCircle(128, 128, 100, paint);
const image = surface.makeImageSnapshot();
```

## Headless Rendering

```tsx
const surface = headless.MakeSurface(256, 256);
const canvas = surface.getCanvas();
canvas.drawRect({ x: 0, y: 0, width: 256, height: 256 }, paint);
const image = surface.makeImageSnapshot();
const data = image.encodeToBytes();
```

## Canvas Snapshots

```tsx
const ref = useCanvasRef();
const takeSnapshot = async () => {
  const image = ref.current?.makeImageSnapshot();
  if (image) {
    const bytes = image.encodeToBytes();
  }
};
<Canvas style={{ flex: 1 }} ref={ref}>{/* drawing */}</Canvas>
```

## Custom Renderers

```tsx
useFrameCallback((info) => {
  // Called before every frame
  // info.timeStamp, info.size
});
```

## Rendering Modes Comparison

| Feature | Retained Mode | Immediate Mode |
|---------|--------------|----------------|
| API Style | Declarative (React) | Imperative (Direct) |
| FFI Cost | Near-zero | Higher |
| Structure Changes | Expensive | Cheap |
| Property Animation | Excellent | Good |
| Use Case | UI, stable scenes | Games, particles |

## Platform Considerations

- **Android**: Use `androidWarmup` for static icons; consider TextureView; API 26+ for video.
- **iOS**: Metal rendering by default; best with retained mode.
- **Web**: CanvasKit (WASM); larger bundle; good for prototyping.
