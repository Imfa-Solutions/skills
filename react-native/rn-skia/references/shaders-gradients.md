# Shaders & Gradients

## Overview

Shaders are GPU programs for complex visual effects. Skia provides built-in gradient/noise shaders and custom runtime shaders via SkSL (Skia Shading Language).

## Gradients

### Linear Gradient

```tsx
import { LinearGradient, Rect, vec } from "@shopify/react-native-skia";

<Rect x={0} y={0} width={256} height={256}>
  <LinearGradient start={vec(0, 0)} end={vec(256, 256)} colors={["blue", "yellow"]} />
</Rect>
```

| Prop | Type | Description |
|------|------|-------------|
| `start` | `Point` | Start position |
| `end` | `Point` | End position |
| `colors` | `string[]` | Gradient colors |
| `positions` | `number[]` | Color stops (0-1) |
| `mode` | `TileMode` | `clamp`, `repeat`, `mirror`, `decal` |

### Radial Gradient

```tsx
import { RadialGradient, Circle, vec } from "@shopify/react-native-skia";

<Circle cx={128} cy={128} r={128}>
  <RadialGradient c={vec(128, 128)} r={128} colors={["red", "blue"]} />
</Circle>
```

| Prop | Type | Description |
|------|------|-------------|
| `c` | `Point` | Center |
| `r` | `number` | Radius |
| `colors` | `string[]` | Colors |
| `positions` | `number[]` | Stops |

### Sweep Gradient

```tsx
import { SweepGradient, Circle, vec } from "@shopify/react-native-skia";

<Circle cx={128} cy={128} r={128}>
  <SweepGradient c={vec(128, 128)}
    colors={["red", "yellow", "green", "cyan", "blue", "magenta", "red"]} />
</Circle>
```

### Two-Point Conical Gradient

```tsx
import { TwoPointConicalGradient, Rect, vec } from "@shopify/react-native-skia";

<Rect x={0} y={0} width={256} height={256}>
  <TwoPointConicalGradient
    start={vec(100, 100)} startR={20}
    end={vec(156, 156)} endR={100}
    colors={["white", "black"]}
  />
</Rect>
```

### Multiple Color Stops

```tsx
<LinearGradient
  start={vec(0, 0)} end={vec(256, 0)}
  colors={["#FF0080", "#7928CA", "#FF0080"]}
  positions={[0, 0.5, 1]}
/>
```

### Animated Gradients

```tsx
const progress = useSharedValue(0);
const start = useDerivedValue(() => vec(
  128 + Math.cos(progress.value) * 100,
  128 + Math.sin(progress.value) * 100
));

useEffect(() => {
  progress.value = withRepeat(withTiming(2 * Math.PI, { duration: 3000 }), -1);
}, []);

<Rect x={0} y={0} width={256} height={256}>
  <LinearGradient start={start} end={vec(128, 128)} colors={["blue", "yellow"]} />
</Rect>
```

## Noise Shaders

### Fractal Noise

```tsx
import { FractalNoise, Rect } from "@shopify/react-native-skia";

<Rect x={0} y={0} width={256} height={256}>
  <FractalNoise freqX={0.05} freqY={0.05} octaves={4} seed={0} />
</Rect>
```

| Prop | Type | Description |
|------|------|-------------|
| `freqX` | `number` | X frequency |
| `freqY` | `number` | Y frequency |
| `octaves` | `number` | Noise octaves |
| `seed` | `number` | Random seed |

### Turbulence

```tsx
import { Turbulence, Rect } from "@shopify/react-native-skia";

<Rect x={0} y={0} width={256} height={256}>
  <Turbulence freqX={0.02} freqY={0.02} octaves={3} seed={1} />
</Rect>
```

### Animated Noise

```tsx
const time = useSharedValue(0);
const seed = useDerivedValue(() => Math.floor(time.value / 100));
useEffect(() => { time.value = withRepeat(withTiming(1000, { duration: 10000 }), -1); }, []);

<Rect x={0} y={0} width={256} height={256}>
  <FractalNoise freqX={0.05} freqY={0.05} octaves={4} seed={seed} />
</Rect>
```

## Custom Runtime Shaders (SkSL)

### Basic Runtime Shader

```tsx
import { RuntimeShader, Rect } from "@shopify/react-native-skia";

const source = `
  uniform shader image;
  uniform float time;

  half4 main(float2 coord) {
    half4 color = image.eval(coord);
    float wave = sin(coord.x * 0.1 + time) * 0.1;
    return half4(color.r + wave, color.g, color.b, color.a);
  }
`;

<Rect x={0} y={0} width={256} height={256}>
  <RuntimeShader source={source} uniforms={{ time }} />
</Rect>
```

### Common Shader Patterns

**Gradient Animation:**
```glsl
uniform float progress;
half4 main(float2 coord) {
  float t = coord.x / 256.0;
  half3 color1 = half3(1.0, 0.0, 0.5);
  half3 color2 = half3(0.5, 0.0, 1.0);
  half3 finalColor = mix(color1, color2, t + progress * 0.5);
  return half4(finalColor, 1.0);
}
```

