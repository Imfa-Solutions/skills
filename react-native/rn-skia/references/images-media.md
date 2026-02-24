# Images & Media

## Loading Images

### useImage Hook

```tsx
import { useImage, Image } from "@shopify/react-native-skia";

const image1 = useImage(require("./assets/photo.png"));       // From require
const image2 = useImage("https://example.com/image.jpg");     // From URL
const image3 = useImage("Logo");                               // From bundle
const image4 = useImage("https://example.com/img.jpg", (err) => {
  console.error("Failed to load:", err);
});

// Always handle loading
if (!image) return <LoadingIndicator />;
```

## Image Component

```tsx
<Image image={image} x={0} y={0} width={200} height={200} fit="cover" />
```

### Fit Modes

| Mode | Description |
|------|-------------|
| `contain` | Scale to fit, preserve aspect ratio |
| `cover` | Fill area, preserve ratio (may crop) |
| `fill` | Stretch to fill |
| `fitWidth` | Scale to width |
| `fitHeight` | Scale to height |
| `scaleDown` | Scale down if needed, never up |
| `none` | Original size |

### Sampling Options

```tsx
import { FilterMode, MipmapMode } from "@shopify/react-native-skia";

<Image image={image} x={0} y={0} width={200} height={200}
  sampling={{ filter: FilterMode.Linear, mipmap: MipmapMode.Linear }} />
```

## Creating Images Programmatically

### From Base64

```tsx
const data = Skia.Data.fromBase64(base64String);
const image = Skia.Image.MakeImageFromEncoded(data);
```

### From Raw Pixels

```tsx
import { Skia, AlphaType, ColorType } from "@shopify/react-native-skia";

const width = 256, height = 256;
const pixels = new Uint8Array(width * height * 4);

for (let y = 0; y < height; y++) {
  for (let x = 0; x < width; x++) {
    const i = (y * width + x) * 4;
    pixels[i] = (x / width) * 255;        // R
    pixels[i + 1] = (y / height) * 255;   // G
    pixels[i + 2] = 128;                  // B
    pixels[i + 3] = 255;                  // A
  }
}

const data = Skia.Data.fromBytes(pixels);
const image = Skia.Image.MakeImage({
  width, height,
  alphaType: AlphaType.Opaque,
  colorType: ColorType.RGBA_8888,
  pixels: data,
});
```

### Drawing to Image

```tsx
import { drawAsImage } from "@shopify/react-native-skia";

const image = drawAsImage(
  <Group>
    <Circle cx={50} cy={50} r={50} color="red" />
    <Circle cx={100} cy={50} r={50} color="blue" />
  </Group>,
  { width: 150, height: 100 }
);
```

## Image Manipulation

```tsx
// Resize
const resized = image.resize({ width: 100, height: 100, filter: FilterMode.Linear });

// Encode
const pngData = image.encodeToBytes(ImageFormat.PNG, 100);
const jpegData = image.encodeToBytes(ImageFormat.JPEG, 90);
const base64 = image.encodeToBase64(ImageFormat.PNG);

// Read pixels
const pixels = image.readPixels();
const idx = (y * image.width() + x) * 4;
const r = pixels[idx], g = pixels[idx+1], b = pixels[idx+2], a = pixels[idx+3];
```

## Animated Images (GIF/WebP)

```tsx
import { useAnimatedImage, AnimatedImage } from "@shopify/react-native-skia";

const animatedImage = useAnimatedImage(require("./animation.gif"));
const frame = useSharedValue(0);

useEffect(() => {
  if (!animatedImage) return;
  const duration = animatedImage.duration() * 1000;
  frame.value = withRepeat(
    withTiming(animatedImage.frameCount() - 1, { duration }),
    -1
  );
}, [animatedImage]);

if (!animatedImage) return null;
<AnimatedImage animatedImage={animatedImage} frame={frame}
  x={0} y={0} width={200} height={200} />
```

### With useAnimatedImageValue

```tsx
import { useAnimatedImageValue } from "@shopify/react-native-skia";
const currentFrame = useAnimatedImageValue(animatedImage);
<AnimatedImage animatedImage={animatedImage} frame={currentFrame} ... />
```

## SVG Images

### Loading SVG

```tsx
import { useSVG, ImageSVG } from "@shopify/react-native-skia";

const svg = useSVG(require("./icon.svg"));
if (!svg) return null;
<ImageSVG svg={svg} x={0} y={0} width={100} height={100} />
```

### SVG from String

```tsx
const svgString = `<svg width="100" height="100" xmlns="http://www.w3.org/2000/svg">
  <circle cx="50" cy="50" r="40" fill="red"/>
