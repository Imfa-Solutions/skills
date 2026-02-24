# Shapes & Drawing

## Basic Shapes

### Circle

```tsx
import { Circle, vec } from "@shopify/react-native-skia";

<Circle cx={100} cy={100} r={50} color="red" />
<Circle c={vec(100, 100)} r={50} color="blue" />
<Circle cx={100} cy={100} r={50} color="green" opacity={0.5} />
```

| Prop | Type | Description |
|------|------|-------------|
| `cx` / `c.x` | `number` | Center X |
| `cy` / `c.y` | `number` | Center Y |
| `r` | `number` | Radius |
| `color` | `string` | Fill color |
| `opacity` | `number` | Opacity (0-1) |

### Rect

```tsx
import { Rect, Skia } from "@shopify/react-native-skia";

<Rect x={10} y={10} width={100} height={50} color="blue" />
<Rect rect={{ x: 10, y: 10, width: 100, height: 50 }} color="green" />
<Rect rect={Skia.XYWHRect(10, 10, 100, 50)} color="red" />
```

### RoundedRect

```tsx
import { RoundedRect } from "@shopify/react-native-skia";

// Uniform radius
<RoundedRect x={10} y={10} width={100} height={50} r={10} color="purple" />

// Per-corner radii
<RoundedRect x={10} y={10} width={100} height={50}
  topLeft={10} topRight={20} bottomRight={10} bottomLeft={20} color="orange" />
```

### Oval, Line, Points

```tsx
import { Oval, Line, Points, vec } from "@shopify/react-native-skia";

<Oval x={10} y={10} width={100} height={50} color="pink" />

<Line p1={vec(0, 0)} p2={vec(100, 100)} color="black" strokeWidth={2} />

<Points
  points={[{ x: 10, y: 10 }, { x: 20, y: 20 }, { x: 30, y: 30 }]}
  mode="points" // "points" | "lines" | "polygon"
  color="red" strokeWidth={4}
/>
```

Line props: `p1`, `p2`, `strokeWidth`, `strokeCap` (`butt`/`round`/`square`).

### Fill

```tsx
import { Fill, LinearGradient, vec } from "@shopify/react-native-skia";

<Fill color="white" />
<Fill color="black" opacity={0.5} />
<Fill>
  <LinearGradient start={vec(0, 0)} end={vec(256, 256)} colors={["blue", "yellow"]} />
</Fill>
```

## Path Component

Most powerful shape — supports SVG path notation.

### SVG Notation

```tsx
import { Path } from "@shopify/react-native-skia";

// Star shape
<Path
  path="M 128 0 L 168 80 L 256 93 L 192 155 L 207 244 L 128 202 L 49 244 L 64 155 L 0 93 L 88 80 Z"
  color="lightblue"
/>

// Stroke
<Path path="M 0 0 L 100 100" style="stroke" strokeWidth={4} strokeCap="round" color="red" />
```

### Path Object

```tsx
const path = Skia.Path.Make();
path.moveTo(128, 0);
path.lineTo(168, 80);
path.lineTo(256, 93);
path.close();
<Path path={path} color="lightblue" />
```

| Prop | Type | Description |
|------|------|-------------|
| `path` | `string \| SkPath` | SVG string or SkPath |
| `start` | `number` | Trim start (0-1) |
| `end` | `number` | Trim end (0-1) |
| `style` | `"fill" \| "stroke"` | Drawing style |
| `strokeWidth` | `number` | Stroke thickness |
| `strokeCap` | `StrokeCap` | Line end style |
| `strokeJoin` | `StrokeJoin` | Line join style |

### Path Trimming Animation

```tsx
const progress = useSharedValue(0);
useEffect(() => { progress.value = withTiming(1, { duration: 2000 }); }, []);

<Path path="M 0 0 L 100 100 L 200 0" style="stroke" strokeWidth={4}
  start={0} end={progress} color="blue" />
```

## Atlas (Sprite Batching)

Efficiently render many instances of the same texture — **single draw call**.

```tsx
import { Atlas, Skia, drawAsImage, rect } from "@shopify/react-native-skia";

const size = { width: 25, height: 11.25 };
const atlasImage = drawAsImage(
  <Group><Rect rect={rect(0, 0, size.width, size.height)} color="cyan" /></Group>,
  size
);

const sprites = [rect(0, 0, size.width, size.height)];
const transforms = [
  Skia.RSXform(1, 0, 50, 50),
  Skia.RSXform(1, 0, 100, 100),
  Skia.RSXform(1, 0, 150, 150),
];

<Atlas image={atlasImage} sprites={sprites} transforms={transforms} />
```

