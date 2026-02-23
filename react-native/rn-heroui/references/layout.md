# Layout Components

## Table of Contents
- [Card](#card)
- [Surface](#surface)
- [Separator](#separator)

---

## Card

Displays a card container with flexible layout sections for structured content.

Links:
- Source: card/card.tsx
- Styles: card/card.styles.ts

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/card-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/card-docs-dark.mp4"
/>

### Import

```tsx
import { Card } from 'heroui-native';
```

### Anatomy

```tsx
<Card>
  <Card.Header>...</Card.Header>
  <Card.Body>
    <Card.Title>...</Card.Title>
    <Card.Description>...</Card.Description>
  </Card.Body>
  <Card.Footer>...</Card.Footer>
</Card>
```

- **Card**: Main container that extends Surface component. Provides base card structure with configurable surface variants and handles overall layout.
- **Card.Header**: Header section for top-aligned content like icons or badges.
- **Card.Body**: Main content area with flex-1 that expands to fill all available space between Card.Header and Card.Footer.
- **Card.Title**: Title text with foreground color and medium font weight.
- **Card.Description**: Description text with muted color and smaller font size.
- **Card.Footer**: Footer section for bottom-aligned actions like buttons.

### Usage

#### Basic Usage

The Card component creates a container with built-in sections for organized content.

```tsx
<Card>
  <Card.Body>...</Card.Body>
</Card>
```

#### With Title and Description

Combine title and description components for structured text content.

```tsx
<Card>
  <Card.Body>
    <Card.Title>...</Card.Title>
    <Card.Description>...</Card.Description>
  </Card.Body>
</Card>
```

#### With Header and Footer

Add header and footer sections for icons, badges, or actions.

```tsx
<Card>
  <Card.Header>...</Card.Header>
  <Card.Body>...</Card.Body>
  <Card.Footer>...</Card.Footer>
</Card>
```

#### Variants

Control the card's background appearance using different variants.

```tsx
<Card variant="default">...</Card>
<Card variant="secondary">...</Card>
<Card variant="tertiary">...</Card>
<Card variant="transparent">...</Card>
```

#### Horizontal Layout

Create horizontal cards by using flex-row styling.

```tsx
<Card className="flex-row gap-4">
  <Image source={...} className="size-24 rounded-lg" />
</Card>
```

#### Background Image

Use an image as an absolute positioned background.

```tsx
<Card>
  <Image source={...} className="absolute inset-0" />
  <View className="gap-4">...</View>
</Card>
```

### Example

```tsx
import { Button, Card } from 'heroui-native';
import { Ionicons } from '@expo/vector-icons';
import { View } from 'react-native';

export default function CardExample() {
  return (
    <Card>
      <View className="gap-4">
        <Card.Body className="mb-4">
          <View className="gap-1 mb-2">
            <Card.Title className="text-pink-500">$450</Card.Title>
            <Card.Title>Living room Sofa • Collection 2025</Card.Title>
          </View>
          <Card.Description>
            This sofa is perfect for modern tropical spaces, baroque inspired
            spaces.
          </Card.Description>
        </Card.Body>
        <Card.Footer className="gap-3">
          <Button variant="primary">Buy now</Button>
          <Button variant="ghost">
            <Button.Label>Add to cart</Button.Label>
            <Ionicons name="bag-outline" size={16} />
          </Button>
        </Card.Footer>
      </View>
    </Card>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/card.tsx).

### API Reference

#### Card

| prop           | type                                                      | default     | description                                                                               |
| -------------- | --------------------------------------------------------- | ----------- | ----------------------------------------------------------------------------------------- |
| `children`     | `React.ReactNode`                                         | -           | Content to be rendered inside the card                                                    |
| `variant`      | `'default' \| 'secondary' \| 'tertiary' \| 'transparent'` | `'default'` | Visual variant of the card surface                                                        |
| `className`    | `string`                                                  | -           | Additional CSS classes to apply                                                           |
| `animation`    | `"disable-all" \| undefined`                              | `undefined` | Animation configuration. Use `"disable-all"` to disable all animations including children |
| `...ViewProps` | `ViewProps`                                               | -           | All standard React Native View props are supported                                        |

#### Card.Header

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Children elements to be rendered inside the header |
| `className`    | `string`          | -       | Additional CSS classes                             |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported |

#### Card.Body

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Children elements to be rendered inside the body   |
| `className`    | `string`          | -       | Additional CSS classes                             |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported |

#### Card.Footer

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Children elements to be rendered inside the footer |
| `className`    | `string`          | -       | Additional CSS classes                             |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported |

#### Card.Title

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Children elements to be rendered as the title text |
| `className`    | `string`          | -       | Additional CSS classes                             |
| `...TextProps` | `TextProps`       | -       | All standard React Native Text props are supported |

#### Card.Description

| prop           | type              | default | description                                              |
| -------------- | ----------------- | ------- | -------------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Children elements to be rendered as the description text |
| `className`    | `string`          | -       | Additional CSS classes                                   |
| `...TextProps` | `TextProps`       | -       | All standard React Native Text props are supported       |

### Examples from card.md

#### Components

- `Card` - Root container component
- `Card.Header` - Card header section
- `Card.Body` - Card body/content section
- `Card.Footer` - Card footer section
- `Card.Title` - Card title text
- `Card.Description` - Card description text

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'default' \| 'secondary' \| 'tertiary' \| 'transparent'` | `'default'` | Visual style variant |
| `className` | `string` | - | Additional CSS classes |

#### Basic Card

```tsx
import { Card, Button } from 'heroui-native';
import { View } from 'react-native';

function BasicCardExample() {
  return (
    <Card>
      <View className="gap-4">
        <Card.Body className="mb-4">
          <View className="gap-1 mb-2">
            <Card.Title className="text-pink-400">$450</Card.Title>
            <Card.Title>Living room Sofa</Card.Title>
          </View>
          <Card.Description>
            This sofa is perfect for modern tropical spaces, baroque inspired
            spaces.
          </Card.Description>
        </Card.Body>
        <Card.Footer className="gap-3">
          <Button variant="primary">Buy now</Button>
          <Button variant="ghost">
            <Button.Label>Add to cart</Button.Label>
          </Button>
        </Card.Footer>
      </View>
    </Card>
  );
}
```

#### Card with Image

```tsx
import { Card } from 'heroui-native';
import { Image, View } from 'react-native';

function CardWithImageExample() {
  return (
    <View className="flex-row gap-4">
      <Card className="flex-1 aspect-[1/1.3]">
        <View className="flex-1 gap-4">
          <Card.Header>
            <Image
              source={{
                uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/demo1.jpg',
              }}
              className="h-16 aspect-square rounded-xl"
            />
          </Card.Header>
          <Card.Body className="flex-1">
            <Card.Title>Indie Hackers</Card.Title>
            <Card.Description className="text-sm">
              148 members
            </Card.Description>
          </Card.Body>
          <Card.Footer className="flex-row items-center gap-2">
            <View className="size-3 rounded-full bg-rose-400" />
            <Text className="text-sm font-medium text-foreground">
              @indiehackers
            </Text>
          </Card.Footer>
        </View>
      </Card>
    </View>
  );
}
```

#### Horizontal Card

```tsx
import { Card } from 'heroui-native';
import { Image, View, Pressable } from 'react-native';

function HorizontalCardExample() {
  return (
    <View className="w-full gap-4">
      <Card className="flex-row gap-4 p-4" variant="tertiary">
        <Image
          source={{
            uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/avocado.jpeg',
          }}
          className="h-28 aspect-square rounded-2xl"
          resizeMode="cover"
        />
        <View className="flex-1 gap-4">
          <Card.Body className="flex-1">
            <Card.Title>Avocado Hackathon</Card.Title>
            <Card.Description numberOfLines={2} className="text-sm">
              Today, 6:30 PM
            </Card.Description>
          </Card.Body>
          <Card.Footer>
            <Pressable className="flex-row items-center gap-1">
              <Text className="text-sm font-medium text-accent">
                View Details
              </Text>
            </Pressable>
          </Card.Footer>
        </View>
      </Card>
    </View>
  );
}
```

#### Background Image Card

```tsx
import { LinearGradient } from 'expo-linear-gradient';
import { Card, Button } from 'heroui-native';
import { Image, View, StyleSheet } from 'react-native';

function BackgroundImageCardExample() {
  return (
    <Card className="w-full aspect-square">
      <Image
        source={{
          uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/neo2.jpeg',
        }}
        className="absolute inset-0"
        resizeMode="cover"
      />
      <LinearGradient
        colors={['rgba(0,0,0,0.1)', 'rgba(0,0,0,0.5)']}
        style={StyleSheet.absoluteFill}
      />
      <View className="flex-1 gap-4">
        <Card.Body className="flex-1">
          <Card.Title className="text-base text-zinc-50 uppercase mb-0.5">
            Neo
          </Card.Title>
          <Card.Description className="text-zinc-50 font-medium text-base">
            Home robot
          </Card.Description>
        </Card.Body>
        <Card.Footer className="gap-3">
          <View className="flex-row items-center justify-between">
            <View>
              <Text className="text-base text-white">
                Available soon
              </Text>
              <Text className="text-base text-zinc-300">
                Get notified
              </Text>
            </View>
            <Button size="sm" className="bg-white" feedbackVariant="scale">
              <Button.Label className="text-black">Notify me</Button.Label>
            </Button>
          </View>
        </Card.Footer>
      </View>
    </Card>
  );
}
```

#### Card Variants

```tsx
import { Card } from 'heroui-native';

function CardVariantsExample() {
  return (
    <View className="gap-2 w-full px-5">
      <Card variant="default">
        <Text className="text-foreground font-medium">Default</Text>
        <Text className="text-muted">
          Standard card appearance (surface-secondary).
        </Text>
      </Card>
      <Card variant="secondary">
        <Text className="text-foreground font-medium">Secondary</Text>
        <Text className="text-muted">
          Medium prominence (surface-tertiary).
        </Text>
      </Card>
      <Card variant="tertiary">
        <Text className="text-foreground font-medium">Tertiary</Text>
        <Text className="text-muted">
          Higher prominence (surface-tertiary).
        </Text>
      </Card>
      <Card variant="transparent" className="border border-border shadow-none">
        <Text className="text-foreground font-medium">Transparent</Text>
        <Text className="text-muted">
          Minimal prominence with transparent background.
        </Text>
      </Card>
    </View>
  );
}
```

#### Complete Example Code

```tsx
import { Ionicons } from '@expo/vector-icons';
import { LinearGradient } from 'expo-linear-gradient';
import { Button, Card, cn, type CardRootProps } from 'heroui-native';
import { Image, Pressable, StyleSheet, View, Text } from 'react-native';

const BasicCardContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <Card>
        <View className="gap-4">
          <Card.Body className="mb-4">
            <View className="gap-1 mb-2">
              <Card.Title className="text-pink-400">$450</Card.Title>
              <Card.Title>Living room Sofa</Card.Title>
            </View>
            <Card.Description>
              This sofa is perfect for modern tropical spaces.
            </Card.Description>
          </Card.Body>
          <Card.Footer className="gap-3">
            <Button variant="primary">Buy now</Button>
            <Button variant="ghost">
              <Button.Label>Add to cart</Button.Label>
            </Button>
          </Card.Footer>
        </View>
      </Card>
    </View>
  );
};

const CardWithImageContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <View className="flex-row gap-4">
        <Card className="flex-1 aspect-[1/1.3]">
          <View className="flex-1 gap-4">
            <Card.Header>
              <Image
                source={{
                  uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/demo1.jpg',
                }}
                className="h-16 aspect-square rounded-xl"
              />
            </Card.Header>
            <Card.Body className="flex-1">
              <Card.Title>Indie Hackers</Card.Title>
              <Card.Description className="text-sm">
                148 members
              </Card.Description>
            </Card.Body>
            <Card.Footer className="flex-row items-center gap-2">
              <View className="size-3 rounded-full bg-rose-400" />
              <Text className="text-sm font-medium text-foreground">
                @indiehackers
              </Text>
            </Card.Footer>
          </View>
        </Card>
      </View>
    </View>
  );
};

const HorizontalCardWithImageContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <View className="w-full gap-4">
        <Card className="flex-row gap-4 p-4" variant="tertiary">
          <Image
            source={{
              uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/avocado.jpeg',
            }}
            className="h-28 aspect-square rounded-2xl"
            resizeMode="cover"
          />
          <View className="flex-1 gap-4">
            <Card.Body className="flex-1">
              <Card.Title>Avocado Hackathon</Card.Title>
              <Card.Description numberOfLines={2} className="text-sm">
                Today, 6:30 PM
              </Card.Description>
            </Card.Body>
            <Card.Footer>
              <Pressable className="flex-row items-center gap-1">
                <Text className="text-sm font-medium text-accent">
                  View Details
                </Text>
              </Pressable>
            </Card.Footer>
          </View>
        </Card>
      </View>
    </View>
  );
};

export default function CardExample() {
  return <BasicCardContent />;
}
```

---

## Surface

Container component that provides elevation and background styling.

Links:
- Source: surface/surface.tsx
- Styles: surface/surface.styles.ts

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/surface-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/surface-docs-dark.mp4"
/>

### Import

```tsx
import { Surface } from 'heroui-native';
```

### Anatomy

The Surface component is a container that provides elevation and background styling. It accepts children and can be customized with variants and styling props.

```tsx
<Surface>...</Surface>
```

- **Surface**: Main container component that provides consistent padding, background styling, and elevation through variants.

### Usage

#### Basic Usage

The Surface component creates a container with consistent padding and styling.

```tsx
<Surface>...</Surface>
```

#### Variants

Control the visual appearance with different surface levels.

```tsx
<Surface variant="default">
  ...
</Surface>

<Surface variant="secondary">
  ...
</Surface>

<Surface variant="tertiary">
  ...
</Surface>
```

#### Nested Surfaces

Create visual hierarchy by nesting surfaces with different variants.

```tsx
<Surface variant="default">
  ...
  <Surface variant="secondary">
    ...
    <Surface variant="tertiary">...</Surface>
  </Surface>
</Surface>
```

#### Custom Styling

Apply custom styles using className or style props.

```tsx
<Surface className="bg-accent-soft">
  ...
</Surface>

<Surface variant="tertiary" className="p-0">
  ...
</Surface>
```

#### Disable All Animations

Disable all animations including children by using the `"disable-all"` value for the `animation` prop.

```tsx
{
  /* Disable all animations including children */
}
<Surface animation="disable-all">No Animations</Surface>;
```

### Example

```tsx
import { Surface } from 'heroui-native';
import { Text, View } from 'react-native';

export default function SurfaceExample() {
  return (
    <View className="gap-4">
      <Surface variant="default" className="gap-2">
        <AppText className="text-foreground">Surface Content</AppText>
        <AppText className="text-muted">
          This is a default surface variant. It uses bg-surface styling.
        </AppText>
      </Surface>

      <Surface variant="secondary" className="gap-2">
        <AppText className="text-foreground">Surface Content</AppText>
        <AppText className="text-muted">
          This is a secondary surface variant. It uses bg-surface-secondary
          styling.
        </AppText>
      </Surface>

      <Surface variant="tertiary" className="gap-2">
        <AppText className="text-foreground">Surface Content</AppText>
        <AppText className="text-muted">
          This is a tertiary surface variant. It uses bg-surface-tertiary
          styling.
        </AppText>
      </Surface>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/surface.tsx).

### API Reference

#### Surface

| prop           | type                                                      | default     | description                                                                               |
| -------------- | --------------------------------------------------------- | ----------- | ----------------------------------------------------------------------------------------- |
| `variant`      | `'default' \| 'secondary' \| 'tertiary' \| 'transparent'` | `'default'` | Visual variant controlling background color and border                                    |
| `children`     | `React.ReactNode`                                         | -           | Content to be rendered inside the surface                                                 |
| `className`    | `string`                                                  | -           | Additional CSS classes to apply                                                           |
| `animation`    | `"disable-all" \| undefined`                              | `undefined` | Animation configuration. Use `"disable-all"` to disable all animations including children |
| `...ViewProps` | `ViewProps`                                               | -           | All standard React Native View props are supported                                        |

### Examples from surface.md

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'default' \| 'secondary' \| 'tertiary'` | `'default'` | Surface variant |
| `className` | `string` | - | Additional CSS classes |

#### Basic Surface

```tsx
import { Surface } from 'heroui-native';
import { View, Text } from 'react-native';

function BasicSurfaceExample() {
  return (
    <View className="p-5">
      <Surface className="p-4">
        <Text className="text-foreground">Content on surface</Text>
      </Surface>
    </View>
  );
}
```

#### Surface Variants

```tsx
import { Surface } from 'heroui-native';
import { View, Text } from 'react-native';

function SurfaceVariantsExample() {
  return (
    <View className="p-5 gap-4">
      <Surface variant="default" className="p-4">
        <Text className="text-foreground">Default Surface</Text>
      </Surface>
      <Surface variant="secondary" className="p-4">
        <Text className="text-foreground">Secondary Surface</Text>
      </Surface>
      <Surface variant="tertiary" className="p-4">
        <Text className="text-foreground">Tertiary Surface</Text>
      </Surface>
    </View>
  );
}
```

#### Complete Example Code

```tsx
import { Surface } from 'heroui-native';
import { View, Text } from 'react-native';

export default function SurfaceExample() {
  return (
    <View className="flex-1 p-5 justify-center">
      <Surface className="p-4">
        <Text className="text-foreground">Content on surface</Text>
      </Surface>
    </View>
  );
}
```

---

## Separator

A simple line to separate content visually.

Links:
- Source: separator/separator.tsx
- Styles: separator/separator.styles.ts

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/separator-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/separator-docs-dark.mp4"
/>

### Import

```tsx
import { Separator } from "heroui-native";
```

### Anatomy

```tsx
<Separator />
```

- **Separator**: A simple line component that separates content visually. Can be oriented horizontally or vertically, with customizable thickness and variant styles.

### Usage

#### Basic Usage

The Separator component creates a visual separation between content sections.

```tsx
<Separator />
```

#### Orientation

Control the direction of the separator with the `orientation` prop.

```tsx
<View>
  <Text>Horizontal separator</Text>
  <Separator orientation="horizontal" />
  <Text>Content below</Text>
</View>

<View className="h-24 flex-row">
  <Text>Left</Text>
  <Separator orientation="vertical" />
  <Text>Right</Text>
</View>
```

#### Variants

Choose between thin and thick variants for different visual emphasis.

```tsx
<Separator variant="thin" />
<Separator variant="thick" />
```

#### Custom Thickness

Set a specific thickness value for precise control.

```tsx
<Separator thickness={1} />
<Separator thickness={5} />
<Separator thickness={10} />
```

### Example

```tsx
import { Separator, Surface } from 'heroui-native';
import { Text, View } from 'react-native';

export default function SeparatorExample() {
  return (
    <Surface variant="secondary" className="px-6 py-7">
      <Text className="text-base font-medium text-foreground">
        HeroUI Native
      </Text>
      <Text className="text-sm text-muted">
        A modern React Native component library.
      </Text>
      <Separator className="my-4" />
      <View className="flex-row items-center h-5">
        <Text className="text-sm text-foreground">Components</Text>
        <Separator orientation="vertical" className="mx-3" />
        <Text className="text-sm text-foreground">Themes</Text>
        <Separator orientation="vertical" className="mx-3" />
        <Text className="text-sm text-foreground">Examples</Text>
      </View>
    </Surface>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/separator.tsx).

### API Reference

#### Separator

| prop           | type                         | default        | description                                                                                  |
| -------------- | ---------------------------- | -------------- | -------------------------------------------------------------------------------------------- |
| `variant`      | `'thin' \| 'thick'`          | `'thin'`       | Variant style of the separator                                                               |
| `orientation`  | `'horizontal' \| 'vertical'` | `'horizontal'` | Orientation of the separator                                                                 |
| `thickness`    | `number`                     | `undefined`    | Custom thickness in pixels. Controls height for horizontal or width for vertical orientation |
| `className`    | `string`                     | `undefined`    | Additional CSS classes to apply                                                              |
| `...ViewProps` | `ViewProps`                  | -              | All standard React Native View props are supported                                           |

### Examples from separator.md

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `orientation` | `'horizontal' \| 'vertical'` | `'horizontal'` | Separator orientation |
| `className` | `string` | - | Additional CSS classes |

#### Horizontal Separator

```tsx
import { Separator } from 'heroui-native';
import { View, Text } from 'react-native';

function HorizontalSeparatorExample() {
  return (
    <View className="p-5">
      <Text className="text-foreground">Section 1</Text>
      <Separator className="my-4" />
      <Text className="text-foreground">Section 2</Text>
    </View>
  );
}
```

#### Vertical Separator

```tsx
import { Separator } from 'heroui-native';
import { View, Text } from 'react-native';

function VerticalSeparatorExample() {
  return (
    <View className="flex-row items-center p-5">
      <Text className="text-foreground">Left</Text>
      <Separator orientation="vertical" className="mx-4 h-6" />
      <Text className="text-foreground">Right</Text>
    </View>
  );
}
```

#### Complete Example Code

```tsx
import { Separator } from 'heroui-native';
import { View, Text } from 'react-native';

export default function SeparatorExample() {
  return (
    <View className="flex-1 p-5">
      <Text className="text-foreground">Section 1</Text>
      <Separator className="my-4" />
      <Text className="text-foreground">Section 2</Text>
      <Separator className="my-4" />
      <Text className="text-foreground">Section 3</Text>
    </View>
  );
}
```
