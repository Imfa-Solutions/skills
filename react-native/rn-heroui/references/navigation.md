# Navigation Components

## Table of Contents
- [Accordion](#accordion)
- [Tabs](#tabs)
- [ListGroup](#listgroup)

---

## Accordion

---
title: Accordion
description: A collapsible content panel for organizing information in a compact space
links:
  source_native: accordion/accordion.tsx
  styles_native: accordion/accordion.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/accordion-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/accordion-docs-dark-1.mp4"
/>

### Import

```tsx
import { Accordion } from 'heroui-native';
```

### Anatomy

```tsx
<Accordion>
  <Accordion.Item>
    <Accordion.Trigger>
      ...
      <Accordion.Indicator>...</Accordion.Indicator>
    </Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

- **Accordion**: Main container that manages the accordion state and behavior. Controls expansion/collapse of items, supports single or multiple selection modes, and provides variant styling (default or surface).
- **Accordion.Item**: Container for individual accordion items. Wraps the trigger and content, managing the expanded state for each item.
- **Accordion.Trigger**: Interactive element that toggles item expansion. Built on Header and Trigger primitives.
- **Accordion.Indicator**: Optional visual indicator showing expansion state. Defaults to an animated chevron icon that rotates based on item state.
- **Accordion.Content**: Container for expandable content. Animated with layout transitions for smooth expand/collapse effects.

### Usage

#### Basic Usage

The Accordion component uses compound parts to create expandable content sections.

```tsx
<Accordion selectionMode="single">
  <Accordion.Item value="1">
    <Accordion.Trigger>
      ...
      <Accordion.Indicator />
    </Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

#### Single Selection Mode

Allow only one item to be expanded at a time.

```tsx
<Accordion selectionMode="single" defaultValue="2">
  <Accordion.Item value="1">
    <Accordion.Trigger>...</Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
  <Accordion.Item value="2">
    <Accordion.Trigger>...</Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

#### Multiple Selection Mode

Allow multiple items to be expanded simultaneously.

```tsx
<Accordion selectionMode="multiple" defaultValue={['1', '3']}>
  <Accordion.Item value="1">
    <Accordion.Trigger>...</Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
  <Accordion.Item value="2">
    <Accordion.Trigger>...</Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
  <Accordion.Item value="3">
    <Accordion.Trigger>...</Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

#### Surface Variant

Apply a surface container style to the accordion.

```tsx
<Accordion selectionMode="single" variant="surface">
  <Accordion.Item value="1">
    <Accordion.Trigger>
      ...
      <Accordion.Indicator />
    </Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

#### Custom Indicator

Replace the default chevron indicator with custom content.

```tsx
<Accordion selectionMode="single">
  <Accordion.Item value="1">
    <Accordion.Trigger>
      ...
      <Accordion.Indicator>
        <CustomIndicator />
      </Accordion.Indicator>
    </Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

#### Without Separators

Hide the separators between accordion items.

```tsx
<Accordion selectionMode="single" hideSeparator>
  <Accordion.Item value="1">
    <Accordion.Trigger>...</Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
  <Accordion.Item value="2">
    <Accordion.Trigger>...</Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

#### Custom Styling

Apply custom styles using className, classNames, or styles props.

```tsx
<Accordion
  className="rounded-lg"
  classNames={{
    container: 'bg-surface',
    separator: 'bg-separator/50',
  }}
  styles={{
    container: { padding: 16 },
    separator: { height: 2 },
  }}
>
  <Accordion.Item value="1">
    <Accordion.Trigger>...</Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

#### With PressableFeedback

Use `Accordion.Trigger` with `asChild` prop and wrap content with `PressableFeedback` to add custom press feedback animations.

```tsx
import { Accordion, PressableFeedback } from 'heroui-native';
import { View } from 'react-native';

<Accordion>
  <Accordion.Item value="1">
    <Accordion.Trigger asChild>
      <PressableFeedback animation={{ scale: false }}>
        <PressableFeedback.Scale className="flex-row items-center flex-1 gap-3">
          <Text>Item Title</Text>
        </PressableFeedback.Scale>
        <Accordion.Indicator />
        <PressableFeedback.Highlight
          animation={{ opacity: { value: [0, 0.05] } }}
        />
      </PressableFeedback>
    </Accordion.Trigger>
    <Accordion.Content>...</Accordion.Content>
  </Accordion.Item>
</Accordion>;
```

### Example

```tsx
import { Accordion, useThemeColor } from 'heroui-native';
import { Ionicons } from '@expo/vector-icons';
import { View, Text } from 'react-native';

export default function AccordionExample() {
  const themeColorMuted = useThemeColor('muted');

  const accordionData = [
    {
      id: '1',
      title: 'How do I place an order?',
      icon: <Ionicons name="bag-outline" size={16} color={themeColorMuted} />,
      content:
        'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat. Libero placerat dignissim consectetur nisl.',
    },
    {
      id: '2',
      title: 'What payment methods do you accept?',
      icon: <Ionicons name="card-outline" size={16} color={themeColorMuted} />,
      content:
        'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat. Libero placerat dignissim consectetur nisl.',
    },
    {
      id: '3',
      title: 'How much does shipping cost?',
      icon: <Ionicons name="cube-outline" size={16} color={themeColorMuted} />,
      content:
        'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat. Libero placerat dignissim consectetur nisl.',
    },
  ];

  return (
    <Accordion selectionMode="single" variant="surface" defaultValue="2">
      {accordionData.map((item) => (
        <Accordion.Item key={item.id} value={item.id}>
          <Accordion.Trigger>
            <View className="flex-row items-center flex-1 gap-3">
              {item.icon}
              <Text className="text-foreground text-base flex-1">
                {item.title}
              </Text>
            </View>
            <Accordion.Indicator />
          </Accordion.Trigger>
          <Accordion.Content>
            <Text className="text-muted text-base/relaxed px-[25px]">
              {item.content}
            </Text>
          </Accordion.Content>
        </Accordion.Item>
      ))}
    </Accordion>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/accordion.tsx).

### API Reference

#### Accordion

| prop                    | type                                               | default     | description                                                    |
| ----------------------- | -------------------------------------------------- | ----------- | -------------------------------------------------------------- |
| `children`              | `React.ReactNode`                                  | -           | Children elements to be rendered inside the accordion          |
| `selectionMode`         | `'single' \| 'multiple'`                           | -           | Whether the accordion allows single or multiple expanded items |
| `variant`               | `'default' \| 'surface'`                           | `'default'` | Visual variant of the accordion                                |
| `hideSeparator`         | `boolean`                                          | `false`     | Whether to hide the separator between accordion items          |
| `defaultValue`          | `string \| string[] \| undefined`                  | -           | Default expanded item(s) in uncontrolled mode                  |
| `value`                 | `string \| string[] \| undefined`                  | -           | Controlled expanded item(s)                                    |
| `isDisabled`            | `boolean`                                          | -           | Whether all accordion items are disabled                       |
| `isCollapsible`         | `boolean`                                          | `true`      | Whether expanded items can be collapsed                        |
| `animation`             | `AccordionRootAnimation`                           | -           | Animation configuration for accordion                          |
| `className`             | `string`                                           | -           | Additional CSS classes for the container                       |
| `classNames`            | `ElementSlots<RootSlots>`                          | -           | Additional CSS classes for the slots                           |
| `styles`                | `Partial<Record<RootSlots, ViewStyle>>`            | -           | Styles for different parts of the accordion root               |
| `onValueChange`         | `(value: string \| string[] \| undefined) => void` | -           | Callback when expanded items change                            |
| `...Animated.ViewProps` | `Animated.ViewProps`                               | -           | All Reanimated Animated.View props are supported               |

##### `ElementSlots<RootSlots>`

| prop        | type     | description                                       |
| ----------- | -------- | ------------------------------------------------- |
| `container` | `string` | Custom class name for the accordion container     |
| `separator` | `string` | Custom class name for the separator between items |

##### `styles`

| prop        | type        | description                            |
| ----------- | ----------- | -------------------------------------- |
| `container` | `ViewStyle` | Styles for the accordion container     |
| `separator` | `ViewStyle` | Styles for the separator between items |

##### AccordionRootAnimation

Animation configuration for accordion root component. Can be:

- `false` or `"disabled"`: Disable only root animations
- `"disable-all"`: Disable all animations including children
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop           | type                                     | default                                                                                         | description                                       |
| -------------- | ---------------------------------------- | ----------------------------------------------------------------------------------------------- | ------------------------------------------------- |
| `state`        | `'disabled' \| 'disable-all' \| boolean` | -                                                                                               | Disable animations while customizing properties   |
| `layout.value` | `LayoutTransition`                       | `LinearTransition`<br/>`.springify()`<br/>`.damping(140)`<br/>`.stiffness(1600)`<br/>`.mass(4)` | Custom layout animation for accordion transitions |

#### Accordion.Item

| prop                    | type                                                                        | default | description                                                                      |
| ----------------------- | --------------------------------------------------------------------------- | ------- | -------------------------------------------------------------------------------- |
| `children`              | `React.ReactNode \| ((props: AccordionItemRenderProps) => React.ReactNode)` | -       | Children elements to be rendered inside the accordion item, or a render function |
| `value`                 | `string`                                                                    | -       | Unique value to identify this item                                               |
| `isDisabled`            | `boolean`                                                                   | -       | Whether this specific item is disabled                                           |
| `className`             | `string`                                                                    | -       | Additional CSS classes                                                           |
| `...Animated.ViewProps` | `Animated.ViewProps`                                                        | -       | All Reanimated Animated.View props are supported                                 |

##### AccordionItemRenderProps

| prop         | type      | description                                      |
| ------------ | --------- | ------------------------------------------------ |
| `isExpanded` | `boolean` | Whether the accordion item is currently expanded |
| `value`      | `string`  | Unique value identifier for this accordion item  |

#### Accordion.Trigger

| prop                | type              | default | description                                             |
| ------------------- | ----------------- | ------- | ------------------------------------------------------- |
| `children`          | `React.ReactNode` | -       | Children elements to be rendered inside the trigger     |
| `className`         | `string`          | -       | Additional CSS classes                                  |
| `isDisabled`        | `boolean`         | -       | Whether the trigger is disabled                         |
| `...PressableProps` | `PressableProps`  | -       | All standard React Native Pressable props are supported |

#### Accordion.Indicator

| prop                    | type                          | default | description                                                            |
| ----------------------- | ----------------------------- | ------- | ---------------------------------------------------------------------- |
| `children`              | `React.ReactNode`             | -       | Custom indicator content, if not provided defaults to animated chevron |
| `className`             | `string`                      | -       | Additional CSS classes                                                 |
| `iconProps`             | `AccordionIndicatorIconProps` | -       | Icon configuration                                                     |
| `animation`             | `AccordionIndicatorAnimation` | -       | Animation configuration for indicator                                  |
| `isAnimatedStyleActive` | `boolean`                     | `true`  | Whether animated styles (react-native-reanimated) are active           |
| `...Animated.ViewProps` | `Animated.ViewProps`          | -       | All Reanimated Animated.View props are supported                       |

##### AccordionIndicatorIconProps

| prop    | type     | default      | description       |
| ------- | -------- | ------------ | ----------------- |
| `size`  | `number` | `16`         | Size of the icon  |
| `color` | `string` | `foreground` | Color of the icon |

##### AccordionIndicatorAnimation

Animation configuration for accordion indicator component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                    | type                    | default                                      | description                                      |
| ----------------------- | ----------------------- | -------------------------------------------- | ------------------------------------------------ |
| `state`                 | `'disabled' \| boolean` | -                                            | Disable animations while customizing properties  |
| `rotation.value`        | `[number, number]`      | `[0, -180]`                                  | Rotation values [collapsed, expanded] in degrees |
| `rotation.springConfig` | `WithSpringConfig`      | `{ damping: 140, stiffness: 1000, mass: 4 }` | Spring animation configuration for rotation      |

#### Accordion.Content

| prop           | type                        | default | description                                         |
| -------------- | --------------------------- | ------- | --------------------------------------------------- |
| `children`     | `React.ReactNode`           | -       | Children elements to be rendered inside the content |
| `className`    | `string`                    | -       | Additional CSS classes                              |
| `animation`    | `AccordionContentAnimation` | -       | Animation configuration for content                 |
| `...ViewProps` | `ViewProps`                 | -       | All standard React Native View props are supported  |

##### AccordionContentAnimation

Animation configuration for accordion content component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop             | type                    | default                                                              | description                                     |
| ---------------- | ----------------------- | -------------------------------------------------------------------- | ----------------------------------------------- |
| `state`          | `'disabled' \| boolean` | -                                                                    | Disable animations while customizing properties |
| `entering.value` | `EntryOrExitLayoutType` | `FadeIn`<br/>`.duration(200)`<br/>`.easing(Easing.out(Easing.ease))` | Custom entering animation for content           |
| `exiting.value`  | `EntryOrExitLayoutType` | `FadeOut`<br/>`.duration(200)`<br/>`.easing(Easing.in(Easing.ease))` | Custom exiting animation for content            |

### Hooks

#### useAccordion

Hook to access the accordion root context. Must be used within an `Accordion` component.

```tsx
import { useAccordion } from 'heroui-native';

const { value, onValueChange, selectionMode, isCollapsible, isDisabled } =
  useAccordion();
```

##### Returns

| property        | type                                                                  | description                                                                  |
| --------------- | --------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| `selectionMode` | `'single' \| 'multiple' \| undefined`                                 | Whether the accordion allows single or multiple expanded items               |
| `value`         | `(string \| undefined) \| string[]`                                   | Currently expanded item(s) - string for single mode, array for multiple mode |
| `onValueChange` | `(value: string \| undefined) => void \| ((value: string[]) => void)` | Callback function to update expanded items                                   |
| `isCollapsible` | `boolean`                                                             | Whether expanded items can be collapsed                                      |
| `isDisabled`    | `boolean \| undefined`                                                | Whether all accordion items are disabled                                     |

#### useAccordionItem

Hook to access the accordion item context. Must be used within an `Accordion.Item` component.

```tsx
import { useAccordionItem } from 'heroui-native';

const { value, isExpanded, isDisabled, nativeID } = useAccordionItem();
```

##### Returns

| property     | type                   | description                                          |
| ------------ | ---------------------- | ---------------------------------------------------- |
| `value`      | `string`               | Unique value identifier for this accordion item      |
| `isExpanded` | `boolean`              | Whether the accordion item is currently expanded     |
| `isDisabled` | `boolean \| undefined` | Whether this specific item is disabled               |
| `nativeID`   | `string`               | Native ID used for accessibility and ARIA attributes |

### Special Notes

When using the Accordion component alongside other components in the same view, you should import and apply `AccordionLayoutTransition` to those components to ensure smooth and consistent layout animations across the entire screen.

```jsx
import { Accordion, AccordionLayoutTransition } from 'heroui-native';
import Animated from 'react-native-reanimated';

<Animated.ScrollView layout={AccordionLayoutTransition}>
  <Animated.View layout={AccordionLayoutTransition}>
    {/* Other content */}
  </Animated.View>

  <Accordion>{/* Accordion items */}</Accordion>
</Animated.ScrollView>;
```

This ensures that when the accordion expands or collapses, all components on the screen animate with the same timing and easing, creating a cohesive user experience.

### Examples from accordion.md

# Accordion Component

The Accordion component is a collapsible content container that allows users to expand and collapse sections of content.

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { Accordion, PressableFeedback, useAccordionItem } from 'heroui-native';
```

#### Components

- `Accordion` - Root container component
- `Accordion.Item` - Individual accordion item
- `Accordion.Trigger` - Clickable header to expand/collapse
- `Accordion.Content` - Collapsible content area
- `Accordion.Indicator` - Visual indicator (chevron/arrow)

#### Props

##### Accordion Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'default' \| 'surface'` | `'default'` | Visual style variant |
| `selectionMode` | `'single' \| 'multiple'` | `'single'` | Selection behavior |
| `defaultValue` | `string \| string[]` | - | Default expanded item(s) |
| `hideSeparator` | `boolean` | `false` | Hide separator lines |

##### Accordion.Item Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `value` | `string` | required | Unique identifier for the item |

#### Example: Default Variant

```tsx
import { Accordion, PressableFeedback } from 'heroui-native';
import { View } from 'react-native';

const accordionData = [
  {
    id: '1',
    title: 'How do I place an order?',
    content: 'Lorem ipsum dolor sit amet consectetur...',
  },
  {
    id: '2',
    title: 'Can I modify or cancel my order?',
    content: 'Lorem ipsum dolor sit amet consectetur...',
  },
];

function DefaultVariantExample() {
  return (
    <Accordion defaultValue="2" className="w-full">
      {accordionData.map((item) => (
        <Accordion.Item key={item.id} value={item.id}>
          <Accordion.Trigger asChild>
            <PressableFeedback animation={{ scale: false }}>
              <View className="flex-row items-center flex-1 gap-3">
                <Text className="text-foreground text-base flex-1">
                  {item.title}
                </Text>
              </View>
              <Accordion.Indicator />
            </PressableFeedback>
          </Accordion.Trigger>
          <Accordion.Content>
            <Text className="text-muted text-base/relaxed px-[28px]">
              {item.content}
            </Text>
          </Accordion.Content>
        </Accordion.Item>
      ))}
    </Accordion>
  );
}
```

#### Example: Surface Variant

```tsx
<Accordion variant="surface" className="w-full">
  {accordionData.map((item) => (
    <Accordion.Item key={item.id} value={item.id}>
      <Accordion.Trigger>
        <View className="flex-row items-center flex-1 gap-3">
          {item.icon}
          <Text className="text-foreground text-base flex-1">
            {item.title}
          </Text>
        </View>
        <Accordion.Indicator />
      </Accordion.Trigger>
      <Accordion.Content>
        <Text className="text-muted text-base/relaxed px-[28px]">
          {item.content}
        </Text>
      </Accordion.Content>
    </Accordion.Item>
  ))}
</Accordion>
```

#### Example: Multiple Selection

```tsx
<Accordion
  selectionMode="multiple"
  variant="surface"
  defaultValue={['1', '3']}
  className="w-full"
>
  {accordionData.map((item) => (
    <Accordion.Item key={item.id} value={item.id}>
      <Accordion.Trigger>
        <Text className="text-foreground text-base flex-1">
          {item.title}
        </Text>
        <Accordion.Indicator />
      </Accordion.Trigger>
      <Accordion.Content>
        <Text className="text-muted text-base/relaxed">
          {item.content}
        </Text>
      </Accordion.Content>
    </Accordion.Item>
  ))}
</Accordion>
```

#### Example: Custom Indicator

```tsx
import { useAccordionItem } from 'heroui-native';
import Animated, { ZoomIn, ZoomOut } from 'react-native-reanimated';

const CustomIndicator = () => {
  const { isExpanded } = useAccordionItem();

  return (
    <View className="size-5 items-center justify-center">
      {isExpanded ? (
        <Animated.View entering={ZoomIn} exiting={ZoomOut}>
          <MinusIcon size={14} colorClassName="accent-muted" />
        </Animated.View>
      ) : (
        <Animated.View entering={ZoomIn} exiting={ZoomOut}>
          <PlusIcon size={14} colorClassName="accent-muted" />
        </Animated.View>
      )}
    </View>
  );
};

<Accordion variant="surface" className="w-full">
  <Accordion.Item value="1">
    <Accordion.Trigger>
      <Text>Item Title</Text>
      <Accordion.Indicator>
        <CustomIndicator />
      </Accordion.Indicator>
    </Accordion.Trigger>
    <Accordion.Content>
      <Text>Item content here</Text>
    </Accordion.Content>
  </Accordion.Item>
</Accordion>
```

#### Example: Custom Entering Animation

```tsx
import { FadeInRight, FadeInLeft, ZoomIn } from 'react-native-reanimated';

<Accordion variant="surface" className="w-full">
  {accordionData.map((item, index) => (
    <Accordion.Item key={item.id} value={item.id}>
      <Accordion.Trigger>
        <Text>{item.title}</Text>
        <Accordion.Indicator
          animation={{
            rotation: {
              springConfig: { damping: 60, stiffness: 900, mass: 3 },
            },
          }}
        />
      </Accordion.Trigger>
      <Accordion.Content
        animation={{
          entering: {
            value: FadeInRight.delay(50),
          },
        }}
      >
        <Text>{item.content}</Text>
      </Accordion.Content>
    </Accordion.Item>
  ))}
</Accordion>
```

#### Complete Example Code

```tsx
import { Accordion, PressableFeedback, useAccordionItem } from 'heroui-native';
import { View, Text } from 'react-native';
import Animated, {
  Easing,
  FadeInLeft,
  FadeInRight,
  ZoomIn,
  ZoomOut,
} from 'react-native-reanimated';

const ICON_SIZE = 16;

const CUSTOM_INDICATOR_ENTERING = ZoomIn.duration(200).easing(
  Easing.inOut(Easing.ease)
);
const CUSTOM_INDICATOR_EXITING = ZoomOut.duration(200).easing(
  Easing.inOut(Easing.ease)
);

const CustomIndicator = () => {
  const { isExpanded } = useAccordionItem();

  return (
    <View className="size-5 items-center justify-center">
      {isExpanded ? (
        <Animated.View
          key="minus"
          entering={CUSTOM_INDICATOR_ENTERING}
          exiting={CUSTOM_INDICATOR_EXITING}
        >
          <MinusIcon size={14} colorClassName="accent-muted" />
        </Animated.View>
      ) : (
        <Animated.View
          key="plus"
          entering={CUSTOM_INDICATOR_ENTERING}
          exiting={CUSTOM_INDICATOR_EXITING}
        >
          <PlusIcon size={14} colorClassName="accent-muted" />
        </Animated.View>
      )}
    </View>
  );
};

const accordionData = [
  {
    id: '1',
    title: 'How do I place an order?',
    icon: <ShoppingBagIcon size={ICON_SIZE} colorClassName="accent-muted" />,
    content:
      'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat.',
  },
  {
    id: '2',
    title: 'Can I modify or cancel my order?',
    icon: <ReceiptIcon size={ICON_SIZE} colorClassName="accent-muted" />,
    content:
      'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat.',
  },
  {
    id: '3',
    title: 'How much does shipping cost?',
    icon: <BoxIcon size={ICON_SIZE} colorClassName="accent-muted" />,
    content:
      'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat.',
  },
  {
    id: '4',
    title: 'Do you ship internationally?',
    icon: <PlanetEarthIcon size={ICON_SIZE} colorClassName="accent-muted" />,
    content:
      'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat.',
  },
];

const classNames = {
  triggerContentContainer: 'flex-row items-center flex-1 gap-3',
  triggerTitle: 'text-foreground text-base flex-1',
  contentText: 'text-muted text-base/relaxed px-[28px]',
};

export default function AccordionExample() {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <Accordion variant="surface" className="w-full">
        {accordionData.map((item) => (
          <Accordion.Item key={item.id} value={item.id}>
            <Accordion.Trigger>
              <View className={classNames.triggerContentContainer}>
                {item.icon}
                <Text className={classNames.triggerTitle}>
                  {item.title}
                </Text>
              </View>
              <Accordion.Indicator>
                <CustomIndicator />
              </Accordion.Indicator>
            </Accordion.Trigger>
            <Accordion.Content>
              <Text className={classNames.contentText}>
                {item.content}
              </Text>
            </Accordion.Content>
          </Accordion.Item>
        ))}
      </Accordion>
    </View>
  );
}
```

### Full Example Code (accordion-example.tsx)

```tsx
import { Accordion, PressableFeedback, useAccordionItem } from 'heroui-native';
import { View } from 'react-native';
import Animated, {
  Easing,
  FadeInLeft,
  FadeInRight,
  ZoomIn,
  ZoomOut,
} from 'react-native-reanimated';
import { AccordionWithDepthEffect } from '../../../components/accordion/accordion-with-depth-effect';
import { AppText } from '../../../components/app-text';
import type { UsageVariant } from '../../../components/component-presentation/types';
import { UsageVariantFlatList } from '../../../components/component-presentation/usage-variant-flatlist';
import { BoxIcon } from '../../../components/icons/box';
import { MinusIcon } from '../../../components/icons/minus';
import { PlanetEarthIcon } from '../../../components/icons/planet-earth';
import { PlusIcon } from '../../../components/icons/plus';
import { ReceiptIcon } from '../../../components/icons/receipt';
import { ShoppingBagIcon } from '../../../components/icons/shopping-bag';

const ICON_SIZE = 16;

const CUSTOM_INDICATOR_ENTERING = ZoomIn.duration(200).easing(
  Easing.inOut(Easing.ease)
);
const CUSTOM_INDICATOR_EXITING = ZoomOut.duration(200).easing(
  Easing.inOut(Easing.ease)
);

const CustomIndicator = () => {
  const { isExpanded } = useAccordionItem();

  return (
    <View className="size-5 items-center justify-center">
      {isExpanded ? (
        <Animated.View
          key="minus"
          entering={CUSTOM_INDICATOR_ENTERING}
          exiting={CUSTOM_INDICATOR_EXITING}
        >
          <MinusIcon size={14} colorClassName="accent-muted" />
        </Animated.View>
      ) : (
        <Animated.View
          key="plus"
          entering={CUSTOM_INDICATOR_ENTERING}
          exiting={CUSTOM_INDICATOR_EXITING}
        >
          <PlusIcon size={14} colorClassName="accent-muted" />
        </Animated.View>
      )}
    </View>
  );
};

const accordionData = [
  {
    id: '1',
    title: 'How do I place an order?',
    icon: <ShoppingBagIcon size={ICON_SIZE} colorClassName="accent-muted" />,
    content:
      'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat. Libero placerat dignissim consectetur nisl.',
  },
  {
    id: '2',
    title: 'Can I modify or cancel my order?',
    icon: <ReceiptIcon size={ICON_SIZE} colorClassName="accent-muted" />,
    content:
      'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat. Libero placerat dignissim consectetur nisl. Ornare imperdiet amet lorem adipiscing.',
  },
  {
    id: '3',
    title: 'How much does shipping cost?',
    icon: <BoxIcon size={ICON_SIZE} colorClassName="accent-muted" />,
    content:
      'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat.',
  },
  {
    id: '4',
    title: 'Do you ship internationally?',
    icon: <PlanetEarthIcon size={ICON_SIZE} colorClassName="accent-muted" />,
    content:
      'Lorem ipsum dolor sit amet consectetur. Netus nunc mauris risus consequat. Libero placerat dignissim consectetur nisl. Ornare imperdiet amet lorem adipiscing.',
  },
];

const classNames = {
  triggerContentContainer: 'flex-row items-center flex-1 gap-3',
  triggerTitle: 'text-foreground text-base flex-1',
  contentText: 'text-muted text-base/relaxed px-[28px]',
};

const DefaultVariantContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <Accordion defaultValue="2" className="w-full">
        {accordionData.map((item) => (
          <Accordion.Item key={item.id} value={item.id}>
            <Accordion.Trigger asChild>
              <PressableFeedback animation={{ scale: false }}>
                <PressableFeedback.Scale
                  className={classNames.triggerContentContainer}
                >
                  {item.icon}
                  <AppText className="text-foreground text-base flex-1">
                    {item.title}
                  </AppText>
                </PressableFeedback.Scale>
                <Accordion.Indicator />
                <PressableFeedback.Highlight
                  animation={{ opacity: { value: [0, 0.05] } }}
                />
              </PressableFeedback>
            </Accordion.Trigger>
            <Accordion.Content>
              <AppText className={classNames.contentText}>
                {item.content}
              </AppText>
            </Accordion.Content>
          </Accordion.Item>
        ))}
      </Accordion>
    </View>
  );
};

const SurfaceVariantContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <Accordion variant="surface" className="w-full">
        {accordionData.map((item) => (
          <Accordion.Item key={item.id} value={item.id}>
            <Accordion.Trigger>
              <View className={classNames.triggerContentContainer}>
                {item.icon}
                <AppText className={classNames.triggerTitle}>
                  {item.title}
                </AppText>
              </View>
              <Accordion.Indicator />
            </Accordion.Trigger>
            <Accordion.Content>
              <AppText className={classNames.contentText}>
                {item.content}
              </AppText>
            </Accordion.Content>
          </Accordion.Item>
        ))}
      </Accordion>
    </View>
  );
};

const MultipleSelectionContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <Accordion
        selectionMode="multiple"
        variant="surface"
        defaultValue={['1', '3']}
        className="w-full"
      >
        {accordionData.slice(0, 3).map((item) => (
          <Accordion.Item key={item.id} value={item.id}>
            <Accordion.Trigger>
              <View className={classNames.triggerContentContainer}>
                {item.icon}
                <AppText className={classNames.triggerTitle}>
                  {item.title}
                </AppText>
              </View>
              <Accordion.Indicator />
            </Accordion.Trigger>
            <Accordion.Content>
              <AppText className={classNames.contentText}>
                {item.content}
              </AppText>
            </Accordion.Content>
          </Accordion.Item>
        ))}
      </Accordion>
    </View>
  );
};

const WithoutSeparatorsContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <Accordion hideSeparator className="w-full">
        {accordionData.slice(0, 3).map((item) => (
          <Accordion.Item key={item.id} value={item.id}>
            <Accordion.Trigger className="rounded-lg">
              <View className={classNames.triggerContentContainer}>
                {item.icon}
                <AppText className={classNames.triggerTitle}>
                  {item.title}
                </AppText>
              </View>
              <Accordion.Indicator />
            </Accordion.Trigger>
            <Accordion.Content>
              <AppText className={classNames.contentText}>
                {item.content}
              </AppText>
            </Accordion.Content>
          </Accordion.Item>
        ))}
      </Accordion>
    </View>
  );
};

const CustomIndicatorContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <Accordion variant="surface" className="w-full">
        {accordionData.slice(0, 2).map((item) => (
          <Accordion.Item key={item.id} value={item.id}>
            <Accordion.Trigger>
              <View className={classNames.triggerContentContainer}>
                {item.icon}
                <AppText className={classNames.triggerTitle}>
                  {item.title}
                </AppText>
              </View>
              <Accordion.Indicator>
                <CustomIndicator />
              </Accordion.Indicator>
            </Accordion.Trigger>
            <Accordion.Content>
              <AppText className={classNames.contentText}>
                {item.content}
              </AppText>
            </Accordion.Content>
          </Accordion.Item>
        ))}
      </Accordion>
    </View>
  );
};

const CustomEnteringAnimationContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <Accordion variant="surface" className="w-full">
        {accordionData.slice(0, 3).map((item, index) => (
          <Accordion.Item key={item.id} value={item.id}>
            <Accordion.Trigger>
              <View className={classNames.triggerContentContainer}>
                {item.icon}
                <AppText className={classNames.triggerTitle}>
                  {item.title}
                </AppText>
              </View>
              <Accordion.Indicator
                animation={{
                  rotation: {
                    springConfig:
                      index === 0
                        ? { damping: 60, stiffness: 900, mass: 3 }
                        : index === 1
                          ? { damping: 50, stiffness: 900, mass: 3 }
                          : { damping: 40, stiffness: 900, mass: 3 },
                  },
                }}
              />
            </Accordion.Trigger>
            <Accordion.Content
              animation={{
                entering: {
                  value:
                    index === 0
                      ? FadeInRight.delay(50).easing(Easing.inOut(Easing.ease))
                      : index === 1
                        ? FadeInLeft.delay(50).easing(Easing.inOut(Easing.ease))
                        : ZoomIn.delay(50).easing(Easing.out(Easing.exp)),
                },
              }}
            >
              <AppText className={classNames.contentText}>
                {item.content}
              </AppText>
            </Accordion.Content>
          </Accordion.Item>
        ))}
      </Accordion>
    </View>
  );
};

const WithDepthEffectContent = () => {
  return <AccordionWithDepthEffect />;
};

const ACCORDION_VARIANTS: UsageVariant[] = [
  {
    value: 'default-variant',
    label: 'Default variant',
    content: <DefaultVariantContent />,
  },
  {
    value: 'surface-variant',
    label: 'Surface variant',
    content: <SurfaceVariantContent />,
  },
  {
    value: 'multiple-selection',
    label: 'Multiple selection',
    content: <MultipleSelectionContent />,
  },
  {
    value: 'without-separators',
    label: 'Without separators',
    content: <WithoutSeparatorsContent />,
  },
  {
    value: 'custom-indicator',
    label: 'Custom indicator',
    content: <CustomIndicatorContent />,
  },
  {
    value: 'custom-entering-animation',
    label: 'Custom entering animation',
    content: <CustomEnteringAnimationContent />,
  },
  {
    value: 'with-depth-effect',
    label: 'With depth effect',
    content: <WithDepthEffectContent />,
  },
];

export default function AccordionScreen() {
  return <UsageVariantFlatList data={ACCORDION_VARIANTS} />;
}
```

---

## Tabs

---
title: Tabs
description: Organize content into tabbed views with animated transitions and indicators.
links:
  source_native: tabs/tabs.tsx
  styles_native: tabs/tabs.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/tabs-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/tabs-docs-dark.mp4"
/>

### Import

```tsx
import { Tabs } from 'heroui-native';
```

### Anatomy

```tsx
<Tabs>
  <Tabs.List>
    <Tabs.ScrollView>
      <Tabs.Indicator />
      <Tabs.Trigger>
        <Tabs.Label>...</Tabs.Label>
      </Tabs.Trigger>
      <Tabs.Separator />
      <Tabs.Trigger>
        <Tabs.Label>...</Tabs.Label>
      </Tabs.Trigger>
    </Tabs.ScrollView>
  </Tabs.List>
  <Tabs.Content>...</Tabs.Content>
</Tabs>
```

- **Tabs**: Main container that manages tab state and selection. Controls active tab, handles value changes, and provides context to child components.
- **Tabs.List**: Container for tab triggers. Groups triggers together with optional styling variants (primary or secondary).
- **Tabs.ScrollView**: Optional scrollable wrapper for tab triggers. Enables horizontal scrolling when tabs overflow with automatic centering of active tab.
- **Tabs.Trigger**: Interactive button for each tab. Handles press events to change active tab and measures its position for indicator animation.
- **Tabs.Label**: Text content for tab triggers. Displays the tab title with appropriate styling.
- **Tabs.Indicator**: Animated visual indicator for active tab. Smoothly transitions between tabs using spring or timing animations.
- **Tabs.Separator**: Visual separator between tabs. Shows when the current tab value is not in the `betweenValues` array, with animated opacity transitions.
- **Tabs.Content**: Container for tab panel content. Shows content when its value matches the active tab.

### Usage

#### Basic Usage

The Tabs component uses compound parts to create navigable content sections.

```tsx
<Tabs value="tab1" onValueChange={setActiveTab}>
  <Tabs.List>
    <Tabs.Indicator />
    <Tabs.Trigger value="tab1">
      <Tabs.Label>Tab 1</Tabs.Label>
    </Tabs.Trigger>
    <Tabs.Trigger value="tab2">
      <Tabs.Label>Tab 2</Tabs.Label>
    </Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="tab1">...</Tabs.Content>
  <Tabs.Content value="tab2">...</Tabs.Content>
</Tabs>
```

#### Primary Variant

Default rounded primary style for tab triggers.

```tsx
<Tabs value={activeTab} onValueChange={setActiveTab} variant="primary">
  <Tabs.List>
    <Tabs.Indicator />
    <Tabs.Trigger value="settings">
      <Tabs.Label>Settings</Tabs.Label>
    </Tabs.Trigger>
    <Tabs.Trigger value="profile">
      <Tabs.Label>Profile</Tabs.Label>
    </Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="settings">...</Tabs.Content>
  <Tabs.Content value="profile">...</Tabs.Content>
</Tabs>
```

#### Secondary Variant

Underline style indicator for a more minimal appearance.

```tsx
<Tabs value={activeTab} onValueChange={setActiveTab} variant="secondary">
  <Tabs.List>
    <Tabs.Indicator />
    <Tabs.Trigger value="overview">
      <Tabs.Label>Overview</Tabs.Label>
    </Tabs.Trigger>
    <Tabs.Trigger value="analytics">
      <Tabs.Label>Analytics</Tabs.Label>
    </Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="overview">...</Tabs.Content>
  <Tabs.Content value="analytics">...</Tabs.Content>
</Tabs>
```

#### Scrollable Tabs

Handle many tabs with horizontal scrolling.

```tsx
<Tabs value={activeTab} onValueChange={setActiveTab}>
  <Tabs.List>
    <Tabs.ScrollView scrollAlign="center">
      <Tabs.Indicator />
      <Tabs.Trigger value="tab1">
        <Tabs.Label>First Tab</Tabs.Label>
      </Tabs.Trigger>
      <Tabs.Trigger value="tab2">
        <Tabs.Label>Second Tab</Tabs.Label>
      </Tabs.Trigger>
      <Tabs.Trigger value="tab3">
        <Tabs.Label>Third Tab</Tabs.Label>
      </Tabs.Trigger>
      <Tabs.Trigger value="tab4">
        <Tabs.Label>Fourth Tab</Tabs.Label>
      </Tabs.Trigger>
      <Tabs.Trigger value="tab5">
        <Tabs.Label>Fifth Tab</Tabs.Label>
      </Tabs.Trigger>
    </Tabs.ScrollView>
  </Tabs.List>
  <Tabs.Content value="tab1">...</Tabs.Content>
  <Tabs.Content value="tab2">...</Tabs.Content>
  <Tabs.Content value="tab3">...</Tabs.Content>
  <Tabs.Content value="tab4">...</Tabs.Content>
  <Tabs.Content value="tab5">...</Tabs.Content>
</Tabs>
```

#### Disabled Tabs

Disable specific tabs to prevent interaction.

```tsx
<Tabs value={activeTab} onValueChange={setActiveTab}>
  <Tabs.List>
    <Tabs.Indicator />
    <Tabs.Trigger value="active">
      <Tabs.Label>Active</Tabs.Label>
    </Tabs.Trigger>
    <Tabs.Trigger value="disabled" isDisabled>
      <Tabs.Label>Disabled</Tabs.Label>
    </Tabs.Trigger>
    <Tabs.Trigger value="another">
      <Tabs.Label>Another</Tabs.Label>
    </Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="active">...</Tabs.Content>
  <Tabs.Content value="another">...</Tabs.Content>
</Tabs>
```

#### With Icons

Combine icons with labels for enhanced visual context.

```tsx
<Tabs value={activeTab} onValueChange={setActiveTab}>
  <Tabs.List>
    <Tabs.Indicator />
    <Tabs.Trigger value="home">
      <Icon name="home" size={16} />
      <Tabs.Label>Home</Tabs.Label>
    </Tabs.Trigger>
    <Tabs.Trigger value="search">
      <Icon name="search" size={16} />
      <Tabs.Label>Search</Tabs.Label>
    </Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="home">...</Tabs.Content>
  <Tabs.Content value="search">...</Tabs.Content>
</Tabs>
```

#### With Render Function

Use a render function on `Tabs.Trigger` to access state and customize content based on selection.

```tsx
<Tabs value={activeTab} onValueChange={setActiveTab}>
  <Tabs.List>
    <Tabs.Indicator />
    <Tabs.Trigger value="settings">
      {({ isSelected, value, isDisabled }) => (
        <Tabs.Label
          className={isSelected ? 'text-accent font-medium' : 'text-foreground'}
        >
          Settings
        </Tabs.Label>
      )}
    </Tabs.Trigger>
    <Tabs.Trigger value="profile">
      {({ isSelected }) => (
        <>
          <Icon name="user" size={16} />
          <Tabs.Label className={isSelected ? 'text-accent' : 'text-muted'}>
            Profile
          </Tabs.Label>
        </>
      )}
    </Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="settings">...</Tabs.Content>
  <Tabs.Content value="profile">...</Tabs.Content>
</Tabs>
```

#### With Separators

Add visual separators between tabs that show when the active tab is not between specified values.

```tsx
<Tabs value={activeTab} onValueChange={setActiveTab}>
  <Tabs.List>
    <Tabs.ScrollView>
      <Tabs.Indicator />
      <Tabs.Trigger value="general">
        <Tabs.Label>General</Tabs.Label>
      </Tabs.Trigger>
      <Tabs.Separator betweenValues={['general', 'notifications']} />
      <Tabs.Trigger value="notifications">
        <Tabs.Label>Notifications</Tabs.Label>
      </Tabs.Trigger>
      <Tabs.Separator betweenValues={['notifications', 'profile']} />
      <Tabs.Trigger value="profile">
        <Tabs.Label>Profile</Tabs.Label>
      </Tabs.Trigger>
    </Tabs.ScrollView>
  </Tabs.List>
  <Tabs.Content value="general">...</Tabs.Content>
  <Tabs.Content value="notifications">...</Tabs.Content>
  <Tabs.Content value="profile">...</Tabs.Content>
</Tabs>
```

### Example

```tsx
import {
  Button,
  Checkbox,
  Description,
  ControlField,
  Label,
  Tabs,
  TextField,
} from 'heroui-native';
import { useState } from 'react';
import { View, Text } from 'react-native';
import Animated, {
  FadeIn,
  FadeOut,
  LinearTransition,
} from 'react-native-reanimated';

const AnimatedContentContainer = ({
  children,
}: {
  children: React.ReactNode;
}) => (
  <Animated.View
    entering={FadeIn.duration(200)}
    exiting={FadeOut.duration(200)}
    className="gap-6"
  >
    {children}
  </Animated.View>
);

export default function TabsExample() {
  const [activeTab, setActiveTab] = useState('general');

  const [showSidebar, setShowSidebar] = useState(true);
  const [accountActivity, setAccountActivity] = useState(true);
  const [name, setName] = useState('');

  return (
    <Tabs value={activeTab} onValueChange={setActiveTab} variant="primary">
      <Tabs.List>
        <Tabs.ScrollView>
          <Tabs.Indicator />
          <Tabs.Trigger value="general">
            <Tabs.Label>General</Tabs.Label>
          </Tabs.Trigger>
          <Tabs.Trigger value="notifications">
            <Tabs.Label>Notifications</Tabs.Label>
          </Tabs.Trigger>
          <Tabs.Trigger value="profile">
            <Tabs.Label>Profile</Tabs.Label>
          </Tabs.Trigger>
        </Tabs.ScrollView>
      </Tabs.List>

      <Animated.View
        layout={LinearTransition.duration(200)}
        className="px-4 py-6 border border-border rounded-xl"
      >
        <Tabs.Content value="general">
          <AnimatedContentContainer>
            <ControlField
              isSelected={showSidebar}
              onSelectedChange={setShowSidebar}
            >
              <ControlField.Indicator variant="checkbox" />
              <View className="flex-1">
                <Label>Show sidebar</Label>
                <Description>Display the sidebar navigation panel</Description>
              </View>
            </ControlField>
          </AnimatedContentContainer>
        </Tabs.Content>

        <Tabs.Content value="notifications">
          <AnimatedContentContainer>
            <ControlField
              isSelected={accountActivity}
              onSelectedChange={setAccountActivity}
            >
              <ControlField.Indicator variant="checkbox" />
              <View className="flex-1">
                <Label>Account activity</Label>
                <Description>
                  Notifications about your account activity
                </Description>
              </View>
            </ControlField>
          </AnimatedContentContainer>
        </Tabs.Content>

        <Tabs.Content value="profile">
          <AnimatedContentContainer>
            <TextField isRequired>
              <Label>Name</Label>
              <Input
                value={name}
                onChangeText={setName}
                placeholder="Enter your full name"
              />
            </TextField>
            <Button size="sm" className="self-start">
              <Button.Label>Update profile</Button.Label>
            </Button>
          </AnimatedContentContainer>
        </Tabs.Content>
      </Animated.View>
    </Tabs>
  );
}
```

You can find more examples in the [GitHub repository](<https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/tabs.tsx>).

### API Reference

#### Tabs

| prop            | type                         | default     | description                                                                               |
| --------------- | ---------------------------- | ----------- | ----------------------------------------------------------------------------------------- |
| `children`      | `React.ReactNode`            | -           | Children elements to be rendered inside tabs                                              |
| `value`         | `string`                     | -           | Currently active tab value                                                                |
| `variant`       | `'primary' \| 'secondary'`   | `'primary'` | Visual variant of the tabs                                                                |
| `className`     | `string`                     | -           | Additional CSS classes for the container                                                  |
| `animation`     | `"disable-all" \| undefined` | `undefined` | Animation configuration. Use `"disable-all"` to disable all animations including children |
| `onValueChange` | `(value: string) => void`    | -           | Callback when the active tab changes                                                      |
| `...ViewProps`  | `ViewProps`                  | -           | All standard React Native View props are supported                                        |

#### Tabs.List

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Children elements to be rendered inside the list   |
| `className`    | `string`          | -       | Additional CSS classes                             |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported |

#### Tabs.ScrollView

| prop                        | type                                     | default    | description                                              |
| --------------------------- | ---------------------------------------- | ---------- | -------------------------------------------------------- |
| `children`                  | `React.ReactNode`                        | -          | Children elements to be rendered inside the scroll view  |
| `scrollAlign`               | `'start' \| 'center' \| 'end' \| 'none'` | `'center'` | Scroll alignment variant for the selected item           |
| `className`                 | `string`                                 | -          | Additional CSS classes for the scroll view               |
| `contentContainerClassName` | `string`                                 | -          | Additional CSS classes for the content container         |
| `...ScrollViewProps`        | `ScrollViewProps`                        | -          | All standard React Native ScrollView props are supported |

#### Tabs.Trigger

| prop                | type                                                                      | default | description                                                               |
| ------------------- | ------------------------------------------------------------------------- | ------- | ------------------------------------------------------------------------- |
| `children`          | `React.ReactNode \| ((props: TabsTriggerRenderProps) => React.ReactNode)` | -       | Children elements to be rendered inside the trigger, or a render function |
| `value`             | `string`                                                                  | -       | The unique value identifying this tab                                     |
| `isDisabled`        | `boolean`                                                                 | `false` | Whether the trigger is disabled                                           |
| `className`         | `string`                                                                  | -       | Additional CSS classes                                                    |
| `...PressableProps` | `PressableProps`                                                          | -       | All standard React Native Pressable props are supported                   |

##### TabsTriggerRenderProps

When using a render function for `children`, the following props are provided:

| property     | type      | description                                |
| ------------ | --------- | ------------------------------------------ |
| `isSelected` | `boolean` | Whether this trigger is currently selected |
| `value`      | `string`  | The value of the trigger                   |
| `isDisabled` | `boolean` | Whether the trigger is disabled            |

#### Tabs.Label

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Text content to be rendered as label               |
| `className`    | `string`          | -       | Additional CSS classes                             |
| `...TextProps` | `TextProps`       | -       | All standard React Native Text props are supported |

#### Tabs.Indicator

| prop                    | type                     | default | description                                                  |
| ----------------------- | ------------------------ | ------- | ------------------------------------------------------------ |
| `children`              | `React.ReactNode`        | -       | Custom indicator content                                     |
| `className`             | `string`                 | -       | Additional CSS classes                                       |
| `animation`             | `TabsIndicatorAnimation` | -       | Animation configuration                                      |
| `isAnimatedStyleActive` | `boolean`                | `true`  | Whether animated styles (react-native-reanimated) are active |
| `...Animated.ViewProps` | `Animated.ViewProps`     | -       | All Reanimated Animated.View props are supported             |

##### TabsIndicatorAnimation

Animation configuration for Tabs.Indicator component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                | type                                   | default                                                                      | description                                     |
| ------------------- | -------------------------------------- | ---------------------------------------------------------------------------- | ----------------------------------------------- |
| `state`             | `'disabled' \| boolean`                | -                                                                            | Disable animations while customizing properties |
| `width.type`        | `'spring' \| 'timing'`                 | `'spring'`                                                                   | Type of animation to use                        |
| `width.config`      | `WithSpringConfig \| WithTimingConfig` | `{ stiffness: 1200, damping: 120 }` (spring) or `{ duration: 200 }` (timing) | Reanimated animation configuration              |
| `height.type`       | `'spring' \| 'timing'`                 | `'spring'`                                                                   | Type of animation to use                        |
| `height.config`     | `WithSpringConfig \| WithTimingConfig` | `{ stiffness: 1200, damping: 120 }` (spring) or `{ duration: 200 }` (timing) | Reanimated animation configuration              |
| `translateX.type`   | `'spring' \| 'timing'`                 | `'spring'`                                                                   | Type of animation to use                        |
| `translateX.config` | `WithSpringConfig \| WithTimingConfig` | `{ stiffness: 1200, damping: 120 }` (spring) or `{ duration: 200 }` (timing) | Reanimated animation configuration              |

#### Tabs.Separator

| prop                    | type                     | default | description                                                                                                                            |
| ----------------------- | ------------------------ | ------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `betweenValues`         | `string[]`               | -       | Array of tab values between which the separator should be visible. The separator shows when the current tab value is NOT in this array |
| `isAlwaysVisible`       | `boolean`                | `false` | If true, opacity is always 1 regardless of the current tab value                                                                       |
| `className`             | `string`                 | -       | Additional CSS classes                                                                                                                 |
| `animation`             | `TabsSeparatorAnimation` | -       | Animation configuration                                                                                                                |
| `isAnimatedStyleActive` | `boolean`                | `true`  | Whether animated styles (react-native-reanimated) are active                                                                           |
| `children`              | `React.ReactNode`        | -       | Custom separator content                                                                                                               |
| `...Animated.ViewProps` | `Animated.ViewProps`     | -       | All Reanimated Animated.View props are supported                                                                                       |

**Note:** The following style properties are occupied by animations and cannot be set via className:

- `opacity` - Animated for separator visibility transitions (0 when current tab is in `betweenValues`, 1 when not)

To customize these properties, use the `animation` prop. To completely disable animated styles and use your own via className or style prop, set `isAnimatedStyleActive={false}`.

##### TabsSeparatorAnimation

Animation configuration for Tabs.Separator component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                   | type                    | default             | description                                     |
| ---------------------- | ----------------------- | ------------------- | ----------------------------------------------- |
| `state`                | `'disabled' \| boolean` | -                   | Disable animations while customizing properties |
| `opacity.value`        | `[number, number]`      | `[0, 1]`            | Opacity values [hidden, visible]                |
| `opacity.timingConfig` | `WithTimingConfig`      | `{ duration: 200 }` | Animation timing configuration                  |

#### Tabs.Content

| prop           | type              | default | description                                         |
| -------------- | ----------------- | ------- | --------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Children elements to be rendered inside the content |
| `value`        | `string`          | -       | The value of the tab this content belongs to        |
| `className`    | `string`          | -       | Additional CSS classes                              |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported  |

### Hooks

#### useTabs

Hook to access tabs root context values within custom components or compound components.

```tsx
import { useTabs } from 'heroui-native';

const CustomComponent = () => {
  const { value, onValueChange, nativeID } = useTabs();
  // ... your implementation
};
```

**Returns:** `UseTabsReturn`

| property        | type                      | description                                |
| --------------- | ------------------------- | ------------------------------------------ |
| `value`         | `string`                  | Currently active tab value                 |
| `onValueChange` | `(value: string) => void` | Callback function to change the active tab |
| `nativeID`      | `string`                  | Unique identifier for the tabs instance    |

**Note:** This hook must be used within a `Tabs` component. It will throw an error if called outside of the tabs context.

#### useTabsMeasurements

Hook to access tab measurements context values for managing tab trigger positions and dimensions.

```tsx
import { useTabsMeasurements } from 'heroui-native';

const CustomIndicator = () => {
  const { measurements, variant } = useTabsMeasurements();
  // ... your implementation
};
```

**Returns:** `UseTabsMeasurementsReturn`

| property          | type                                                    | description                                       |
| ----------------- | ------------------------------------------------------- | ------------------------------------------------- |
| `measurements`    | `Record<string, ItemMeasurements>`                      | Record of measurements for each tab trigger       |
| `setMeasurements` | `(key: string, measurements: ItemMeasurements) => void` | Function to update measurements for a tab trigger |
| `variant`         | `'primary' \| 'secondary'`                              | Visual variant of the tabs                        |

##### ItemMeasurements

| property | type     | description                         |
| -------- | -------- | ----------------------------------- |
| `width`  | `number` | Width of the tab trigger in pixels  |
| `height` | `number` | Height of the tab trigger in pixels |
| `x`      | `number` | X position of the tab trigger       |

**Note:** This hook must be used within a `Tabs` component. It will throw an error if called outside of the tabs context.

#### useTabsTrigger

Hook to access tab trigger context values within custom components or compound components.

```tsx
import { useTabsTrigger } from 'heroui-native';

const CustomLabel = () => {
  const { value, isSelected, nativeID } = useTabsTrigger();
  // ... your implementation
};
```

**Returns:** `UseTabsTriggerReturn`

| property     | type      | description                                |
| ------------ | --------- | ------------------------------------------ |
| `value`      | `string`  | The value of this trigger                  |
| `nativeID`   | `string`  | Unique identifier for this trigger         |
| `isSelected` | `boolean` | Whether this trigger is currently selected |

**Note:** This hook must be used within a `Tabs.Trigger` component. It will throw an error if called outside of the trigger context.

### Examples from tabs.md

# Tabs Component

The Tabs component organizes content into separate views.

#### Installation

```bash
npm install heroui-native @react-navigation/elements react-native-reanimated uniwind
```

#### Import

```tsx
import { Tabs, Button, ControlField, Description, FieldError, Input, Label, Radio, RadioGroup, TextField } from 'heroui-native';
```

#### Components

- `Tabs` - Root container component
- `Tabs.List` - Container for tab triggers
- `Tabs.ScrollView` - Scrollable tabs container
- `Tabs.Indicator` - Active tab indicator
- `Tabs.Trigger` - Individual tab button
- `Tabs.Label` - Tab label text
- `Tabs.Content` - Tab panel content

#### Props

##### Tabs Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `value` | `string` | - | Controlled active tab value |
| `defaultValue` | `string` | - | Default active tab |
| `onValueChange` | `(value: string) => void` | - | Tab change handler |
| `variant` | `'primary' \| 'secondary'` | `'primary'` | Visual style variant |

##### Tabs.Trigger Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `value` | `string` | required | Tab identifier |

---

#### Example 1: Primary Variant (Pill Style)

A pill-style tab navigation with a complete settings panel.

```tsx
import { useHeaderHeight } from '@react-navigation/elements';
import {
  Button,
  cn,
  ControlField,
  Description,
  FieldError,
  Input,
  Label,
  Radio,
  RadioGroup,
  Tabs,
  TextField,
} from 'heroui-native';
import { useState } from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
  FadeIn,
  FadeOut,
  LinearTransition,
} from 'react-native-reanimated';
import { withUniwind } from 'uniwind';

const StyleAnimatedView = withUniwind(Animated.View);

const DURATION = 200;

/**
 * AnimatedContentContainer - Wrapper for animated tab content
 */
const AnimatedContentContainer = ({
  children,
}: {
  children: React.ReactNode;
}) => (
  <StyleAnimatedView
    entering={FadeIn.duration(DURATION)}
    exiting={FadeOut.duration(DURATION)}
    className="gap-6"
  >
    {children}
  </StyleAnimatedView>
);

interface FormErrors {
  name?: string;
  username?: string;
}

interface TabsContentProps {
  variant: 'primary' | 'secondary';
}

interface TabTriggerProps {
  value: string;
  label: string;
}

/**
 * TabTrigger - Individual tab button
 */
const TabTrigger = ({ value, label }: TabTriggerProps) => {
  return (
    <Tabs.Trigger value={value}>
      <Tabs.Label>{label}</Tabs.Label>
    </Tabs.Trigger>
  );
};

/**
 * TabsContent - Main content with all tabs
 */
const TabsContent = ({ variant }: TabsContentProps) => {
  const [activeTab, setActiveTab] = useState('general');

  const [homepage] = useState('heroui.com');
  const [showSidebar, setShowSidebar] = useState(true);
  const [showStatusBar, setShowStatusBar] = useState(false);

  const [theme, setTheme] = useState('auto');
  const [fontSize, setFontSize] = useState('medium');

  const [accountActivity, setAccountActivity] = useState(true);
  const [mentions, setMentions] = useState(true);
  const [directMessages, setDirectMessages] = useState(false);
  const [marketingEmail, setMarketingEmail] = useState(false);

  const [name, setName] = useState('');
  const [username, setUsername] = useState('');
  const [errors, setErrors] = useState<FormErrors>({});

  /**
   * Validate profile form
   */
  const validateProfile = (): boolean => {
    const newErrors: FormErrors = {};

    if (!name.trim()) {
      newErrors.name = 'Name is required';
    }

    if (!username.trim()) {
      newErrors.username = 'Username is required';
    } else if (!/^[a-zA-Z0-9_]{3,20}$/.test(username)) {
      newErrors.username =
        'Username must be 3-20 characters (letters, numbers, underscore only)';
    }

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  /**
   * Handle profile update
   */
  const handleUpdateProfile = () => {
    if (validateProfile()) {
      console.log('Profile updated:', { name, username });
    }
  };

  return (
    <Tabs
      variant={variant}
      value={activeTab}
      onValueChange={setActiveTab}
      className={cn('gap-1.5', variant === 'secondary' && 'gap-0')}
    >
      <Tabs.List
        className={cn('border-b-0', variant === 'secondary' && 'mx-4')}
      >
        <Tabs.ScrollView contentContainerClassName="gap-1">
          <Tabs.Indicator />
          <TabTrigger value="general" label="General" />
          <TabTrigger value="appearance" label="Appearance" />
          <TabTrigger value="notifications" label="Notifications" />
          <TabTrigger value="profile" label="Profile" />
        </Tabs.ScrollView>
      </Tabs.List>
      <StyleAnimatedView
        layout={LinearTransition.duration(DURATION)}
        className={cn(
          'px-2 py-6',
          variant === 'secondary' &&
            'px-5 border border-foreground/10 rounded-2xl'
        )}
        style={styles.borderCurve}
      >
        {/* General Tab */}
        <Tabs.Content value="general">
          <AnimatedContentContainer>
            <TextField>
              <Label>Homepage</Label>
              <Input value={homepage} />
            </TextField>

            <ControlField
              isSelected={showSidebar}
              onSelectedChange={setShowSidebar}
            >
              <ControlField.Indicator variant="checkbox" />
              <View className="flex-1">
                <Label>Show sidebar</Label>
                <Description>Display the sidebar navigation panel</Description>
              </View>
            </ControlField>

            <ControlField
              isSelected={showStatusBar}
              onSelectedChange={setShowStatusBar}
            >
              <ControlField.Indicator variant="checkbox" />
              <View className="flex-1">
                <Label>Show status bar</Label>
                <Description>Display the status bar at the bottom</Description>
              </View>
            </ControlField>
          </AnimatedContentContainer>
        </Tabs.Content>

        {/* Appearance Tab */}
        <Tabs.Content value="appearance">
          <AnimatedContentContainer>
            <RadioGroup value={theme} onValueChange={setTheme} className="mb-6">
              <View className="mb-2">
                <Label>Theme</Label>
                <Description>Select your preferred color theme</Description>
              </View>
              <View className="gap-3">
                <RadioGroup.Item value="auto" className="self-start">
                  <Radio />
                  <Label>Auto</Label>
                </RadioGroup.Item>
                <RadioGroup.Item value="light" className="self-start">
                  <Radio />
                  <Label>Light</Label>
                </RadioGroup.Item>
                <RadioGroup.Item value="dark" className="self-start">
                  <Radio />
                  <Label>Dark</Label>
                </RadioGroup.Item>
              </View>
            </RadioGroup>

            <RadioGroup value={fontSize} onValueChange={setFontSize}>
              <View className="mb-2">
                <Label>Font Size</Label>
                <Description>
                  Adjust the text size throughout the app
                </Description>
              </View>
              <View className="gap-3">
                <RadioGroup.Item value="small" className="self-start">
                  <Radio />
                  <Label>Small</Label>
                </RadioGroup.Item>
                <RadioGroup.Item value="medium" className="self-start">
                  <Radio />
                  <Label>Medium</Label>
                </RadioGroup.Item>
                <RadioGroup.Item value="large" className="self-start">
                  <Radio />
                  <Label>Large</Label>
                </RadioGroup.Item>
              </View>
            </RadioGroup>
          </AnimatedContentContainer>
        </Tabs.Content>

        {/* Notifications Tab */}
        <Tabs.Content value="notifications">
          <AnimatedContentContainer>
            <ControlField
              isSelected={accountActivity}
              onSelectedChange={setAccountActivity}
            >
              <ControlField.Indicator variant="checkbox" />
              <View className="flex-1">
                <Label>Account activity</Label>
                <Description>
                  Notifications about your account activity
                </Description>
              </View>
            </ControlField>

            <ControlField isSelected={mentions} onSelectedChange={setMentions}>
              <ControlField.Indicator variant="checkbox" />
              <View className="flex-1">
                <Label>Mentions</Label>
                <Description>
                  When someone mentions you in a comment
                </Description>
              </View>
            </ControlField>

            <ControlField
              isSelected={directMessages}
              onSelectedChange={setDirectMessages}
            >
              <ControlField.Indicator variant="checkbox" />
              <View className="flex-1">
                <Label>Direct messages</Label>
                <Description>Notifications for new direct messages</Description>
              </View>
            </ControlField>

            <ControlField
              isSelected={marketingEmail}
              onSelectedChange={setMarketingEmail}
            >
              <ControlField.Indicator variant="checkbox" />
              <View className="flex-1">
                <Label>Marketing email</Label>
                <Description>
                  Receive emails about new features and updates
                </Description>
              </View>
            </ControlField>
          </AnimatedContentContainer>
        </Tabs.Content>

        {/* Profile Tab */}
        <Tabs.Content value="profile">
          <AnimatedContentContainer>
            <TextField isRequired isInvalid={!!errors.name}>
              <Label>Name</Label>
              <Input
                value={name}
                onChangeText={(text) => {
                  setName(text);
                  if (errors.name) {
                    setErrors((prev) => ({ ...prev, name: undefined }));
                  }
                }}
                placeholder="Enter your full name"
              />
              <FieldError>{errors.name}</FieldError>
            </TextField>

            <TextField isRequired isInvalid={!!errors.username}>
              <Label>Username</Label>
              <Input
                value={username}
                onChangeText={(text) => {
                  setUsername(text);
                  if (errors.username) {
                    setErrors((prev) => ({ ...prev, username: undefined }));
                  }
                }}
                placeholder="Enter username"
                autoCapitalize="none"
              />
              <Description hideOnInvalid>
                3-20 characters, letters, numbers only
              </Description>
              <FieldError>{errors.username}</FieldError>
            </TextField>

            <Button
              variant="secondary"
              size="sm"
              className="self-start px-6"
              onPress={handleUpdateProfile}
            >
              <Button.Label className="text-base">Update profile</Button.Label>
            </Button>
          </AnimatedContentContainer>
        </Tabs.Content>
      </StyleAnimatedView>
    </Tabs>
  );
};

/**
 * PillVariantContent - Primary variant example
 */
const PillVariantContent = () => {
  const headerHeight = useHeaderHeight();

  return (
    <View className="flex-1 px-5" style={{ paddingTop: headerHeight + 20 }}>
      <TabsContent variant="primary" />
    </View>
  );
};
```

---

#### Example 2: Secondary Variant (Line Style)

A line-style tab navigation with border styling.

```tsx
import { useHeaderHeight } from '@react-navigation/elements';
import { View } from 'react-native';

/**
 * LineVariantContent - Secondary variant example
 */
const LineVariantContent = () => {
  const headerHeight = useHeaderHeight();

  return (
    <View className="flex-1 px-5" style={{ paddingTop: headerHeight + 20 }}>
      <TabsContent variant="secondary" />
    </View>
  );
};
```

---

#### Complete Usage Example

```tsx
import { Tabs } from 'heroui-native';
import { useState } from 'react';
import { View, Text } from 'react-native';

export default function TabsExample() {
  const [activeTab, setActiveTab] = useState('tab1');

  return (
    <View className="flex-1">
      <Tabs value={activeTab} onValueChange={setActiveTab}>
        <Tabs.List>
          <Tabs.Trigger value="tab1">
            <Tabs.Label>Tab 1</Tabs.Label>
          </Tabs.Trigger>
          <Tabs.Trigger value="tab2">
            <Tabs.Label>Tab 2</Tabs.Label>
          </Tabs.Trigger>
          <Tabs.Trigger value="tab3">
            <Tabs.Label>Tab 3</Tabs.Label>
          </Tabs.Trigger>
        </Tabs.List>

        <Tabs.Content value="tab1">
          <View className="p-4">
            <Text className="text-foreground">Content for Tab 1</Text>
          </View>
        </Tabs.Content>

        <Tabs.Content value="tab2">
          <View className="p-4">
            <Text className="text-foreground">Content for Tab 2</Text>
          </View>
        </Tabs.Content>

        <Tabs.Content value="tab3">
          <View className="p-4">
            <Text className="text-foreground">Content for Tab 3</Text>
          </View>
        </Tabs.Content>
      </Tabs>
    </View>
  );
}

const styles = StyleSheet.create({
  borderCurve: {
    borderCurve: 'continuous',
  },
});
```

---

## ListGroup

---
title: ListGroup
description: A Surface-based container that groups related list items with consistent layout and spacing.
icon: new
links:
  source_native: list-group/list-group.tsx
  styles_native: list-group/list-group.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/list-group-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/list-group-docs-dark.mp4"
/>

### Import

```tsx
import { ListGroup } from 'heroui-native';
```

### Anatomy

```tsx
<ListGroup>
  <ListGroup.Item>
    <ListGroup.ItemPrefix>...</ListGroup.ItemPrefix>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>...</ListGroup.ItemTitle>
      <ListGroup.ItemDescription>...</ListGroup.ItemDescription>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix />
  </ListGroup.Item>
</ListGroup>
```

- **ListGroup**: Surface-based root container that groups related list items. Supports all Surface variants (default, secondary, tertiary, transparent).
- **ListGroup.Item**: Pressable horizontal flex-row container for a single item, providing consistent spacing and alignment.
- **ListGroup.ItemPrefix**: Optional leading content slot for icons, avatars, or other visual elements.
- **ListGroup.ItemContent**: Flex-1 wrapper for title and description, occupying the remaining horizontal space.
- **ListGroup.ItemTitle**: Primary text label styled with foreground color and medium font weight.
- **ListGroup.ItemDescription**: Secondary text styled with muted color and smaller font size.
- **ListGroup.ItemSuffix**: Optional trailing content slot. Renders a chevron-right icon by default; accepts children to override the default icon.

### Usage

#### Basic Usage

The ListGroup component uses compound parts to create grouped list items with title and description.

```tsx
<ListGroup>
  <ListGroup.Item>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>Personal Info</ListGroup.ItemTitle>
      <ListGroup.ItemDescription>
        Name, email, phone number
      </ListGroup.ItemDescription>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix />
  </ListGroup.Item>
  <Separator className="mx-4" />
  <ListGroup.Item>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>Payment Methods</ListGroup.ItemTitle>
      <ListGroup.ItemDescription>
        Visa ending in 4829
      </ListGroup.ItemDescription>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix />
  </ListGroup.Item>
</ListGroup>
```

#### With Icons

Add leading icons using the `ListGroup.ItemPrefix` slot.

```tsx
<ListGroup>
  <ListGroup.Item>
    <ListGroup.ItemPrefix>
      <Icon name="person-outline" size={22} />
    </ListGroup.ItemPrefix>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>Profile</ListGroup.ItemTitle>
      <ListGroup.ItemDescription>
        Name, photo, bio
      </ListGroup.ItemDescription>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix />
  </ListGroup.Item>
  <Separator className="mx-4" />
  <ListGroup.Item>
    <ListGroup.ItemPrefix>
      <Icon name="lock-closed-outline" size={22} />
    </ListGroup.ItemPrefix>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>Security</ListGroup.ItemTitle>
      <ListGroup.ItemDescription>
        Password, 2FA
      </ListGroup.ItemDescription>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix />
  </ListGroup.Item>
</ListGroup>
```

#### Title Only

Omit `ListGroup.ItemDescription` to display title-only items.

```tsx
<ListGroup>
  <ListGroup.Item>
    <ListGroup.ItemPrefix>
      <Icon name="wifi-outline" size={22} />
    </ListGroup.ItemPrefix>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>Wi-Fi</ListGroup.ItemTitle>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix />
  </ListGroup.Item>
  <Separator className="mx-4" />
  <ListGroup.Item>
    <ListGroup.ItemPrefix>
      <Icon name="bluetooth-outline" size={22} />
    </ListGroup.ItemPrefix>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>Bluetooth</ListGroup.ItemTitle>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix />
  </ListGroup.Item>
</ListGroup>
```

#### Surface Variant

Apply a different visual variant to the root container.

```tsx
<ListGroup variant="transparent">
  <ListGroup.Item>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>Wi-Fi</ListGroup.ItemTitle>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix />
  </ListGroup.Item>
</ListGroup>
```

#### Custom Suffix

Override the default chevron icon by passing children to `ListGroup.ItemSuffix`.

```tsx
<ListGroup>
  <ListGroup.Item>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>Language</ListGroup.ItemTitle>
      <ListGroup.ItemDescription>English</ListGroup.ItemDescription>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix>
      <Icon name="arrow-forward" size={18} />
    </ListGroup.ItemSuffix>
  </ListGroup.Item>
  <Separator className="mx-4" />
  <ListGroup.Item>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>Notifications</ListGroup.ItemTitle>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix>
      <Chip variant="primary" color="danger">
        <Chip.Label className="font-bold">7</Chip.Label>
      </Chip>
    </ListGroup.ItemSuffix>
  </ListGroup.Item>
</ListGroup>
```

#### Custom Suffix Icon Props

Customise the default chevron icon size and color using `iconProps`.

```tsx
<ListGroup>
  <ListGroup.Item>
    <ListGroup.ItemContent>
      <ListGroup.ItemTitle>Storage</ListGroup.ItemTitle>
      <ListGroup.ItemDescription>
        12.4 GB of 50 GB used
      </ListGroup.ItemDescription>
    </ListGroup.ItemContent>
    <ListGroup.ItemSuffix iconProps={{ size: 18, color: mutedColor }} />
  </ListGroup.Item>
</ListGroup>
```

#### With PressableFeedback

Wrap items with `PressableFeedback` to add scale and ripple press feedback animations. When using this pattern, pass `onPress` on `PressableFeedback` instead of `ListGroup.Item` and disable the item with `disabled` prop.

```tsx
import { ListGroup, PressableFeedback, Separator } from 'heroui-native';

<ListGroup>
  <PressableFeedback animation={false} onPress={() => {}}>
    <PressableFeedback.Scale>
      <ListGroup.Item disabled>
        <ListGroup.ItemContent>
          <ListGroup.ItemTitle>Appearance</ListGroup.ItemTitle>
          <ListGroup.ItemDescription>
            Theme, font size, display
          </ListGroup.ItemDescription>
        </ListGroup.ItemContent>
        <ListGroup.ItemSuffix />
      </ListGroup.Item>
    </PressableFeedback.Scale>
    <PressableFeedback.Ripple />
  </PressableFeedback>
  <Separator className="mx-4" />
  <PressableFeedback animation={false} onPress={() => {}}>
    <PressableFeedback.Scale>
      <ListGroup.Item disabled>
        <ListGroup.ItemContent>
          <ListGroup.ItemTitle>Notifications</ListGroup.ItemTitle>
          <ListGroup.ItemDescription>
            Alerts, sounds, badges
          </ListGroup.ItemDescription>
        </ListGroup.ItemContent>
        <ListGroup.ItemSuffix />
      </ListGroup.Item>
    </PressableFeedback.Scale>
    <PressableFeedback.Ripple />
  </PressableFeedback>
</ListGroup>
```

### Example

```tsx
import { Ionicons } from '@expo/vector-icons';
import { ListGroup, Separator, useThemeColor } from 'heroui-native';
import { View, Text } from 'react-native';
import { withUniwind } from 'uniwind';

const StyledIonicons = withUniwind(Ionicons);

export default function ListGroupExample() {
  const mutedColor = useThemeColor('muted');

  return (
    <View className="flex-1 justify-center px-5">
      <Text className="text-sm text-muted mb-2 ml-2">Account</Text>
      <ListGroup className="mb-6">
        <ListGroup.Item>
          <ListGroup.ItemPrefix>
            <StyledIonicons
              name="person-outline"
              size={22}
              className="text-foreground"
            />
          </ListGroup.ItemPrefix>
          <ListGroup.ItemContent>
            <ListGroup.ItemTitle>Personal Info</ListGroup.ItemTitle>
            <ListGroup.ItemDescription>
              Name, email, phone number
            </ListGroup.ItemDescription>
          </ListGroup.ItemContent>
          <ListGroup.ItemSuffix />
        </ListGroup.Item>
        <Separator className="mx-4" />
        <ListGroup.Item>
          <ListGroup.ItemPrefix>
            <StyledIonicons
              name="card-outline"
              size={22}
              className="text-foreground"
            />
          </ListGroup.ItemPrefix>
          <ListGroup.ItemContent>
            <ListGroup.ItemTitle>Payment Methods</ListGroup.ItemTitle>
            <ListGroup.ItemDescription>
              Visa ending in 4829
            </ListGroup.ItemDescription>
          </ListGroup.ItemContent>
          <ListGroup.ItemSuffix />
        </ListGroup.Item>
      </ListGroup>
      <Text className="text-sm text-muted mb-2 ml-2">Preferences</Text>
      <ListGroup>
        <ListGroup.Item>
          <ListGroup.ItemPrefix>
            <StyledIonicons
              name="color-palette-outline"
              size={22}
              className="text-foreground"
            />
          </ListGroup.ItemPrefix>
          <ListGroup.ItemContent>
            <ListGroup.ItemTitle>Appearance</ListGroup.ItemTitle>
            <ListGroup.ItemDescription>
              Theme, font size, display
            </ListGroup.ItemDescription>
          </ListGroup.ItemContent>
          <ListGroup.ItemSuffix />
        </ListGroup.Item>
        <Separator className="mx-4" />
        <ListGroup.Item>
          <ListGroup.ItemPrefix>
            <StyledIonicons
              name="notifications-outline"
              size={22}
              className="text-foreground"
            />
          </ListGroup.ItemPrefix>
          <ListGroup.ItemContent>
            <ListGroup.ItemTitle>Notifications</ListGroup.ItemTitle>
            <ListGroup.ItemDescription>
              Alerts, sounds, badges
            </ListGroup.ItemDescription>
          </ListGroup.ItemContent>
          <ListGroup.ItemSuffix iconProps={{ size: 18, color: mutedColor }} />
        </ListGroup.Item>
      </ListGroup>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/beta/example/src/app/(home)/components/list-group.tsx).

### API Reference

#### ListGroup

| prop           | type                                                       | default     | description                                        |
| -------------- | ---------------------------------------------------------- | ----------- | -------------------------------------------------- |
| `children`     | `React.ReactNode`                                          | -           | Children elements to be rendered inside the group  |
| `variant`      | `'default' \| 'secondary' \| 'tertiary' \| 'transparent'` | `'default'` | Visual variant of the underlying Surface container |
| `className`    | `string`                                                   | -           | Additional CSS classes for the root container      |
| `...ViewProps` | `ViewProps`                                                | -           | All standard React Native View props are supported |

#### ListGroup.Item

| prop                | type              | default | description                                             |
| ------------------- | ----------------- | ------- | ------------------------------------------------------- |
| `children`          | `React.ReactNode` | -       | Children elements to be rendered inside the item        |
| `className`         | `string`          | -       | Additional CSS classes for the item                     |
| `...PressableProps` | `PressableProps`  | -       | All standard React Native Pressable props are supported |

#### ListGroup.ItemPrefix

| prop           | type              | default | description                                            |
| -------------- | ----------------- | ------- | ------------------------------------------------------ |
| `children`     | `React.ReactNode` | -       | Leading content such as icons or avatars               |
| `className`    | `string`          | -       | Additional CSS classes for the prefix                  |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported     |

#### ListGroup.ItemContent

| prop           | type              | default | description                                            |
| -------------- | ----------------- | ------- | ------------------------------------------------------ |
| `children`     | `React.ReactNode` | -       | Content area, typically title and description          |
| `className`    | `string`          | -       | Additional CSS classes for the content area            |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported     |

#### ListGroup.ItemTitle

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Title text or custom content                       |
| `className`    | `string`          | -       | Additional CSS classes for the title               |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported |

#### ListGroup.ItemDescription

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Description text or custom content                 |
| `className`    | `string`          | -       | Additional CSS classes for the description         |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported |

#### ListGroup.ItemSuffix

| prop           | type                 | default | description                                                                        |
| -------------- | -------------------- | ------- | ---------------------------------------------------------------------------------- |
| `children`     | `React.ReactNode`    | -       | Custom trailing content; overrides the default chevron-right icon when provided    |
| `className`    | `string`             | -       | Additional CSS classes for the suffix                                              |
| `iconProps`    | `ListGroupIconProps` | -       | Props to customise the default chevron-right icon. Only applies when no children   |
| `...ViewProps` | `ViewProps`          | -       | All standard React Native View props are supported                                 |

##### ListGroupIconProps

| prop    | type     | default             | description                        |
| ------- | -------- | ------------------- | ---------------------------------- |
| `size`  | `number` | `16`                | Size of the chevron icon in pixels |
| `color` | `string` | theme `muted` color | Color of the chevron icon          |

### Examples from list-group.md

# List Group Component

The List Group component displays a list of related items.

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { ListGroup } from 'heroui-native';
```

#### Components

- `ListGroup` - Root container component
- `ListGroup.Item` - Individual list item
- `ListGroup.ItemText` - Item text content
- `ListGroup.ItemIcon` - Item icon

#### Props

##### ListGroup Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'default' \| 'surface'` | `'default'` | Visual style variant |
| `className` | `string` | - | Additional CSS classes |

##### ListGroup.Item Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `onPress` | `() => void` | - | Press handler |
| `className` | `string` | - | Additional CSS classes |

#### Example: Basic List Group

```tsx
import { ListGroup } from 'heroui-native';
import { View } from 'react-native';

function BasicListGroupExample() {
  const items = [
    { id: '1', title: 'Profile', icon: 'user' },
    { id: '2', title: 'Settings', icon: 'settings' },
    { id: '3', title: 'Notifications', icon: 'bell' },
    { id: '4', title: 'Security', icon: 'lock' },
  ];

  return (
    <View className="p-5">
      <ListGroup>
        {items.map((item) => (
          <ListGroup.Item key={item.id} onPress={() => {}}>
            <ListGroup.ItemIcon name={item.icon} />
            <ListGroup.ItemText>{item.title}</ListGroup.ItemText>
          </ListGroup.Item>
        ))}
      </ListGroup>
    </View>
  );
}
```

#### Example: Surface List Group

```tsx
import { ListGroup } from 'heroui-native';
import { View } from 'react-native';

function SurfaceListGroupExample() {
  return (
    <View className="p-5">
      <ListGroup variant="surface">
        <ListGroup.Item onPress={() => {}}>
          <ListGroup.ItemText>Account</ListGroup.ItemText>
        </ListGroup.Item>
        <ListGroup.Item onPress={() => {}}>
          <ListGroup.ItemText>Privacy</ListGroup.ItemText>
        </ListGroup.Item>
        <ListGroup.Item onPress={() => {}}>
          <ListGroup.ItemText>Help</ListGroup.ItemText>
        </ListGroup.Item>
      </ListGroup>
    </View>
  );
}
```

#### Complete Example Code

```tsx
import { ListGroup } from 'heroui-native';
import { View } from 'react-native';

export default function ListGroupExample() {
  const items = [
    { id: '1', title: 'Profile' },
    { id: '2', title: 'Settings' },
    { id: '3', title: 'Notifications' },
    { id: '4', title: 'Security' },
  ];

  return (
    <View className="flex-1 p-5 justify-center">
      <ListGroup>
        {items.map((item) => (
          <ListGroup.Item key={item.id} onPress={() => {}}>
            <ListGroup.ItemText>{item.title}</ListGroup.ItemText>
          </ListGroup.Item>
        ))}
      </ListGroup>
    </View>
  );
}
```
