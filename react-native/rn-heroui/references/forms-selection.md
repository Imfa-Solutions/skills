# Form Selection Components

## Table of Contents
- [Checkbox](#checkbox)
- [RadioGroup](#radiogroup)
- [Select](#select)
- [SearchField](#searchfield)
- [TextArea](#textarea)
- [TextField](#textfield)

---

## Checkbox

---
title: Checkbox
description: A selectable control that allows users to toggle between checked and unchecked states.
links:
  source_native: checkbox/checkbox.tsx
  styles_native: checkbox/checkbox.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/checkbox-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/checkbox-docs-dark.mp4"
/>

### Import

```tsx
import { Checkbox } from 'heroui-native';
```

### Anatomy

```tsx
<Checkbox>
  <Checkbox.Indicator>...</Checkbox.Indicator>
</Checkbox>
```

- **Checkbox**: Main container that handles selection state and user interaction. Renders default indicator with animated checkmark if no children provided. Automatically detects surface context for proper styling. Features press scale animation that can be customized or disabled. Supports render function children to access state (`isSelected`, `isInvalid`, `isDisabled`).
- **Checkbox.Indicator**: Optional checkmark container with default slide, scale, opacity, and border radius animations when selected. Renders animated check icon with SVG path drawing animation if no children provided. All animations can be individually customized or disabled. Supports render function children to access state.

### Usage

#### Basic Usage

The Checkbox component renders with a default animated indicator if no children are provided. It automatically detects whether it's on a surface background for proper styling.

```tsx
<Checkbox isSelected={isSelected} onSelectedChange={setIsSelected} />
```

#### With Custom Indicator

Use a render function in the Indicator to show/hide custom icons based on state.

```tsx
<Checkbox isSelected={isSelected} onSelectedChange={setIsSelected}>
  <Checkbox.Indicator>
    {({ isSelected }) => (isSelected ? <CheckIcon /> : null)}
  </Checkbox.Indicator>
</Checkbox>
```

#### Invalid State

Show validation errors with the `isInvalid` prop, which applies danger color styling.

```tsx
<Checkbox
  isSelected={isSelected}
  onSelectedChange={setIsSelected}
  isInvalid={hasError}
/>
```

#### Custom Animations

Customize or disable animations for both the root checkbox and indicator.

```tsx
{
  /* Disable all animations (root and indicator) */
}
<Checkbox
  animation="disable-all"
  isSelected={isSelected}
  onSelectedChange={setIsSelected}
>
  <Checkbox.Indicator />
</Checkbox>;

{
  /* Disable only root animation */
}
<Checkbox
  animation="disabled"
  isSelected={isSelected}
  onSelectedChange={setIsSelected}
>
  <Checkbox.Indicator />
</Checkbox>;

{
  /* Disable only indicator animation */
}
<Checkbox isSelected={isSelected} onSelectedChange={setIsSelected}>
  <Checkbox.Indicator animation="disabled" />
</Checkbox>;

{
  /* Custom animation configuration */
}
<Checkbox
  animation={{ scale: { value: [1, 0.9], timingConfig: { duration: 200 } } }}
  isSelected={isSelected}
  onSelectedChange={setIsSelected}
>
  <Checkbox.Indicator
    animation={{
      scale: { value: [0.5, 1] },
      opacity: { value: [0, 1] },
      translateX: { value: [-8, 0] },
      borderRadius: { value: [12, 0] },
    }}
  />
</Checkbox>;
```

### Example

```tsx
import {
  Checkbox,
  Description,
  ControlField,
  Label,
  Separator,
  Surface,
} from "heroui-native";
import React from 'react';
import { View, Text } from 'react-native';

interface CheckboxFieldProps {
  isSelected: boolean;
  onSelectedChange: (value: boolean) => void;
  title: string;
  description: string;
}

const CheckboxField: React.FC<CheckboxFieldProps> = ({
  isSelected,
  onSelectedChange,
  title,
  description,
}) => {
  return (
    <ControlField isSelected={isSelected} onSelectedChange={onSelectedChange}>
      <ControlField.Indicator>
        <Checkbox className="mt-0.5" />
      </ControlField.Indicator>
      <View className="flex-1">
        <Label className="text-lg">{title}</Label>
        <Description className="text-base">
          {description}
        </Description>
      </View>
    </ControlField>
  );
};

export default function BasicUsage() {
  const [fields, setFields] = React.useState({
    newsletter: true,
    marketing: false,
    terms: false,
  });

  const fieldConfigs: Record<
    keyof typeof fields,
    { title: string; description: string }
  > = {
    newsletter: {
      title: 'Subscribe to newsletter',
      description: 'Get weekly updates about new features and tips',
    },
    marketing: {
      title: 'Marketing communications',
      description: 'Receive promotional emails and special offers',
    },
    terms: {
      title: 'Accept terms and conditions',
      description: 'Agree to our Terms of Service and Privacy Policy',
    },
  };

  const handleFieldChange = (key: keyof typeof fields) => (value: boolean) => {
    setFields((prev) => ({ ...prev, [key]: value }));
  };

  const fieldKeys = Object.keys(fields) as Array<keyof typeof fields>;

  return (
    <View className="flex-1 items-center justify-center px-5">
      <Surface className="py-5 w-full">
        {fieldKeys.map((key, index) => (
          <React.Fragment key={key}>
            {index > 0 && <Separator className="my-4" />}
            <CheckboxField
              isSelected={fields[key]}
              onSelectedChange={handleFieldChange(key)}
              title={fieldConfigs[key].title}
              description={fieldConfigs[key].description}
            />
          </React.Fragment>
        ))}
      </Surface>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](<https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/checkbox.tsx>).

### API Reference

#### Checkbox

| prop                    | type                                                                   | default     | description                                                               |
| ----------------------- | ---------------------------------------------------------------------- | ----------- | ------------------------------------------------------------------------- |
| `children`              | `React.ReactNode \| ((props: CheckboxRenderProps) => React.ReactNode)` | `undefined` | Child elements or render function to customize the checkbox               |
| `isSelected`            | `boolean`                                                              | `undefined` | Whether the checkbox is currently selected                                |
| `onSelectedChange`      | `(isSelected: boolean) => void`                                        | `undefined` | Callback fired when the checkbox selection state changes                  |
| `isDisabled`            | `boolean`                                                              | `false`     | Whether the checkbox is disabled and cannot be interacted with            |
| `isInvalid`             | `boolean`                                                              | `false`     | Whether the checkbox is invalid (shows danger color)                      |
| `variant`               | `'primary' \| 'secondary'`                                             | `'primary'` | Variant style for the checkbox                                            |
| `hitSlop`               | `number`                                                               | `6`         | Hit slop for the pressable area                                           |
| `animation`             | `CheckboxRootAnimation`                                                | -           | Animation configuration                                                   |
| `isAnimatedStyleActive` | `boolean`                                                              | `true`      | Whether animated styles (react-native-reanimated) are active              |
| `className`             | `string`                                                               | `undefined` | Additional CSS classes to apply                                           |
| `...PressableProps`     | `PressableProps`                                                       | -           | All standard React Native Pressable props are supported (except disabled) |

##### CheckboxRenderProps

| prop         | type      | description                      |
| ------------ | --------- | -------------------------------- |
| `isSelected` | `boolean` | Whether the checkbox is selected |
| `isInvalid`  | `boolean` | Whether the checkbox is invalid  |
| `isDisabled` | `boolean` | Whether the checkbox is disabled |

##### CheckboxRootAnimation

Animation configuration for checkbox root component. Can be:

- `false` or `"disabled"`: Disable only root animations
- `"disable-all"`: Disable all animations including children
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                 | type                                     | default             | description                                     |
| -------------------- | ---------------------------------------- | ------------------- | ----------------------------------------------- |
| `state`              | `'disabled' \| 'disable-all' \| boolean` | -                   | Disable animations while customizing properties |
| `scale.value`        | `[number, number]`                       | `[1, 0.96]`         | Scale values [unpressed, pressed]               |
| `scale.timingConfig` | `WithTimingConfig`                       | `{ duration: 150 }` | Animation timing configuration                  |

#### Checkbox.Indicator

| prop                    | type                                                                   | default     | description                                                  |
| ----------------------- | ---------------------------------------------------------------------- | ----------- | ------------------------------------------------------------ |
| `children`              | `React.ReactNode \| ((props: CheckboxRenderProps) => React.ReactNode)` | `undefined` | Content or render function for the checkbox indicator        |
| `className`             | `string`                                                               | `undefined` | Additional CSS classes for the indicator                     |
| `iconProps`             | `CheckboxIndicatorIconProps`                                           | `undefined` | Custom props for the default animated check icon             |
| `animation`             | `CheckboxIndicatorAnimation`                                           | -           | Animation configuration                                      |
| `isAnimatedStyleActive` | `boolean`                                                              | `true`      | Whether animated styles (react-native-reanimated) are active |
| `...AnimatedViewProps`  | `AnimatedProps<ViewProps>`                                             | -           | All standard React Native Animated View props are supported  |

##### CheckboxIndicatorIconProps

Props for customizing the default animated check icon.

| prop            | type     | description                                      |
| --------------- | -------- | ------------------------------------------------ |
| `size`          | `number` | Icon size                                        |
| `strokeWidth`   | `number` | Icon stroke width                                |
| `color`         | `string` | Icon color (defaults to theme accent-foreground) |
| `enterDuration` | `number` | Duration of enter animation (check appearing)    |
| `exitDuration`  | `number` | Duration of exit animation (check disappearing)  |

##### CheckboxIndicatorAnimation

Animation configuration for checkbox indicator component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                        | type                    | default             | description                                     |
| --------------------------- | ----------------------- | ------------------- | ----------------------------------------------- |
| `state`                     | `'disabled' \| boolean` | -                   | Disable animations while customizing properties |
| `opacity.value`             | `[number, number]`      | `[0, 1]`            | Opacity values [unselected, selected]           |
| `opacity.timingConfig`      | `WithTimingConfig`      | `{ duration: 100 }` | Animation timing configuration                  |
| `borderRadius.value`        | `[number, number]`      | `[8, 0]`            | Border radius values [unselected, selected]     |
| `borderRadius.timingConfig` | `WithTimingConfig`      | `{ duration: 50 }`  | Animation timing configuration                  |
| `translateX.value`          | `[number, number]`      | `[-4, 0]`           | TranslateX values [unselected, selected]        |
| `translateX.timingConfig`   | `WithTimingConfig`      | `{ duration: 100 }` | Animation timing configuration                  |
| `scale.value`               | `[number, number]`      | `[0.8, 1]`          | Scale values [unselected, selected]             |
| `scale.timingConfig`        | `WithTimingConfig`      | `{ duration: 100 }` | Animation timing configuration                  |

### Hooks

#### useCheckbox

Hook to access checkbox context values within custom components or compound components.

```tsx
import { useCheckbox } from 'heroui-native';

const CustomIndicator = () => {
  const { isSelected, isInvalid, isDisabled } = useCheckbox();
  // ... your implementation
};
```

**Returns:** `UseCheckboxReturn`

| property           | type                                           | description                                                    |
| ------------------ | ---------------------------------------------- | -------------------------------------------------------------- |
| `isSelected`       | `boolean \| undefined`                         | Whether the checkbox is currently selected                     |
| `onSelectedChange` | `((isSelected: boolean) => void) \| undefined` | Callback function to change the checkbox selection state       |
| `isDisabled`       | `boolean`                                      | Whether the checkbox is disabled and cannot be interacted with |
| `isInvalid`        | `boolean`                                      | Whether the checkbox is invalid (shows danger color)           |
| `nativeID`         | `string \| undefined`                          | Native ID for the checkbox element                             |

**Note:** This hook must be used within a `Checkbox` component. It will throw an error if called outside of the checkbox context.

### Examples from checkbox.md

#### Checkbox Component

The Checkbox component allows users to select one or more options from a set.

##### Installation

```bash
npm install heroui-native
```

##### Import

```tsx
import { Checkbox, ControlField, Description, Label, Surface } from 'heroui-native';
```

##### Components

- `Checkbox` - Root container component
- `Checkbox.Indicator` - Visual check indicator

##### Props

###### Checkbox Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `isSelected` | `boolean` | `false` | Controlled selected state |
| `onSelectedChange` | `(value: boolean) => void` | - | Selection change handler |
| `isInvalid` | `boolean` | `false` | Invalid state |
| `isDisabled` | `boolean` | `false` | Disabled state |
| `className` | `string` | - | Additional CSS classes |

###### Checkbox.Indicator Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `className` | `string` | - | Additional CSS classes |
| `iconProps` | `object` | - | Icon customization props |
| `animation` | `object` | - | Animation configuration |

##### Example: Basic Usage with ControlField

```tsx
import { Checkbox, ControlField, Description, Label, Surface } from 'heroui-native';
import React from 'react';
import { View } from 'react-native';

interface CheckboxFieldProps {
  isSelected: boolean;
  onSelectedChange: (value: boolean) => void;
  title: string;
  description: string;
}

const CheckboxField: React.FC<CheckboxFieldProps> = ({
  isSelected,
  onSelectedChange,
  title,
  description,
}) => {
  return (
    <ControlField
      isSelected={isSelected}
      onSelectedChange={onSelectedChange}
      className="items-start"
    >
      <ControlField.Indicator>
        <Checkbox className="mt-0.5" />
      </ControlField.Indicator>
      <View className="flex-1">
        <Label className="text-lg">{title}</Label>
        <Description className="text-base">{description}</Description>
      </View>
    </ControlField>
  );
};

function BasicUsageExample() {
  const [fields, setFields] = React.useState({
    newsletter: true,
    marketing: false,
    terms: false,
  });

  const handleFieldChange = (key: keyof typeof fields) => (value: boolean) => {
    setFields((prev) => ({ ...prev, [key]: value }));
  };

  return (
    <View className="flex-1 items-center justify-center px-5">
      <Surface className="py-5 w-full">
        <CheckboxField
          isSelected={fields.newsletter}
          onSelectedChange={handleFieldChange('newsletter')}
          title="Subscribe to newsletter"
          description="Get weekly updates about new features and tips"
        />
        <Separator className="my-4" />
        <CheckboxField
          isSelected={fields.marketing}
          onSelectedChange={handleFieldChange('marketing')}
          title="Marketing communications"
          description="Receive promotional emails and special offers"
        />
        <Separator className="my-4" />
        <CheckboxField
          isSelected={fields.terms}
          onSelectedChange={handleFieldChange('terms')}
          title="Accept terms and conditions"
          description="Agree to our Terms of Service and Privacy Policy"
        />
      </Surface>
    </View>
  );
}
```

##### Example: States

```tsx
import { Checkbox } from 'heroui-native';

function StatesExample() {
  const [defaultState, setDefaultState] = React.useState(true);
  const [invalid, setInvalid] = React.useState(true);
  const [disabled, setDisabled] = React.useState(true);

  return (
    <View className="flex-row gap-8">
      <View className="items-center gap-2">
        <Checkbox
          isSelected={defaultState}
          onSelectedChange={setDefaultState}
        />
        <Text className="text-xs text-muted">Default</Text>
      </View>
      <View className="items-center gap-2">
        <Checkbox
          isSelected={invalid}
          onSelectedChange={setInvalid}
          isInvalid
        />
        <Text className="text-xs text-muted">Invalid</Text>
      </View>
      <View className="items-center gap-2">
        <Checkbox
          isSelected={disabled}
          onSelectedChange={setDisabled}
          isDisabled
        />
        <Text className="text-xs text-muted">Disabled</Text>
      </View>
    </View>
  );
}
```

##### Example: Custom Styles

```tsx
import { Checkbox } from 'heroui-native';
import Animated, { FadeInLeft, FadeInRight, ZoomIn } from 'react-native-reanimated';

function CustomStylesExample() {
  const [customBackground, setCustomBackground] = React.useState(true);
  const [customBoth, setCustomBoth] = React.useState(true);

  return (
    <View className="flex-1 items-center justify-center gap-12">
      <Checkbox
        isSelected={customBackground}
        onSelectedChange={setCustomBackground}
        className="size-6 rounded-full bg-yellow-400 overflow-visible"
      >
        <Checkbox.Indicator
          className="bg-transparent"
          animation={{
            translateX: { value: [-3, 3] },
          }}
          iconProps={{
            size: 32,
            strokeWidth: 1.5,
            color: 'blue',
            enterDuration: 350,
            exitDuration: 200,
          }}
        />
      </Checkbox>

      <Checkbox
        isSelected={customBoth}
        onSelectedChange={setCustomBoth}
        className="size-12 rounded-full bg-slate-200"
      >
        {({ isSelected }) => {
          return isSelected ? (
            <Animated.View
              key="sunny"
              entering={FadeInLeft.springify()}
              className="absolute inset-0 items-center justify-center rounded-full bg-slate-200"
            >
              <Animated.View entering={ZoomIn.springify()}>
                <Ionicons name="sunny" size={24} color="#1e293b" />
              </Animated.View>
            </Animated.View>
          ) : (
            <Animated.View
              key="moon"
              entering={FadeInRight.springify()}
              className="absolute inset-0 items-center justify-center rounded-full bg-slate-800"
            >
              <Animated.View entering={ZoomIn.springify()}>
                <Ionicons name="moon" size={20} color="#e2e8f0" />
              </Animated.View>
            </Animated.View>
          );
        }}
      </Checkbox>
    </View>
  );
}
```

##### Complete Example Code

```tsx
import { Checkbox, ControlField, Description, Label, Surface } from 'heroui-native';
import React from 'react';
import { View, Text } from 'react-native';

interface CheckboxFieldProps {
  isSelected: boolean;
  onSelectedChange: (value: boolean) => void;
  title: string;
  description: string;
}

const CheckboxField: React.FC<CheckboxFieldProps> = ({
  isSelected,
  onSelectedChange,
  title,
  description,
}) => {
  return (
    <ControlField
      isSelected={isSelected}
      onSelectedChange={onSelectedChange}
      className="items-start"
    >
      <ControlField.Indicator>
        <Checkbox className="mt-0.5" />
      </ControlField.Indicator>
      <View className="flex-1">
        <Label className="text-lg">{title}</Label>
        <Description className="text-base">{description}</Description>
      </View>
    </ControlField>
  );
};

const BasicUsage = () => {
  const [fields, setFields] = React.useState({
    newsletter: true,
    marketing: false,
    terms: false,
  });

  const fieldConfigs = {
    newsletter: {
      title: 'Subscribe to newsletter',
      description: 'Get weekly updates about new features and tips',
    },
    marketing: {
      title: 'Marketing communications',
      description: 'Receive promotional emails and special offers',
    },
    terms: {
      title: 'Accept terms and conditions',
      description: 'Agree to our Terms of Service and Privacy Policy',
    },
  };

  const handleFieldChange = (key: keyof typeof fields) => (value: boolean) => {
    setFields((prev) => ({ ...prev, [key]: value }));
  };

  const fieldKeys = Object.keys(fields) as Array<keyof typeof fields>;

  return (
    <View className="flex-1 items-center justify-center px-5">
      <Surface className="py-5 w-full">
        {fieldKeys.map((key, index) => (
          <React.Fragment key={key}>
            {index > 0 && <Separator className="my-4" />}
            <CheckboxField
              isSelected={fields[key]}
              onSelectedChange={handleFieldChange(key)}
              title={fieldConfigs[key].title}
              description={fieldConfigs[key].description}
            />
          </React.Fragment>
        ))}
      </Surface>
    </View>
  );
};

export default function CheckboxExample() {
  return <BasicUsage />;
}
```

---

## RadioGroup

---
title: RadioGroup
description: A set of radio buttons where only one option can be selected at a time.
links:
  source_native: radio-group/radio-group.tsx
  styles_native: radio-group/radio-group.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/radio-group-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/radio-group-docs-dark.mp4"
/>

### Import

```tsx
import { RadioGroup } from 'heroui-native';
```

### Anatomy

```tsx
<RadioGroup>
  <RadioGroup.Item>
    <Label>...</Label>
    <Description>...</Description>
    <Radio>
      <Radio.Indicator>
        <Radio.IndicatorThumb />
      </Radio.Indicator>
    </Radio>
  </RadioGroup.Item>
  <FieldError>...</FieldError>
</RadioGroup>
```

- **RadioGroup**: Container that manages the selection state of radio items. Supports both horizontal and vertical orientations.
- **RadioGroup.Item**: Individual radio option within a RadioGroup. Must be used inside RadioGroup. Handles selection state and renders a default `<Radio />` indicator when text children are provided. Supports render function children to access state (`isSelected`, `isInvalid`, `isDisabled`).
- **Label**: Optional clickable text label for the radio option. Linked to the radio for accessibility. Use the [Label](./label) component directly.
- **Description**: Optional secondary text below the label. Provides additional context about the radio option. Use the [Description](./description) component directly.
- **Radio**: The [Radio](./radio) component used inside `RadioGroup.Item` to render the radio indicator. Automatically detects the `RadioGroupItem` context and derives `isSelected`, `isDisabled`, `isInvalid`, and `variant` from it.
- **Radio.Indicator**: Optional container for the radio circle. Renders default thumb if no children provided. Manages the visual selection state. See [Radio](./radio) for full API.
- **Radio.IndicatorThumb**: Optional inner circle that appears when selected. Animates scale based on selection. Can be replaced with custom content. See [Radio](./radio) for full API.
- **FieldError**: Error message displayed when radio group is invalid. Shown with animation below the radio group content. Use the [FieldError](./field-error) component directly.

### Usage

#### Basic Usage

RadioGroup with simple string children automatically renders title and indicator.

```tsx
<RadioGroup value={value} onValueChange={setValue}>
  <RadioGroup.Item value="option1">Option 1</RadioGroup.Item>
  <RadioGroup.Item value="option2">Option 2</RadioGroup.Item>
  <RadioGroup.Item value="option3">Option 3</RadioGroup.Item>
</RadioGroup>
```

#### With Descriptions

Add descriptive text below each radio option for additional context.

```tsx
import { RadioGroup, Radio, Label, Description } from 'heroui-native';
import { View } from 'react-native';

<RadioGroup value={value} onValueChange={setValue}>
  <RadioGroup.Item value="standard">
    <View>
      <Label>Standard Shipping</Label>
      <Description>Delivered in 5-7 business days</Description>
    </View>
    <Radio />
  </RadioGroup.Item>
  <RadioGroup.Item value="express">
    <View>
      <Label>Express Shipping</Label>
      <Description>Delivered in 2-3 business days</Description>
    </View>
    <Radio />
  </RadioGroup.Item>
</RadioGroup>;
```

#### Custom Indicator

Replace the default indicator thumb with custom content using `Radio` sub-components.

```tsx
import { RadioGroup, Radio, Label } from 'heroui-native';

<RadioGroup value={value} onValueChange={setValue}>
  <RadioGroup.Item value="custom">
    {({ isSelected }) => (
      <>
        <Label>Custom Option</Label>
        <Radio>
          <Radio.Indicator>
            {isSelected && (
              <Animated.View entering={FadeIn}>
                <Icon name="check" size={12} />
              </Animated.View>
            )}
          </Radio.Indicator>
        </Radio>
      </>
    )}
  </RadioGroup.Item>
</RadioGroup>;
```

#### With Render Function

Use a render function on RadioGroup.Item to access state and customize the entire content.

```tsx
import { RadioGroup, Radio, Label } from 'heroui-native';

<RadioGroup value={value} onValueChange={setValue}>
  <RadioGroup.Item value="option1">
    {({ isSelected, isInvalid, isDisabled }) => (
      <>
        <Label>Option 1</Label>
        <Radio>
          <Radio.Indicator>{isSelected && <CustomIcon />}</Radio.Indicator>
        </Radio>
      </>
    )}
  </RadioGroup.Item>
</RadioGroup>;
```

#### With Error Message

Display validation errors below the radio group.

```tsx
import { RadioGroup, FieldError } from 'heroui-native';

function RadioGroupWithError() {
  const [value, setValue] = React.useState<string | undefined>(undefined);

  return (
    <RadioGroup value={value} onValueChange={setValue} isInvalid={!value}>
      <RadioGroup.Item value="agree">I agree to the terms</RadioGroup.Item>
      <RadioGroup.Item value="disagree">I do not agree</RadioGroup.Item>
      <FieldError isInvalid={!value}>
        Please select an option to continue
      </FieldError>
    </RadioGroup>
  );
}
```

### Example

```tsx
import {
  Description,
  Label,
  Radio,
  RadioGroup,
  Separator,
  Surface,
} from 'heroui-native';
import React from 'react';
import { View } from 'react-native';

export default function RadioGroupExample() {
  const [selection, setSelection] = React.useState('desc1');

  return (
    <Surface className="w-full">
      <RadioGroup value={selection} onValueChange={setSelection}>
        <RadioGroup.Item value="desc1">
          <View>
            <Label>Standard Shipping</Label>
            <Description>Delivered in 5-7 business days</Description>
          </View>
          <Radio />
        </RadioGroup.Item>
        <Separator className="my-1" />
        <RadioGroup.Item value="desc2">
          <View>
            <Label>Express Shipping</Label>
            <Description>Delivered in 2-3 business days</Description>
          </View>
          <Radio />
        </RadioGroup.Item>
        <Separator className="my-1" />
        <RadioGroup.Item value="desc3">
          <View>
            <Label>Overnight Shipping</Label>
            <Description>Delivered next business day</Description>
          </View>
          <Radio />
        </RadioGroup.Item>
      </RadioGroup>
    </Surface>
  );
}
```

You can find more examples in the [GitHub repository](<https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/radio-group.tsx>).

### API Reference

#### RadioGroup

| prop            | type                         | default     | description                                                                               |
| --------------- | ---------------------------- | ----------- | ----------------------------------------------------------------------------------------- |
| `children`      | `React.ReactNode`            | `undefined` | Radio group content                                                                       |
| `value`         | `string \| undefined`        | `undefined` | The currently selected value of the radio group                                           |
| `onValueChange` | `(val: string) => void`      | `undefined` | Callback fired when the selected value changes                                            |
| `isDisabled`    | `boolean`                    | `false`     | Whether the entire radio group is disabled                                                |
| `isInvalid`     | `boolean`                    | `false`     | Whether the radio group is invalid                                                        |
| `variant`       | `'primary' \| 'secondary'`   | `undefined` | Variant style for the radio group (inherited by items if not set on item)                 |
| `animation`     | `"disable-all" \| undefined` | `undefined` | Animation configuration. Use `"disable-all"` to disable all animations including children |
| `className`     | `string`                     | `undefined` | Custom class name                                                                         |
| `...ViewProps`  | `ViewProps`                  | -           | All standard React Native View props are supported                                        |

#### RadioGroup.Item

| prop                | type                                                                         | default     | description                                                               |
| ------------------- | ---------------------------------------------------------------------------- | ----------- | ------------------------------------------------------------------------- |
| `children`          | `React.ReactNode \| ((props: RadioGroupItemRenderProps) => React.ReactNode)` | `undefined` | Radio item content or render function to customize the radio item         |
| `value`             | `string`                                                                     | `undefined` | The value associated with this radio item                                 |
| `isDisabled`        | `boolean`                                                                    | `false`     | Whether this specific radio item is disabled                              |
| `isInvalid`         | `boolean`                                                                    | `false`     | Whether the radio item is invalid                                         |
| `variant`           | `'primary' \| 'secondary'`                                                   | `'primary'` | Variant style for the radio item                                          |
| `hitSlop`           | `number`                                                                     | `6`         | Hit slop for the pressable area                                           |
| `className`         | `string`                                                                     | `undefined` | Custom class name                                                         |
| `...PressableProps` | `PressableProps`                                                             | -           | All standard React Native Pressable props are supported (except disabled) |

##### RadioGroupItemRenderProps

| prop         | type      | description                        |
| ------------ | --------- | ---------------------------------- |
| `isSelected` | `boolean` | Whether the radio item is selected |
| `isInvalid`  | `boolean` | Whether the radio item is invalid  |
| `isDisabled` | `boolean` | Whether the radio item is disabled |

#### Radio (inside RadioGroup.Item)

The [Radio](./radio) component is used inside `RadioGroup.Item` to render the radio indicator. When placed inside a `RadioGroup.Item`, the Radio component automatically detects the `RadioGroupItem` context and derives `isSelected`, `isDisabled`, `isInvalid`, and `variant` from it — no manual prop passing is needed.

Use `<Radio />` for the default indicator, or compose with `Radio.Indicator` and `Radio.IndicatorThumb` for custom styling.

**Note:** For complete Radio prop documentation (including `Radio.Indicator` and `Radio.IndicatorThumb`), see the [Radio component documentation](./radio).

**Note:** For labels, descriptions, and error messages, use the base components directly:

- Use [Label](./label) component for labels
- Use [Description](./description) component for descriptions
- Use [FieldError](./field-error) component for error messages

### Hooks

#### useRadioGroup

**Returns:**

| Property        | Type                       | Description                                    |
| --------------- | -------------------------- | ---------------------------------------------- |
| `value`         | `string \| undefined`      | Currently selected value                       |
| `isDisabled`    | `boolean`                  | Whether the radio group is disabled            |
| `isInvalid`     | `boolean`                  | Whether the radio group is in an invalid state |
| `variant`       | `'primary' \| 'secondary'` | Variant style for the radio group              |
| `onValueChange` | `(value: string) => void`  | Function to change the selected value          |

#### useRadioGroupItem

**Returns:**

| Property           | Type                                           | Description                                                             |
| ------------------ | ---------------------------------------------- | ----------------------------------------------------------------------- |
| `isSelected`       | `boolean`                                      | Whether the radio item is selected                                      |
| `isDisabled`       | `boolean \| undefined`                         | Whether the radio item is disabled                                      |
| `isInvalid`        | `boolean \| undefined`                         | Whether the radio item is invalid                                       |
| `variant`          | `'primary' \| 'secondary' \| undefined`        | Variant style for the radio item                                        |
| `onSelectedChange` | `((isSelected: boolean) => void) \| undefined` | Callback to change the selection state (selects this item in the group) |

### Examples from radio-group.md

#### Radio Group Component

The Radio Group component allows users to select one option from a set.

##### Installation

```bash
npm install heroui-native
```

##### Import

```tsx
import { RadioGroup, Radio, ControlField, Label, Description } from 'heroui-native';
```

##### Components

- `RadioGroup` - Root container component
- `RadioGroup.Item` - Individual radio option
- `Radio` - Radio indicator component

##### Props

###### RadioGroup Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `value` | `string` | - | Controlled selected value |
| `onValueChange` | `(value: string) => void` | - | Value change handler |

###### RadioGroup.Item Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `value` | `string` | required | Radio option value |

##### Example: Basic Radio Group

```tsx
import { RadioGroup, Radio } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function BasicRadioGroupExample() {
  const [selected, setSelected] = useState('option1');

  return (
    <View className="p-5">
      <RadioGroup value={selected} onValueChange={setSelected}>
        <RadioGroup.Item value="option1">
          <Radio />
          <Text className="text-foreground ml-2">Option 1</Text>
        </RadioGroup.Item>
        <RadioGroup.Item value="option2">
          <Radio />
          <Text className="text-foreground ml-2">Option 2</Text>
        </RadioGroup.Item>
        <RadioGroup.Item value="option3">
          <Radio />
          <Text className="text-foreground ml-2">Option 3</Text>
        </RadioGroup.Item>
      </RadioGroup>
    </View>
  );
}
```

##### Example: Radio Group with ControlField

```tsx
import { RadioGroup, ControlField, Label, Description } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function RadioGroupWithControlFieldExample() {
  const [plan, setPlan] = useState('free');

  const plans = [
    { value: 'free', title: 'Free Plan', description: 'Basic features' },
    { value: 'pro', title: 'Pro Plan', description: 'Advanced features' },
    { value: 'enterprise', title: 'Enterprise', description: 'All features' },
  ];

  return (
    <View className="p-5">
      <RadioGroup value={plan} onValueChange={setPlan}>
        {plans.map((p) => (
          <RadioGroup.Item key={p.value} value={p.value}>
            <ControlField.Indicator>
              <Radio />
            </ControlField.Indicator>
            <View className="flex-1">
              <Label>{p.title}</Label>
              <Description>{p.description}</Description>
            </View>
          </RadioGroup.Item>
        ))}
      </RadioGroup>
    </View>
  );
}
```

##### Complete Example Code

```tsx
import { RadioGroup, Radio } from 'heroui-native';
import { useState } from 'react';
import { View, Text } from 'react-native';

export default function RadioGroupExample() {
  const [selected, setSelected] = useState('option1');

  return (
    <View className="flex-1 p-5">
      <RadioGroup value={selected} onValueChange={setSelected}>
        <RadioGroup.Item value="option1">
          <Radio />
          <Text className="text-foreground ml-2">Option 1</Text>
        </RadioGroup.Item>
        <RadioGroup.Item value="option2">
          <Radio />
          <Text className="text-foreground ml-2">Option 2</Text>
        </RadioGroup.Item>
        <RadioGroup.Item value="option3">
          <Radio />
          <Text className="text-foreground ml-2">Option 3</Text>
        </RadioGroup.Item>
      </RadioGroup>
    </View>
  );
}
```

---

## Select

---
title: Select
description: Displays a list of options for the user to pick from — triggered by a button.
icon: updated
links:
  source_native: select/select.tsx
  styles_native: select/select.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/select-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/select-docs-dark.mp4"
/>

### Import

```tsx
import { Select } from 'heroui-native';
```

### Anatomy

```tsx
<Select>
  <Select.Trigger>
    <Select.Value />
    <Select.TriggerIndicator />
  </Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content>
      <Select.Close />
      <Select.ListLabel>...</Select.ListLabel>
      <Select.Item>
        <Select.ItemLabel />
        <Select.ItemDescription>...</Select.ItemDescription>
        <Select.ItemIndicator />
      </Select.Item>
    </Select.Content>
  </Select.Portal>
</Select>
```

- **Select**: Main container that manages open/close state, value selection and provides context to child components.
- **Select.Trigger**: Clickable element that toggles the select visibility. Wraps any child element with press handlers. Supports `variant` prop (`'default'` or `'unstyled'`).
- **Select.Value**: Displays the selected value or placeholder text. Automatically updates when selection changes. Styling changes based on selection state.
- **Select.TriggerIndicator**: Optional visual indicator showing open/close state. Renders an animated chevron icon by default that rotates when the select opens/closes.
- **Select.Portal**: Renders select content in a portal layer above other content. Ensures proper stacking and positioning.
- **Select.Overlay**: Optional background overlay. Can be transparent or semi-transparent to capture outside clicks.
- **Select.Content**: Container for select content with three presentation modes: popover (floating with positioning), bottom sheet modal, or dialog modal.
- **Select.Close**: Close button for the select. Can accept custom children or uses default close icon.
- **Select.ListLabel**: Label for the list of items with pre-styled typography.
- **Select.Item**: Selectable option item. Handles selection state and press events.
- **Select.ItemLabel**: Displays the label text for an item.
- **Select.ItemDescription**: Optional description text for items with muted styling.
- **Select.ItemIndicator**: Optional indicator shown for selected items. Renders a check icon by default.

### Usage

#### Basic Usage

The Select component uses compound parts to create dropdown selection interfaces.

```tsx
<Select>
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover">
      <Select.Item value="1" label="Option 1" />
      <Select.Item value="2" label="Option 2" />
    </Select.Content>
  </Select.Portal>
</Select>
```

#### With Value Display

Display the selected value in the trigger using the Value component.

```tsx
<Select>
  <Select.Trigger>
    <Select.Value placeholder="Choose an option" />
    <Select.TriggerIndicator />
  </Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover">
      <Select.Item value="apple" label="Apple" />
      <Select.Item value="orange" label="Orange" />
      <Select.Item value="banana" label="Banana" />
    </Select.Content>
  </Select.Portal>
</Select>
```

#### Popover Presentation

Use popover presentation for floating content with automatic positioning.

```tsx
<Select>
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover" placement="bottom" align="center">
      <Select.Item value="1" label="Item 1" />
      <Select.Item value="2" label="Item 2" />
    </Select.Content>
  </Select.Portal>
</Select>
```

#### Width Control

Control the width of the select content using the `width` prop. This only works with popover presentation.

```tsx
{
  /* Fixed width in pixels */
}
<Select>
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover" width={280}>
      <Select.Item value="1" label="Item 1" />
    </Select.Content>
  </Select.Portal>
</Select>;

{
  /* Match trigger width */
}
<Select>
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover" width="trigger">
      <Select.Item value="1" label="Item 1" />
    </Select.Content>
  </Select.Portal>
</Select>;

{
  /* Full width (100%) */
}
<Select>
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover" width="full">
      <Select.Item value="1" label="Item 1" />
    </Select.Content>
  </Select.Portal>
</Select>;

{
  /* Auto-size to content (default) */
}
<Select>
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover" width="content-fit">
      <Select.Item value="1" label="Item 1" />
    </Select.Content>
  </Select.Portal>
</Select>;
```

#### Bottom Sheet Presentation

Use bottom sheet for mobile-optimized selection experience.

```tsx
<Select presentation="bottom-sheet">
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="bottom-sheet" snapPoints={['35%']}>
      <Select.Item value="1" label="Item 1" />
      <Select.Item value="2" label="Item 2" />
    </Select.Content>
  </Select.Portal>
</Select>
```

#### Dialog Presentation

Use dialog presentation for centered modal-style selection.

```tsx
<Select presentation="dialog">
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="dialog">
      <Select.Close />
      <Select.ListLabel>Choose an option</Select.ListLabel>
      <Select.Item value="1" label="Item 1" />
      <Select.Item value="2" label="Item 2" />
    </Select.Content>
  </Select.Portal>
</Select>
```

#### Custom Item Content

Customize item appearance with custom content and indicators.

```tsx
<Select>
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover">
      <Select.Item value="us" label="United States">
        <View className="flex-row items-center gap-3 flex-1">
          <Text>🇺🇸</Text>
          <Select.ItemLabel />
        </View>
        <Select.ItemIndicator />
      </Select.Item>
      <Select.Item value="uk" label="United Kingdom">
        <View className="flex-row items-center gap-3 flex-1">
          <Text>🇬🇧</Text>
          <Select.ItemLabel />
        </View>
        <Select.ItemIndicator />
      </Select.Item>
    </Select.Content>
  </Select.Portal>
</Select>
```

#### With Render Function

Use a render function on `Select.Item` to access state and customize content based on selection.

```tsx
<Select>
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover">
      <Select.Item value="us" label="United States">
        {({ isSelected, value, isDisabled }) => (
          <>
            <View className="flex-row items-center gap-3 flex-1">
              <Text>🇺🇸</Text>
              <Select.ItemLabel
                className={
                  isSelected ? 'text-accent font-medium' : 'text-foreground'
                }
              />
            </View>
            <Select.ItemIndicator />
          </>
        )}
      </Select.Item>
      <Select.Item value="uk" label="United Kingdom">
        {({ isSelected }) => (
          <>
            <View className="flex-row items-center gap-3 flex-1">
              <Text>🇬🇧</Text>
              <Select.ItemLabel
                className={
                  isSelected ? 'text-accent font-medium' : 'text-foreground'
                }
              />
            </View>
            <Select.ItemIndicator />
          </>
        )}
      </Select.Item>
    </Select.Content>
  </Select.Portal>
</Select>
```

#### With Item Description

Add descriptions to items for additional context.

```tsx
<Select>
  <Select.Trigger>...</Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover">
      <Select.Item value="basic" label="Basic">
        <View className="flex-1">
          <Select.ItemLabel />
          <Select.ItemDescription>
            Essential features for personal use
          </Select.ItemDescription>
        </View>
        <Select.ItemIndicator />
      </Select.Item>
    </Select.Content>
  </Select.Portal>
</Select>
```

#### With Trigger Indicator

Add a visual indicator to show the open/close state of the select. The indicator rotates when the select opens/closes.

```tsx
<Select>
  <Select.Trigger>
    <Select.Value placeholder="Select one" />
    <Select.TriggerIndicator />
  </Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover">
      <Select.Item value="1" label="Option 1" />
      <Select.Item value="2" label="Option 2" />
    </Select.Content>
  </Select.Portal>
</Select>
```

#### Custom Trigger with Unstyled Variant

Use the `unstyled` variant when composing a custom trigger with other components like Button.

```tsx
<Select>
  <Select.Trigger variant="unstyled" asChild>
    <Button variant="secondary">
      <Select.Value placeholder="Select..." />
      <Select.TriggerIndicator />
    </Button>
  </Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover">
      <Select.Item value="1" label="Option 1" />
      <Select.Item value="2" label="Option 2" />
    </Select.Content>
  </Select.Portal>
</Select>
```

#### Controlled Mode

Control the select state programmatically.

```tsx
const [value, setValue] = useState();
const [isOpen, setIsOpen] = useState(false);

<Select
  value={value}
  onValueChange={setValue}
  isOpen={isOpen}
  onOpenChange={setIsOpen}
>
  <Select.Trigger>
    <Select.Value placeholder="Select..." />
    <Select.TriggerIndicator />
  </Select.Trigger>
  <Select.Portal>
    <Select.Overlay />
    <Select.Content presentation="popover">
      <Select.Item value="1" label="Option 1" />
      <Select.Item value="2" label="Option 2" />
    </Select.Content>
  </Select.Portal>
</Select>;
```

### Example

```tsx
import { Select, Separator } from 'heroui-native';
import React, { useState } from 'react';

type SelectOption = {
  value: string;
  label: string;
};

const US_STATES: SelectOption[] = [
  { value: 'CA', label: 'California' },
  { value: 'NY', label: 'New York' },
  { value: 'TX', label: 'Texas' },
  { value: 'FL', label: 'Florida' },
];

export default function SelectExample() {
  const [value, setValue] = useState<SelectOption | undefined>();

  return (
    <Select value={value} onValueChange={setValue}>
      <Select.Trigger>
        <Select.Value placeholder="Select one" />
        <Select.TriggerIndicator />
      </Select.Trigger>
      <Select.Portal>
        <Select.Overlay />
        <Select.Content presentation="popover" width="trigger">
          <Select.ListLabel className="mb-2">Choose a state</Select.ListLabel>
          {US_STATES.map((state, index) => (
            <React.Fragment key={state.value}>
              <Select.Item value={state.value} label={state.label} />
              {index < US_STATES.length - 1 && <Separator />}
            </React.Fragment>
          ))}
        </Select.Content>
      </Select.Portal>
    </Select>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/select.tsx).

### API Reference

#### Select

| prop            | type                                      | default     | description                                                            |
| --------------- | ----------------------------------------- | ----------- | ---------------------------------------------------------------------- |
| `children`      | `ReactNode`                               | -           | The content of the select                                              |
| `value`         | `SelectOption \| SelectOption[]`                  | -           | The selected value(s) (controlled mode)                                |
| `onValueChange` | `(value: SelectOption \| SelectOption[]) => void` | -           | Callback when the value changes                                        |
| `defaultValue`  | `SelectOption \| SelectOption[]`                  | -           | The default selected value(s) (uncontrolled mode)                      |
| `isOpen`        | `boolean`                                 | -           | Whether the select is open (controlled mode)                           |
| `isDefaultOpen` | `boolean`                                 | -           | Whether the select is open when initially rendered (uncontrolled mode) |
| `onOpenChange`  | `(isOpen: boolean) => void`               | -           | Callback when the select open state changes                            |
| `isDisabled`    | `boolean`                                 | `false`     | Whether the select is disabled                                         |
| `presentation`  | `'popover' \| 'bottom-sheet' \| 'dialog'` | `'popover'` | Presentation mode for the select content                               |
| `animation`     | `SelectRootAnimation`                     | -           | Animation configuration                                                |
| `asChild`       | `boolean`                                 | `false`     | Whether to render as a child element                                   |
| `...ViewProps`  | `ViewProps`                               | -           | All standard React Native View props are supported                     |

##### SelectRootAnimation

Animation configuration for Select component. Can be:

- `false` or `"disabled"`: Disable only root animations
- `"disable-all"`: Disable all animations including children
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop             | type                                             | default | description                                     |
| ---------------- | ------------------------------------------------ | ------- | ----------------------------------------------- |
| `state`          | `'disabled' \| 'disable-all' \| boolean`         | -       | Disable animations while customizing properties |
| `entering.value` | `SpringAnimationConfig \| TimingAnimationConfig` | -       | Animation configuration for when select opens   |
| `exiting.value`  | `SpringAnimationConfig \| TimingAnimationConfig` | -       | Animation configuration for when select closes  |

##### SpringAnimationConfig

| prop     | type               | default | description                               |
| -------- | ------------------ | ------- | ----------------------------------------- |
| `type`   | `'spring'`         | -       | Animation type (must be `'spring'`)       |
| `config` | `WithSpringConfig` | -       | Reanimated spring animation configuration |

##### TimingAnimationConfig

| prop     | type               | default | description                               |
| -------- | ------------------ | ------- | ----------------------------------------- |
| `type`   | `'timing'`         | -       | Animation type (must be `'timing'`)       |
| `config` | `WithTimingConfig` | -       | Reanimated timing animation configuration |

#### Select.Trigger

| prop                | type                      | default     | description                                                                                                       |
| ------------------- | ------------------------- | ----------- | ----------------------------------------------------------------------------------------------------------------- |
| `variant`           | `'default' \| 'unstyled'` | `'default'` | The variant of the trigger. `'default'` applies pre-styled container styles, `'unstyled'` removes default styling |
| `children`          | `ReactNode`               | -           | The trigger element content                                                                                       |
| `className`         | `string`                  | -           | Additional CSS classes for the trigger                                                                            |
| `asChild`           | `boolean`                 | `true`      | Whether to render as a child element                                                                              |
| `isDisabled`        | `boolean`                 | -           | Whether the trigger is disabled                                                                                   |
| `...PressableProps` | `PressableProps`          | -           | All standard React Native Pressable props are supported                                                           |

#### Select.Value

| prop           | type        | default | description                                        |
| -------------- | ----------- | ------- | -------------------------------------------------- |
| `placeholder`  | `string`    | -       | Placeholder text when no value is selected         |
| `className`    | `string`    | -       | Additional CSS classes for the value               |
| `...TextProps` | `TextProps` | -       | All standard React Native Text props are supported |

**Note:** The value component automatically applies different text colors based on selection state:

- When a value is selected: `text-foreground`
- When no value is selected (placeholder): `text-field-placeholder`

#### Select.TriggerIndicator

| prop                    | type                              | default | description                                                  |
| ----------------------- | --------------------------------- | ------- | ------------------------------------------------------------ |
| `children`              | `ReactNode`                       | -       | Custom indicator content. Defaults to animated chevron icon  |
| `className`             | `string`                          | -       | Additional CSS classes for the trigger indicator             |
| `style`                 | `ViewStyle`                       | -       | Custom styles for the trigger indicator                      |
| `iconProps`             | `SelectTriggerIndicatorIconProps` | -       | Chevron icon configuration                                   |
| `animation`             | `SelectTriggerIndicatorAnimation` | -       | Animation configuration                                      |
| `isAnimatedStyleActive` | `boolean`                         | `true`  | Whether animated styles (react-native-reanimated) are active |
| `...ViewProps`          | `ViewProps`                       | -       | All standard React Native View props are supported           |

**Note:** The following style properties are occupied by animations and cannot be set via className:

- `transform` (specifically `rotate`) - Animated for open/close rotation transitions

To customize this property, use the `animation` prop. To completely disable animated styles and use your own via className or style prop, set `isAnimatedStyleActive={false}`.

##### SelectTriggerIndicatorIconProps

| prop    | type     | default | description                                            |
| ------- | -------- | ------- | ------------------------------------------------------ |
| `size`  | `number` | `16`    | Size of the icon                                       |
| `color` | `string` | -       | Color of the icon (defaults to foreground theme color) |

##### SelectTriggerIndicatorAnimation

Animation configuration for Select.TriggerIndicator component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations (rotation from 0 to -180)
- `object`: Custom animation configuration

| prop                    | type                    | default                                      | description                                     |
| ----------------------- | ----------------------- | -------------------------------------------- | ----------------------------------------------- |
| `state`                 | `'disabled' \| boolean` | -                                            | Disable animations while customizing properties |
| `rotation.value`        | `[number, number]`      | `[0, -180]`                                  | Rotation values [closed, open] in degrees       |
| `rotation.springConfig` | `WithSpringConfig`      | `{ damping: 140, stiffness: 1000, mass: 4 }` | Spring animation configuration for rotation     |

#### Select.Portal

| prop                       | type        | default | description                                                                                                                   |
| -------------------------- | ----------- | ------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `children`                 | `ReactNode` | -       | The portal content (required)                                                                                                 |
| `disableFullWindowOverlay` | `boolean`   | `false` | When true on iOS, uses View instead of FullWindowOverlay. Enables element inspector; overlay won't appear above native modals |
| `className`                | `string`    | -       | Additional CSS classes for the portal container                                                                               |
| `hostName`                 | `string`    | -       | Optional name of the host element for the portal                                                                              |
| `forceMount`               | `boolean`   | -       | Whether to force mount the component in the DOM                                                                               |
| `...ViewProps`             | `ViewProps` | -       | All standard React Native View props are supported                                                                            |

#### Select.Overlay

| prop                    | type                     | default | description                                                  |
| ----------------------- | ------------------------ | ------- | ------------------------------------------------------------ |
| `className`             | `string`                 | -       | Additional CSS classes for the overlay                       |
| `animation`             | `SelectOverlayAnimation` | -       | Animation configuration                                      |
| `isAnimatedStyleActive` | `boolean`                | `true`  | Whether animated styles (react-native-reanimated) are active |
| `closeOnPress`          | `boolean`                | `true`  | Whether to close the select when overlay is pressed          |
| `forceMount`            | `boolean`                | -       | Whether to force mount the component in the DOM              |
| `asChild`               | `boolean`                | `false` | Whether to render as a child element                         |
| `...Animated.ViewProps` | `Animated.ViewProps`     | -       | All Reanimated Animated.View props are supported             |

##### SelectOverlayAnimation

Animation configuration for Select.Overlay component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations (progress-based opacity for bottom-sheet/dialog, Keyframe animations for popover)
- `object`: Custom animation configuration

| prop            | type                       | default     | description                                                                  |
| --------------- | -------------------------- | ----------- | ---------------------------------------------------------------------------- |
| `state`         | `'disabled' \| boolean`    | -           | Disable animations while customizing properties                              |
| `opacity.value` | `[number, number, number]` | `[0, 1, 0]` | Opacity values [idle, open, close] (for bottom-sheet/dialog presentation)    |
| `entering`      | `EntryOrExitLayoutType`    | -           | Custom Keyframe animation for entering transition (for popover presentation) |
| `exiting`       | `EntryOrExitLayoutType`    | -           | Custom Keyframe animation for exiting transition (for popover presentation)  |

#### Select.Content (Popover Presentation)

| prop                    | type                                             | default         | description                                            |
| ----------------------- | ------------------------------------------------ | --------------- | ------------------------------------------------------ |
| `children`              | `ReactNode`                                      | -               | The select content                                     |
| `width`                 | `number \| 'trigger' \| 'content-fit' \| 'full'` | `'content-fit'` | Width sizing strategy for the content                  |
| `presentation`          | `'popover'`                                      | `'popover'`     | Presentation mode for the select                       |
| `placement`             | `'top' \| 'bottom' \| 'left' \| 'right'`         | `'bottom'`      | Placement of the content relative to trigger           |
| `align`                 | `'start' \| 'center' \| 'end'`                   | `'center'`      | Alignment along the placement axis                     |
| `avoidCollisions`       | `boolean`                                        | `true`          | Whether to flip placement when close to viewport edges |
| `offset`                | `number`                                         | `8`             | Distance from trigger element in pixels                |
| `alignOffset`           | `number`                                         | `0`             | Offset along the alignment axis in pixels              |
| `className`             | `string`                                         | -               | Additional CSS classes for the content container       |
| `animation`             | `SelectContentPopoverAnimation`                  | -               | Animation configuration                                |
| `forceMount`            | `boolean`                                        | -               | Whether to force mount the component in the DOM        |
| `insets`                | `Insets`                                         | -               | Screen edge insets to respect when positioning         |
| `asChild`               | `boolean`                                        | `false`         | Whether to render as a child element                   |
| `...Animated.ViewProps` | `Animated.ViewProps`                             | -               | All Reanimated Animated.View props are supported       |

##### SelectContentPopoverAnimation

Animation configuration for Select.Content component (popover presentation). Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default Keyframe animations (translateY/translateX, scale, opacity based on placement)
- `object`: Custom animation configuration with `entering` and/or `exiting` Keyframe animations

| prop       | type                    | default | description                                                                                                                                |
| ---------- | ----------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `state`    | `'disabled' \| boolean` | -       | Disable animations while customizing properties                                                                                            |
| `entering` | `EntryOrExitLayoutType` | -       | Custom Keyframe animation for entering transition (default: Keyframe with translateY/translateX, scale, opacity based on placement, 200ms) |
| `exiting`  | `EntryOrExitLayoutType` | -       | Custom Keyframe animation for exiting transition (default: Keyframe mirroring entering animation, 150ms)                                   |

#### Select.Content (Bottom Sheet Presentation)

| prop                        | type               | default | description                                      |
| --------------------------- | ------------------ | ------- | ------------------------------------------------ |
| `children`                  | `ReactNode`        | -       | The bottom sheet content                         |
| `presentation`              | `'bottom-sheet'`   | -       | Presentation mode for the select                 |
| `contentContainerClassName` | `string`           | -       | Additional CSS classes for the content container |
| `...BottomSheetProps`       | `BottomSheetProps` | -       | All @gorhom/bottom-sheet props are supported     |

#### Select.Content (Dialog Presentation)

| prop           | type                                                     | default | description                                         |
| -------------- | -------------------------------------------------------- | ------- | --------------------------------------------------- |
| `children`     | `ReactNode`                                              | -       | The dialog content                                  |
| `presentation` | `'dialog'`                                               | -       | Presentation mode for the select                    |
| `classNames`   | `{ wrapper?: string; content?: string }`                 | -       | Additional CSS classes for wrapper and content      |
| `styles`       | `Partial<Record<DialogContentFallbackSlots, ViewStyle>>` | -       | Styles for different parts of the dialog content    |
| `animation`    | `SelectContentAnimation`                                 | -       | Animation configuration                             |
| `isSwipeable`  | `boolean`                                                | `true`  | Whether the dialog content can be swiped to dismiss |
| `forceMount`   | `boolean`                                                | -       | Whether to force mount the component in the DOM     |
| `asChild`      | `boolean`                                                | `false` | Whether to render as a child element                |
| `...ViewProps` | `ViewProps`                                              | -       | All standard React Native View props are supported  |

##### `styles`

| prop      | type        | description                      |
| --------- | ----------- | -------------------------------- |
| `wrapper` | `ViewStyle` | Styles for the wrapper container |
| `content` | `ViewStyle` | Styles for the dialog content    |

##### SelectContentAnimation

Animation configuration for Select.Content component (dialog presentation). Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default Keyframe animations (scale and opacity transitions)
- `object`: Custom animation configuration with `entering` and/or `exiting` Keyframe animations

| prop       | type                    | default | description                                                                                              |
| ---------- | ----------------------- | ------- | -------------------------------------------------------------------------------------------------------- |
| `state`    | `'disabled' \| boolean` | -       | Disable animations while customizing properties                                                          |
| `entering` | `EntryOrExitLayoutType` | -       | Custom Keyframe animation for entering transition (default: Keyframe with scale and opacity, 200ms)      |
| `exiting`  | `EntryOrExitLayoutType` | -       | Custom Keyframe animation for exiting transition (default: Keyframe mirroring entering animation, 150ms) |

#### Select.Close

Select.Close extends [CloseButton](./close-button) and automatically handles select dismissal when pressed.

#### Select.ListLabel

| prop           | type        | default | description                                        |
| -------------- | ----------- | ------- | -------------------------------------------------- |
| `children`     | `ReactNode` | -       | The label text content                             |
| `className`    | `string`    | -       | Additional CSS classes for the list label          |
| `...TextProps` | `TextProps` | -       | All standard React Native Text props are supported |

#### Select.Item

| prop                | type                                                         | default | description                                                                |
| ------------------- | ------------------------------------------------------------ | ------- | -------------------------------------------------------------------------- |
| `children`          | `ReactNode \| ((props: SelectItemRenderProps) => ReactNode)` | -       | Custom item content. Defaults to label and indicator, or a render function |
| `value`             | `any`                                                        | -       | The value associated with this item (required)                             |
| `label`             | `string`                                                     | -       | The label text for this item (required)                                    |
| `isDisabled`        | `boolean`                                                    | `false` | Whether this item is disabled                                              |
| `className`         | `string`                                                     | -       | Additional CSS classes for the item                                        |
| `...PressableProps` | `PressableProps`                                             | -       | All standard React Native Pressable props are supported                    |

##### SelectItemRenderProps

When using a render function for `children`, the following props are provided:

| property     | type      | description                             |
| ------------ | --------- | --------------------------------------- |
| `isSelected` | `boolean` | Whether this item is currently selected |
| `value`      | `string`  | The value of the item                   |
| `isDisabled` | `boolean` | Whether the item is disabled            |

#### Select.ItemLabel

| prop           | type        | default | description                                        |
| -------------- | ----------- | ------- | -------------------------------------------------- |
| `className`    | `string`    | -       | Additional CSS classes for the item label          |
| `...TextProps` | `TextProps` | -       | All standard React Native Text props are supported |

#### Select.ItemDescription

| prop           | type        | default | description                                        |
| -------------- | ----------- | ------- | -------------------------------------------------- |
| `children`     | `ReactNode` | -       | The description text content                       |
| `className`    | `string`    | -       | Additional CSS classes for the item description    |
| `...TextProps` | `TextProps` | -       | All standard React Native Text props are supported |

#### Select.ItemIndicator

| prop           | type                           | default | description                                        |
| -------------- | ------------------------------ | ------- | -------------------------------------------------- |
| `children`     | `ReactNode`                    | -       | Custom indicator content. Defaults to check icon   |
| `className`    | `string`                       | -       | Additional CSS classes for the item indicator      |
| `iconProps`    | `SelectItemIndicatorIconProps` | -       | Check icon configuration                           |
| `...ViewProps` | `ViewProps`                    | -       | All standard React Native View props are supported |

##### SelectItemIndicatorIconProps

| prop    | type     | default          | description       |
| ------- | -------- | ---------------- | ----------------- |
| `size`  | `number` | `16`             | Size of the icon  |
| `color` | `string` | `--colors-muted` | Color of the icon |

### Hooks

#### useSelect

Hook to access the Select root context. Returns the select state and control functions.

```tsx
import { useSelect } from 'heroui-native';

const {
  isOpen,
  onOpenChange,
  isDefaultOpen,
  isDisabled,
  presentation,
  triggerPosition,
  setTriggerPosition,
  contentLayout,
  setContentLayout,
  nativeID,
  value,
  onValueChange,
} = useSelect();
```

##### Return Value

| property             | type                                         | description                                               |
| -------------------- | -------------------------------------------- | --------------------------------------------------------- |
| `isOpen`             | `boolean`                                    | Whether the select is currently open                      |
| `onOpenChange`       | `(open: boolean) => void`                    | Callback to change the open state                         |
| `isDefaultOpen`      | `boolean \| undefined`                       | Whether the select is open by default (uncontrolled mode) |
| `isDisabled`         | `boolean \| undefined`                       | Whether the select is disabled                            |
| `presentation`       | `'popover' \| 'bottom-sheet' \| 'dialog'`    | Presentation mode for the select content                  |
| `triggerPosition`    | `LayoutPosition \| null`                     | Position of the trigger element relative to viewport      |
| `setTriggerPosition` | `(position: LayoutPosition \| null) => void` | Updates the trigger element's position                    |
| `contentLayout`      | `LayoutRectangle \| null`                    | Layout measurements of the select content                 |
| `setContentLayout`   | `(layout: LayoutRectangle \| null) => void`  | Updates the content layout measurements                   |
| `nativeID`           | `string`                                     | Unique identifier for the select instance                 |
| `value`              | `SelectOption \| SelectOption[]`                   | Currently selected option                                 |
| `onValueChange`      | `(option: SelectOption \| SelectOption[]) => void` | Callback fired when the selected value changes            |

**Note:** This hook must be used within a `Select` component. It will throw an error if called outside of the select context.

#### useSelectAnimation

Hook to access the Select animation state values within custom components or compound components.

```tsx
import { useSelectAnimation } from 'heroui-native';

const { selectState, progress, isDragging, isGestureReleaseAnimationRunning } =
  useSelectAnimation();
```

##### Return Value

| property                           | type                   | description                                                |
| ---------------------------------- | ---------------------- | ---------------------------------------------------------- |
| `progress`                         | `SharedValue<number>`  | Progress value for animations (0=idle, 1=open, 2=close)    |
| `isDragging`                       | `SharedValue<boolean>` | Whether the select content is currently being dragged      |
| `isGestureReleaseAnimationRunning` | `SharedValue<boolean>` | Whether the gesture release animation is currently running |

**Note:** This hook must be used within a `Select` component. It will throw an error if called outside of the select animation context.

##### SelectOption

| property | type     | description                  |
| -------- | -------- | ---------------------------- |
| `value`  | `string` | The value of the option      |
| `label`  | `string` | The label text of the option |

#### useSelectItem

Hook to access the Select Item context. Returns the item's value and label.

```tsx
import { useSelectItem } from 'heroui-native';

const { itemValue, label } = useSelectItem();
```

##### Return Value

| property    | type     | description                        |
| ----------- | -------- | ---------------------------------- |
| `itemValue` | `string` | The value of the current item      |
| `label`     | `string` | The label text of the current item |

### Special Notes

#### Element Inspector (iOS)

Select uses FullWindowOverlay on iOS. To enable the React Native element inspector during development, set `disableFullWindowOverlay={true}` on `Select.Portal`. Tradeoff: the select dropdown will not appear above native modals when disabled.

### Examples from select.md

#### Select Component

The Select component allows users to choose from a list of options.

##### Installation

```bash
npm install heroui-native
```

##### Import

```tsx
import { Select, Button, BottomSheet } from 'heroui-native';
```

##### Components

- `Select` - Root container component
- `Select.Trigger` - Trigger element to open select
- `Select.Value` - Display selected value
- `Select.Portal` - Portal for rendering select content
- `Select.Content` - Select options container
- `Select.Item` - Individual option item
- `Select.ItemText` - Option item text
- `Select.ItemIndicator` - Selected item indicator

##### Props

###### Select Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `value` | `string \| string[]` | - | Controlled selected value(s) |
| `onValueChange` | `(value: string \| string[]) => void` | - | Value change handler |
| `selectionMode` | `'single' \| 'multiple'` | `'single'` | Selection mode |

###### Select.Trigger Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'default' \| 'secondary'` | `'default'` | Visual style variant |

##### Example: Basic Select

```tsx
import { Select, Button } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

const options = [
  { value: 'apple', label: 'Apple' },
  { value: 'banana', label: 'Banana' },
  { value: 'cherry', label: 'Cherry' },
  { value: 'date', label: 'Date' },
];

function BasicSelectExample() {
  const [value, setValue] = useState('');

  return (
    <View className="px-5">
      <Select value={value} onValueChange={setValue}>
        <Select.Trigger variant="secondary">
          <Select.Value placeholder="Select a fruit" />
        </Select.Trigger>
        <Select.Portal>
          <Select.Content>
            {options.map((option) => (
              <Select.Item key={option.value} value={option.value}>
                <Select.ItemText>{option.label}</Select.ItemText>
                <Select.ItemIndicator />
              </Select.Item>
            ))}
          </Select.Content>
        </Select.Portal>
      </Select>
    </View>
  );
}
```

##### Example: Multiple Select

```tsx
import { Select } from 'heroui-native';
import { useState } from 'react';

const options = [
  { value: 'react', label: 'React' },
  { value: 'vue', label: 'Vue' },
  { value: 'angular', label: 'Angular' },
  { value: 'svelte', label: 'Svelte' },
];

function MultipleSelectExample() {
  const [selectedValues, setSelectedValues] = useState<string[]>([]);

  return (
    <View className="px-5">
      <Select
        value={selectedValues}
        onValueChange={setSelectedValues}
        selectionMode="multiple"
      >
        <Select.Trigger variant="secondary">
          <Select.Value placeholder="Select frameworks" />
        </Select.Trigger>
        <Select.Portal>
          <Select.Content>
            {options.map((option) => (
              <Select.Item key={option.value} value={option.value}>
                <Select.ItemText>{option.label}</Select.ItemText>
                <Select.ItemIndicator />
              </Select.Item>
            ))}
          </Select.Content>
        </Select.Portal>
      </Select>
    </View>
  );
}
```

##### Example: Select with Bottom Sheet

```tsx
import { Select, BottomSheet } from 'heroui-native';
import { useState } from 'react';

const options = [
  { value: 'us', label: 'United States' },
  { value: 'uk', label: 'United Kingdom' },
  { value: 'ca', label: 'Canada' },
  { value: 'au', label: 'Australia' },
];

function SelectWithBottomSheetExample() {
  const [value, setValue] = useState('');
  const [isOpen, setIsOpen] = useState(false);

  return (
    <View className="px-5">
      <Select value={value} onValueChange={setValue}>
        <Select.Trigger variant="secondary">
          <Select.Value placeholder="Select country" />
        </Select.Trigger>
        <Select.Portal>
          <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
            <BottomSheet.Content>
              <BottomSheet.Header>
                <BottomSheet.Title>Select Country</BottomSheet.Title>
              </BottomSheet.Header>
              <Select.Content>
                {options.map((option) => (
                  <Select.Item key={option.value} value={option.value}>
                    <Select.ItemText>{option.label}</Select.ItemText>
                    <Select.ItemIndicator />
                  </Select.Item>
                ))}
              </Select.Content>
            </BottomSheet.Content>
          </BottomSheet>
        </Select.Portal>
      </Select>
    </View>
  );
}
```

##### Complete Example Code

```tsx
import { Select } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

const options = [
  { value: 'apple', label: 'Apple' },
  { value: 'banana', label: 'Banana' },
  { value: 'cherry', label: 'Cherry' },
  { value: 'date', label: 'Date' },
];

export default function SelectExample() {
  const [value, setValue] = useState('');

  return (
    <View className="flex-1 items-center justify-center px-5">
      <Select value={value} onValueChange={setValue}>
        <Select.Trigger variant="secondary">
          <Select.Value placeholder="Select a fruit" />
        </Select.Trigger>
        <Select.Portal>
          <Select.Content>
            {options.map((option) => (
              <Select.Item key={option.value} value={option.value}>
                <Select.ItemText>{option.label}</Select.ItemText>
                <Select.ItemIndicator />
              </Select.Item>
            ))}
          </Select.Content>
        </Select.Portal>
      </Select>
    </View>
  );
}
```

---

## SearchField

---
title: SearchField
description: A compound search input for filtering and querying content.
icon: new
links:
  source_native: search-field/search-field.tsx
  styles_native: search-field/search-field.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/search-field-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/search-field-docs-dark.mp4"
/>

### Import

```tsx
import { SearchField } from 'heroui-native';
```

### Anatomy

```tsx
<SearchField value={value} onChange={onChange}>
  <SearchField.Group>
    <SearchField.SearchIcon />
    <SearchField.Input />
    <SearchField.ClearButton />
  </SearchField.Group>
</SearchField>
```

- **SearchField**: Root container that accepts `value` and `onChange`, providing them to children via context. Also provides form field state (isDisabled, isInvalid, isRequired) and animation settings.
- **SearchField.Group**: Flex-row container that positions the search icon, input, and clear button horizontally.
- **SearchField.SearchIcon**: Magnifying glass icon positioned absolutely on the left side of the input. Supports custom children to replace the default icon.
- **SearchField.Input**: Wraps the Input component with search-specific defaults. Reads `value` and `onChangeText` from the SearchField context automatically.
- **SearchField.ClearButton**: Small icon-only button to clear the search input. Automatically hidden when value is empty. Calls `onChange("")` from context on press.

### Usage

#### Basic Usage

The SearchField component uses compound parts to create a search input. Pass `value` and `onChange` to the root; the Input and ClearButton consume them via context.

```tsx
<SearchField value={searchValue} onChange={setSearchValue}>
  <SearchField.Group>
    <SearchField.SearchIcon />
    <SearchField.Input />
    <SearchField.ClearButton />
  </SearchField.Group>
</SearchField>
```

#### With Label and Description

Add a Label and Description outside the Group to provide context for the search field.

```tsx
<SearchField value={searchValue} onChange={setSearchValue}>
  <Label>Find products</Label>
  <SearchField.Group>
    <SearchField.SearchIcon />
    <SearchField.Input />
    <SearchField.ClearButton />
  </SearchField.Group>
  <Description>Search by name, category, or SKU</Description>
</SearchField>
```

#### With Validation

Use `isInvalid` and `isRequired` on the root to control validation state. Pair with FieldError to display error messages.

```tsx
<SearchField
  value={searchValue}
  onChange={setSearchValue}
  isRequired
  isInvalid={isInvalid}
>
  <Label>Search users</Label>
  <SearchField.Group>
    <SearchField.SearchIcon />
    <SearchField.Input />
    <SearchField.ClearButton />
  </SearchField.Group>
  <Description hideOnInvalid>Enter at least 3 characters to search</Description>
  <FieldError>No results found. Please try a different search term.</FieldError>
</SearchField>
```

#### Custom Search Icon

Replace the default magnifying glass icon by passing children to `SearchField.SearchIcon`.

```tsx
<SearchField value={searchValue} onChange={setSearchValue}>
  <SearchField.Group>
    <SearchField.SearchIcon>
      <Text className="text-base">🔍</Text>
    </SearchField.SearchIcon>
    <SearchField.Input className="pl-10" />
    <SearchField.ClearButton />
  </SearchField.Group>
</SearchField>
```

#### Disabled

Set `isDisabled` on the root to disable all child components via context.

```tsx
<SearchField value="Previous query" isDisabled>
  <Label>Disabled search</Label>
  <SearchField.Group>
    <SearchField.SearchIcon />
    <SearchField.Input />
  </SearchField.Group>
  <Description>Search is temporarily unavailable</Description>
</SearchField>
```

### Example

```tsx
import { Description, Label, SearchField } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function SearchFieldExample() {
  const [searchValue, setSearchValue] = useState('');

  return (
    <View className="px-5">
      <SearchField value={searchValue} onChange={setSearchValue}>
        <Label>Find products</Label>
        <SearchField.Group>
          <SearchField.SearchIcon />
          <SearchField.Input />
          <SearchField.ClearButton />
        </SearchField.Group>
        <Description>Search by name, category, or SKU</Description>
      </SearchField>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](<https://github.com/heroui-inc/heroui-native/blob/beta/example/src/app/(home)/components/search-field.tsx>).

### API Reference

#### SearchField

| prop           | type                      | default | description                                              |
| -------------- | ------------------------- | ------- | -------------------------------------------------------- |
| `children`     | `React.ReactNode`         | -       | Children elements to be rendered inside the search field |
| `value`        | `string`                  | -       | Controlled search text value                             |
| `onChange`     | `(value: string) => void` | -       | Callback fired when the search text changes              |
| `isDisabled`   | `boolean`                 | `false` | Whether the search field is disabled                     |
| `isInvalid`    | `boolean`                 | `false` | Whether the search field is in an invalid state          |
| `isRequired`   | `boolean`                 | `false` | Whether the search field is required                     |
| `className`    | `string`                  | -       | Additional CSS classes                                   |
| `animation`    | `AnimationRootDisableAll` | -       | Animation configuration for the search field             |
| `...ViewProps` | `ViewProps`               | -       | All standard React Native View props are supported       |

##### AnimationRootDisableAll

Animation configuration for the SearchField root component. Can be:

- `"disable-all"`: Disable all animations including children (cascades down)
- `undefined`: Use default animations

#### SearchField.Group

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Children elements to be rendered inside the group  |
| `className`    | `string`          | -       | Additional CSS classes                             |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported |

#### SearchField.SearchIcon

| prop           | type                             | default | description                                                                        |
| -------------- | -------------------------------- | ------- | ---------------------------------------------------------------------------------- |
| `children`     | `React.ReactNode`                | -       | Custom content to replace the default search icon                                  |
| `className`    | `string`                         | -       | Additional CSS classes                                                             |
| `iconProps`    | `SearchFieldSearchIconIconProps` | -       | Props for customizing the default search icon (ignored when children are provided) |
| `...ViewProps` | `ViewProps`                      | -       | All standard React Native View props are supported                                 |

##### SearchFieldSearchIconIconProps

| prop    | type     | default             | description       |
| ------- | -------- | ------------------- | ----------------- |
| `size`  | `number` | `16`                | Size of the icon  |
| `color` | `string` | Theme `muted` color | Color of the icon |

#### SearchField.Input

Extends [Input](./input) props with search-specific defaults (`placeholder="Search..."`, `returnKeyType="search"`, `accessibilityRole="search"`). Omits `value` and `onChangeText` because they are provided by the SearchField context.

#### SearchField.ClearButton

Automatically hidden when the controlled `value` is an empty string. Calls `onChange("")` from context on press. Additional `onPress` handlers passed via props are called after clearing.

| prop             | type                              | default | description                                      |
| ---------------- | --------------------------------- | ------- | ------------------------------------------------ |
| `children`       | `React.ReactNode`                 | -       | Custom content to replace the default close icon |
| `iconProps`      | `SearchFieldClearButtonIconProps` | -       | Props for customizing the clear button icon      |
| `className`      | `string`                          | -       | Additional CSS classes                           |
| `...ButtonProps` | `ButtonRootProps`                 | -       | All Button root props are supported              |

##### SearchFieldClearButtonIconProps

| prop    | type     | default             | description       |
| ------- | -------- | ------------------- | ----------------- |
| `size`  | `number` | `14`                | Size of the icon  |
| `color` | `string` | Theme `muted` color | Color of the icon |

### Hooks

#### useSearchField

Hook to access the search field state from context. Must be used within a `SearchField` component.

```tsx
import { useSearchField } from 'heroui-native';

const { value, onChange, isDisabled, isInvalid, isRequired } = useSearchField();
```

##### Returns

| property     | type                                     | description                                     |
| ------------ | ---------------------------------------- | ----------------------------------------------- |
| `value`      | `string \| undefined`                    | Current controlled search text value            |
| `onChange`   | `((value: string) => void) \| undefined` | Callback to update the search text              |
| `isDisabled` | `boolean`                                | Whether the search field is disabled            |
| `isInvalid`  | `boolean`                                | Whether the search field is in an invalid state |
| `isRequired` | `boolean`                                | Whether the search field is required            |

### Examples from search-field.md

#### Search Field Component

The Search Field component provides a styled input for search functionality.

##### Installation

```bash
npm install heroui-native react-native-keyboard-controller react-native-reanimated
```

##### Import

```tsx
import { SearchField, Label, Description, FieldError } from 'heroui-native';
```

##### Components

- `SearchField` - Root container component
- `SearchField.Group` - Input group container
- `SearchField.SearchIcon` - Search icon
- `SearchField.Input` - Search input field
- `SearchField.ClearButton` - Clear button

##### Props

###### SearchField Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `value` | `string` | - | Controlled value |
| `onChange` | `(value: string) => void` | - | Value change handler |
| `isRequired` | `boolean` | `false` | Required field indicator |
| `isInvalid` | `boolean` | `false` | Invalid state |
| `isDisabled` | `boolean` | `false` | Disabled state |

---

##### Example 1: Basic Search Field

A simple search field with icon, input, and clear button.

```tsx
import { SearchField } from 'heroui-native';
import { useState } from 'react';
import { useWindowDimensions, View } from 'react-native';
import { useReanimatedKeyboardAnimation } from 'react-native-keyboard-controller';
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';

/**
 * KeyboardAvoidingContainer - Moves content up when keyboard appears
 */
const KeyboardAvoidingContainer = ({
  children,
}: {
  children: React.ReactNode;
}) => {
  const { height } = useWindowDimensions();
  const { progress } = useReanimatedKeyboardAnimation();

  const rStyle = useAnimatedStyle(() => {
    return {
      transform: [
        {
          translateY: withTiming(progress.get() === 1 ? -height * 0.15 : 0, {
            duration: 250,
          }),
        },
      ],
    };
  });

  return <Animated.View style={rStyle}>{children}</Animated.View>;
};

const BasicSearchFieldContent = () => {
  const [searchValue, setSearchValue] = useState('');

  return (
    <View className="flex-1 justify-center px-5">
      <KeyboardAvoidingContainer>
        <SearchField value={searchValue} onChange={setSearchValue}>
          <SearchField.Group>
            <SearchField.SearchIcon />
            <SearchField.Input />
            <SearchField.ClearButton />
          </SearchField.Group>
        </SearchField>
      </KeyboardAvoidingContainer>
    </View>
  );
};
```

---

##### Example 2: With Label and Description

Search field with label and helper text.

```tsx
import { SearchField, Label, Description } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

const WithDescriptionContent = () => {
  const [searchValue, setSearchValue] = useState('');

  return (
    <View className="flex-1 justify-center px-5">
      <KeyboardAvoidingContainer>
        <SearchField value={searchValue} onChange={setSearchValue}>
          <Label>Find products</Label>
          <SearchField.Group>
            <SearchField.SearchIcon />
            <SearchField.Input />
            <SearchField.ClearButton />
          </SearchField.Group>
          <Description>Search by name, category, or SKU</Description>
        </SearchField>
      </KeyboardAvoidingContainer>
    </View>
  );
};
```

---

##### Example 3: With Validation

Search field with error state and validation message.

```tsx
import { SearchField, Label, Description, FieldError } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

const WithValidationContent = () => {
  const [isInvalid, setIsInvalid] = useState(false);
  const [searchValue, setSearchValue] = useState('');

  return (
    <WithStateToggle
      isSelected={isInvalid}
      onSelectedChange={setIsInvalid}
      label="Simulate Error"
      description="Toggle validation error state"
    >
      <View className="flex-1 pt-[55%]">
        <KeyboardAvoidingContainer>
          <SearchField
            value={searchValue}
            onChange={setSearchValue}
            isRequired
            isInvalid={isInvalid}
          >
            <Label>Search users</Label>
            <SearchField.Group>
              <SearchField.SearchIcon />
              <SearchField.Input />
              <SearchField.ClearButton />
            </SearchField.Group>
            <Description hideOnInvalid>
              Enter at least 3 characters to search
            </Description>
            <FieldError>
              No results found. Please try a different search term.
            </FieldError>
          </SearchField>
        </KeyboardAvoidingContainer>
      </View>
    </WithStateToggle>
  );
};
```

---

##### Example 4: Custom Search Icon

Search field with a custom emoji icon.

```tsx
import { SearchField, Label, Description } from 'heroui-native';
import { useState } from 'react';
import { Text, View } from 'react-native';

const CustomSearchIconContent = () => {
  const [searchValue, setSearchValue] = useState('');

  return (
    <View className="flex-1 justify-center px-5">
      <KeyboardAvoidingContainer>
        <SearchField value={searchValue} onChange={setSearchValue}>
          <Label>Search</Label>
          <SearchField.Group>
            <SearchField.SearchIcon>
              <Text className="text-base">🔍</Text>
            </SearchField.SearchIcon>
            <SearchField.Input className="pl-10" />
            <SearchField.ClearButton />
          </SearchField.Group>
          <Description>Uses a custom search emoji icon</Description>
        </SearchField>
      </KeyboardAvoidingContainer>
    </View>
  );
};
```

---

##### Example 5: Disabled State

Active and disabled search fields side by side.

```tsx
import { SearchField, Label, Description } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

const DisabledContent = () => {
  const [activeValue, setActiveValue] = useState('');

  return (
    <View className="flex-1 justify-center px-5">
      <KeyboardAvoidingContainer>
        <View className="gap-8">
          <SearchField value={activeValue} onChange={setActiveValue}>
            <Label>Active search</Label>
            <SearchField.Group>
              <SearchField.SearchIcon />
              <SearchField.Input />
              <SearchField.ClearButton />
            </SearchField.Group>
            <Description>Type to search</Description>
          </SearchField>

          <SearchField value="Previous query" isDisabled>
            <Label>Disabled search</Label>
            <SearchField.Group>
              <SearchField.SearchIcon />
              <SearchField.Input />
            </SearchField.Group>
            <Description>Search is temporarily unavailable</Description>
          </SearchField>
        </View>
      </KeyboardAvoidingContainer>
    </View>
  );
};
```

---

##### Complete Usage Example

```tsx
import { SearchField, Label, Description } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function SearchFieldExample() {
  const [searchValue, setSearchValue] = useState('');

  return (
    <View className="flex-1 justify-center px-5">
      <SearchField value={searchValue} onChange={setSearchValue}>
        <Label>Search</Label>
        <SearchField.Group>
          <SearchField.SearchIcon />
          <SearchField.Input placeholder="Search..." />
          <SearchField.ClearButton />
        </SearchField.Group>
        <Description>Enter keywords to search</Description>
      </SearchField>
    </View>
  );
}
```

---

## TextArea

---
title: TextArea
description: A multiline text input component with styled border and background for collecting longer user input.
links:
  source_native: text-area/text-area.tsx
  styles_native: text-area/text-area.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/text-area-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/text-area-docs-dark.mp4"
/>

### Import

```tsx
import { TextArea } from 'heroui-native';
```

### Usage

#### Basic Usage

TextArea can be used standalone or within a TextField component.

```tsx
import { TextArea } from 'heroui-native';

<TextArea placeholder="Enter your message" />
```

#### Within TextField

TextArea works seamlessly with TextField for complete form structure.

```tsx
import { Description, Label, TextArea, TextField } from 'heroui-native';

<TextField>
  <Label>Message</Label>
  <TextArea placeholder="Enter your message here..." />
  <Description>Please provide as much detail as possible.</Description>
</TextField>
```

#### With Validation

Display error state when the text area is invalid.

```tsx
import { FieldError, Label, TextArea, TextField } from 'heroui-native';

<TextField isRequired isInvalid={true}>
  <Label>Message</Label>
  <TextArea placeholder="Enter your message" />
  <FieldError>Please enter a valid message</FieldError>
</TextField>
```

#### Disabled State

Disable the text area to prevent interaction.

```tsx
import { Label, TextArea, TextField } from 'heroui-native';

<TextField isDisabled>
  <Label>Disabled Field</Label>
  <TextArea placeholder="Cannot edit" value="Read only value" />
</TextField>
```

#### With Variant

Use different variants to style the text area based on context.

```tsx
import { Label, TextArea, TextField } from 'heroui-native';

<TextField>
  <Label>Primary Variant</Label>
  <TextArea placeholder="Primary style text area" variant="primary" />
</TextField>

<TextField>
  <Label>Secondary Variant</Label>
  <TextArea placeholder="Secondary style text area" variant="secondary" />
</TextField>
```

#### Custom Styling

Customize the text area appearance using className.

```tsx
import { Label, TextArea, TextField } from 'heroui-native';

<TextField>
  <Label>Custom Styled</Label>
  <TextArea
    placeholder="Custom colors"
    className="bg-blue-50 border-blue-500 focus:border-blue-700"
  />
</TextField>
```

### Example

```tsx
import { Description, FieldError, Label, TextArea, TextField } from 'heroui-native';
import { View } from 'react-native';

export default function TextAreaExample() {
  return (
    <View className="gap-8">
      <TextField>
        <Label>Primary Variant</Label>
        <TextArea placeholder="Primary style text area" variant="primary" />
        <Description>Default variant with primary styling</Description>
      </TextField>

      <TextField>
        <Label>Secondary Variant</Label>
        <TextArea
          placeholder="Secondary style text area"
          variant="secondary"
        />
        <Description>Secondary variant for surfaces</Description>
      </TextField>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/text-area.tsx).

### API Reference

TextArea extends [Input](./input) component and inherits all its props. The only differences are default values: `multiline` defaults to `true` and `textAlignVertical` defaults to `'top'`.

### Examples from text-area.md

#### TextArea Component

The TextArea component is a multi-line text input field.

##### Installation

```bash
npm install heroui-native
```

##### Import

```tsx
import { TextArea, TextField, Label, FieldError } from 'heroui-native';
```

##### Components

- `TextArea` - Multi-line text input
- `TextField` - Wrapper with label and error

##### Props

###### TextArea Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `value` | `string` | - | Controlled value |
| `onChangeText` | `(text: string) => void` | - | Text change handler |
| `placeholder` | `string` | - | Placeholder text |
| `numberOfLines` | `number` | `4` | Number of visible lines |
| `maxLength` | `number` | - | Maximum character length |

##### Example: Basic TextArea

```tsx
import { TextArea, TextField, Label } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function BasicTextAreaExample() {
  const [value, setValue] = useState('');

  return (
    <View className="p-5">
      <TextField>
        <Label>Description</Label>
        <TextArea
          placeholder="Enter your description..."
          value={value}
          onChangeText={setValue}
          numberOfLines={4}
        />
      </TextField>
    </View>
  );
}
```

##### Example: TextArea with Validation

```tsx
import { TextArea, TextField, Label, FieldError } from 'heroui-native';
import { useState } from 'react';
import { View, Text } from 'react-native';

function TextAreaWithValidationExample() {
  const [value, setValue] = useState('');
  const [error, setError] = useState('');

  const validate = (text: string) => {
    if (text.length < 10) {
      setError('Description must be at least 10 characters');
    } else {
      setError('');
    }
  };

  return (
    <View className="p-5">
      <TextField isInvalid={!!error}>
        <Label>Bio</Label>
        <TextArea
          placeholder="Tell us about yourself..."
          value={value}
          onChangeText={(text) => {
            setValue(text);
            validate(text);
          }}
          numberOfLines={5}
          maxLength={200}
        />
        <FieldError>{error}</FieldError>
      </TextField>
      <Text className="text-muted text-sm mt-2">
        {value.length}/200 characters
      </Text>
    </View>
  );
}
```

##### Complete Example Code

```tsx
import { TextArea, TextField, Label } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function TextAreaExample() {
  const [value, setValue] = useState('');

  return (
    <View className="flex-1 p-5 justify-center">
      <TextField>
        <Label>Description</Label>
        <TextArea
          placeholder="Enter your description..."
          value={value}
          onChangeText={setValue}
          numberOfLines={4}
        />
      </TextField>
    </View>
  );
}
```

---

## TextField

---
title: TextField
description: A text input component with label, description, and error handling for collecting user input.
links:
  source_native: text-field/text-field.tsx
  styles_native: text-field/text-field.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/text-field-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/text-field-docs-dark.mp4"
/>

### Import

```tsx
import { TextField } from 'heroui-native';
```

### Anatomy

```tsx
<TextField>
  <Label>...</Label>
  <Input />
  <Description>...</Description>
  <FieldError>...</FieldError>
</TextField>
```

- **TextField**: Root container that provides spacing and state management
- **Label**: Label with optional asterisk for required fields (from [Label](./label) component)
- **Input**: Input container with animated border and background (from [Input](./input) component)
- **Description**: Secondary descriptive helper text (from [Description](./description) component)
- **FieldError**: Validation error message display (from [FieldError](./field-error) component)

### Usage

#### Basic Usage

TextField provides a complete form input structure with label and description.

```tsx
<TextField>
  <Label>Email</Label>
  <Input placeholder="Enter your email" />
  <Description>We'll never share your email</Description>
</TextField>
```

#### With Required Field

Mark fields as required to show an asterisk in the label.

```tsx
<TextField isRequired>
  <Label>Username</Label>
  <Input placeholder="Choose a username" />
</TextField>
```

#### With Validation

Display error messages when the field is invalid.

```tsx
import { FieldError, Input, Label, TextField } from 'heroui-native';

<TextField isRequired isInvalid={true}>
  <Label>Email</Label>
  <Input placeholder="Enter your email" />
  <FieldError>Please enter a valid email</FieldError>
</TextField>;
```

#### With Local Invalid State Override

Override the context's invalid state for individual components.

```tsx
import {
  Description,
  FieldError,
  Input,
  Label,
  TextField,
} from 'heroui-native';

<TextField isInvalid={true}>
  <Label isInvalid={false}>Email</Label>
  <Input placeholder="Enter your email" isInvalid={false} />
  <Description isInvalid={false}>
    This shows despite input being invalid
  </Description>
  <FieldError>Email format is incorrect</FieldError>
</TextField>;
```

#### Multiline Input

Create text areas for longer content.

```tsx
<TextField>
  <Label>Message</Label>
  <Input placeholder="Type your message..." multiline numberOfLines={4} />
  <Description>Maximum 500 characters</Description>
</TextField>
```

#### Disabled State

Disable the entire field to prevent interaction.

```tsx
<TextField isDisabled>
  <Label>Disabled Field</Label>
  <Input placeholder="Cannot edit" value="Read only value" />
</TextField>
```

#### With Variant

Use different variants to style the input based on context.

```tsx
<TextField>
  <Label>Primary Variant</Label>
  <Input placeholder="Primary style" variant="primary" />
</TextField>

<TextField>
  <Label>Secondary Variant</Label>
  <Input placeholder="Secondary style" variant="secondary" />
</TextField>
```

#### Custom Styling

Customize the input appearance using className.

```tsx
<TextField>
  <Label>Custom Styled</Label>
  <Input
    placeholder="Custom colors"
    className="bg-blue-50 border-blue-500 focus:border-blue-700"
  />
</TextField>
```

### Example

```tsx
import { Ionicons } from '@expo/vector-icons';
import { Description, Input, Label, TextField } from 'heroui-native';
import { useState } from 'react';
import { Pressable, View } from 'react-native';
import { withUniwind } from 'uniwind';

const StyledIonicons = withUniwind(Ionicons);

export const TextInputContent = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [isPasswordVisible, setIsPasswordVisible] = useState(false);

  return (
    <View className="gap-4">
      <TextField isRequired>
        <Label>Email</Label>
        <Input
          placeholder="Enter your email"
          keyboardType="email-address"
          autoCapitalize="none"
          value={email}
          onChangeText={setEmail}
        />
        <Description>
          We'll never share your email with anyone else.
        </Description>
      </TextField>

      <TextField isRequired>
        <Label>New password</Label>
        <View className="w-full flex-row items-center">
          <Input
            value={password}
            onChangeText={setPassword}
            className="flex-1 px-10"
            placeholder="Enter your password"
            secureTextEntry={!isPasswordVisible}
          />
          <StyledIonicons
            name="lock-closed-outline"
            size={16}
            className="absolute left-3.5 text-muted"
            pointerEvents="none"
          />
          <Pressable
            className="absolute right-4"
            onPress={() => setIsPasswordVisible(!isPasswordVisible)}
          >
            <StyledIonicons
              name={isPasswordVisible ? 'eye-off-outline' : 'eye-outline'}
              size={16}
              className="text-muted"
            />
          </Pressable>
        </View>
        <Description>Password must be at least 6 characters</Description>
      </TextField>
    </View>
  );
};
```

You can find more examples in the [GitHub repository](<https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/text-field.tsx>).

### API Reference

#### TextField

| prop         | type                         | default     | description                                                                               |
| ------------ | ---------------------------- | ----------- | ----------------------------------------------------------------------------------------- |
| children     | `React.ReactNode`            | -           | Content to render inside the text field                                                   |
| isDisabled   | `boolean`                    | `false`     | Whether the entire text field is disabled                                                 |
| isInvalid    | `boolean`                    | `false`     | Whether the text field is in an invalid state                                             |
| isRequired   | `boolean`                    | `false`     | Whether the text field is required (shows asterisk)                                       |
| className    | `string`                     | -           | Custom class name for the root element                                                    |
| animation    | `"disable-all" \| undefined` | `undefined` | Animation configuration. Use `"disable-all"` to disable all animations including children |
| ...ViewProps | `ViewProps`                  | -           | All standard React Native View props are supported                                        |

> **Note**: For Label, Input, Description, and FieldError components, see their respective documentation:
>
> - [Label documentation](./label)
> - [Input documentation](./input)
> - [Description documentation](./description)
> - [FieldError documentation](./field-error)
>
> These components automatically consume form state from TextField via the form-item-state context.

### Hooks

#### useTextField

Hook to access the TextField context values. Must be used within a `TextField` component.

```tsx
import { TextField, useTextField } from 'heroui-native';

function CustomComponent() {
  const { isDisabled, isInvalid, isRequired } = useTextField();

  // Use the context values...
}
```

##### Returns

| property   | type      | description                                   |
| ---------- | --------- | --------------------------------------------- |
| isDisabled | `boolean` | Whether the entire text field is disabled     |
| isInvalid  | `boolean` | Whether the text field is in an invalid state |
| isRequired | `boolean` | Whether the text field is required            |