**RSXform:**
```tsx
Skia.RSXform(1, 0, 0, 0)           // Identity
Skia.RSXform(2, 0, 50, 100)        // Scale 2x + translate
Skia.RSXform(Math.cos(r), Math.sin(r), 50, 100) // Rotate
Skia.RSXformFromRadians(2, r, 0, 0, 25, 25)     // Scale + rotate with pivot
```

## Vertices

```tsx
import { Vertices } from "@shopify/react-native-skia";

<Vertices
  vertices={[{ x: 0, y: 0 }, { x: 100, y: 0 }, { x: 50, y: 100 }]}
  colors={["red", "green", "blue"]}
  indices={[0, 1, 2]}
/>
```

## Patch (Coons Patch)

```tsx
import { Patch, vec } from "@shopify/react-native-skia";

<Patch patch={patchData} colors={["red", "green", "blue", "yellow"]} />
```

## DiffRect (Difference Rectangle)

Rectangle with a hole:
```tsx
import { DiffRect, rect, rrect } from "@shopify/react-native-skia";

const outer = rrect(rect(0, 0, 200, 200), 10, 10);
const inner = rrect(rect(50, 50, 100, 100), 5, 5);
<DiffRect outer={outer} inner={inner} color="purple" />
```

## Group Component

Applies properties to all children.

### Transforms

```tsx
<Group transform={[{ translateX: 50 }, { translateY: 50 }]}>...</Group>
<Group transform={[{ scale: 2 }]}>...</Group>
<Group transform={[{ rotate: Math.PI / 4 }]} origin={{ x: 50, y: 50 }}>...</Group>
<Group transform={[{ translateX: 100 }, { scale: 1.5 }, { rotate: Math.PI / 6 }]}>...</Group>
```

### Blend Modes

```tsx
<Group blendMode="multiply">
  <Circle cx={50} cy={50} r={50} color="cyan" />
  <Circle cx={100} cy={50} r={50} color="magenta" />
</Group>
```

Modes: `srcOver`, `srcIn`, `srcOut`, `srcATop`, `dstOver`, `dstIn`, `dstOut`, `dstATop`, `xor`, `plus`, `modulate`, `screen`, `overlay`, `darken`, `lighten`, `colorDodge`, `colorBurn`, `hardLight`, `softLight`, `difference`, `exclusion`, `multiply`, `hue`, `saturation`, `color`, `luminosity`.

### Clipping

```tsx
// Clip to rectangle
<Group clip={{ x: 0, y: 0, width: 100, height: 100 }}>
  <Circle cx={100} cy={100} r={80} color="orange" />
</Group>

// Clip to path
const clipPath = Skia.Path.Make();
clipPath.addCircle(100, 100, 80);
<Group clip={clipPath}>
  <Rect x={0} y={0} width={200} height={200} color="pink" />
</Group>
```

### Layer Effects

```tsx
<Group>
  <BlurMask blur={10} style="normal" />
  <Circle cx={100} cy={100} r={50} color="red" />
</Group>
```

## FitBox

Scale content to fit a target rectangle:
```tsx
import { FitBox, rect } from "@shopify/react-native-skia";

<FitBox src={rect(0, 0, 100, 100)} dst={rect(0, 0, 200, 200)}>
  <Circle cx={50} cy={50} r={50} color="blue" />
</FitBox>
```

Fit modes: `contain`, `cover`, `fill`, `fitHeight`, `fitWidth`, `scaleDown`, `none`.

## Box with Shadow

```tsx
import { Box, rect, rrect } from "@shopify/react-native-skia";

<Box
  box={rrect(rect(10, 10, 100, 100), 10, 10)}
  paint={{ color: "white" }}
  shadow={{ color: "rgba(0,0,0,0.2)", blur: 10, offset: { x: 0, y: 5 } }}
/>
```

## Best Practices

1. **Use simple shapes when possible** — `<Circle>` not `<Path path="M 150 100 A...">`.
2. **Reuse Path objects** — `useMemo(() => { const p = Skia.Path.Make(); ... return p; }, [])`.
3. **Batch with Atlas** — For 10+ similar shapes, use Atlas instead of individual components.
4. **Group shared properties** — Apply `opacity`, `transform` on Group, not each child.

## Performance Table

| Shape | Performance |
|-------|-------------|
| `Circle`, `Rect`, `RoundedRect`, `Oval`, `Line`, `Points` | Excellent |
| `Atlas`, `DiffRect` | Excellent |
| `Path` (complex), `Vertices`, `Patch` | Good |
