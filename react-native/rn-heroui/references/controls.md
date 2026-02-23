# Control Components

## Table of Contents
- [Switch](#switch)
- [Slider](#slider)

---

## Switch

A toggle control that allows users to switch between on and off states.

### Import

```tsx
import { Switch } from 'heroui-native';
```

### Anatomy

```tsx
<Switch>
  <Switch.Thumb>...</Switch.Thumb>
  <Switch.StartContent>...</Switch.StartContent>
  <Switch.EndContent>...</Switch.EndContent>
</Switch>
```

- **Switch**: Main container that handles toggle state and user interaction. Renders default thumb if no children provided. Animates scale (on press) and background color based on selection state. Acts as a pressable area for toggling.
- **Switch.Thumb**: Optional sliding thumb element that moves between positions. Uses spring animation for smooth transitions. Can contain custom content like icons or be customized with different styles and animations.
- **Switch.StartContent**: Optional content displayed on the left side of the switch. Typically used for icons or text that appear when switch is off. Positioned absolutely within the switch container.
- **Switch.EndContent**: Optional content displayed on the right side of the switch. Typically used for icons or text that appear when switch is on. Positioned absolutely within the switch container.

### Usage

#### Basic Usage

The Switch component renders with default thumb if no children provided.

```tsx
<Switch isSelected={isSelected} onSelectedChange={setIsSelected} />
```

#### With Custom Thumb

Replace the default thumb with custom content using the Thumb component.

```tsx
<Switch isSelected={isSelected} onSelectedChange={setIsSelected}>
  <Switch.Thumb>...</Switch.Thumb>
</Switch>
```

#### With Start and End Content

Add icons or text that appear on each side of the switch.

```tsx
<Switch isSelected={isSelected} onSelectedChange={setIsSelected}>
  <Switch.Thumb />
  <Switch.StartContent>...</Switch.StartContent>
  <Switch.EndContent>...</Switch.EndContent>
</Switch>
```

#### With Render Function

Use render functions for dynamic content based on switch state.

```tsx
<Switch isSelected={isSelected} onSelectedChange={setIsSelected}>
  {({ isSelected, isDisabled }) => (
    <>
      <Switch.Thumb>
        {({ isSelected }) => (isSelected ? <CheckIcon /> : <XIcon />)}
      </Switch.Thumb>
    </>
  )}
</Switch>
```

#### With Custom Animations

Customize animations for the switch root and thumb components.

```tsx
<Switch
  animation={{
    scale: {
      value: [1, 0.9],
      timingConfig: { duration: 200 },
    },
    backgroundColor: {
      value: ['#172554', '#eab308'],
    },
  }}
>
  <Switch.Thumb
    animation={{
      left: {
        value: 4,
        springConfig: {
          damping: 30,
          stiffness: 300,
          mass: 1,
        },
      },
      backgroundColor: {
        value: ['#dbeafe', '#854d0e'],
      },
    }}
  />
</Switch>
```

#### Disable Animations

Disable animations entirely or only for specific components.

```tsx
{
  /* Disable all animations including children */
}
<Switch animation="disable-all">
  <Switch.Thumb />
</Switch>;

{
  /* Disable only root animations, thumb can still animate */
}
<Switch>
  <Switch.Thumb animation={false} />
</Switch>;
```

### Example

```tsx
import { Switch } from 'heroui-native';
import { Ionicons } from '@expo/vector-icons';
import React from 'react';
import { View } from 'react-native';
import Animated, { ZoomIn } from 'react-native-reanimated';

export default function SwitchExample() {
  const [darkMode, setDarkMode] = React.useState(false);

  return (
    <View className="flex-row gap-4">
      <Switch
        isSelected={darkMode}
        onSelectedChange={setDarkMode}
        className="w-[56px] h-[32px]"
        animation={{
          backgroundColor: {
            value: ['#172554', '#eab308'],
          },
        }}
      >
        <Switch.Thumb
          className="size-[22px]"
          animation={{
            left: {
              value: 4,
              springConfig: {
                damping: 30,
                stiffness: 300,
                mass: 1,
              },
            },
          }}
        />
        <Switch.StartContent className="left-2">
          {darkMode && (
            <Animated.View key="sun" entering={ZoomIn.springify()}>
              <Ionicons name="sunny" size={16} color="#854d0e" />
            </Animated.View>
          )}
        </Switch.StartContent>
        <Switch.EndContent className="right-2">
          {!darkMode && (
            <Animated.View key="moon" entering={ZoomIn.springify()}>
              <Ionicons name="moon" size={16} color="#dbeafe" />
            </Animated.View>
          )}
        </Switch.EndContent>
      </Switch>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/switch.tsx).

### API Reference

#### Switch

| prop                        | type                                                                 | default     | description                                                  |
| --------------------------- | -------------------------------------------------------------------- | ----------- | ------------------------------------------------------------ |
| `children`                  | `React.ReactNode \| ((props: SwitchRenderProps) => React.ReactNode)` | `undefined` | Content to render inside the switch, or a render function    |
| `isSelected`                | `boolean`                                                            | `undefined` | Whether the switch is currently selected                     |
| `isDisabled`                | `boolean`                                                            | `false`     | Whether the switch is disabled and cannot be interacted with |
| `className`                 | `string`                                                             | `undefined` | Custom class name for the switch                             |
| `animation`                 | `SwitchRootAnimation`                                                | -           | Animation configuration                                      |
| `isAnimatedStyleActive`     | `boolean`                                                            | `true`      | Whether animated styles (react-native-reanimated) are active |
| `onSelectedChange`          | `(isSelected: boolean) => void`                                      | -           | Callback fired when the switch selection state changes       |
| `...AnimatedPressableProps` | `AnimatedProps<PressableProps>`                                      | -           | All React Native Reanimated Pressable props are supported    |

#### SwitchRenderProps

| prop         | type      | description                    |
| ------------ | --------- | ------------------------------ |
| `isSelected` | `boolean` | Whether the switch is selected |
| `isDisabled` | `boolean` | Whether the switch is disabled |

#### SwitchRootAnimation

Animation configuration for Switch component. Can be:

- `false` or `"disabled"`: Disable only root animations
- `"disable-all"`: Disable all animations including children
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                           | type                                     | default                                                        | description                                     |
| ------------------------------ | ---------------------------------------- | -------------------------------------------------------------- | ----------------------------------------------- |
| `state`                        | `'disabled' \| 'disable-all' \| boolean` | -                                                              | Disable animations while customizing properties |
| `scale.value`                  | `[number, number]`                       | `[1, 0.96]`                                                    | Scale values [unpressed, pressed]               |
| `scale.timingConfig`           | `WithTimingConfig`                       | `{ duration: 150 }`                                            | Animation timing configuration                  |
| `backgroundColor.value`        | `[string, string]`                       | Uses theme colors                                              | Background color values [unselected, selected]  |
| `backgroundColor.timingConfig` | `WithTimingConfig`                       | `{ duration: 175, easing: Easing.bezier(0.25, 0.1, 0.25, 1) }` | Animation timing configuration                  |

#### Switch.Thumb

| prop                    | type                                                                 | default     | description                                                  |
| ----------------------- | -------------------------------------------------------------------- | ----------- | ------------------------------------------------------------ |
| `children`              | `React.ReactNode \| ((props: SwitchRenderProps) => React.ReactNode)` | `undefined` | Content to render inside the thumb, or a render function     |
| `className`             | `string`                                                             | `undefined` | Custom class name for the thumb element                      |
| `animation`             | `SwitchThumbAnimation`                                               | -           | Animation configuration                                      |
| `isAnimatedStyleActive` | `boolean`                                                            | `true`      | Whether animated styles (react-native-reanimated) are active |
| `...ViewProps`          | `ViewProps`                                                          | -           | All standard React Native View props are supported           |

#### SwitchThumbAnimation

Animation configuration for Switch.Thumb component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                           | type                    | default                                                        | description                                                             |
| ------------------------------ | ----------------------- | -------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `state`                        | `'disabled' \| boolean` | -                                                              | Disable animations while customizing properties                         |
| `left.value`                   | `number`                | `2`                                                            | Offset value from the edges (left when unselected, right when selected) |
| `left.springConfig`            | `WithSpringConfig`      | `{ damping: 120, stiffness: 1600, mass: 2 }`                   | Spring animation configuration for thumb position                       |
| `backgroundColor.value`        | `[string, string]`      | `['white', theme accent-foreground color]`                     | Background color values [unselected, selected]                          |
| `backgroundColor.timingConfig` | `WithTimingConfig`      | `{ duration: 175, easing: Easing.bezier(0.25, 0.1, 0.25, 1) }` | Animation timing configuration                                          |

#### Switch.StartContent

| prop           | type              | default     | description                                        |
| -------------- | ----------------- | ----------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | `undefined` | Content to render inside the switch content        |
| `className`    | `string`          | `undefined` | Custom class name for the content element          |
| `...ViewProps` | `ViewProps`       | -           | All standard React Native View props are supported |

#### Switch.EndContent

| prop           | type              | default     | description                                        |
| -------------- | ----------------- | ----------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | `undefined` | Content to render inside the switch content        |
| `className`    | `string`          | `undefined` | Custom class name for the content element          |
| `...ViewProps` | `ViewProps`       | -           | All standard React Native View props are supported |

### Hooks

#### useSwitch

A hook that provides access to the Switch context. This is useful when building custom switch components or when you need to access switch state in child components.

**Returns:**

| Property     | Type      | Description                    |
| ------------ | --------- | ------------------------------ |
| `isSelected` | `boolean` | Whether the switch is selected |
| `isDisabled` | `boolean` | Whether the switch is disabled |

**Example:**

```tsx
import { useSwitch } from 'heroui-native';

function CustomSwitchContent() {
  const { isSelected, isDisabled } = useSwitch();

  return (
    <View>
      <Text>Status: {isSelected ? 'On' : 'Off'}</Text>
      {isDisabled && <Text>Disabled</Text>}
    </View>
  );
}

// Usage
<Switch>
  <CustomSwitchContent />
  <Switch.Thumb />
</Switch>;
```

### Special Notes

#### Border Styling

If you need to apply a border to the switch root, use the `outline` style properties instead of `border`. This ensures the border doesn't affect the internal layout calculations for the thumb position:

```tsx
<Switch className="outline outline-accent">
  <Switch.Thumb />
</Switch>
```

Using `outline` keeps the border visual without impacting the switch's internal width calculations, ensuring the thumb animates correctly.

#### Integration with ControlField

The Switch component integrates seamlessly with ControlField for press state sharing:

```tsx
import { Description, ControlField, Label } from 'heroui-native';

<ControlField isSelected={isSelected} onSelectedChange={setIsSelected}>
  <View className="flex-1">
    <Label>Enable notifications</Label>
    <Description>Receive push notifications</Description>
  </View>
  <ControlField.Indicator />
</ControlField>
```

When wrapped in ControlField, the Switch will automatically respond to press events on the entire ControlField container, creating a larger touch target and better user experience.

### Examples from switch.md

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { Switch, ControlField, Label, Description } from 'heroui-native';
```

#### Components

- `Switch` - Root container component
- `Switch.Thumb` - Switch thumb/handle

#### Props

##### Switch Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `isSelected` | `boolean` | `false` | Controlled selected state |
| `onSelectedChange` | `(value: boolean) => void` | - | Selection change handler |
| `isDisabled` | `boolean` | `false` | Disabled state |

#### Example: Basic Switch

```tsx
import { Switch } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function BasicSwitchExample() {
  const [isOn, setIsOn] = useState(false);

  return (
    <View className="flex-1 items-center justify-center">
      <Switch isSelected={isOn} onSelectedChange={setIsOn} />
    </View>
  );
}
```

#### Example: Switch with Label

```tsx
import { Switch, ControlField, Label, Description } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function SwitchWithLabelExample() {
  const [notifications, setNotifications] = useState(true);

  return (
    <View className="px-5">
      <ControlField
        isSelected={notifications}
        onSelectedChange={setNotifications}
      >
        <View className="flex-1">
          <Label>Push Notifications</Label>
          <Description>Receive notifications about new messages</Description>
        </View>
        <ControlField.Indicator>
          <Switch />
        </ControlField.Indicator>
      </ControlField>
    </View>
  );
}
```

#### Example: Multiple Switches

```tsx
import { Switch, ControlField, Label, Surface } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function MultipleSwitchesExample() {
  const [settings, setSettings] = useState({
    wifi: true,
    bluetooth: false,
    airplane: false,
  });

  return (
    <Surface className="p-5">
      <ControlField
        isSelected={settings.wifi}
        onSelectedChange={(v) => setSettings({ ...settings, wifi: v })}
      >
        <Label>Wi-Fi</Label>
        <ControlField.Indicator>
          <Switch />
        </ControlField.Indicator>
      </ControlField>

      <ControlField
        isSelected={settings.bluetooth}
        onSelectedChange={(v) => setSettings({ ...settings, bluetooth: v })}
      >
        <Label>Bluetooth</Label>
        <ControlField.Indicator>
          <Switch />
        </ControlField.Indicator>
      </ControlField>

      <ControlField
        isSelected={settings.airplane}
        onSelectedChange={(v) => setSettings({ ...settings, airplane: v })}
      >
        <Label>Airplane Mode</Label>
        <ControlField.Indicator>
          <Switch />
        </ControlField.Indicator>
      </ControlField>
    </Surface>
  );
}
```

#### Complete Example Code

```tsx
import { Switch, ControlField, Label, Description } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function SwitchExample() {
  const [isOn, setIsOn] = useState(false);

  return (
    <View className="flex-1 items-center justify-center px-5">
      <ControlField
        isSelected={isOn}
        onSelectedChange={setIsOn}
      >
        <View className="flex-1">
          <Label>Enable Feature</Label>
          <Description>Turn on to access advanced features</Description>
        </View>
        <ControlField.Indicator>
          <Switch />
        </ControlField.Indicator>
      </ControlField>
    </View>
  );
}
```

---

## Slider

A draggable input for selecting a value or range within a bounded interval.

### Import

```tsx
import { Slider } from 'heroui-native';
```

### Anatomy

```tsx
<Slider>
  <Slider.Output />
  <Slider.Track>
    <Slider.Fill />
    <Slider.Thumb />
  </Slider.Track>
</Slider>
```

- **Slider**: Main container that manages slider value state, orientation, and provides context to all sub-components. Supports single value and range modes.
- **Slider.Output**: Optional display of the current value(s). Supports render functions for custom formatting. Shows a formatted value label by default.
- **Slider.Track**: Sizing container for Fill and Thumb elements. Reports its layout size for position calculations. Supports tap-to-position and render-function children for dynamic content (e.g. multiple thumbs for range sliders).
- **Slider.Fill**: Responsive fill bar that stretches the full cross-axis of the Track. Only the main-axis position and size are computed.
- **Slider.Thumb**: Draggable thumb element using react-native-gesture-handler. Centered on the cross-axis by the Track layout. Animates scale on press via react-native-reanimated. Each thumb gets `role="slider"` with full `accessibilityValue`.

### Usage

#### Basic Usage

The Slider component uses compound parts to create a draggable value input.

```tsx
<Slider defaultValue={30}>
  <Slider.Output />
  <Slider.Track>
    <Slider.Fill />
    <Slider.Thumb />
  </Slider.Track>
</Slider>
```

#### With Label and Output

Display a label alongside the current value output.

```tsx
<Slider defaultValue={50}>
  <View className="flex-row items-center justify-between">
    <Label>Volume</Label>
    <Slider.Output />
  </View>
  <Slider.Track>
    <Slider.Fill />
    <Slider.Thumb />
  </Slider.Track>
</Slider>
```

#### Vertical Orientation

Render the slider vertically by setting `orientation` to `"vertical"`.

```tsx
<View className="h-48">
  <Slider defaultValue={50} orientation="vertical">
    <Slider.Track>
      <Slider.Fill />
      <Slider.Thumb />
    </Slider.Track>
  </Slider>
</View>
```

#### Range Slider

Pass an array as the value and use a render function on `Slider.Track` to create multiple thumbs.

```tsx
<Slider
  defaultValue={[200, 800]}
  minValue={0}
  maxValue={1000}
  step={10}
  formatOptions={{ style: 'currency', currency: 'USD' }}
>
  <View className="flex-row items-center justify-between">
    <Label>Price range</Label>
    <Slider.Output />
  </View>
  <Slider.Track>
    {({ state }) => (
      <>
        <Slider.Fill />
        {state.values.map((_, i) => (
          <Slider.Thumb key={i} index={i} />
        ))}
      </>
    )}
  </Slider.Track>
</Slider>
```

#### Controlled Value

Use `value` and `onChange` for controlled mode. The `onChangeEnd` callback fires when a drag or tap interaction completes.

```tsx
const [volume, setVolume] = useState(50);

<Slider value={volume} onChange={setVolume} onChangeEnd={(v) => save(v)}>
  <Slider.Output />
  <Slider.Track>
    <Slider.Fill />
    <Slider.Thumb />
  </Slider.Track>
</Slider>;
```

#### Custom Styling

Apply custom styles using `className`, `classNames`, or `styles` on the thumb and other sub-components.

```tsx
<Slider defaultValue={65}>
  <Slider.Track className="h-3 rounded-full bg-success/10">
    <Slider.Fill className="rounded-full bg-success" />
    <Slider.Thumb
      classNames={{
        thumbContainer: 'size-6 rounded-full bg-success',
        thumbKnob: 'bg-success-foreground rounded-full',
      }}
      animation={{
        scale: { value: [1, 0.7] },
      }}
    />
  </Slider.Track>
</Slider>
```

#### Disabled

Disable the entire slider to prevent interaction.

```tsx
<Slider defaultValue={40} isDisabled>
  <Slider.Track>
    <Slider.Fill />
    <Slider.Thumb />
  </Slider.Track>
</Slider>
```

### Example

```tsx
import { Label, Slider } from 'heroui-native';
import { useState } from 'react';
import { View, Text } from 'react-native';

export default function SliderExample() {
  const [price, setPrice] = useState<number[]>([200, 800]);

  return (
    <View className="px-8 gap-8">
      <Slider defaultValue={30}>
        <View className="flex-row items-center justify-between">
          <Label>Volume</Label>
          <Slider.Output />
        </View>
        <Slider.Track>
          <Slider.Fill />
          <Slider.Thumb />
        </Slider.Track>
      </Slider>

      <Slider
        value={price}
        onChange={setPrice}
        minValue={0}
        maxValue={1000}
        step={10}
        formatOptions={{ style: 'currency', currency: 'USD' }}
      >
        <View className="flex-row items-center justify-between">
          <Label>Price range</Label>
          <Slider.Output />
        </View>
        <Slider.Track>
          {({ state }) => (
            <>
              <Slider.Fill />
              {state.values.map((_, i) => (
                <Slider.Thumb key={i} index={i} />
              ))}
            </>
          )}
        </Slider.Track>
      </Slider>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](<https://github.com/heroui-inc/heroui-native/blob/beta/example/src/app/(home)/components/slider.tsx>).

### API Reference

#### Slider

| prop            | type                                  | default        | description                                                     |
| --------------- | ------------------------------------- | -------------- | --------------------------------------------------------------- |
| `children`      | `React.ReactNode`                     | -              | Children elements to be rendered inside the slider              |
| `value`         | `number \| number[]`                  | -              | Current slider value (controlled mode)                          |
| `defaultValue`  | `number \| number[]`                  | `0`            | Default slider value (uncontrolled mode)                        |
| `minValue`      | `number`                              | `0`            | Minimum value of the slider                                     |
| `maxValue`      | `number`                              | `100`          | Maximum value of the slider                                     |
| `step`          | `number`                              | `1`            | Step increment for the slider                                   |
| `formatOptions` | `Intl.NumberFormatOptions`            | -              | Number format options for value label formatting                |
| `orientation`   | `'horizontal' \| 'vertical'`          | `'horizontal'` | Orientation of the slider                                       |
| `isDisabled`    | `boolean`                             | `false`        | Whether the slider is disabled                                  |
| `className`     | `string`                              | -              | Additional CSS classes                                          |
| `animation`     | `AnimationRootDisableAll`             | -              | Animation configuration for the slider                          |
| `onChange`      | `(value: number \| number[]) => void` | -              | Callback fired when the slider value changes during interaction |
| `onChangeEnd`   | `(value: number \| number[]) => void` | -              | Callback fired when an interaction completes (drag end or tap)  |
| `...ViewProps`  | `ViewProps`                           | -              | All standard React Native View props are supported              |

#### AnimationRootDisableAll

Animation configuration for the slider root component. Can be:

- `"disable-all"`: Disable all animations including children
- `undefined`: Use default animations

#### Slider.Output

| prop           | type                                                                 | default | description                                                                                 |
| -------------- | -------------------------------------------------------------------- | ------- | ------------------------------------------------------------------------------------------- |
| `children`     | `React.ReactNode \| ((props: SliderRenderProps) => React.ReactNode)` | -       | Custom content or render function receiving slider state. Defaults to formatted value label |
| `className`    | `string`                                                             | -       | Additional CSS classes                                                                      |
| `...ViewProps` | `ViewProps`                                                          | -       | All standard React Native View props are supported                                          |

#### SliderRenderProps

| prop          | type                | description                    |
| ------------- | ------------------- | ------------------------------ |
| `state`       | `SliderState`       | Current slider state           |
| `orientation` | `SliderOrientation` | Orientation of the slider      |
| `isDisabled`  | `boolean`           | Whether the slider is disabled |

#### SliderState

| prop                 | type                        | description                                    |
| -------------------- | --------------------------- | ---------------------------------------------- |
| `values`             | `number[]`                  | Current slider value(s) by thumb index         |
| `getThumbValueLabel` | `(index: number) => string` | Returns the formatted string label for a thumb |

#### Slider.Track

| prop           | type                                                                 | default | description                                                                   |
| -------------- | -------------------------------------------------------------------- | ------- | ----------------------------------------------------------------------------- |
| `children`     | `React.ReactNode \| ((props: SliderRenderProps) => React.ReactNode)` | -       | Content or render function receiving slider state for dynamic thumb rendering |
| `className`    | `string`                                                             | -       | Additional CSS classes                                                        |
| `hitSlop`      | `number`                                                             | `8`     | Extra touch area around the track                                             |
| `...ViewProps` | `ViewProps`                                                          | -       | All standard React Native View props are supported                            |

#### Slider.Fill

| prop           | type        | default | description                                        |
| -------------- | ----------- | ------- | -------------------------------------------------- |
| `className`    | `string`    | -       | Additional CSS classes                             |
| `...ViewProps` | `ViewProps` | -       | All standard React Native View props are supported |

#### Slider.Thumb

| prop           | type                                     | default | description                                        |
| -------------- | ---------------------------------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode`                        | -       | Custom thumb content. Defaults to an animated knob |
| `index`        | `number`                                 | `0`     | Index of this thumb within the slider              |
| `isDisabled`   | `boolean`                                | -       | Whether this individual thumb is disabled          |
| `className`    | `string`                                 | -       | Additional CSS classes for the thumb container     |
| `classNames`   | `ElementSlots<ThumbSlots>`               | -       | Additional CSS classes for thumb slots             |
| `styles`       | `Partial<Record<ThumbSlots, ViewStyle>>` | -       | Inline styles for thumb slots                      |
| `hitSlop`      | `number`                                 | `12`    | Extra touch area around the thumb                  |
| `animation`    | `SliderThumbAnimation`                   | -       | Animation configuration for the thumb knob         |
| `...ViewProps` | `ViewProps`                              | -       | All standard React Native View props are supported |

#### ElementSlots\<ThumbSlots\>

| prop             | type     | description                                     |
| ---------------- | -------- | ----------------------------------------------- |
| `thumbContainer` | `string` | Custom class name for the outer thumb container |
| `thumbKnob`      | `string` | Custom class name for the inner thumb knob      |

#### styles

| prop             | type        | description                          |
| ---------------- | ----------- | ------------------------------------ |
| `thumbContainer` | `ViewStyle` | Styles for the outer thumb container |
| `thumbKnob`      | `ViewStyle` | Styles for the inner thumb knob      |

#### SliderThumbAnimation

Animation configuration for the thumb knob scale effect. Can be:

- `false` or `"disabled"`: Disable thumb animation
- `undefined`: Use default animations
- `object`: Custom scale animation configuration

| prop                 | type               | default                                      | description                                     |
| -------------------- | ------------------ | -------------------------------------------- | ----------------------------------------------- |
| `scale.value`        | `[number, number]` | `[1, 0.9]`                                   | Scale values [idle, dragging]                   |
| `scale.springConfig` | `WithSpringConfig` | `{ damping: 15, stiffness: 200, mass: 0.5 }` | Spring animation configuration for scale effect |

### Hooks

#### useSlider

Hook to access the slider context. Must be used within a `Slider` component.

```tsx
import { useSlider } from 'heroui-native';

const { values, orientation, isDisabled, getThumbValueLabel } = useSlider();
```

##### Returns

| property             | type                                         | description                                                    |
| -------------------- | -------------------------------------------- | -------------------------------------------------------------- |
| `values`             | `number[]`                                   | Current slider values (one per thumb)                          |
| `minValue`           | `number`                                     | Minimum value of the slider                                    |
| `maxValue`           | `number`                                     | Maximum value of the slider                                    |
| `step`               | `number`                                     | Step increment                                                 |
| `orientation`        | `'horizontal' \| 'vertical'`                 | Current orientation                                            |
| `isDisabled`         | `boolean`                                    | Whether the slider is disabled                                 |
| `formatOptions`      | `Intl.NumberFormatOptions \| undefined`      | Number format options for labels                               |
| `getThumbPercent`    | `(index: number) => number`                  | Returns the percentage position (0-1) for a given thumb index  |
| `getThumbValueLabel` | `(index: number) => string`                  | Returns the formatted label for a given thumb index            |
| `getThumbMinValue`   | `(index: number) => number`                  | Returns the minimum allowed value for a thumb                  |
| `getThumbMaxValue`   | `(index: number) => number`                  | Returns the maximum allowed value for a thumb                  |
| `updateValue`        | `(index: number, newValue: number) => void`  | Updates a thumb value by index                                 |
| `isThumbDragging`    | `(index: number) => boolean`                 | Returns whether a given thumb is currently being dragged       |
| `setThumbDragging`   | `(index: number, dragging: boolean) => void` | Sets the dragging state of a thumb                             |
| `trackSize`          | `number`                                     | Track layout width (horizontal) or height (vertical) in pixels |
| `thumbSize`          | `number`                                     | Measured thumb size (main-axis dimension) in pixels            |

### Examples from slider.md

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { Slider, Label } from 'heroui-native';
```

#### Components

- `Slider` - Root container component
- `Slider.Track` - Slider track
- `Slider.Fill` - Selected range fill
- `Slider.Thumb` - Draggable thumb
- `Slider.Output` - Value display
- `Slider.Group` - Group for multiple thumbs

#### Props

##### Slider Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `value` | `number \| number[]` | - | Controlled value(s) |
| `defaultValue` | `number \| number[]` | - | Default value(s) |
| `onValueChange` | `(value: number \| number[]) => void` | - | Value change handler |
| `minValue` | `number` | `0` | Minimum value |
| `maxValue` | `number` | `100` | Maximum value |
| `step` | `number` | `1` | Step increment |
| `orientation` | `'horizontal' \| 'vertical'` | `'horizontal'` | Slider orientation |
| `formatOptions` | `Intl.NumberFormatOptions` | - | Number formatting options |

##### Slider.Thumb Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `index` | `number` | - | Thumb index for range sliders |
| `classNames` | `object` | - | Custom class names |
| `animation` | `object` | - | Animation configuration |

---

#### Example 1: Basic Slider

A simple horizontal slider with label and value output.

```tsx
import { Slider, Label } from 'heroui-native';
import { View } from 'react-native';

const BasicContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-8">
      <View className="w-full gap-4">
        <Slider defaultValue={30}>
          <View className="flex-row items-center justify-between">
            <Label>Volume</Label>
            <Slider.Output />
          </View>
          <Slider.Track>
            <Slider.Fill />
            <Slider.Thumb />
          </Slider.Track>
        </Slider>
      </View>
    </View>
  );
};
```

---

#### Example 2: Vertical Slider

Vertical orientation sliders.

```tsx
import { Slider } from 'heroui-native';
import { View } from 'react-native';

const VerticalContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-8">
      <View className="h-48 flex-row gap-12 items-center justify-center">
        <Slider defaultValue={30} orientation="vertical">
          <Slider.Track>
            <Slider.Fill />
            <Slider.Thumb />
          </Slider.Track>
        </Slider>

        <Slider defaultValue={50} orientation="vertical">
          <Slider.Track>
            <Slider.Fill />
            <Slider.Thumb />
          </Slider.Track>
        </Slider>

        <Slider defaultValue={70} orientation="vertical">
          <Slider.Track>
            <Slider.Fill />
            <Slider.Thumb />
          </Slider.Track>
        </Slider>
      </View>
    </View>
  );
};
```

---

#### Example 3: Range Slider

Sliders with multiple thumbs for selecting ranges.

```tsx
import { Slider, Label } from 'heroui-native';
import { View } from 'react-native';

const RangeContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-8">
      <View className="w-full gap-8">
        {/* Percent range */}
        <Slider
          defaultValue={[0.2, 0.75]}
          minValue={0}
          maxValue={1}
          step={0.01}
          formatOptions={{ style: 'percent' }}
        >
          <View className="flex-row items-center justify-between mb-1">
            <Label>Discount</Label>
            <Slider.Output />
          </View>
          <Slider.Track>
            {({ state }) => (
              <>
                <Slider.Fill />
                {state.values.map((_, i) => (
                  <Slider.Thumb key={i} index={i} />
                ))}
              </>
            )}
          </Slider.Track>
        </Slider>

        {/* Price range */}
        <Slider
          defaultValue={[200, 800]}
          minValue={0}
          maxValue={1000}
          step={10}
          formatOptions={{ style: 'currency', currency: 'USD' }}
        >
          <View className="flex-row items-center justify-between mb-1">
            <Label>Price range</Label>
            <Slider.Output />
          </View>
          <Slider.Track>
            {({ state }) => (
              <>
                <Slider.Fill />
                {state.values.map((_, i) => (
                  <Slider.Thumb key={i} index={i} />
                ))}
              </>
            )}
          </Slider.Track>
        </Slider>

        {/* Temperature range */}
        <Slider
          defaultValue={[18, 24]}
          minValue={10}
          maxValue={35}
          step={1}
          formatOptions={{
            style: 'unit',
            unit: 'celsius',
            maximumFractionDigits: 1,
          }}
        >
          <View className="flex-row items-center justify-between mb-1">
            <Label>Comfort zone</Label>
            <Slider.Output />
          </View>
          <Slider.Track>
            {({ state }) => (
              <>
                <Slider.Fill />
                {state.values.map((_, i) => (
                  <Slider.Thumb key={i} index={i} />
                ))}
              </>
            )}
          </Slider.Track>
        </Slider>

        {/* Age range */}
        <Slider defaultValue={[25, 45]} minValue={18} maxValue={65} step={1}>
          <View className="flex-row items-center justify-between mb-1">
            <Label>Age range</Label>
            <Slider.Output />
          </View>
          <Slider.Track>
            {({ state }) => (
              <>
                <Slider.Fill />
                {state.values.map((_, i) => (
                  <Slider.Thumb key={i} index={i} />
                ))}
              </>
            )}
          </Slider.Track>
        </Slider>
      </View>
    </View>
  );
};
```

---

#### Example 4: Custom Styles

Sliders with custom styling and gradients.

```tsx
import { Slider, Label } from 'heroui-native';
import { View } from 'react-native';

const CustomStylesContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-8">
      <View className="w-full gap-8">
        {/* Success / green theme */}
        <Slider defaultValue={65}>
          <View className="flex-row items-center justify-between mb-1">
            <Label>Battery</Label>
            <Slider.Output />
          </View>
          <Slider.Track className="h-3 rounded-full bg-success/10">
            <Slider.Fill
              className="rounded-full"
              style={{
                experimental_backgroundImage:
                  'linear-gradient(to right, #f87171, #facc15, #4ade80)',
              }}
            />
            <Slider.Thumb
              classNames={{
                thumbContainer: 'size-6 rounded-full bg-success',
                thumbKnob: 'bg-success-foreground rounded-full',
              }}
              animation={{
                scale: { value: [1, 0.7] },
              }}
            />
          </Slider.Track>
        </Slider>

        {/* Warning / thin bar */}
        <Slider defaultValue={40}>
          <View className="flex-row items-center justify-between mb-1">
            <Label>Volume</Label>
            <Slider.Output />
          </View>
          <Slider.Track className="h-1 rounded-full bg-warning/15">
            <Slider.Fill className="bg-warning rounded-full" />
            <Slider.Thumb
              classNames={{
                thumbContainer: 'size-5 rounded-sm bg-warning',
                thumbKnob: 'bg-amber-900 border border-warning rounded-sm',
              }}
              animation={{
                scale: { value: [1, 1.5] },
              }}
            />
          </Slider.Track>
        </Slider>

        {/* Gradient / wide track */}
        <Slider defaultValue={70}>
          <View className="flex-row items-center justify-between mb-1">
            <Label>Mood</Label>
            <Slider.Output />
          </View>
          <Slider.Track className="h-7 rounded-2xl bg-default/40">
            <Slider.Fill
              className="rounded-2xl"
              style={{
                experimental_backgroundImage:
                  'linear-gradient(to right, #a78bfa, #7dd3fc, #6ee7b7)',
              }}
            />
            <Slider.Thumb
              classNames={{
                thumbContainer: 'w-5 h-7 rounded-xl bg-teal-200',
                thumbKnob: 'bg-white rounded-xl',
              }}
              animation={{
                scale: { value: [1, 1.25] },
              }}
            />
          </Slider.Track>
        </Slider>
      </View>
    </View>
  );
};
```

---

#### Example 5: Slider Inside Bottom Sheet

Sliders used within a bottom sheet for settings.

```tsx
import { BottomSheet, Button, Label, Slider } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

