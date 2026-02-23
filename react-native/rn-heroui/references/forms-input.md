# Form Input Components

## Table of Contents
- [Input](#input)
- [InputOTP](#inputotp)
- [Label](#label)
- [Description](#description)
- [FieldError](#fielderror)
- [ControlField](#controlfield)

---

## Input

A text input component with styled border and background for collecting user input.

**Source:** `input/input.tsx`
**Styles:** `input/input.styles.ts`

### Import

```tsx
import { Input } from 'heroui-native';
```

### Usage

#### Basic Usage

Input can be used standalone or within a TextField component.

```tsx
import { Input } from 'heroui-native';

<Input placeholder="Enter your email" />;
```

#### Within TextField

Input works seamlessly with TextField for complete form structure.

```tsx
import { Input, Label, TextField } from 'heroui-native';

<TextField>
  <Label>Email</Label>
  <Input placeholder="Enter your email" />
</TextField>;
```

#### With Validation

Display error state when the input is invalid.

```tsx
import { FieldError, Input, Label, TextField } from 'heroui-native';

<TextField isRequired isInvalid={true}>
  <Label>Email</Label>
  <Input placeholder="Enter your email" />
  <FieldError>Please enter a valid email</FieldError>
</TextField>;
```

#### With Local Invalid State Override

Override the context's invalid state for the input.

```tsx
import { FieldError, Input, Label, TextField } from 'heroui-native';

<TextField isInvalid={true}>
  <Label isInvalid={false}>Email</Label>
  <Input placeholder="Enter your email" isInvalid={false} />
  <FieldError>Email format is incorrect</FieldError>
</TextField>;
```

#### Disabled State

Disable the input to prevent interaction.

```tsx
import { Input, Label, TextField } from 'heroui-native';

<TextField isDisabled>
  <Label>Disabled Field</Label>
  <Input placeholder="Cannot edit" value="Read only value" />
</TextField>;
```

#### With Variant

Use different variants to style the input based on context.

```tsx
import { Input, Label, TextField } from 'heroui-native';

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
import { Input, Label, TextField } from 'heroui-native';

<TextField>
  <Label>Custom Styled</Label>
  <Input
    placeholder="Custom colors"
    className="bg-blue-50 border-blue-500 focus:border-blue-700"
  />
</TextField>;
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

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/input.tsx).

### API Reference

#### Input

| prop                      | type                       | default               | description                                                  |
| ------------------------- | -------------------------- | --------------------- | ------------------------------------------------------------ |
| isInvalid                 | `boolean`                  | `undefined`           | Whether the input is in an invalid state (overrides context) |
| variant                   | `'primary' \| 'secondary'` | `'primary'`           | Variant style for the input                                  |
| className                 | `string`                   | -                     | Custom class name for the input                              |
| selectionColorClassName   | `string`                   | `"accent-accent"`     | Custom className for the selection color                     |
| placeholderColorClassName | `string`                   | `"field-placeholder"` | Custom className for the placeholder text color              |
| isBottomSheetAware        | `boolean`                  | `true`                | Whether the input automatically handles keyboard state when rendered inside a BottomSheet. Set to `false` to disable |
| animation                 | `AnimationRoot`            | `undefined`           | Animation configuration for the input                        |
| ...TextInputProps         | `TextInputProps`           | -                     | All standard React Native TextInput props are supported      |

> **Note**: When used within a TextField component, Input automatically consumes form state (isDisabled, isInvalid) from TextField via the form-item-state context.

### Examples from input.md

#### Input Component

The Input component is a text field for user input.

##### Installation

```bash
npm install heroui-native
```

##### Import

```tsx
import { Input, TextField, Label, FieldError } from 'heroui-native';
```

##### Components

- `Input` - Text input field
- `TextField` - Wrapper for input with label and error
- `Label` - Input label
- `FieldError` - Error message display

##### Props

###### Input Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'default' \| 'secondary'` | `'default'` | Visual style variant |
| `placeholder` | `string` | - | Placeholder text |
| `value` | `string` | - | Controlled value |
| `onChangeText` | `(text: string) => void` | - | Text change handler |
| `isInvalid` | `boolean` | `false` | Invalid state |
| `autoFocus` | `boolean` | `false` | Auto focus on mount |
| `autoCapitalize` | `'none' \| 'sentences' \| 'words' \| 'characters'` | - | Auto capitalization |
| `secureTextEntry` | `boolean` | `false` | Mask text (password) |
| `selectionColor` | `string` | - | Text selection color |

###### TextField Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `isRequired` | `boolean` | `false` | Required field indicator |
| `isInvalid` | `boolean` | `false` | Invalid state |

##### Example: Basic Input

```tsx
import { Input, TextField, Label } from 'heroui-native';

function BasicInputExample() {
  const [value, setValue] = useState('');

  return (
    <TextField>
      <Label>Username</Label>
      <Input
        placeholder="Enter your username"
        value={value}
        onChangeText={setValue}
      />
    </TextField>
  );
}
```

##### Example: Input with Validation

```tsx
import { Input, TextField, Label, FieldError } from 'heroui-native';
import { useState } from 'react';

function InputWithValidationExample() {
  const [email, setEmail] = useState('');
  const [error, setError] = useState('');

  const validateEmail = (value: string) => {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!value) {
      setError('Email is required');
    } else if (!emailRegex.test(value)) {
      setError('Please enter a valid email');
    } else {
      setError('');
    }
  };

  return (
    <TextField isRequired isInvalid={!!error}>
      <Label>Email Address</Label>
      <Input
        variant="secondary"
        placeholder="email@example.com"
        value={email}
        onChangeText={(text) => {
          setEmail(text);
          if (error) validateEmail(text);
        }}
        autoCapitalize="none"
        keyboardType="email-address"
      />
      <FieldError>{error}</FieldError>
    </TextField>
  );
}
```

##### Example: Password Input

```tsx
import { Input, TextField, Label } from 'heroui-native';
import { useState } from 'react';

function PasswordInputExample() {
  const [password, setPassword] = useState('');

  return (
    <TextField>
      <Label>Password</Label>
      <Input
        placeholder="Enter your password"
        value={password}
        onChangeText={setPassword}
        secureTextEntry
        autoCapitalize="none"
      />
    </TextField>
  );
}
```

##### Example: Multiple Inputs

```tsx
import { Input, TextField, Label, FieldError, Surface } from 'heroui-native';
import { useState } from 'react';
import { ScrollView } from 'react-native';

function MultipleInputsExample() {
  const [form, setForm] = useState({
    name: '',
    email: '',
    phone: '',
  });
  const [errors, setErrors] = useState({
    name: '',
    email: '',
  });

  return (
    <Surface className="p-5">
      <ScrollView contentContainerClassName="gap-5">
        <TextField isRequired isInvalid={!!errors.name}>
          <Label>Full Name</Label>
          <Input
            variant="secondary"
            placeholder="Enter your name"
            value={form.name}
            onChangeText={(text) => setForm({ ...form, name: text })}
            autoCapitalize="words"
            autoFocus
          />
          <FieldError>{errors.name}</FieldError>
        </TextField>

        <TextField isRequired isInvalid={!!errors.email}>
          <Label>Email Address</Label>
          <Input
            variant="secondary"
            placeholder="email@example.com"
            value={form.email}
            onChangeText={(text) => setForm({ ...form, email: text })}
            autoCapitalize="none"
            keyboardType="email-address"
          />
          <FieldError>{errors.email}</FieldError>
        </TextField>

        <TextField>
          <Label>Phone Number</Label>
          <Input
            variant="secondary"
            placeholder="+1 (555) 000-0000"
            value={form.phone}
            onChangeText={(text) => setForm({ ...form, phone: text })}
            keyboardType="phone-pad"
          />
        </TextField>
      </ScrollView>
    </Surface>
  );
}
```

##### Complete Example Code

```tsx
import { Input, TextField, Label, FieldError, Surface } from 'heroui-native';
import { useState } from 'react';
import { ScrollView } from 'react-native';

export default function InputExample() {
  const [form, setForm] = useState({
    name: '',
    email: '',
  });
  const [errors, setErrors] = useState({
    name: '',
    email: '',
  });

  return (
    <Surface className="p-5">
      <ScrollView contentContainerClassName="gap-5">
        <TextField isRequired isInvalid={!!errors.name}>
          <Label>Full Name</Label>
          <Input
            variant="secondary"
            placeholder="Enter your name"
            value={form.name}
            onChangeText={(text) => setForm({ ...form, name: text })}
            autoCapitalize="words"
            autoFocus
          />
          <FieldError>{errors.name}</FieldError>
        </TextField>

        <TextField isRequired isInvalid={!!errors.email}>
          <Label>Email Address</Label>
          <Input
            variant="secondary"
            placeholder="email@example.com"
            value={form.email}
            onChangeText={(text) => setForm({ ...form, email: text })}
            autoCapitalize="none"
            keyboardType="email-address"
          />
          <FieldError>{errors.email}</FieldError>
        </TextField>
      </ScrollView>
    </Surface>
  );
}
```

---

## InputOTP

Input component for entering one-time passwords (OTP) with individual character slots, animations, and validation support.

**Source:** `input-otp/input-otp.tsx`
**Styles:** `input-otp/input-otp.styles.ts`

### Import

```tsx
import { InputOTP } from 'heroui-native';
```

### Anatomy

```tsx
<InputOTP>
  <InputOTP.Group>
    <InputOTP.Slot index={0} />
    <InputOTP.Slot index={1} />
  </InputOTP.Group>
  <InputOTP.Separator />
  <InputOTP.Group>
    <InputOTP.Slot index={2}>
      <InputOTP.SlotPlaceholder />
      <InputOTP.SlotValue />
      <InputOTP.SlotCaret />
    </InputOTP.Slot>
  </InputOTP.Group>
</InputOTP>
```

- **InputOTP**: Main container that manages OTP input state, handles text changes, and provides context to child components. Manages focus, validation, and character input.
- **InputOTP.Group**: Container for grouping multiple slots together. Use this to visually group related slots (e.g., groups of 3 digits).
- **InputOTP.Slot**: Individual slot that displays a single character or placeholder. Each slot must have a unique index matching its position in the OTP sequence. When no children are provided, automatically renders SlotPlaceholder, SlotValue, and SlotCaret.
- **InputOTP.SlotPlaceholder**: Text component that displays the placeholder character for a slot when it's empty. Used by default in Slot if no children provided.
- **InputOTP.SlotValue**: Text component that displays the actual character value for a slot with animations. Used by default in Slot if no children provided.
- **InputOTP.SlotCaret**: Animated caret indicator that shows the current input position. Place this inside a Slot to show where the user is currently typing.
- **InputOTP.Separator**: Visual separator between groups of slots. Use this to visually separate different groups of OTP digits.

### Usage

#### Basic Usage

Create a 6-digit OTP input with grouped slots and separator.

```tsx
<InputOTP maxLength={6} onComplete={(code) => console.log(code)}>
  <InputOTP.Group>
    <InputOTP.Slot index={0} />
    <InputOTP.Slot index={1} />
    <InputOTP.Slot index={2} />
  </InputOTP.Group>
  <InputOTP.Separator />
  <InputOTP.Group>
    <InputOTP.Slot index={3} />
    <InputOTP.Slot index={4} />
    <InputOTP.Slot index={5} />
  </InputOTP.Group>
</InputOTP>
```

#### Four Digits

Create a simple 4-digit PIN input.

```tsx
<InputOTP maxLength={4} onComplete={(code) => console.log(code)}>
  <InputOTP.Slot index={0} />
  <InputOTP.Slot index={1} />
  <InputOTP.Slot index={2} />
  <InputOTP.Slot index={3} />
</InputOTP>
```

#### With Placeholder

Provide custom placeholder characters for each slot position.

```tsx
<InputOTP
  maxLength={6}
  placeholder="------"
  onComplete={(code) => console.log(code)}
>
  <InputOTP.Group>
    {({ slots }) => (
      <>
        {slots.map((slot) => (
          <InputOTP.Slot key={slot.index} index={slot.index} />
        ))}
      </>
    )}
  </InputOTP.Group>
</InputOTP>
```

#### Controlled Value

Control the OTP value programmatically.

```tsx
const [value, setValue] = useState('');

<InputOTP value={value} onChange={setValue} maxLength={6}>
  <InputOTP.Group>
    <InputOTP.Slot index={0} />
    <InputOTP.Slot index={1} />
    <InputOTP.Slot index={2} />
  </InputOTP.Group>
  <InputOTP.Separator />
  <InputOTP.Group>
    <InputOTP.Slot index={3} />
    <InputOTP.Slot index={4} />
    <InputOTP.Slot index={5} />
  </InputOTP.Group>
</InputOTP>;
```

#### With Validation

Display validation errors when the OTP is invalid.

```tsx
<InputOTP value={value} onChange={setValue} maxLength={6} isInvalid={isInvalid}>
  <InputOTP.Group>
    <InputOTP.Slot index={0} />
    <InputOTP.Slot index={1} />
    <InputOTP.Slot index={2} />
  </InputOTP.Group>
  <InputOTP.Separator />
  <InputOTP.Group>
    <InputOTP.Slot index={3} />
    <InputOTP.Slot index={4} />
    <InputOTP.Slot index={5} />
  </InputOTP.Group>
</InputOTP>
```

#### With Pattern

Restrict input to specific character patterns using regex. Three predefined patterns are available: `REGEXP_ONLY_DIGITS` (matches digits 0-9), `REGEXP_ONLY_CHARS` (matches alphabetic characters a-z, A-Z), and `REGEXP_ONLY_DIGITS_AND_CHARS` (matches both digits and alphabetic characters).

```tsx
import { InputOTP, REGEXP_ONLY_CHARS } from 'heroui-native';

<InputOTP
  maxLength={6}
  pattern={REGEXP_ONLY_CHARS}
  inputMode="text"
  onComplete={(code) => console.log(code)}
>
  <InputOTP.Group>
    <InputOTP.Slot index={0} />
    <InputOTP.Slot index={1} />
    <InputOTP.Slot index={2} />
  </InputOTP.Group>
  <InputOTP.Separator />
  <InputOTP.Group>
    <InputOTP.Slot index={3} />
    <InputOTP.Slot index={4} />
    <InputOTP.Slot index={5} />
  </InputOTP.Group>
</InputOTP>;
```

#### Custom Layout

Use render props in Group to create custom slot layouts.

```tsx
<InputOTP maxLength={6}>
  <InputOTP.Group>
    {({ slots, isFocused, isInvalid }) => (
      <>
        {slots.map((slot) => (
          <InputOTP.Slot
            key={slot.index}
            index={slot.index}
            className={cn('custom-class', slot.isActive && 'active-class')}
          >
            <InputOTP.SlotPlaceholder />
            <InputOTP.SlotValue />
            <InputOTP.SlotCaret />
          </InputOTP.Slot>
        ))}
      </>
    )}
  </InputOTP.Group>
</InputOTP>
```

### Example

```tsx
import { InputOTP, Label, Description, type InputOTPRef } from 'heroui-native';
import { View } from 'react-native';
import { useRef } from 'react';

export default function InputOTPExample() {
  const ref = useRef<InputOTPRef>(null);

  const onComplete = (code: string) => {
    console.log('OTP completed:', code);
    setTimeout(() => {
      ref.current?.clear();
    }, 1000);
  };

  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View>
        <Label>Verify account</Label>
        <Description className="mb-3">
          We've sent a code to a****@gmail.com
        </Description>
        <InputOTP ref={ref} maxLength={6} onComplete={onComplete}>
          <InputOTP.Group>
            <InputOTP.Slot index={0} />
            <InputOTP.Slot index={1} />
            <InputOTP.Slot index={2} />
          </InputOTP.Group>
          <InputOTP.Separator />
          <InputOTP.Group>
            <InputOTP.Slot index={3} />
            <InputOTP.Slot index={4} />
            <InputOTP.Slot index={5} />
          </InputOTP.Group>
        </InputOTP>
      </View>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/input-otp.tsx).

### API Reference

#### InputOTP

| prop                       | type                          | default     | description                                                                                |
| -------------------------- | ----------------------------- | ----------- | ------------------------------------------------------------------------------------------ |
| `maxLength`                | `number`                      | -           | Maximum length of the OTP (required)                                                       |
| `value`                    | `string`                      | -           | Controlled value for the OTP input                                                         |
| `defaultValue`             | `string`                      | -           | Default value for uncontrolled usage                                                       |
| `onChange`                 | `(value: string) => void`     | -           | Callback when value changes                                                                |
| `onComplete`               | `(value: string) => void`     | -           | Handler called when all slots are filled                                                   |
| `isDisabled`               | `boolean`                     | `false`     | Whether the input is disabled                                                              |
| `isInvalid`                | `boolean`                     | `false`     | Whether the input is in an invalid state                                                   |
| `pattern`                  | `string`                      | -           | Regex pattern for allowed characters (e.g., REGEXP_ONLY_DIGITS, REGEXP_ONLY_CHARS)         |
| `inputMode`                | `TextInputProps['inputMode']` | `'numeric'` | Input mode for the input                                                                   |
| `placeholder`              | `string`                      | -           | Placeholder text for the input. Each character corresponds to a slot position              |
| `placeholderTextColor`     | `string`                      | -           | Placeholder text color for all slots                                                       |
| `placeholderTextClassName` | `string`                      | -           | Placeholder text class name for all slots                                                  |
| `pasteTransformer`         | `(text: string) => string`    | -           | Transform pasted text (e.g., remove hyphens). Defaults to removing non-matching characters |
| `onFocus`                  | `(e: FocusEvent) => void`     | -           | Handler for focus events                                                                   |
| `onBlur`                   | `(e: BlurEvent) => void`      | -           | Handler for blur events                                                                    |
| `textInputProps`           | `Omit<TextInputProps, ...>`   | -           | Additional props to pass to the underlying TextInput component                             |
| `children`                 | `React.ReactNode`             | -           | Children elements to be rendered inside the InputOTP                                       |
| `className`                | `string`                      | -           | Additional CSS classes to apply                                                            |
| `style`                    | `PressableProps['style']`     | -           | Style to pass to the container Pressable component                                         |
| `isBottomSheetAware`       | `boolean`                     | `true`      | Whether the InputOTP automatically handles keyboard state when rendered inside a BottomSheet. Set to `false` to disable |
| `animation`                | `"disable-all" \| undefined`  | `undefined` | Animation configuration. Use `"disable-all"` to disable all animations including children  |

#### InputOTP.Group

| prop           | type                                                                        | default | description                                                                                                              |
| -------------- | --------------------------------------------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------ |
| `children`     | `React.ReactNode \| ((props: InputOTPGroupRenderProps) => React.ReactNode)` | -       | Children elements to be rendered inside the group, or a render function that receives slot data and other context values |
| `className`    | `string`                                                                    | -       | Additional CSS classes to apply                                                                                          |
| `...ViewProps` | `ViewProps`                                                                 | -       | All standard React Native View props are supported                                                                       |

##### InputOTPGroupRenderProps

| prop         | type         | description                              |
| ------------ | ------------ | ---------------------------------------- |
| `slots`      | `SlotData[]` | Array of slot data for each position     |
| `maxLength`  | `number`     | Maximum length of the OTP                |
| `value`      | `string`     | Current OTP value                        |
| `isFocused`  | `boolean`    | Whether the input is currently focused   |
| `isDisabled` | `boolean`    | Whether the input is disabled            |
| `isInvalid`  | `boolean`    | Whether the input is in an invalid state |

#### InputOTP.Slot

| prop           | type              | default | description                                                                                 |
| -------------- | ----------------- | ------- | ------------------------------------------------------------------------------------------- |
| `index`        | `number`          | -       | Zero-based index of the slot (required). Must be between 0 and maxLength - 1                |
| `children`     | `React.ReactNode` | -       | Custom slot content. If not provided, defaults to SlotPlaceholder, SlotValue, and SlotCaret |
| `className`    | `string`          | -       | Additional CSS classes to apply                                                             |
| `style`        | `ViewStyle`       | -       | Additional styles to apply                                                                  |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported                                          |

#### InputOTP.SlotPlaceholder

| prop           | type        | default | description                                                          |
| -------------- | ----------- | ------- | -------------------------------------------------------------------- |
| `children`     | `string`    | -       | Text content to display (optional, defaults to slot.placeholderChar) |
| `className`    | `string`    | -       | Additional CSS classes to apply                                      |
| `style`        | `TextStyle` | -       | Additional styles to apply                                           |
| `...TextProps` | `TextProps` | -       | All standard React Native Text props are supported                   |

#### InputOTP.SlotValue

| prop           | type                         | default | description                                               |
| -------------- | ---------------------------- | ------- | --------------------------------------------------------- |
| `children`     | `string`                     | -       | Text content to display (optional, defaults to slot.char) |
| `className`    | `string`                     | -       | Additional CSS classes to apply                           |
| `animation`    | `InputOTPSlotValueAnimation` | -       | Animation configuration for SlotValue                     |
| `...TextProps` | `TextProps`                  | -       | All standard React Native Text props are supported        |

##### InputOTPSlotValueAnimation

Animation configuration for InputOTP.SlotValue component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop               | type                    | default                                  | description                                     |
| ------------------ | ----------------------- | ---------------------------------------- | ----------------------------------------------- |
| `state`            | `'disabled' \| boolean` | -                                        | Disable animations while customizing properties |
| `wrapper.entering` | `EntryOrExitLayoutType` | `FadeIn.duration(250)`                   | Entering animation for wrapper                  |
| `wrapper.exiting`  | `EntryOrExitLayoutType` | `FadeOut.duration(100)`                  | Exiting animation for wrapper                   |
| `text.entering`    | `EntryOrExitLayoutType` | `FlipInXDown.duration(250).easing(...)`  | Entering animation for text                     |
| `text.exiting`     | `EntryOrExitLayoutType` | `FlipOutXDown.duration(250).easing(...)` | Exiting animation for text                      |

#### InputOTP.SlotCaret

| prop                    | type                         | default  | description                                                                                                                                  |
| ----------------------- | ---------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `className`             | `string`                     | -        | Additional CSS classes to apply                                                                                                              |
| `style`                 | `ViewStyle`                  | -        | Additional styles to apply                                                                                                                   |
| `animation`             | `InputOTPSlotCaretAnimation` | -        | Animation configuration for SlotCaret                                                                                                        |
| `isAnimatedStyleActive` | `boolean`                    | `true`   | Whether animated styles (react-native-reanimated) are active. When `false`, the animated style is removed and you can implement custom logic |
| `pointerEvents`         | `'none' \| 'auto' \| ...`    | `'none'` | Pointer events configuration                                                                                                                 |
| `...ViewProps`          | `ViewProps`                  | -        | All standard React Native View props are supported                                                                                           |

##### InputOTPSlotCaretAnimation

Animation configuration for InputOTP.SlotCaret component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop               | type                    | default    | description                                     |
| ------------------ | ----------------------- | ---------- | ----------------------------------------------- |
| `state`            | `'disabled' \| boolean` | -          | Disable animations while customizing properties |
| `opacity.value`    | `[number, number]`      | `[0, 1]`   | Opacity values [min, max]                       |
| `opacity.duration` | `number`                | `500`      | Animation duration in milliseconds              |
| `height.value`     | `[number, number]`      | `[16, 18]` | Height values [min, max] in pixels              |
| `height.duration`  | `number`                | `500`      | Animation duration in milliseconds              |

#### InputOTP.Separator

| prop           | type        | default | description                                        |
| -------------- | ----------- | ------- | -------------------------------------------------- |
| `className`    | `string`    | -       | Additional CSS classes to apply                    |
| `...ViewProps` | `ViewProps` | -       | All standard React Native View props are supported |

### Hooks

#### useInputOTP

Hook to access the InputOTP root context. Must be used within an `InputOTP` component.

```tsx
const { value, maxLength, isFocused, isDisabled, isInvalid, slots } =
  useInputOTP();
```

#### useInputOTPSlot

Hook to access the InputOTP.Slot context. Must be used within an `InputOTP.Slot` component.

```tsx
const { slot, isActive, isCaretVisible } = useInputOTPSlot();
```

---

## Label

Text component for labeling form fields and other UI elements with support for required indicators and validation states.

**Source:** `label/label.tsx`
**Styles:** `label/label.styles.ts`

### Import

```tsx
import { Label } from 'heroui-native';
```

### Anatomy

```tsx
<Label>
  <Label.Text>...</Label.Text>
</Label>
```

- **Label**: Root container that manages label state and provides context to child components. When string children are provided, automatically renders as Label.Text. Supports disabled, required, and invalid states.
- **Label.Text**: Text content of the label. Displays the label text and automatically shows an asterisk when the label is required. Changes color when invalid or disabled.

### Usage

#### Basic Usage

Display a label with text content. String children are automatically rendered as Label.Text.

```tsx
<Label>Username</Label>
```

#### With Form Fields

Use Label with form fields to provide accessible labels.

```tsx
<TextField>
  <Label>Username</Label>
  <Input placeholder="Choose a username" />
</TextField>
```

#### Required Fields

Show an asterisk indicator for required fields using the `isRequired` prop.

```tsx
<TextField>
  <Label isRequired>Password</Label>
  <Input placeholder="Create a password" secureTextEntry />
</TextField>
```

#### Invalid State

Display labels in an invalid state to indicate validation errors.

```tsx
import { FieldError, Label, TextField } from 'heroui-native';

<TextField isInvalid>
  <Label isInvalid>Confirm password</Label>
  <Input
    placeholder="Confirm your password"
    secureTextEntry
    value="different"
    editable={false}
  />
  <FieldError>Passwords do not match</FieldError>
</TextField>
```

#### Disabled State

Disable labels to indicate non-interactive fields.

```tsx
<TextField isDisabled>
  <Label>Subscription plan</Label>
  <Input value="Premium" />
</TextField>
```

#### Custom Layout

Use compound components for custom label layouts.

```tsx
<Label>
  <Label.Text>Custom label</Label.Text>
</Label>
```

#### Custom Styling

Apply custom styles using className, classNames, or styles props.

```tsx
<Label className="mb-2">
  <Label.Text
    className="text-lg"
    classNames={{
      text: "font-bold",
      asterisk: "text-danger"
    }}
  >
    Custom styled label
  </Label.Text>
</Label>
```

### Example

```tsx
import { FieldError, Label, TextField } from 'heroui-native';
import { View } from 'react-native';

export default function LabelExample() {
  return (
    <View className="flex-1 justify-center px-5 gap-8">
      <TextField>
        <Label>Username</Label>
        <Input placeholder="Choose a username" />
      </TextField>
      <TextField>
        <Label isRequired>Password</Label>
        <Input placeholder="Create a password" secureTextEntry />
      </TextField>
      <TextField isInvalid>
        <Label isInvalid>Confirm password</Label>
        <Input
          placeholder="Confirm your password"
          secureTextEntry
          value="different"
          editable={false}
        />
        <FieldError>Passwords do not match</FieldError>
      </TextField>
      <TextField isDisabled>
        <Label>Subscription plan</Label>
        <Input value="Premium" />
      </TextField>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/label.tsx).

### API Reference

#### Label

| prop                | type                         | default     | description                                                                                                   |
| ------------------- | ---------------------------- | ----------- | ------------------------------------------------------------------------------------------------------------- |
| `children`          | `React.ReactNode`            | -           | Label content. When string is provided, automatically renders as Label.Text. Otherwise renders children as-is |
| `isRequired`        | `boolean`                    | `false`     | Whether the label is required. Shows asterisk indicator when true                                             |
| `isInvalid`         | `boolean`                    | `false`     | Whether the label is in an invalid state. Changes text color to danger                                        |
| `isDisabled`        | `boolean`                    | `false`     | Whether the label is disabled. Applies disabled styling and prevents interaction                              |
| `className`         | `string`                     | -           | Additional CSS classes to apply                                                                               |
| `animation`         | `"disable-all" \| undefined` | `undefined` | Animation configuration. Use `"disable-all"` to disable all animations including children                     |
| `...PressableProps` | `PressableProps`             | -           | All standard React Native Pressable props are supported                                                       |

#### Label.Text

| prop           | type                                     | default | description                                                                        |
| -------------- | ---------------------------------------- | ------- | ---------------------------------------------------------------------------------- |
| `children`     | `React.ReactNode`                        | -       | Label text content                                                                 |
| `className`    | `string`                                 | -       | Additional CSS classes to apply to the text element                                |
| `classNames`   | `ElementSlots<LabelSlots>`               | -       | Additional CSS classes for different parts of the label                            |
| `styles`       | `Partial<Record<LabelSlots, TextStyle>>` | -       | Styles for different parts of the label                                            |
| `nativeID`     | `string`                                 | -       | Native ID for accessibility. Used to link label to form fields via aria-labelledby |
| `...TextProps` | `TextProps`                              | -       | All standard React Native Text props are supported                                 |

##### `ElementSlots<LabelSlots>`

| prop       | type     | description                    |
| ---------- | -------- | ------------------------------ |
| `text`     | `string` | CSS classes for the label text |
| `asterisk` | `string` | CSS classes for the asterisk   |

##### `styles`

| prop       | type        | description               |
| ---------- | ----------- | ------------------------- |
| `text`     | `TextStyle` | Styles for the label text |
| `asterisk` | `TextStyle` | Styles for the asterisk   |

### Examples from label.md

#### Label Component

The Label component provides styled text labels for form fields.

##### Installation

```bash
npm install heroui-native
```

##### Import

```tsx
import { Label } from 'heroui-native';
```

##### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `isInvalid` | `boolean` | `false` | Invalid state styling |
| `isRequired` | `boolean` | `false` | Show required indicator |
| `className` | `string` | - | Additional CSS classes |

##### Example: Basic Label

```tsx
import { Label, Input, TextField } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function BasicLabelExample() {
  const [value, setValue] = useState('');

  return (
    <View className="p-5">
      <TextField>
        <Label>Username</Label>
        <Input value={value} onChangeText={setValue} />
      </TextField>
    </View>
  );
}
```

##### Example: Required Label

```tsx
import { Label, Input, TextField } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function RequiredLabelExample() {
  const [value, setValue] = useState('');

  return (
    <View className="p-5">
      <TextField isRequired>
        <Label isRequired>Email</Label>
        <Input value={value} onChangeText={setValue} />
      </TextField>
    </View>
  );
}
```

##### Complete Example Code

```tsx
import { Label, Input, TextField } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function LabelExample() {
  const [value, setValue] = useState('');

  return (
    <View className="flex-1 p-5 justify-center">
      <TextField>
        <Label>Username</Label>
        <Input value={value} onChangeText={setValue} />
      </TextField>
    </View>
  );
}
```

---

## Description

Text component for providing accessible descriptions and helper text for form fields and other UI elements.

**Source:** `description/description.tsx`
**Styles:** `description/description.styles.ts`

### Import

```tsx
import { Description } from 'heroui-native';
```

### Anatomy

```tsx
<Description>...</Description>
```

- **Description**: Text component that displays description or helper text with muted styling. Can be linked to form fields via `nativeID` for accessibility support.

### Usage

#### Basic Usage

Display description text with default muted styling.

```tsx
<Description>This is a helpful description.</Description>
```

#### With Form Fields

Provide accessible descriptions for form fields using the `nativeID` prop.

```tsx
<TextField>
  <Label>Email address</Label>
  <Input
    placeholder="Enter your email"
    keyboardType="email-address"
    autoCapitalize="none"
  />
  <Description nativeID="email-desc">
    We'll never share your email with anyone else.
  </Description>
</TextField>
```

#### Accessibility Linking

Link descriptions to form fields for screen reader support by using `nativeID` and `aria-describedby`.

```tsx
<TextField>
  <Label>Password</Label>
  <Input
    placeholder="Create a password"
    secureTextEntry
    aria-describedby="password-desc"
  />
  <Description nativeID="password-desc">
    Use at least 8 characters with a mix of letters, numbers, and symbols.
  </Description>
</TextField>
```

#### Hiding on Invalid State

Control whether the description should be hidden when the form field is invalid using the `hideOnInvalid` prop.

```tsx
<TextField isInvalid={isInvalid}>
  <Label>Email</Label>
  <Input placeholder="Enter your email" />
  <Description hideOnInvalid>
    We'll never share your email with anyone else.
  </Description>
  <FieldError>Please enter a valid email address</FieldError>
</TextField>
```

When `hideOnInvalid` is `true`, the description will be hidden when the field is invalid. When `false` (default), the description remains visible even when invalid.

### Example

```tsx
import { Description, TextField } from 'heroui-native';
import { View } from 'react-native';

export default function DescriptionExample() {
  return (
    <View className="flex-1 justify-center px-5 gap-8">
      <TextField>
        <Label>Email address</Label>
        <Input
          placeholder="Enter your email"
          keyboardType="email-address"
          autoCapitalize="none"
        />
        <Description nativeID="email-desc">
          We'll never share your email with anyone else.
        </Description>
      </TextField>
      <TextField>
        <Label>Password</Label>
        <Input placeholder="Create a password" secureTextEntry />
        <Description nativeID="password-desc">
          Use at least 8 characters with a mix of letters, numbers, and symbols.
        </Description>
      </TextField>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/description.tsx).

### API Reference

#### Description

| prop            | type                                | default | description                                                                                |
| --------------- | ----------------------------------- | ------- | ------------------------------------------------------------------------------------------ |
| `children`      | `React.ReactNode`                   | -       | Description text content                                                                   |
| `className`     | `string`                            | -       | Additional CSS classes to apply                                                            |
| `nativeID`      | `string`                            | -       | Native ID for accessibility. Used to link description to form fields via aria-describedby. |
| `isInvalid`     | `boolean`                           | -       | Whether the description is in an invalid state (overrides context)                         |
| `isDisabled`    | `boolean`                           | -       | Whether the description is disabled (overrides context)                                    |
| `hideOnInvalid` | `boolean`                           | `false` | Whether to hide the description when invalid                                               |
| `animation`     | `DescriptionAnimation \| undefined` | -       | Animation configuration for description transitions                                        |
| `...TextProps`  | `TextProps`                         | -       | All standard React Native Text props are supported                                         |

### Examples from description.md

#### Description Component

The Description component provides helper text for form fields.

##### Installation

```bash
npm install heroui-native
```

##### Import

```tsx
import { Description } from 'heroui-native';
```

##### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `hideOnInvalid` | `boolean` | `false` | Hide when field is invalid |
| `className` | `string` | - | Additional CSS classes |

##### Example: Basic Description

```tsx
import { Description, Label, Input, TextField } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function BasicDescriptionExample() {
  const [value, setValue] = useState('');

  return (
    <View className="p-5">
      <TextField>
        <Label>Username</Label>
        <Input value={value} onChangeText={setValue} />
        <Description>
          Choose a unique username between 3-20 characters.
        </Description>
      </TextField>
    </View>
  );
}
```

##### Complete Example Code

```tsx
import { Description, Label, Input, TextField } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function DescriptionExample() {
  const [value, setValue] = useState('');

  return (
    <View className="flex-1 p-5 justify-center">
      <TextField>
        <Label>Username</Label>
        <Input value={value} onChangeText={setValue} />
        <Description>
          Choose a unique username between 3-20 characters.
        </Description>
      </TextField>
    </View>
  );
}
```

---

## FieldError

Displays validation error message content with smooth animations.

**Source:** `field-error/field-error.tsx`
**Styles:** `field-error/field-error.styles.ts`

### Import

```tsx
import { FieldError } from 'heroui-native';
```

### Anatomy

```tsx
<FieldError>Error message content</FieldError>
```

- **FieldError**: Main container that displays error messages with smooth animations. Accepts string children which are automatically wrapped with Text component, or custom React components for more complex layouts. Controls visibility through the `isInvalid` prop and supports custom entering/exiting animations.

### Usage

#### Basic Usage

The FieldError component displays error messages when validation fails.

```tsx
<FieldError isInvalid={true}>This field is required</FieldError>
```

#### Controlled Visibility

Control when the error appears using the `isInvalid` prop. When used inside a form field component (like TextField), FieldError automatically consumes the form-item-state context.

```tsx
const [isInvalid, setIsInvalid] = useState(false);

<FieldError isInvalid={isInvalid}>Please enter a valid email address</FieldError>;
```

#### With Form Fields

FieldError automatically consumes form state from TextField via the form-item-state context.

```tsx
import { FieldError, Label, TextField } from 'heroui-native';

<TextField isRequired isInvalid={true}>
  <Label>Email</Label>
  <Input placeholder="Enter your email" />
  <FieldError>Please enter a valid email address</FieldError>
</TextField>
```

#### Custom Content

Pass custom React components as children instead of strings.

```tsx
<FieldError isInvalid={true}>
  <View className="flex-row items-center">
    <Icon name="alert-circle" />
    <Text className="ml-2 text-danger">Invalid input</Text>
  </View>
</FieldError>
```

#### Custom Animations

Override default entering and exiting animations using the `animation` prop.

```tsx
import { SlideInDown, SlideOutUp } from 'react-native-reanimated';

<FieldError
  isInvalid={true}
  animation={{
    entering: { value: SlideInDown.duration(200) },
    exiting: { value: SlideOutUp.duration(150) },
  }}
>
  Field validation failed
</FieldError>;
```

Disable animations entirely:

```tsx
<FieldError isInvalid={true} animation={false}>
  Field validation failed
</FieldError>
```

#### Custom Styling

Apply custom styles to the container and text elements.

```tsx
<FieldError
  isInvalid={true}
  className="mt-2"
  classNames={{
    container: 'bg-danger/10 p-2 rounded',
    text: 'text-xs font-medium',
  }}
>
  Password must be at least 8 characters
</FieldError>
```

#### Custom Text Props

Pass additional props to the Text component when children is a string.

```tsx
<FieldError
  isInvalid={true}
  textProps={{
    numberOfLines: 1,
    ellipsizeMode: 'tail',
    style: { letterSpacing: 0.5 },
  }}
>
  This is a very long error message that might need to be truncated
</FieldError>
```

### Example

```tsx
import { Description, FieldError, Label, TextField } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function FieldErrorExample() {
  const [email, setEmail] = useState('');
  const [isInvalid, setIsInvalid] = useState(false);

  const isValidEmail = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);

  const handleBlur = () => {
    setIsInvalid(email !== '' && !isValidEmail);
  };

  return (
    <View className="p-4">
      <TextField isInvalid={isInvalid}>
        <Label>Email Address</Label>
        <Input
          placeholder="Enter your email"
          value={email}
          onChangeText={setEmail}
          onBlur={handleBlur}
          keyboardType="email-address"
          autoCapitalize="none"
        />
        <Description>
          We'll use this to contact you
        </Description>
        <FieldError>Please enter a valid email address</FieldError>
      </TextField>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/field-error.tsx).

### API Reference

#### FieldError

| prop                   | type                                          | default     | description                                                                                                                                   |
| ---------------------- | --------------------------------------------- | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `children`             | `React.ReactNode`                             | `undefined` | The content of the error field. String children are wrapped with Text                                                                         |
| `isInvalid`            | `boolean`                                     | `undefined` | Controls the visibility of the error field (overrides form-item-state context). When used inside TextField, automatically consumes form state |
| `animation`            | `FieldErrorRootAnimation`                     | -           | Animation configuration                                                                                                                       |
| `className`            | `string`                                      | `undefined` | Additional CSS classes for the container                                                                                                      |
| `classNames`           | `ElementSlots<FieldErrorSlots>`               | `undefined` | Additional CSS classes for different parts of the component                                                                                   |
| `styles`               | `{ container?: ViewStyle; text?: TextStyle }` | `undefined` | Styles for different parts of the field error                                                                                                 |
| `textProps`            | `TextProps`                                   | `undefined` | Additional props to pass to the Text component when children is a string                                                                      |
| `...AnimatedViewProps` | `AnimatedProps<ViewProps>`                    | -           | All Reanimated Animated.View props are supported                                                                                              |

**classNames prop:** `ElementSlots<FieldErrorSlots>` provides type-safe CSS classes for different parts of the field error component. Available slots: `container`, `text`.

##### `styles`

| prop        | type        | description                 |
| ----------- | ----------- | --------------------------- |
| `container` | `ViewStyle` | Styles for the container    |
| `text`      | `TextStyle` | Styles for the text content |

##### FieldErrorRootAnimation

Animation configuration for field error root component. Can be:

- `false` or `"disabled"`: Disable only root animations
- `"disable-all"`: Disable all animations including children
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop             | type                                     | default                                                               | description                                     |
| ---------------- | ---------------------------------------- | --------------------------------------------------------------------- | ----------------------------------------------- |
| `state`          | `'disabled' \| 'disable-all' \| boolean` | -                                                                     | Disable animations while customizing properties |
| `entering.value` | `EntryOrExitLayoutType`                  | `FadeIn``.duration(150)``.easing(Easing.out(Easing.ease))`  | Custom entering animation for field error       |
| `exiting.value`  | `EntryOrExitLayoutType`                  | `FadeOut``.duration(100)``.easing(Easing.out(Easing.ease))` | Custom exiting animation for field error        |

### Examples from field-error.md

#### Field Error Component

The Field Error component displays validation error messages.

##### Installation

```bash
npm install heroui-native
```

##### Import

```tsx
import { FieldError } from 'heroui-native';
```

##### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `className` | `string` | - | Additional CSS classes |

##### Example: Basic Field Error

```tsx
import { FieldError, Label, Input, TextField } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function BasicFieldErrorExample() {
  const [value, setValue] = useState('');
  const [error, setError] = useState('');

  const validate = (text: string) => {
    if (text.length < 3) {
      setError('Username must be at least 3 characters');
    } else {
      setError('');
    }
  };

  return (
    <View className="p-5">
      <TextField isInvalid={!!error}>
        <Label>Username</Label>
        <Input
          value={value}
          onChangeText={(text) => {
            setValue(text);
            validate(text);
          }}
        />
        <FieldError>{error}</FieldError>
      </TextField>
    </View>
  );
}
```

##### Complete Example Code

```tsx
import { FieldError, Label, Input, TextField } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function FieldErrorExample() {
  const [value, setValue] = useState('');
  const [error, setError] = useState('');

  const validate = (text: string) => {
    if (text.length < 3) {
      setError('Username must be at least 3 characters');
    } else {
      setError('');
    }
  };

  return (
    <View className="flex-1 p-5 justify-center">
      <TextField isInvalid={!!error}>
        <Label>Username</Label>
        <Input
          value={value}
          onChangeText={(text) => {
            setValue(text);
            validate(text);
          }}
        />
        <FieldError>{error}</FieldError>
      </TextField>
    </View>
  );
}
```

---

## ControlField

A field component that combines a label, description (or other content), and a control component (Switch or Checkbox) into a single pressable area.

**Source:** `control-field/control-field.tsx`
**Styles:** `control-field/control-field.styles.ts`

### Import

```tsx
import { ControlField } from 'heroui-native';
```

### Anatomy

```tsx
<ControlField>
  <Label>...</Label>
  <Description>...</Description>
  <ControlField.Indicator>...</ControlField.Indicator>
  <FieldError>...</FieldError>
</ControlField>
```

- **ControlField**: Root container that manages layout and state propagation
- **Label**: Primary text label for the control (from Label component)
- **Description**: Secondary descriptive helper text (from Description component)
- **ControlField.Indicator**: Container for the form control component (Switch, Checkbox, Radio)
- **FieldError**: Validation error message display (from FieldError component)

### Usage

#### Basic Usage

ControlField wraps form controls to provide consistent layout and state management.

```tsx
<ControlField isSelected={value} onSelectedChange={setValue}>
  <Label className="flex-1">Label text</Label>
  <ControlField.Indicator />
</ControlField>
```

#### With Description

Add helper text below the label using the Description component.

```tsx
<ControlField isSelected={value} onSelectedChange={setValue}>
  <View className="flex-1">
    <Label>Enable notifications</Label>
    <Description>
      Receive push notifications about your account activity
    </Description>
  </View>
  <ControlField.Indicator />
</ControlField>
```

#### With Error Message

Display validation errors using the ErrorMessage component.

```tsx
<ControlField
  isSelected={value}
  onSelectedChange={setValue}
  isInvalid={!value}
  className="flex-col items-start gap-1"
>
  <View className="flex-row items-center gap-2">
    <View className="flex-1">
      <Label>I agree to the terms</Label>
      <Description>
        By checking this box, you agree to our Terms of Service
      </Description>
    </View>
    <ControlField.Indicator variant="checkbox" />
  </View>
  <FieldError>This field is required</FieldError>
</ControlField>
```

#### Disabled State

Control interactivity with the disabled prop.

```tsx
<ControlField isSelected={value} onSelectedChange={setValue} isDisabled>
  <View className="flex-1">
    <Label>Disabled field</Label>
    <Description>This field is disabled</Description>
  </View>
  <ControlField.Indicator />
</ControlField>
```

#### Disabling All Animations

Disable all animations including children by using `"disable-all"`. This cascades down to all child components.

```tsx
<ControlField
  isSelected={value}
  onSelectedChange={setValue}
  animation="disable-all"
>
  <View className="flex-1">
    <Label>Label text</Label>
    <Description>Description text</Description>
  </View>
  <ControlField.Indicator />
</ControlField>
```

### Example

```tsx
import {
  Checkbox,
  Description,
  FieldError,
  ControlField,
  Label,
  Switch,
} from 'heroui-native';
import React from 'react';
import { ScrollView, View } from 'react-native';

export default function ControlFieldExample() {
  const [notifications, setNotifications] = React.useState(false);
  const [terms, setTerms] = React.useState(false);
  const [newsletter, setNewsletter] = React.useState(true);

  return (
    <ScrollView className="bg-background p-4">
      <View className="gap-4">
        <ControlField
          isSelected={notifications}
          onSelectedChange={setNotifications}
        >
          <View className="flex-1">
            <Label>Enable notifications</Label>
            <Description>
              Receive push notifications about your account activity
            </Description>
          </View>
          <ControlField.Indicator />
        </ControlField>

        <ControlField
          isSelected={terms}
          onSelectedChange={setTerms}
          isInvalid={!terms}
          className="flex-col items-start gap-1"
        >
          <View className="flex-row items-center gap-2">
            <View className="flex-1">
              <Label>I agree to the terms and conditions</Label>
              <Description>
                By checking this box, you agree to our Terms of Service
              </Description>
            </View>
            <ControlField.Indicator className="mt-0.5">
              <Checkbox />
            </ControlField.Indicator>
          </View>
          <FieldError>This field is required</FieldError>
        </ControlField>

        <ControlField isSelected={newsletter} onSelectedChange={setNewsletter}>
          <View className="flex-1">
            <Label>Subscribe to newsletter</Label>
          </View>
          <ControlField.Indicator>
            <Checkbox color="warning" />
          </ControlField.Indicator>
        </ControlField>
      </View>
    </ScrollView>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/control-field.tsx).

### API Reference

#### ControlField

| prop              | type                                                                       | default     | description                                                                               |
| ----------------- | -------------------------------------------------------------------------- | ----------- | ----------------------------------------------------------------------------------------- |
| children          | `React.ReactNode \| ((props: ControlFieldRenderProps) => React.ReactNode)` | -           | Content to render inside the form control, or a render function                           |
| isSelected        | `boolean`                                                                  | `undefined` | Whether the control is selected/checked                                                   |
| isDisabled        | `boolean`                                                                  | `false`     | Whether the form control is disabled                                                      |
| isInvalid         | `boolean`                                                                  | `false`     | Whether the form control is invalid                                                       |
| isRequired        | `boolean`                                                                  | `false`     | Whether the form control is required                                                      |
| className         | `string`                                                                   | -           | Custom class name for the root element                                                    |
| onSelectedChange  | `(isSelected: boolean) => void`                                            | -           | Callback when selection state changes                                                     |
| animation         | `"disable-all" \| undefined`                                               | `undefined` | Animation configuration. Use `"disable-all"` to disable all animations including children |
| ...PressableProps | `PressableProps`                                                           | -           | All React Native Pressable props are supported                                            |

#### Label (within ControlField)

The `Label` component automatically consumes form state (`isDisabled`, `isInvalid`) from the ControlField context.

**Note**: For complete prop documentation, see the [Label component documentation](#label).

#### Description (within ControlField)

The `Description` component automatically consumes form state (`isDisabled`, `isInvalid`) from the ControlField context.

**Note**: For complete prop documentation, see the [Description component documentation](#description).

#### ControlField.Indicator

| prop         | type                                | default    | description                                                |
| ------------ | ----------------------------------- | ---------- | ---------------------------------------------------------- |
| children     | `React.ReactNode`                   | -          | Control component to render (Switch, Checkbox, Radio)      |
| variant      | `'checkbox' \| 'radio' \| 'switch'` | `'switch'` | Variant of the control to render when no children provided |
| className    | `string`                            | -          | Custom class name for the indicator element                |
| ...ViewProps | `ViewProps`                         | -          | All React Native View props are supported                  |

**Note**: When children are provided, the component automatically passes down `isSelected`, `onSelectedChange`, `isDisabled`, and `isInvalid` props from the ControlField context if they are not already present on the child component. When using the `radio` variant, the Radio component renders in standalone mode (outside of a RadioGroup).

#### FieldError (within ControlField)

The `FieldError` component automatically consumes form state (`isInvalid`) from the ControlField context.

**Note**: For complete prop documentation, see the [FieldError component documentation](#fielderror). The error message visibility is controlled by the `isInvalid` state of the parent ControlField.

### Hooks

#### useControlField

**Returns:**

| property           | type                                           | description                                    |
| ------------------ | ---------------------------------------------- | ---------------------------------------------- |
| `isSelected`       | `boolean \| undefined`                         | Whether the control is selected/checked        |
| `onSelectedChange` | `((isSelected: boolean) => void) \| undefined` | Callback when selection state changes          |
| `isDisabled`       | `boolean`                                      | Whether the form control is disabled           |
| `isInvalid`        | `boolean`                                      | Whether the form control is invalid            |
| `isPressed`        | `SharedValue<boolean>`                         | Reanimated shared value indicating press state |

### Examples from control-field.md

#### Control Field Component

The Control Field component wraps form controls with label and indicator support.

##### Installation

```bash
npm install heroui-native
```

##### Import

```tsx
import { ControlField, Label, Description } from 'heroui-native';
```

##### Components

- `ControlField` - Root container component
- `ControlField.Indicator` - Control indicator (checkbox/radio/switch)

##### Props

###### ControlField Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `isSelected` | `boolean` | `false` | Selected state |
| `onSelectedChange` | `(value: boolean) => void` | - | Selection change handler |
| `className` | `string` | - | Additional CSS classes |

##### Example: Basic Control Field

```tsx
import { ControlField, Checkbox, Label, Description } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

function BasicControlFieldExample() {
  const [isSelected, setIsSelected] = useState(false);

  return (
    <View className="p-5">
      <ControlField
        isSelected={isSelected}
        onSelectedChange={setIsSelected}
        className="items-start"
      >
        <ControlField.Indicator>
          <Checkbox className="mt-0.5" />
        </ControlField.Indicator>
        <View className="flex-1">
          <Label>Subscribe to newsletter</Label>
          <Description>Get weekly updates</Description>
        </View>
      </ControlField>
    </View>
  );
}
```

##### Complete Example Code

```tsx
import { ControlField, Checkbox, Label, Description } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function ControlFieldExample() {
  const [isSelected, setIsSelected] = useState(false);

  return (
    <View className="flex-1 p-5 justify-center">
      <ControlField
        isSelected={isSelected}
        onSelectedChange={setIsSelected}
        className="items-start"
      >
        <ControlField.Indicator>
          <Checkbox className="mt-0.5" />
        </ControlField.Indicator>
        <View className="flex-1">
          <Label>Subscribe to newsletter</Label>
          <Description>Get weekly updates about new features</Description>
        </View>
      </ControlField>
    </View>
  );
}
```