</svg>`;
const svg = Skia.SVG.MakeFromString(svgString);
```

## Video

**Requirements:** Android API Level 26+.

```tsx
import { useVideo, Video } from "@shopify/react-native-skia";

const video = useVideo(require("./video.mp4"));
const frame = useSharedValue(0);

useEffect(() => {
  if (!video) return;
  const duration = video.duration() * 1000;
  const fps = video.fps();
  const totalFrames = (duration / 1000) * fps;
  frame.value = withRepeat(withTiming(totalFrames, { duration }), -1);
}, [video]);

if (!video) return null;
<Video video={video} frame={frame} x={0} y={0} width={300} height={200} />
```

### Playback Control

```tsx
const isPlaying = useSharedValue(true);
const currentTime = useSharedValue(0);
const frame = useDerivedValue(() => {
  if (!video || !isPlaying.value) return currentTime.value;
  return (currentTime.value + 1) % (video.duration() * video.fps());
});
```

## Skottie (Lottie Animations)

### Basic Usage

```tsx
import { Skottie, Skia } from "@shopify/react-native-skia";

const animationData = require("./animation.json");
const animation = Skia.Skottie.Make(JSON.stringify(animationData));

<Group transform={[{ scale: 0.5 }]}>
  <Skottie animation={animation} frame={41} />
</Group>
```

### Animated Skottie

```tsx
const clock = useClock();
const frame = useDerivedValue(() => {
  const fps = animation.fps();
  const duration = animation.duration();
  return Math.floor((clock.value / 1000) * fps) % (duration * fps);
});
<Skottie animation={animation} frame={frame} />
```

### Animation Properties

```tsx
const fps = animation.fps();
const duration = animation.duration();
const frameCount = fps * duration;
const bounds = animation.bounds();
```

### Dynamic Properties

```tsx
const properties = animation.properties();

// Modify color
const colorProp = animation.getProperty("Shape Layer 1", "Fill 1", "Color");
if (colorProp) colorProp.setColor(Skia.Color("red"));

// Modify opacity
const opacityProp = animation.getProperty("Layer", "Transform", "Opacity");
if (opacityProp) opacityProp.setValue(50);

// Modify position
const posProp = animation.getProperty("Layer", "Transform", "Position");
if (posProp) posProp.setValue({ x: 100, y: 200 });
```

### Slot Management

```tsx
const slots = animation.getSlots();
animation.setSlotColor("SlotName", Skia.Color("blue"));
animation.setSlotText("TextSlot", "Hello World");
```

## Image Effects

### Shaders on Images

```tsx
const shader = `
  uniform shader image;
  uniform float intensity;
  half4 main(float2 coord) {
    half4 color = image.eval(coord);
    return half4(color.rgb * intensity, color.a);
  }
`;

<Rect x={0} y={0} width={256} height={256}>
  <ImageShader image={image} fit="cover" rect={{ x: 0, y: 0, width: 256, height: 256 }}>
    <RuntimeShader source={shader} uniforms={{ intensity: 1.2 }} />
  </ImageShader>
</Rect>
```

### Color Filters on Images

```tsx
<Image image={image} x={0} y={0} width={200} height={200}>
  <MatrixColorFilter matrix={[
    0.299, 0.587, 0.114, 0, 0,
    0.299, 0.587, 0.114, 0, 0,
    0.299, 0.587, 0.114, 0, 0,
    0, 0, 0, 1, 0,
  ]} />
</Image>
```

## Canvas Snapshots

```tsx
const ref = useCanvasRef();
const takeSnapshot = () => {
  const image = ref.current?.makeImageSnapshot();
  if (image) {
    const data = image.encodeToBytes();
  }
};
<Canvas style={{ width: 300, height: 300 }} ref={ref}>{/* drawing */}</Canvas>
```

## Performance Best Practices

1. **Preload images** — use `useImage` early, show loading state.
2. **Use appropriate sizes** — resize large images: `image.resize({ width: 200, height: 200 })`.
3. **Cache drawn images** — `useMemo(() => drawAsImage(...), [])`.
4. **Use Atlas for repeated images** — single draw call instead of N `<Image>` components.

## Summary

| Feature | Component | Use Case |
|---------|-----------|----------|
| Static Images | `Image` | Photos, icons |
| Animated GIF/WebP | `AnimatedImage` | Simple animations |
| SVG | `ImageSVG` | Vector graphics |
| Video | `Video` | Video playback |
| Lottie | `Skottie` | Complex animations |
| Programmatic | `drawAsImage` | Generated graphics |
