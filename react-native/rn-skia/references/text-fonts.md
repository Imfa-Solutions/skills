# Text & Fonts

## Simple Text

```tsx
import { Canvas, Text, useFont, Fill } from "@shopify/react-native-skia";

const SimpleTextExample = () => {
  const fontSize = 32;
  const font = useFont(require("./Roboto-Regular.ttf"), fontSize);
  if (!font) return null;

  return (
    <Canvas style={{ flex: 1 }}>
      <Fill color="white" />
      <Text x={0} y={fontSize} text="Hello World" font={font} />
    </Canvas>
  );
};
```

**Important:** `y` is the **baseline**, not the top.

| Prop | Type | Description |
|------|------|-------------|
| `text` | `string` | Text to draw |
| `font` | `SkFont` | Font to use |
| `x` | `number` | Left position (default: 0) |
| `y` | `number` | Baseline position (default: 0) |

## Loading Fonts

### useFont Hook

```tsx
import { useFont } from "@shopify/react-native-skia";

const font = useFont(require("./fonts/Roboto-Regular.ttf"), 32);
const largeFont = useFont(require("./fonts/Roboto-Bold.ttf"), 48);

if (!font) return <Loading />;
```

### System Fonts

```tsx
const fontMgr = Skia.FontMgr.System();
const families = [];
for (let i = 0; i < fontMgr.countFamilies(); i++) {
  families.push(fontMgr.getFamilyName(i));
}
const typeface = fontMgr.matchFamilyStyle("Arial");
const font = Skia.Font(typeface, 32);
```

### matchFont

```tsx
import { matchFont } from "@shopify/react-native-skia";
const font = matchFont(32, { family: "Roboto", weight: "bold", style: "italic" });
```

## Text Styling

```tsx
<Text x={10} y={50} text="Colored" font={font} color="red" opacity={0.8} />

// With Paint (outlined text)
<Paint color="blue" strokeWidth={2} style="stroke">
  <Text x={10} y={50} text="Outlined Text" font={font} />
</Paint>
```

## Paragraph Component

Rich text with multiple styles:

```tsx
import { Paragraph, Skia } from "@shopify/react-native-skia";

const paragraph = Skia.ParagraphBuilder.Make()
  .pushStyle({ color: Skia.Color("black"), fontSize: 24 })
  .addText("Hello ")
  .pushStyle({ color: Skia.Color("red"), fontWeight: "bold" })
  .addText("World!")
  .pop()
  .addText(" This is ")
  .pushStyle({ color: Skia.Color("blue"), fontStyle: "italic" })
  .addText("rich text")
  .pop()
  .build();

<Paragraph paragraph={paragraph} x={10} y={10} width={300} />
```

### Paragraph Styles

```tsx
const style = {
  color: Skia.Color("black"),
  fontSize: 16,
  fontFamily: "Roboto",
  fontWeight: "normal",       // "normal", "bold"
  fontStyle: "normal",        // "normal", "italic"
  letterSpacing: 0,
  wordSpacing: 0,
  heightMultiplier: 1.2,      // Line height
  decoration: "underline",    // "none", "underline", "lineThrough"
  decorationColor: Skia.Color("red"),
  decorationStyle: "solid",   // "solid", "double", "dotted", "dashed", "wavy"
};
```

### Text Alignment

```tsx
import { TextAlign } from "@shopify/react-native-skia";

const paragraph = Skia.ParagraphBuilder.Make({ textAlign: TextAlign.Center })
  .addText("Centered text")
  .build();
```

Options: `TextAlign.Left`, `.Right`, `.Center`, `.Justify`, `.Start`, `.End`.

## TextPath

Draw text along a path:

```tsx
import { TextPath, Skia } from "@shopify/react-native-skia";

const path = Skia.Path.Make();
path.addCircle(150, 150, 100);

<TextPath path={path} text="Text along a circular path!" font={font} color="blue" />
```

## TextBlob

High-performance rendering of many text elements:

```tsx
import { TextBlob, Skia } from "@shopify/react-native-skia";

const blob = Skia.TextBlob.MakeFromText("Hello", font);
<TextBlob blob={blob} x={10} y={50} color="black" />
```