const InsideBottomSheetContent = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
          <BottomSheet.Trigger asChild>
            <Button variant="secondary" isDisabled={isOpen}>
              Open slider settings
            </Button>
          </BottomSheet.Trigger>
          <BottomSheet.Portal>
            <BottomSheet.Overlay />
            <BottomSheet.Content>
              <BottomSheet.Title className="text-xl font-semibold mb-4">
                Audio settings
              </BottomSheet.Title>
              <View className="gap-6">
                <Slider defaultValue={70}>
                  <View className="flex-row items-center justify-between">
                    <Label>Volume</Label>
                    <Slider.Output />
                  </View>
                  <Slider.Track>
                    <Slider.Fill />
                    <Slider.Thumb />
                  </Slider.Track>
                </Slider>

                <Slider defaultValue={50}>
                  <View className="flex-row items-center justify-between">
                    <Label>Bass</Label>
                    <Slider.Output />
                  </View>
                  <Slider.Track>
                    <Slider.Fill />
                    <Slider.Thumb />
                  </Slider.Track>
                </Slider>

                <Slider defaultValue={40}>
                  <View className="flex-row items-center justify-between">
                    <Label>Treble</Label>
                    <Slider.Output />
                  </View>
                  <Slider.Track>
                    <Slider.Fill />
                    <Slider.Thumb />
                  </Slider.Track>
                </Slider>
              </View>
            </BottomSheet.Content>
          </BottomSheet.Portal>
        </BottomSheet>
      </View>
    </View>
  );
};
```

---

#### Complete Usage Example

```tsx
import { Slider, Label } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function SliderExample() {
  const [value, setValue] = useState(50);

  return (
    <View className="flex-1 items-center justify-center px-8">
      <Slider value={value} onValueChange={setValue}>
        <View className="flex-row items-center justify-between">
          <Label>Brightness</Label>
          <Slider.Output />
        </View>
        <Slider.Track>
          <Slider.Fill />
          <Slider.Thumb />
        </Slider.Track>
      </Slider>
    </View>
  );
}
```
