# Filters & Effects

## Image Filters

### Blur

```tsx
import { Blur, Rect } from "@shopify/react-native-skia";

<Rect x={0} y={0} width={256} height={256} color="red">
  <Blur blur={10} mode="normal" />
</Rect>
```

| Prop | Type | Description |
|------|------|-------------|
| `blur` | `number` | Blur radius |
| `mode` | `TileMode` | `clamp`, `repeat`, `mirror`, `decal` |

### Offset

```tsx
import { Offset, Rect } from "@shopify/react-native-skia";

<Rect x={0} y={0} width={100} height={100} color="blue">
  <Offset x={10} y={10} />
</Rect>
```

### Morphology

```tsx
import { Morphology, Rect } from "@shopify/react-native-skia";

// Dilate (expand)
<Rect color="green"><Morphology radius={5} operator="dilate" /></Rect>
// Erode (shrink)
<Rect color="green"><Morphology radius={3} operator="erode" /></Rect>
```

### Displacement Map

```tsx
import { DisplacementMap, Rect, useImage } from "@shopify/react-native-skia";

const displacementMap = useImage(require("./displacement.png"));
<Rect x={0} y={0} width={256} height={256} color="red">
  <DisplacementMap channelX="R" channelY="G" scale={50} displacement={displacementMap} />
</Rect>
```

### Shadow (Drop Shadow)

```tsx
import { Shadow, Rect } from "@shopify/react-native-skia";

<Rect x={50} y={50} width={100} height={100} color="white">
  <Shadow dx={10} dy={10} blur={20} color="rgba(0, 0, 0, 0.5)" shadowOnly={false} />
</Rect>
```

| Prop | Type | Description |
|------|------|-------------|
| `dx` | `number` | Horizontal offset |
| `dy` | `number` | Vertical offset |
| `blur` | `number` | Shadow blur |
| `color` | `string` | Shadow color |
| `shadowOnly` | `boolean` | Show only shadow |

### Runtime Shader Filter

```tsx
const shader = `
  uniform shader image;
  uniform float intensity;
  half4 main(float2 coord) {
    half4 color = image.eval(coord);
    return half4(color.rgb * intensity, color.a);
  }
`;
<Rect x={0} y={0} width={256} height={256} color="blue">
  <RuntimeShader source={shader} uniforms={{ intensity: 1.5 }} />
</Rect>
```

## Mask Filters

### BlurMask

```tsx
import { BlurMask, Circle, vec } from "@shopify/react-native-skia";

<Circle c={vec(128, 128)} r={100} color="lightblue">
  <BlurMask blur={20} style="normal" respectCTM={false} />
</Circle>
```

| Style | Effect |
|-------|--------|
| `normal` | Standard blur |
| `solid` | Inner fill + outer blur |
| `outer` | Blur outside only |
| `inner` | Blur inside only |

```tsx
<Circle c={vec(64, 64)} r={50} color="red"><BlurMask blur={15} style="normal" /></Circle>
<Circle c={vec(192, 64)} r={50} color="green"><BlurMask blur={15} style="solid" /></Circle>
<Circle c={vec(64, 192)} r={50} color="blue"><BlurMask blur={15} style="outer" /></Circle>
<Circle c={vec(192, 192)} r={50} color="yellow"><BlurMask blur={15} style="inner" /></Circle>
```

## Color Filters

### MatrixColorFilter

```tsx
import { MatrixColorFilter, Rect } from "@shopify/react-native-skia";

// Grayscale
const grayscale = [
  0.299, 0.587, 0.114, 0, 0,
  0.299, 0.587, 0.114, 0, 0,
  0.299, 0.587, 0.114, 0, 0,
  0, 0, 0, 1, 0,
];
<Rect color="red"><MatrixColorFilter matrix={grayscale} /></Rect>
```

