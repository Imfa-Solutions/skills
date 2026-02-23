# Buttons

## Table of Contents
- [Button](#button)
- [CloseButton](#closebutton)

---

## Button

Interactive component that triggers an action when pressed.

**Source:** `button/button.tsx`
**Styles:** `button/button.styles.ts`

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/button-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/button-docs-dark.mp4"
/>

### Import

```tsx
import { Button } from 'heroui-native';
```

### Anatomy

```tsx
<Button>
  <Button.Label>...</Button.Label>
</Button>
```

- **Button**: Main container that handles press interactions, animations, and variants. Renders string children as label or accepts compound components for custom layouts.
- **Button.Label**: Text content of the button. Inherits size and variant styling from parent Button context.

### Usage

#### Basic Usage

The Button component accepts string children that automatically render as label.

```tsx
<Button>Basic Button</Button>
```

#### With Compound Parts

Use Button.Label for explicit control over the label component.

```tsx
<Button>
  <Button.Label>Click me</Button.Label>
</Button>
```

#### With Icons

Combine icons with labels for enhanced visual communication.

```tsx
<Button>
  <Icon name="add" size={20} />
  <Button.Label>Add Item</Button.Label>
</Button>

<Button>
  <Button.Label>Download</Button.Label>
  <Icon name="download" size={18} />
</Button>
```

#### Icon Only

Create square icon-only buttons using the isIconOnly prop.

```tsx
<Button isIconOnly>
  <Icon name="heart" size={18} />
</Button>
```

#### Sizes

Control button dimensions with three size options.

```tsx
<Button size="sm">Small</Button>
<Button size="md">Medium</Button>
<Button size="lg">Large</Button>
```

#### Variants

Choose from seven visual variants for different emphasis levels.

```tsx
<Button variant="primary">Primary</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="tertiary">Tertiary</Button>
<Button variant="outline">Outline</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="danger">Danger</Button>
<Button variant="danger-soft">Danger Soft</Button>
```

#### Feedback Variants

The `feedbackVariant` prop controls which press feedback effects are rendered:

- `'scale-highlight'` (default): Built-in scale + highlight overlay
- `'scale-ripple'`: Built-in scale + ripple overlay
- `'scale'`: Built-in scale only (no overlay)
- `'none'`: No feedback animations at all

```tsx
{/* Scale + Highlight (default) */}
<Button feedbackVariant="scale-highlight">Highlight Effect</Button>

{/* Scale + Ripple */}
<Button feedbackVariant="scale-ripple">Ripple Effect</Button>

{/* Scale only */}
<Button feedbackVariant="scale">Scale Only</Button>

{/* No feedback */}
<Button feedbackVariant="none">No Feedback</Button>
```

#### Custom Animation

The `animation` prop controls individual sub-animations. Its shape depends on the `feedbackVariant`.

```tsx
{/* Customize scale and highlight (default feedbackVariant) */}
<Button
  animation={{
    scale: { value: 0.97 },
    highlight: {
      backgroundColor: { value: '#3b82f6' },
      opacity: { value: [0, 0.2] },
    },
  }}
>
  Custom Highlight
</Button>

{/* Customize scale and ripple */}
<Button
  feedbackVariant="scale-ripple"
  animation={{
    scale: { value: 0.97 },
    ripple: {
      backgroundColor: { value: '#3b82f6' },
      opacity: { value: [0, 0.3, 0] },
    },
  }}
>
  Custom Ripple
</Button>
```

#### Disable Individual Animations

Disable specific sub-animations by setting them to `false`:

```tsx
{/* Disable scale, keep highlight */}
<Button animation={{ scale: false }}>No Scale</Button>

{/* Disable highlight, keep scale */}
<Button animation={{ highlight: false }}>No Highlight</Button>

{/* Disable both */}
<Button animation={{ scale: false, highlight: false }}>No Animations</Button>
```

#### Disable All Animations

Use `animation={false}` to disable all feedback, or `animation="disable-all"` for cascading disable:

```tsx
<Button animation={false}>Disabled</Button>
<Button animation="disable-all">Disable All (cascading)</Button>
```

#### Loading State with Spinner

Transform button to loading state with spinner animation.

```tsx
const themeColorAccentForeground = useThemeColor('accent-foreground');

<Button
  layout={LinearTransition.springify()}
  variant="primary"
  onPress={() => {
    setIsDownloading(true);
    setTimeout(() => {
      setIsDownloading(false);
    }, 3000);
  }}
  isIconOnly={isDownloading}
  className="self-center"
>
  {isDownloading ? (
    <Spinner entering={FadeIn.delay(50)} color={themeColorAccentForeground} />
  ) : (
    'Download now'
  )}
</Button>;
```

#### Custom Background with LinearGradient

Add gradient backgrounds using absolute positioned elements. Use `feedbackVariant="none"` to disable the default highlight overlay, or use `feedbackVariant="scale-ripple"` for a custom ripple effect.

```tsx
import { Button, PressableFeedback } from 'heroui-native';
import { LinearGradient } from 'expo-linear-gradient';
import { StyleSheet } from 'react-native';

{/* Gradient with no feedback overlay */}
<Button feedbackVariant="none">
  <LinearGradient
    colors={['#9333ea', '#ec4899']}
    start={{ x: 0, y: 0 }}
    end={{ x: 1, y: 0 }}
    style={StyleSheet.absoluteFill}
  />
  <Button.Label className="text-white font-bold">Gradient</Button.Label>
</Button>

{/* Gradient with custom ripple effect */}
<Button
  feedbackVariant="scale-ripple"
  animation={{
    ripple: {
      backgroundColor: { value: 'white' },
      opacity: { value: [0, 0.5, 0] },
    },
  }}
>
  <LinearGradient
    colors={['#0d9488', '#ec4899']}
    start={{ x: 0, y: 0 }}
    end={{ x: 1, y: 0 }}
    style={StyleSheet.absoluteFill}
  />
  <Button.Label className="text-white font-bold" pointerEvents="none">
    Gradient with Ripple
  </Button.Label>
</Button>
```

### Docs Example

```tsx
import { Button, useThemeColor } from 'heroui-native';
import { Ionicons } from '@expo/vector-icons';
import { View } from 'react-native';

export default function ButtonExample() {
  const [
    themeColorAccentForeground,
    themeColorAccentSoftForeground,
    themeColorDangerForeground,
    themeColorDefaultForeground,
  ] = useThemeColor([
    'accent-foreground',
    'accent-soft-foreground',
    'danger-foreground',
    'default-foreground',
  ]);

  return (
    <View className="gap-4 p-4">
      <Button variant="primary">
        <Ionicons name="add" size={20} color={themeColorAccentForeground} />
        <Button.Label>Add Item</Button.Label>
      </Button>

      <View className="flex-row gap-4">
        <Button size="sm" isIconOnly>
          <Ionicons name="heart" size={16} color={themeColorAccentForeground} />
        </Button>
        <Button size="sm" variant="secondary" isIconOnly>
          <Ionicons
            name="bookmark"
            size={16}
            color={themeColorAccentSoftForeground}
          />
        </Button>
        <Button size="sm" variant="danger" isIconOnly>
          <Ionicons name="trash" size={16} color={themeColorDangerForeground} />
        </Button>
      </View>

      <Button variant="tertiary">
        <Button.Label>Learn More</Button.Label>
        <Ionicons
          name="chevron-forward"
          size={18}
          color={themeColorDefaultForeground}
        />
      </Button>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](<https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/button.tsx>).

### API Reference

#### Button

Button extends all props from [PressableFeedback](./pressable-feedback) (except `animation`, which is redefined) with additional button-specific props.

| prop              | type                                                                                          | default             | description                                                    |
| ----------------- | --------------------------------------------------------------------------------------------- | ------------------- | -------------------------------------------------------------- |
| `variant`         | `'primary' \| 'secondary' \| 'tertiary' \| 'outline' \| 'ghost' \| 'danger' \| 'danger-soft'` | `'primary'`         | Visual variant of the button                                   |
| `size`            | `'sm' \| 'md' \| 'lg'`                                                                        | `'md'`              | Size of the button                                             |
| `isIconOnly`      | `boolean`                                                                                     | `false`             | Whether the button displays an icon only (square aspect ratio) |
| `feedbackVariant` | `'scale-highlight' \| 'scale-ripple' \| 'scale' \| 'none'`                                    | `'scale-highlight'` | Determines which feedback effects are rendered                 |
| `animation`       | `ButtonAnimation`                                                                             | -                   | Animation configuration (shape depends on `feedbackVariant`)   |

For inherited props including `isDisabled`, `className`, `children`, and all Pressable props, see [PressableFeedback API Reference](./pressable-feedback#api-reference).

#### ButtonAnimation

The `animation` prop is a discriminated union based on `feedbackVariant`. It follows the `AnimationRoot` control flow:

- `true` or `undefined`: Use default animations
- `false` or `"disabled"`: Disable all feedback animations
- `"disable-all"`: Cascade-disable all animations including child compound parts
- `object`: Custom configuration with sub-animation keys (see below)

**When `feedbackVariant="scale-highlight"` (default):**

| prop        | type                                     | default | description                                                  |
| ----------- | ---------------------------------------- | ------- | ------------------------------------------------------------ |
| `scale`     | `PressableFeedbackScaleAnimation`        | -       | Scale animation config (`false` to disable)                  |
| `highlight` | `PressableFeedbackHighlightAnimation`    | -       | Highlight overlay config (`false` to disable)                |
| `state`     | `'disabled' \| 'disable-all' \| boolean` | -       | Control animation state while keeping config (runtime toggle) |

**When `feedbackVariant="scale-ripple"`:**

| prop     | type                                     | default | description                                                  |
| -------- | ---------------------------------------- | ------- | ------------------------------------------------------------ |
| `scale`  | `PressableFeedbackScaleAnimation`        | -       | Scale animation config (`false` to disable)                  |
| `ripple` | `PressableFeedbackRippleAnimation`       | -       | Ripple overlay config (`false` to disable)                   |
| `state`  | `'disabled' \| 'disable-all' \| boolean` | -       | Control animation state while keeping config (runtime toggle) |

**When `feedbackVariant="scale"`:**

| prop    | type                                     | default | description                                                  |
| ------- | ---------------------------------------- | ------- | ------------------------------------------------------------ |
| `scale` | `PressableFeedbackScaleAnimation`        | -       | Scale animation config (`false` to disable)                  |
| `state` | `'disabled' \| 'disable-all' \| boolean` | -       | Control animation state while keeping config (runtime toggle) |

**When `feedbackVariant="none"`:**

Only `'disable-all'` is accepted as a string value. All feedback effects are disabled.

For detailed animation sub-types (`PressableFeedbackScaleAnimation`, `PressableFeedbackHighlightAnimation`, `PressableFeedbackRippleAnimation`), see [PressableFeedback API Reference](./pressable-feedback#api-reference).

#### Button.Label

| prop           | type              | default | description                           |
| -------------- | ----------------- | ------- | ------------------------------------- |
| `children`     | `React.ReactNode` | -       | Content to be rendered as label       |
| `className`    | `string`          | -       | Additional CSS classes                |
| `...TextProps` | `TextProps`       | -       | All standard Text props are supported |

### Hooks

#### useButton

Hook to access the Button context values. Returns the button's size, variant, and disabled state.

```tsx
import { useButton } from 'heroui-native';

const { size, variant, isDisabled } = useButton();
```

##### Return Value

| property     | type                                                                                          | description                    |
| ------------ | --------------------------------------------------------------------------------------------- | ------------------------------ |
| `size`       | `'sm' \| 'md' \| 'lg'`                                                                        | Size of the button             |
| `variant`    | `'primary' \| 'secondary' \| 'tertiary' \| 'outline' \| 'ghost' \| 'danger' \| 'danger-soft'` | Visual variant of the button   |
| `isDisabled` | `boolean`                                                                                     | Whether the button is disabled |

**Note:** This hook must be used within a `Button` component. It will throw an error if called outside of the button context.

### Examples from button.md

The Button component is a pressable element that triggers actions when tapped.

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { Button, PressableFeedback, Spinner, cn } from 'heroui-native';
```

#### Components

- `Button` - Root container component
- `Button.Label` - Button text label

#### Props

##### Button Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'primary' \| 'secondary' \| 'tertiary' \| 'outline' \| 'ghost' \| 'danger' \| 'danger-soft'` | `'primary'` | Visual style variant |
| `size` | `'sm' \| 'md' \| 'lg'` | `'md'` | Button size |
| `isDisabled` | `boolean` | `false` | Disable button interactions |
| `isIconOnly` | `boolean` | `false` | Button contains only an icon |
| `feedbackVariant` | `'highlight' \| 'scale' \| 'none'` | `'highlight'` | Press feedback animation |
| `className` | `string` | - | Additional CSS classes |
| `onPress` | `() => void` | - | Press handler |

#### Example: Sizes

```tsx
import { Button } from 'heroui-native';
import { View } from 'react-native';

function SizesExample() {
  return (
    <View className="gap-8 w-full px-8">
      <Button size="sm">Small Button</Button>
      <Button size="md">Medium Button</Button>
      <Button size="lg">Large Button</Button>
    </View>
  );
}
```

#### Example: Variants

```tsx
<View className="gap-6 w-full px-8">
  <Button variant="primary">Primary</Button>
  <Button variant="secondary">Secondary</Button>
  <Button variant="tertiary">Tertiary</Button>
  <Button variant="outline">Outline</Button>
  <Button variant="ghost">Ghost</Button>
  <Button variant="danger">Danger</Button>
  <Button variant="danger-soft">Danger Soft</Button>
</View>
```

#### Example: Disabled State with Loading

```tsx
import { Button, Spinner, useThemeColor } from 'heroui-native';

function DisabledStateExample() {
  const [themeColorAccent, themeColorAccentForeground] = useThemeColor([
    'accent',
    'accent-foreground',
  ]);

  return (
    <View className="gap-8 w-full px-8">
      <Button isDisabled>
        <Spinner color={themeColorAccentForeground} size="sm" />
        <Button.Label>Loading</Button.Label>
      </Button>
      <Button variant="secondary" isDisabled>
        <Spinner size="sm" color={themeColorAccent} />
        <Button.Label>Loading</Button.Label>
      </Button>
      <Button variant="tertiary" isDisabled>
        <CircleInfoFillIcon size={16} colorClassName="accent-muted" />
        <Button.Label>Access Denied</Button.Label>
      </Button>
    </View>
  );
}
```

#### Example: Width and Alignment Control

```tsx
<View className="gap-8 w-full px-8">
  <Button>Full Width Button</Button>
  <View>
    <Button variant="secondary" size="sm" className="self-start">
      Start
    </Button>
    <Button variant="secondary" size="sm" className="self-center">
      Center
    </Button>
    <Button variant="secondary" size="sm" className="self-end">
      End
    </Button>
  </View>
</View>
```

#### Example: With Icons

```tsx
import { PlusIcon, ArrowDownToSquareIcon, TrashIcon } from './icons';

<View className="gap-8 w-full px-8">
  <Button variant="primary">
    <PlusIcon size={16} colorClassName="accent-accent-foreground" />
    <Button.Label>Add Member</Button.Label>
  </Button>

  <Button variant="secondary">
    <Button.Label>Download</Button.Label>
    <ArrowDownToSquareIcon size={16} colorClassName="accent-accent" />
  </Button>

  <Button variant="danger">
    <TrashIcon size={15} colorClassName="accent-danger-foreground" />
    <Button.Label>Delete</Button.Label>
  </Button>
</View>
```

#### Example: Icon Only

```tsx
import { PaperClipIcon, HeartFillIcon, TrashIcon } from './icons';

<View className="flex-row gap-8">
  <Button size="sm" isIconOnly>
    <Button.Label>
      <PaperClipIcon size={16} colorClassName="accent-accent-foreground" />
    </Button.Label>
  </Button>
  <Button size="md" variant="secondary" isIconOnly>
    <Button.Label>
      <HeartFillIcon size={18} colorClassName="accent-danger" />
    </Button.Label>
  </Button>
  <Button size="lg" variant="danger" isIconOnly>
    <Button.Label>
      <TrashIcon size={18} colorClassName="accent-danger-foreground" />
    </Button.Label>
  </Button>
</View>
```

#### Example: Custom Styling

```tsx
import { LinearGradient } from 'expo-linear-gradient';
import { StyleSheet } from 'react-native';

function CustomStylingExample() {
  const { isDark } = useAppTheme();

  return (
    <View className="gap-8 w-full px-8">
      <Button
        className="bg-purple-600"
        animation={{
          highlight: {
            backgroundColor: {
              value: '#c084fc',
            },
            opacity: {
              value: [0, 0.5],
            },
          },
        }}
      >
        <Button.Label className="text-white font-semibold">
          Custom Purple
        </Button.Label>
      </Button>

      <Button feedbackVariant="scale">
        <LinearGradient
          colors={['#0d9488', '#ec4899']}
          start={{ x: 0, y: 0 }}
          end={{ x: 1, y: 0 }}
          style={StyleSheet.absoluteFill}
        />
        <PressableFeedback.Ripple
          animation={{
            backgroundColor: { value: 'white' },
            opacity: { value: [0, 0.5, 0] },
          }}
        />
        <Button.Label className="text-white font-bold" pointerEvents="none">
          Gradient
        </Button.Label>
      </Button>

      <Button
        className={cn(
          'bg-neutral-950 rounded-md',
          isDark && 'bg-neutral-50'
        )}
        feedbackVariant="scale"
      >
        <ShoppingCartIcon
          size={18}
          colorClassName={cn(
            'accent-neutral-50',
            isDark && 'accent-neutral-950'
          )}
        />
        <Button.Label
          className={cn('text-neutral-50', isDark && 'text-neutral-950')}
        >
          Add to Cart
        </Button.Label>
      </Button>
    </View>
  );
}
```

#### Example: Layout Transitions

```tsx
import { FadeIn, LinearTransition } from 'react-native-reanimated';

function LayoutTransitionsExample() {
  const [isDownloading, setIsDownloading] = React.useState(false);

  return (
    <View className="flex-1 items-center justify-center">
      <Button
        layout={LinearTransition.springify()}
        variant="primary"
        onPress={() => {
          setIsDownloading(true);
          setTimeout(() => {
            setIsDownloading(false);
          }, 3000);
        }}
        isIconOnly={isDownloading}
      >
        {isDownloading ? (
          <Spinner entering={FadeIn.delay(50)} color="white" />
        ) : (
          'Download now'
        )}
      </Button>
    </View>
  );
}
```

#### Complete Example Code (from button.md)

```tsx
import { LinearGradient } from 'expo-linear-gradient';
import {
  Button,
  cn,
  PressableFeedback,
  Spinner,
  useThemeColor,
} from 'heroui-native';
import React from 'react';
import { StyleSheet, View } from 'react-native';
import { FadeIn, LinearTransition } from 'react-native-reanimated';
import { PlusIcon } from './icons/plus';
import { ArrowDownToSquareIcon } from './icons/arrow-down-to-square';
import { TrashIcon } from './icons/trash';
import { ShoppingCartIcon } from './icons/shopping-cart';

const SizesContent = () => {
  return (
    <View className="flex-1 items-center justify-center">
      <View className="gap-8 w-full px-8">
        <Button size="sm">Small Button</Button>
        <Button size="md">Medium Button</Button>
        <Button size="lg">Large Button</Button>
      </View>
    </View>
  );
};

const VariantsContent = () => {
  return (
    <View className="flex-1 items-center justify-center">
      <View className="gap-6 w-full px-8">
        <Button variant="primary">Primary</Button>
        <Button variant="secondary">Secondary</Button>
        <Button variant="tertiary">Tertiary</Button>
        <Button variant="outline">Outline</Button>
        <Button variant="ghost">Ghost</Button>
        <Button variant="danger">Danger</Button>
        <Button variant="danger-soft">Danger Soft</Button>
      </View>
    </View>
  );
};

const WithIconsContent = () => {
  return (
    <View className="flex-1 items-center justify-center">
      <View className="gap-8 w-full px-8">
        <Button variant="primary">
          <PlusIcon size={16} colorClassName="accent-accent-foreground" />
          <Button.Label>Add Member</Button.Label>
        </Button>

        <Button variant="secondary">
          <Button.Label>Download</Button.Label>
          <ArrowDownToSquareIcon size={16} colorClassName="accent-accent" />
        </Button>

        <Button variant="danger">
          <TrashIcon size={15} colorClassName="accent-danger-foreground" />
          <Button.Label>Delete</Button.Label>
        </Button>
      </View>
    </View>
  );
};

const LayoutTransitionsContent = () => {
  const [isDownloading, setIsDownloading] = React.useState(false);

  return (
    <View className="flex-1 items-center justify-center">
      <Button
        layout={LinearTransition.springify()}
        variant="primary"
        onPress={() => {
          setIsDownloading(true);
          setTimeout(() => {
            setIsDownloading(false);
          }, 3000);
        }}
        isIconOnly={isDownloading}
      >
        {isDownloading ? (
          <Spinner entering={FadeIn.delay(50)} color="white" />
        ) : (
          'Download now'
        )}
      </Button>
    </View>
  );
};

export default function ButtonExample() {
  return <VariantsContent />;
}
```

### Full Example Code (button-example.tsx)

```tsx
import { LinearGradient } from 'expo-linear-gradient';
import {
  Button,
  cn,
  PressableFeedback,
  Spinner,
  useThemeColor,
} from 'heroui-native';
import React from 'react';
import { StyleSheet, View } from 'react-native';
import { FadeIn, LinearTransition } from 'react-native-reanimated';
import type { UsageVariant } from '../../../components/component-presentation/types';
import { UsageVariantFlatList } from '../../../components/component-presentation/usage-variant-flatlist';
import { ArrowDownToSquareIcon } from '../../../components/icons/arrow-down-to-square';
import { CircleInfoFillIcon } from '../../../components/icons/circle-info-fill';
import { HeartFillIcon } from '../../../components/icons/heart-fill';
import { PaperClipIcon } from '../../../components/icons/paper-clip';
import { PlusIcon } from '../../../components/icons/plus';
import { ShoppingCartIcon } from '../../../components/icons/shopping-cart';
import { TrashIcon } from '../../../components/icons/trash';
import { useAppTheme } from '../../../contexts/app-theme-context';

const SizesContent = () => {
  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <View className="gap-8 w-full px-8">
          <Button size="sm">Small Button</Button>
          <Button size="md">Medium Button</Button>
          <Button size="lg">Large Button</Button>
        </View>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const VariantsContent = () => {
  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <View className="gap-6 w-full px-8">
          <Button variant="primary">Primary</Button>
          <Button variant="secondary">Secondary</Button>
          <Button variant="tertiary">Tertiary</Button>
          <Button variant="outline">Outline</Button>
          <Button variant="ghost">Ghost</Button>
          <Button variant="danger">Danger</Button>
          <Button variant="danger-soft">Danger Soft</Button>
        </View>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const DisabledStateContent = () => {
  const [themeColorAccent, themeColorAccentForeground] = useThemeColor([
    'accent',
    'accent-foreground',
  ]);

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <View className="gap-8 w-full px-8">
          <Button isDisabled>
            <Spinner color={themeColorAccentForeground} size="sm" />
            <Button.Label>Loading</Button.Label>
          </Button>
          <Button variant="secondary" isDisabled>
            <Spinner size="sm" color={themeColorAccent} />
            <Button.Label>Loading</Button.Label>
          </Button>
          <Button variant="tertiary" isDisabled>
            <CircleInfoFillIcon size={16} colorClassName="accent-muted" />
            <Button.Label>Access Denied</Button.Label>
          </Button>
        </View>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const WidthAlignmentContent = () => {
  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <View className="gap-8 w-full px-8">
          <Button>Full Width Button</Button>
          <View>
            <Button variant="secondary" size="sm" className="self-start">
              Start
            </Button>
            <Button variant="secondary" size="sm" className="self-center">
              Center
            </Button>
            <Button variant="secondary" size="sm" className="self-end">
              End
            </Button>
          </View>
        </View>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const WithIconsContent = () => {
  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <View className="gap-8 w-full px-8">
          <Button variant="primary">
            <PlusIcon size={16} colorClassName="accent-accent-foreground" />
            <Button.Label>Add Member</Button.Label>
          </Button>

          <Button variant="secondary">
            <Button.Label>Download</Button.Label>
            <ArrowDownToSquareIcon size={16} colorClassName="accent-accent" />
          </Button>

          <Button variant="danger">
            <TrashIcon size={15} colorClassName="accent-danger-foreground" />
            <Button.Label>Delete</Button.Label>
          </Button>
        </View>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const IconOnlyContent = () => {
  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <View className="flex-row gap-8">
          <Button size="sm" isIconOnly>
            <Button.Label>
              <PaperClipIcon
                size={16}
                colorClassName="accent-accent-foreground"
              />
            </Button.Label>
          </Button>
          <Button size="md" variant="secondary" isIconOnly>
            <Button.Label>
              <HeartFillIcon size={18} colorClassName="accent-danger" />
            </Button.Label>
          </Button>
          <Button size="lg" variant="danger" isIconOnly>
            <Button.Label>
              <TrashIcon size={18} colorClassName="accent-danger-foreground" />
            </Button.Label>
          </Button>
        </View>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const CustomStylingContent = () => {
  const { isDark } = useAppTheme();

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <View className="gap-8 w-full px-8">
          <Button
            className="bg-purple-600"
            animation={{
              highlight: {
                backgroundColor: {
                  value: '#c084fc',
                },
                opacity: {
                  value: [0, 0.5],
                },
              },
            }}
          >
            <Button.Label className="text-white font-semibold">
              Custom Purple
            </Button.Label>
          </Button>

          <Button feedbackVariant="scale">
            <LinearGradient
              colors={['#0d9488', '#ec4899']}
              start={{ x: 0, y: 0 }}
              end={{ x: 1, y: 0 }}
              style={StyleSheet.absoluteFill}
            />
            <PressableFeedback.Ripple
              animation={{
                backgroundColor: { value: 'white' },
                opacity: { value: [0, 0.5, 0] },
              }}
            />
            <Button.Label className="text-white font-bold" pointerEvents="none">
              Gradient
            </Button.Label>
          </Button>
          <Button
            className={cn(
              'bg-neutral-950 rounded-md',
              isDark && 'bg-neutral-50'
            )}
            feedbackVariant="scale"
          >
            <ShoppingCartIcon
              size={18}
              colorClassName={cn(
                'accent-neutral-50',
                isDark && 'accent-neutral-950'
              )}
            />
            <Button.Label
              className={cn('text-neutral-50', isDark && 'text-neutral-950')}
            >
              Add to Cart
            </Button.Label>
          </Button>
        </View>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const LayoutTransitionsContent = () => {
  const [isDownloading, setIsDownloading] = React.useState(false);

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <Button
          layout={LinearTransition.springify()}
          variant="primary"
          onPress={() => {
            setIsDownloading(true);
            setTimeout(() => {
              setIsDownloading(false);
            }, 3000);
          }}
          isIconOnly={isDownloading}
        >
          {isDownloading ? (
            <Spinner entering={FadeIn.delay(50)} color="white" />
          ) : (
            'Download now'
          )}
        </Button>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const BUTTON_VARIANTS: UsageVariant[] = [
  {
    value: 'sizes',
    label: 'Sizes',
    content: <SizesContent />,
  },
  {
    value: 'variants',
    label: 'Variants',
    content: <VariantsContent />,
  },
  {
    value: 'disabled-state',
    label: 'Disabled state',
    content: <DisabledStateContent />,
  },
  {
    value: 'width-alignment',
    label: 'Width/alignment control',
    content: <WidthAlignmentContent />,
  },
  {
    value: 'with-icons',
    label: 'With icons',
    content: <WithIconsContent />,
  },
  {
    value: 'icon-only',
    label: 'Icon only',
    content: <IconOnlyContent />,
  },
  {
    value: 'custom-styling',
    label: 'Custom styling',
    content: <CustomStylingContent />,
  },
  {
    value: 'layout-transitions',
    label: 'Layout transitions demo',
    content: <LayoutTransitionsContent />,
  },
];

export default function ButtonScreen() {
  return <UsageVariantFlatList data={BUTTON_VARIANTS} />;
}
```

---

## CloseButton

Button component for closing dialogs, modals, or dismissing content.

**Source:** `close-button/close-button.tsx`
**Styles:** `close-button/close-button.styles.ts`

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/close-button-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/close-button-docs-dark.mp4"
/>

### Import

```tsx
import { CloseButton } from 'heroui-native';
```

### Usage

#### Basic Usage

The CloseButton component renders a close icon button with default styling.

```tsx
<CloseButton />
```

#### Custom Icon Color

Customize the icon color using the `iconProps` prop.

```tsx
<CloseButton iconProps={{ color: themeColorDanger }} />
<CloseButton iconProps={{ color: themeColorAccent }} />
```

#### Custom Icon Size

Adjust the icon size using the `iconProps` prop.

```tsx
<CloseButton iconProps={{ size: 24 }} />
```

#### Custom Children

Replace the default close icon with custom content.

```tsx
<CloseButton>
  <CustomIcon />
</CloseButton>
```

#### Disabled State

Disable the button to prevent interactions.

```tsx
<CloseButton isDisabled />
```

### Docs Example

```tsx
import { CloseButton, useThemeColor } from 'heroui-native';
import { Ionicons } from '@expo/vector-icons';
import { View } from 'react-native';
import { withUniwind } from 'uniwind';

const StyledIonicons = withUniwind(Ionicons);

export default function CloseButtonExample() {
  const themeColorForeground = useThemeColor('foreground');
  const themeColorDanger = useThemeColor('danger');

  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row items-center gap-4">
        <CloseButton />
        <CloseButton iconProps={{ color: themeColorDanger }} />
        <CloseButton>
          <StyledIonicons
            name="close-circle"
            size={28}
            color={themeColorForeground}
          />
        </CloseButton>
        <CloseButton isDisabled />
      </View>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/close-button.tsx).

### API Reference

#### CloseButton

CloseButton extends all props from [Button](./button) component. It defaults to `variant='tertiary'`, `size='sm'`, and `isIconOnly=true`.

| prop        | type                   | default | description                                      |
| ----------- | ---------------------- | ------- | ------------------------------------------------ |
| `iconProps` | `CloseButtonIconProps` | -       | Props for customizing the close icon             |
| `children`  | `React.ReactNode`      | -       | Custom content to replace the default close icon |

For inherited props including `isDisabled`, `className`, `animation`, `feedbackVariant` and all Pressable props, see [Button API Reference](./button#api-reference).

#### CloseButtonIconProps

| prop    | type     | default                | description       |
| ------- | -------- | ---------------------- | ----------------- |
| `size`  | `number` | `20`                   | Size of the icon  |
| `color` | `string` | Uses theme muted color | Color of the icon |

### Examples from close-button.md

The Close Button component provides a styled button for closing/dismissing UI elements.

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { CloseButton } from 'heroui-native';
```

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'default' \| 'ghost'` | `'default'` | Visual style variant |
| `onPress` | `() => void` | - | Press handler |
| `className` | `string` | - | Additional CSS classes |

#### Example: Basic Close Button

```tsx
import { CloseButton } from 'heroui-native';
import { View } from 'react-native';

function BasicCloseButtonExample() {
  return (
    <View className="p-5">
      <CloseButton onPress={() => console.log('Closed')} />
    </View>
  );
}
```

#### Example: Ghost Close Button

```tsx
import { CloseButton } from 'heroui-native';
import { View } from 'react-native';

function GhostCloseButtonExample() {
  return (
    <View className="p-5">
      <CloseButton variant="ghost" onPress={() => {}} />
    </View>
  );
}
```

#### Complete Example Code

```tsx
import { CloseButton } from 'heroui-native';
import { View } from 'react-native';

export default function CloseButtonExample() {
  return (
    <View className="flex-1 p-5 justify-center items-center">
      <CloseButton onPress={() => console.log('Closed')} />
    </View>
  );
}
```