## Glyphs

Direct glyph rendering for maximum control:

```tsx
import { Glyphs, Skia } from "@shopify/react-native-skia";

const glyphs = font.getGlyphIDs("Hello");
const positions = font.getGlyphPositions(glyphs, { x: 10, y: 50 });
<Glyphs font={font} glyphs={glyphs} positions={positions} color="red" />
```

## Font Metrics

```tsx
if (font) {
  const metrics = font.getMetrics();
  console.log(metrics.ascent);   // Distance baseline → top
  console.log(metrics.descent);  // Distance baseline → bottom
  // Line height = ascent - descent

  const width = font.getTextWidth("Hello World");
  const bounds = font.getGlyphBounds(glyphs);
}
```

## Text Measurement

```tsx
const paragraph = Skia.ParagraphBuilder.Make().addText("Measure me").build();
paragraph.layout(300); // Max width

const height = paragraph.getHeight();
const width = paragraph.getMaxWidth();
const longestLine = paragraph.getLongestLine();
const lineMetrics = paragraph.getLineMetrics();
```

## Animated Text

### Position

```tsx
const x = useSharedValue(0);
useEffect(() => { x.value = withSpring(100); }, []);
<Text x={x} y={50} text="Animated" font={font} />
```

### Font Size

```tsx
const size = useSharedValue(16);
const baseFont = useFont(require("./font.ttf"), 1);
const font = useDerivedValue(() => {
  if (!baseFont) return null;
  return Skia.Font(baseFont.typeface(), size.value);
});
```

## Complete Examples

### Scrolling Marquee

```tsx
const Marquee = ({ text, speed = 50 }) => {
  const font = useFont(require("./Roboto-Regular.ttf"), 32);
  const x = useSharedValue(0);
  const textWidth = useMemo(() => (font ? font.getTextWidth(text) : 0), [font, text]);

  useEffect(() => {
    x.value = withRepeat(
      withTiming(-textWidth, { duration: (textWidth / speed) * 1000, easing: Easing.linear }),
      -1
    );
  }, [textWidth, speed]);

  if (!font) return null;
  return (
    <Canvas style={{ width: 300, height: 50 }}>
      <Group clip={{ x: 0, y: 0, width: 300, height: 50 }}>
        <Text x={x} y={32} text={text} font={font} />
      </Group>
    </Canvas>
  );
};
```

### Typewriter Effect

```tsx
const Typewriter = ({ text, speed = 100 }) => {
  const font = useFont(require("./Roboto-Regular.ttf"), 24);
  const progress = useSharedValue(0);
  const displayedText = useDerivedValue(() => text.slice(0, Math.floor(progress.value)));

  useEffect(() => {
    progress.value = withTiming(text.length, { duration: text.length * speed });
  }, [text]);

  if (!font) return null;
  return (
    <Canvas style={{ height: 50 }}>
      <Text x={10} y={30} text={displayedText} font={font} />
    </Canvas>
  );
};
```

### Text with Shadow

```tsx
const ShadowText = ({ text }) => {
  const font = useFont(require("./Roboto-Bold.ttf"), 48);
  if (!font) return null;
  return (
    <Canvas style={{ height: 80 }}>
      <Group>
        <Shadow dx={4} dy={4} blur={8} color="rgba(0,0,0,0.3)" />
        <Text x={10} y={60} text={text} font={font} color="white" />
      </Group>
    </Canvas>
  );
};
```

## Best Practices

1. **Load fonts at component level** with `useFont` — never inside render.
2. **Handle loading state** — `if (!font) return null` or show loading indicator.
3. **Use Paragraph for rich text** — not multiple Text components.
4. **Cache TextBlobs** — `useMemo(() => font && Skia.TextBlob.MakeFromText(...), [font])`.

## Performance Table

| Component | Performance |
|-----------|-------------|
| `Text` | Excellent |
| `TextBlob`, `Glyphs` | Excellent |
| `Paragraph` | Good |
| `TextPath` | Good |