**Wave Distortion:**
```glsl
uniform shader image;
uniform float time;
uniform float amplitude;
uniform float frequency;

half4 main(float2 coord) {
  float2 distorted = coord;
  distorted.y += sin(coord.x * frequency + time) * amplitude;
  return image.eval(distorted);
}
```

**Chromatic Aberration:**
```glsl
uniform shader image;
uniform float intensity;

half4 main(float2 coord) {
  float r = image.eval(coord + float2(intensity, 0)).r;
  float g = image.eval(coord).g;
  float b = image.eval(coord - float2(intensity, 0)).b;
  return half4(r, g, b, 1.0);
}
```

**Glow Effect:**
```glsl
uniform shader image;
uniform float radius;
uniform half3 glowColor;
uniform float intensity;

half4 main(float2 coord) {
  half4 color = image.eval(coord);
  float glow = 0.0;
  for (float x = -radius; x <= radius; x += 1.0) {
    for (float y = -radius; y <= radius; y += 1.0) {
      float dist = length(float2(x, y));
      if (dist <= radius) {
        half4 sample = image.eval(coord + float2(x, y));
        glow += sample.a * (1.0 - dist / radius);
      }
    }
  }
  half3 finalColor = color.rgb + glowColor * glow * intensity;
  return half4(finalColor, max(color.a, glow * intensity));
}
```

### Aurora Background

```tsx
const shader = `
  uniform float time;
  half4 main(float2 coord) {
    float2 uv = coord / 256.0;
    float wave1 = sin(uv.x * 3.0 + time) * 0.5 + 0.5;
    float wave2 = sin(uv.y * 2.0 - time * 0.5) * 0.5 + 0.5;
    float wave3 = sin((uv.x + uv.y) * 2.0 + time * 0.3) * 0.5 + 0.5;
    half3 c1 = half3(0.0, 0.8, 1.0);
    half3 c2 = half3(1.0, 0.0, 0.8);
    half3 c3 = half3(0.5, 0.0, 1.0);
    half3 finalColor = mix(mix(c1, c2, wave1), c3, wave2 * wave3);
    return half4(finalColor, 1.0);
  }
`;
```

### Water Ripple Effect

```tsx
const shader = `
  uniform shader image;
  uniform float time;
  uniform float2 touch;
  half4 main(float2 coord) {
    float2 center = touch;
    float dist = distance(coord, center);
    float ripple = sin(dist * 0.2 - time * 3.0) * exp(-dist * 0.01);
    float2 distorted = coord + normalize(coord - center) * ripple * 5.0;
    return image.eval(distorted);
  }
`;
```

## Shader Library

```tsx
import { ShaderLib } from "@shopify/react-native-skia";

const source = `
  #include "shaders/lib.glsl"
  uniform shader image;
  half4 main(float2 coord) { return image.eval(coord); }
`;
```

Available functions:
```glsl
half3 rgb2hsl(half3 rgb);   half3 hsl2rgb(half3 hsl);
half3 rgb2hsv(half3 rgb);   half3 hsv2rgb(half3 hsv);
float noise(float2 p);      float fbm(float2 p, int octaves);
float turbulence(float2 p, int octaves);
float map(float value, float inMin, float inMax, float outMin, float outMax);
float2 rotate(float2 p, float angle);
float2 scale(float2 p, float2 s);
```

## Image Shaders

```tsx
import { ImageShader, Rect, useImage } from "@shopify/react-native-skia";

const image = useImage(require("./texture.png"));
if (!image) return null;

<Rect x={0} y={0} width={256} height={256}>
  <ImageShader image={image} fit="cover" rect={{ x: 0, y: 0, width: 256, height: 256 }} />
</Rect>
```

| Prop | Type | Description |
|------|------|-------------|
| `image` | `SkImage` | Texture image |
| `fit` | `ImageFit` | How to fit |
| `rect` | `SkRect` | Target rectangle |
| `sampling` | `Sampling` | Sampling options |

## Color Shader

```tsx
import { Color, Rect } from "@shopify/react-native-skia";
<Rect x={0} y={0} width={256} height={256}><Color color="red" /></Rect>
```

## Shader Composition

```tsx
<Rect x={0} y={0} width={256} height={256}>
  <LinearGradient start={vec(0, 0)} end={vec(256, 0)} colors={["red", "blue"]}>
    <Blur blur={10} />
  </LinearGradient>
</Rect>

<Rect x={0} y={0} width={256} height={256}>
  <FractalNoise freqX={0.05} freqY={0.05} octaves={4}>
    <ColorMatrix matrix={[...]} />
  </FractalNoise>
</Rect>
```

## Performance

1. **Prefer built-in shaders** over custom RuntimeShader for simple effects.
2. **Batch uniform updates**: `useDerivedValue(() => ({ time: t.value, intensity: i.value }))`.
3. **Use half precision** where possible: `half4` not `float4`.
4. **Cache shader programs**: `useMemo(() => Skia.RuntimeEffect.Make(source), [])`.

## Performance Table

| Shader Type | Performance |
|-------------|-------------|
| `LinearGradient`, `RadialGradient`, `SweepGradient`, `TwoPointConicalGradient` | Excellent |
| `ImageShader` | Excellent |
| `FractalNoise`, `Turbulence` | Good |
| `RuntimeShader` | Good (depends on complexity) |