**Common Matrices:**
```tsx
// Sepia
[0.393, 0.769, 0.189, 0, 0, 0.349, 0.686, 0.168, 0, 0, 0.272, 0.534, 0.131, 0, 0, 0, 0, 0, 1, 0]

// Invert
[-1, 0, 0, 0, 1, 0, -1, 0, 0, 1, 0, 0, -1, 0, 1, 0, 0, 0, 1, 0]

// Brightness (1.5x)
[1.5, 0, 0, 0, 0, 0, 1.5, 0, 0, 0, 0, 0, 1.5, 0, 0, 0, 0, 0, 1, 0]

// Contrast (1.5x)
[1.5, 0, 0, 0, -0.25, 0, 1.5, 0, 0, -0.25, 0, 0, 1.5, 0, -0.25, 0, 0, 0, 1, 0]
```

### BlendColor

```tsx
import { BlendColor, Rect } from "@shopify/react-native-skia";
<Rect color="red"><BlendColor color="blue" mode="multiply" /></Rect>
```

Modes: `clear`, `src`, `dst`, `srcOver`, `dstOver`, `srcIn`, `dstIn`, `srcOut`, `dstOut`, `srcATop`, `dstATop`, `xor`, `plus`, `screen`, `overlay`, `darken`, `lighten`, `colorDodge`, `colorBurn`, `hardLight`, `softLight`, `difference`, `exclusion`, `multiply`, `hue`, `saturation`, `color`, `luminosity`.

### LumaColorFilter

```tsx
import { LumaColorFilter, Rect } from "@shopify/react-native-skia";
<Rect color="red"><LumaColorFilter /></Rect>
```

### Gamma Conversion

```tsx
import { LinearToSRGBGamma, SRGBToLinearGamma } from "@shopify/react-native-skia";
<Rect color="red"><LinearToSRGBGamma /></Rect>
<Rect color="red"><SRGBToLinearGamma /></Rect>
```

### Lerp (Linear Interpolation)

```tsx
import { Lerp, MatrixColorFilter } from "@shopify/react-native-skia";
<Rect color="red">
  <Lerp t={0.5}><MatrixColorFilter matrix={grayscale} /></Lerp>
</Rect>
```

## Path Effects

### Discrete Path Effect

```tsx
import { DiscretePathEffect, Path } from "@shopify/react-native-skia";
<Path path="M 0 0 L 100 100" color="blue" style="stroke" strokeWidth={4}>
  <DiscretePathEffect length={10} deviation={2} seed={0} />
</Path>
```

### Dash Path Effect

```tsx
import { DashPathEffect, Path } from "@shopify/react-native-skia";
// 10px on, 5px off
<Path path="M 0 0 L 100 0" style="stroke" strokeWidth={4}>
  <DashPathEffect intervals={[10, 5]} />
</Path>
// Complex: 20 on, 5 off, 5 on, 5 off
<DashPathEffect intervals={[20, 5, 5, 5]} />
```

### Corner Path Effect

```tsx
import { CornerPathEffect, Path } from "@shopify/react-native-skia";
<Path path="M 0 0 L 50 50 L 100 0" style="stroke" strokeWidth={4}>
  <CornerPathEffect r={10} />
</Path>
```

### Path1DPathEffect (Stamp Along Path)

```tsx
const stamp = Skia.Path.Make();
stamp.addCircle(0, 0, 5);
<Path path="M 0 0 L 100 0" style="stroke" strokeWidth={2}>
  <Path1DPathEffect path={stamp} advance={20} phase={0} style="rotate" />
</Path>
```

### Path2DPathEffect (Pattern)

```tsx
const pattern = Skia.Path.Make();
pattern.addCircle(0, 0, 5);
<Rect x={0} y={0} width={100} height={100} color="orange">
  <Path2DPathEffect path={pattern} matrix={[1, 0, 0, 0, 1, 0, 0, 0, 1]} />
</Rect>
```

### Line2DPathEffect

```tsx
<Rect color="cyan">
  <Line2DPathEffect width={2} matrix={[1, 0, 0, 0, 1, 0, 0, 0, 1]} />
</Rect>
```

### SumPathEffect (Combine)

