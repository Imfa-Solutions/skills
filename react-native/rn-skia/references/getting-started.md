# Getting Started

## Overview

React Native Skia brings the **Skia Graphics Library** to React Native — the same engine powering Chrome, Android, Flutter, and Firefox. GPU-accelerated, cross-platform (iOS, Android, Web, macOS, tvOS, visionOS), declarative React API.

## Version Compatibility

| Requirement    | Minimum Version                          |
|---------------|------------------------------------------|
| React Native  | >= 0.79                                  |
| React         | >= 19                                    |
| iOS           | >= 14                                    |
| Android API   | >= 21 (>= 26 for video support)         |

For RN <= 0.78 / React <= 18: use `@shopify/react-native-skia` v1.12.4 or below.

## Installation

### npm / yarn

```bash
yarn add @shopify/react-native-skia
npm install @shopify/react-native-skia
```

### Expo

```bash
yarn create expo-app my-app -e with-skia
npx create-expo-app my-app -e with-skia
```

### Postinstall Script

Downloads prebuilt Skia binaries — **must run**.

```bash
# If issues:
npm rebuild @shopify/react-native-skia
yarn rebuild @shopify/react-native-skia
```

**Bun:**
```bash
bun add --trust @shopify/react-native-skia
```
Or add to `package.json`: `"trustedDependencies": ["@shopify/react-native-skia"]`

**pnpm v10+:** Add to `package.json`:
```json
{ "pnpm": { "onlyBuiltDependencies": ["@shopify/react-native-skia"] } }
```

**Yarn Berry (v2+):**
```bash
yarn config set enableScripts true
```
Or in `.yarnrc.yml`: `enableScripts: true`

## Platform Setup

### iOS
```bash
cd ios && pod install
```
Requirements: iOS 14.0+, Xcode 14.0+.

### Android
Requirements: API 21+, API 26+ for video. ProGuard:
```proguard
-keep class com.shopify.reactnative.skia.** { *; }
```

### Web
Uses CanvasKit (WASM). See official web setup guide.

## Hello World

```tsx
import React from "react";
import { Canvas, Circle, Group } from "@shopify/react-native-skia";

const App = () => {
  const width = 256;
  const height = 256;
  const r = width * 0.33;

  return (
    <Canvas style={{ width, height }}>
      <Group blendMode="multiply">
        <Circle cx={r} cy={r} r={r} color="cyan" />
        <Circle cx={width - r} cy={r} r={r} color="magenta" />
        <Circle cx={width / 2} cy={width - r} r={r} color="yellow" />
      </Group>
    </Canvas>
  );
};
```

## Canvas Component

The root of all Skia drawing.

```tsx
<Canvas style={{ flex: 1 }}>
  {/* Drawing components */}
</Canvas>
```

| Prop | Type | Description |
|------|------|-------------|
| `style` | `ViewStyle` | React Native view style |
| `ref` | `Ref<SkiaView>` | Reference to SkiaView |
| `onSize` | `SharedValue<Size>` | Reanimated value for size changes |
| `androidWarmup` | `boolean` | Draw first frame on Android compositor |

### Getting Canvas Size

**On UI Thread (recommended for animations):**
```tsx
const size = useSharedValue({ width: 0, height: 0 });
const rect = useDerivedValue(() => ({
  x: 0, y: 0, width: size.value.width, height: size.value.height
}));
<Canvas style={{ flex: 1 }} onSize={size}>
  <Rect color="cyan" rect={rect} />
</Canvas>
```

**On JS Thread:**
```tsx
const { ref, size: { width, height } } = useCanvasSize();
<Canvas style={{ flex: 1 }} ref={ref}>
  <Rect color="cyan" rect={{ x: 0, y: 0, width, height }} />
</Canvas>
```

## The Skia Object

Low-level API access:

```tsx
import { Skia } from "@shopify/react-native-skia";

const paint = Skia.Paint();
paint.setColor(Skia.Color("red"));

const path = Skia.Path.Make();
path.moveTo(0, 0);
path.lineTo(100, 100);

const color = Skia.Color("#FF0000");
const colorAlpha = Skia.Color("rgba(255, 0, 0, 0.5)");
```

## Project Structure

```
src/
├── components/skia/      # Reusable canvas wrappers, animated shapes
├── hooks/                # useCanvasAnimation, useSkiaValues
├── utils/                # colors, paths, transforms
├── constants/skia.ts     # Skia constants
└── types/skia.ts         # TypeScript types
```

## Troubleshooting

**"Missing Skia libraries"** — Run `npm rebuild @shopify/react-native-skia`.

**iOS Build Fails** — `sudo gem install cocoapods && cd ios && pod deintegrate && pod install`.

**Android Build Fails** — Ensure NDK installed, `minSdkVersion >= 21`.

**Canvas is blank** — Check Canvas has explicit dimensions; verify components are inside Canvas.

**Animations not working** — Ensure Reanimated configured, worklets marked with `"worklet"`.

## Bundle Size

- iOS: ~15-20 MB
- Android: ~10-15 MB
- Web: ~5-8 MB (CanvasKit WASM)

Optimize with dynamic imports, lazy loading complex drawings, `androidWarmup` for static content.