```tsx
<Path path="M 0 0 L 100 0" style="stroke" strokeWidth={4}>
  <SumPathEffect>
    <DashPathEffect intervals={[10, 5]} />
    <DiscretePathEffect length={5} deviation={1} />
  </SumPathEffect>
</Path>
```

## Backdrop Filters

```tsx
import { BackdropBlur, BackdropFilter, Rect, Blur } from "@shopify/react-native-skia";

// Simple backdrop blur
<BackdropBlur blur={10}>
  <Rect x={50} y={50} width={200} height={200} />
</BackdropBlur>

// Custom filter
<BackdropFilter
  filter={<Blur blur={20} mode="mirror" />}
  clip={{ x: 0, y: 0, width: 256, height: 256 }}
>
  <Rect x={50} y={50} width={200} height={200} />
</BackdropFilter>
```

## Mask Component

### Alpha Mask

```tsx
import { Mask, Group, Circle, Rect } from "@shopify/react-native-skia";

<Mask mode="alpha" mask={
  <Group>
    <Circle cx={128} cy={128} r={100} opacity={1} />
    <Circle cx={128} cy={128} r={50} opacity={0} />
  </Group>
}>
  <Rect x={0} y={0} width={256} height={256} color="lightblue" />
</Mask>
```

### Luminance Mask

```tsx
<Mask mode="luminance" mask={
  <Rect x={0} y={0} width={256} height={256}>
    <LinearGradient start={vec(0, 0)} end={vec(256, 256)} colors={["white", "black"]} />
  </Rect>
}>
  <Rect x={0} y={0} width={256} height={256} color="red" />
</Mask>
```

## Combining Filters

```tsx
// Layered
<Rect color="red">
  <Blur blur={5}>
    <MatrixColorFilter matrix={sepia}><Offset x={10} y={10} /></MatrixColorFilter>
  </Blur>
</Rect>

// Group
<Group>
  <BlurMask blur={10} style="normal" />
  <MatrixColorFilter matrix={grayscale} />
  <Circle cx={100} cy={100} r={50} color="blue" />
</Group>
```

## Complete Examples

### Frosted Glass

```tsx
const FrostedGlass = ({ children }) => (
  <Group>
    <BackdropBlur blur={20}>
      <Rect x={0} y={0} width={300} height={200} />
    </BackdropBlur>
    <Rect x={0} y={0} width={300} height={200} color="rgba(255, 255, 255, 0.2)">
      <Shadow dx={0} dy={4} blur={20} color="rgba(0, 0, 0, 0.1)" />
    </Rect>
    {children}
  </Group>
);
```

### Neon Glow

```tsx
const NeonGlow = () => (
  <Group>
    <BlurMask blur={20} style="normal" />
    <Text x={50} y={100} text="NEON" font={font} color="#FF00FF" />
  </Group>
);
```

### Vintage Photo

```tsx
const VintagePhoto = ({ image }) => (
  <Rect x={0} y={0} width={256} height={256}>
    <ImageShader image={image} fit="cover" rect={{ x: 0, y: 0, width: 256, height: 256 }}>
      <Lerp t={0.7}><MatrixColorFilter matrix={sepia} /></Lerp>
      <Blur blur={0.5} />
    </ImageShader>
  </Rect>
);
```

## Performance Best Practices

1. **Order cheapest-first**: `MatrixColorFilter` (cheap) → `Blur` (expensive).
2. **Use BlurMask for simple shapes** — cheaper than image filter `Blur`.
3. **Cache filter objects**: `const blurFilter = useMemo(() => <Blur blur={10} />, [])`.
4. **Remove no-op filters**: `<Blur blur={0} />` wastes cycles.
5. **Limit blur radius**: 5 is fast, 50 is slow.

## Filter Cost Table

| Filter | Cost |
|--------|------|
| `Offset`, `MatrixColorFilter` | Low |
| `BlurMask`, `Shadow`, `BlendColor` | Medium |
| `Blur`, `Morphology` | Medium |
| `BackdropBlur`, `DisplacementMap`, `RuntimeShader` | High |
