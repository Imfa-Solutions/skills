# Overlay Components

## Table of Contents
- [Dialog](#dialog)
- [BottomSheet](#bottomsheet)
- [Popover](#popover)
- [Toast](#toast)

---

## Dialog

Displays a modal overlay with animated transitions and gesture-based dismissal.

### Import

```tsx
import { Dialog } from 'heroui-native';
```

### Anatomy

```tsx
<Dialog>
  <Dialog.Trigger>...</Dialog.Trigger>
  <Dialog.Portal>
    <Dialog.Overlay>...</Dialog.Overlay>
    <Dialog.Content>
      <Dialog.Close>...</Dialog.Close>
      <Dialog.Title>...</Dialog.Title>
      <Dialog.Description>...</Dialog.Description>
    </Dialog.Content>
  </Dialog.Portal>
</Dialog>
```

- **Dialog**: Root component that manages open state and provides context to child components.
- **Dialog.Trigger**: Pressable element that opens the dialog when pressed.
- **Dialog.Portal**: Renders dialog content in a portal with centered layout and animation control.
- **Dialog.Overlay**: Background overlay that appears behind the dialog content, typically closes dialog when pressed.
- **Dialog.Content**: Main dialog container with gesture support for drag-to-dismiss.
- **Dialog.Close**: Close button for the dialog. Can accept custom children or uses default close icon.
- **Dialog.Title**: Dialog title text with semantic heading role.
- **Dialog.Description**: Dialog description text that provides additional context.

### Usage

#### Basic Dialog

Simple dialog with title, description, and close button.

```tsx
<Dialog isOpen={isOpen} onOpenChange={setIsOpen}>
  <Dialog.Trigger asChild>
    <Button>Open Dialog</Button>
  </Dialog.Trigger>
  <Dialog.Portal>
    <Dialog.Overlay />
    <Dialog.Content>
      <Dialog.Close />
      <Dialog.Title>...</Dialog.Title>
      <Dialog.Description>...</Dialog.Description>
    </Dialog.Content>
  </Dialog.Portal>
</Dialog>
```

#### Scrollable Content

Handle long content with scroll views.

```tsx
<Dialog isOpen={isOpen} onOpenChange={setIsOpen}>
  <Dialog.Trigger>...</Dialog.Trigger>
  <Dialog.Portal>
    <Dialog.Overlay />
    <Dialog.Content>
      <Dialog.Close />
      <Dialog.Title>...</Dialog.Title>
      <View className="h-[300px]">
        <ScrollView>...</ScrollView>
      </View>
    </Dialog.Content>
  </Dialog.Portal>
</Dialog>
```

#### Form Dialog

Dialog with text inputs and keyboard handling.

```tsx
<Dialog isOpen={isOpen} onOpenChange={setIsOpen}>
  <Dialog.Trigger>...</Dialog.Trigger>
  <Dialog.Portal>
    <Dialog.Overlay />
    <KeyboardAvoidingView behavior="padding">
      <Dialog.Content>
        <Dialog.Close />
        <Dialog.Title>...</Dialog.Title>
        <TextField>...</TextField>
        <Button onPress={handleSubmit}>Submit</Button>
      </Dialog.Content>
    </KeyboardAvoidingView>
  </Dialog.Portal>
</Dialog>
```

### Docs Example

```tsx
import { Button, Dialog } from 'heroui-native';
import { View } from 'react-native';
import { useState } from 'react';

export default function DialogExample() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <Dialog isOpen={isOpen} onOpenChange={setIsOpen}>
      <Dialog.Trigger asChild>
        <Button variant="primary">Open Dialog</Button>
      </Dialog.Trigger>
      <Dialog.Portal>
        <Dialog.Overlay />
        <Dialog.Content>
          <Dialog.Close variant="ghost" />
          <View className="mb-5 gap-1.5">
            <Dialog.Title>Confirm Action</Dialog.Title>
            <Dialog.Description>
              Are you sure you want to proceed with this action? This cannot be
              undone.
            </Dialog.Description>
          </View>
          <View className="flex-row justify-end gap-3">
            <Button variant="ghost" size="sm" onPress={() => setIsOpen(false)}>
              Cancel
            </Button>
            <Button size="sm">Confirm</Button>
          </View>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog>
  );
}
```

### API Reference

#### Dialog

| prop            | type                       | default | description                                        |
| --------------- | -------------------------- | ------- | -------------------------------------------------- |
| `children`      | `React.ReactNode`          | -       | Dialog content and trigger elements                |
| `isOpen`        | `boolean`                  | -       | Controlled open state of the dialog                |
| `isDefaultOpen` | `boolean`                  | `false` | Initial open state when uncontrolled               |
| `animation`     | `AnimationRootDisableAll`  | -       | Animation configuration                            |
| `onOpenChange`  | `(value: boolean) => void` | -       | Callback when open state changes                   |
| `...ViewProps`  | `ViewProps`                | -       | All standard React Native View props are supported |

##### AnimationRootDisableAll

Animation configuration for dialog root component. Can be:

- `false` or `"disabled"`: Disable only root animations
- `"disable-all"`: Disable all animations including children
- `true` or `undefined`: Use default animations

#### Dialog.Trigger

| prop                       | type                    | default | description                                                    |
| -------------------------- | ----------------------- | ------- | -------------------------------------------------------------- |
| `children`                 | `React.ReactNode`       | -       | Trigger element content                                        |
| `asChild`                  | `boolean`               | -       | Render as child element without wrapper                        |
| `...TouchableOpacityProps` | `TouchableOpacityProps` | -       | All standard React Native TouchableOpacity props are supported |

#### Dialog.Portal

| prop                       | type                   | default | description                                                                                                                   |
| -------------------------- | ---------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `children`                 | `React.ReactNode`      | -       | Portal content (overlay and dialog)                                                                                           |
| `disableFullWindowOverlay` | `boolean`              | `false` | When true on iOS, uses View instead of FullWindowOverlay. Enables element inspector; overlay won't appear above native modals |
| `className`                | `string`               | -       | Additional CSS classes for portal container                                                                                   |
| `style`                    | `StyleProp<ViewStyle>` | -       | Additional styles for portal container                                                                                        |
| `hostName`                 | `string`               | -       | Optional portal host name for specific container                                                                              |
| `forceMount`               | `boolean`              | -       | Force mount when closed for animation purposes                                                                                |

#### Dialog.Overlay

| prop                    | type                     | default | description                                                  |
| ----------------------- | ------------------------ | ------- | ------------------------------------------------------------ |
| `children`              | `React.ReactNode`        | -       | Custom overlay content                                       |
| `className`             | `string`                 | -       | Additional CSS classes for overlay                           |
| `style`                 | `ViewStyle`              | -       | Additional styles for overlay container                      |
| `animation`             | `DialogOverlayAnimation` | -       | Animation configuration                                      |
| `isAnimatedStyleActive` | `boolean`                | `true`  | Whether animated styles (react-native-reanimated) are active |
| `isCloseOnPress`        | `boolean`                | `true`  | Whether pressing overlay closes dialog                       |
| `forceMount`            | `boolean`                | -       | Force mount when closed for animation purposes               |
| `...PressableProps`     | `PressableProps`         | -       | All standard React Native Pressable props are supported      |

##### DialogOverlayAnimation

Animation configuration for dialog overlay component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop            | type                       | default                 | description                                                                  |
| --------------- | -------------------------- | ----------------------- | ---------------------------------------------------------------------------- |
| `state`         | `'disabled' \| boolean`    | -                       | Disable animations while customizing properties                              |
| `opacity.value` | `[number, number, number]` | `[0, 1, 0]`             | Opacity values [idle, open, close] (progress-based, for dialog presentation) |
| `entering`      | `EntryOrExitLayoutType`    | `FadeIn.duration(200)`  | Custom entering animation (for popover presentation)                         |
| `exiting`       | `EntryOrExitLayoutType`    | `FadeOut.duration(150)` | Custom exiting animation (for popover presentation)                          |

#### Dialog.Content

| prop                    | type                     | default | description                                         |
| ----------------------- | ------------------------ | ------- | --------------------------------------------------- |
| `children`              | `React.ReactNode`        | -       | Dialog content                                      |
| `className`             | `string`                 | -       | Additional CSS classes for content container        |
| `style`                 | `StyleProp<ViewStyle>`   | -       | Additional styles for content container             |
| `animation`             | `DialogContentAnimation` | -       | Animation configuration                             |
| `isSwipeable`           | `boolean`                | `true`  | Whether the dialog content can be swiped to dismiss |
| `forceMount`            | `boolean`                | -       | Force mount when closed for animation purposes      |
| `...Animated.ViewProps` | `Animated.ViewProps`     | -       | All Reanimated Animated.View props are supported    |

##### DialogContentAnimation

Animation configuration for dialog content component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop       | type                    | default                                                                                      | description                                     |
| ---------- | ----------------------- | -------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| `state`    | `'disabled' \| boolean` | -                                                                                            | Disable animations while customizing properties |
| `entering` | `EntryOrExitLayoutType` | Keyframe with `scale: 0.96->1` and `opacity: 0->1` (200ms, easing: `Easing.out(Easing.ease)`) | Custom entering animation                       |
| `exiting`  | `EntryOrExitLayoutType` | Keyframe with `scale: 1->0.96` and `opacity: 1->0` (150ms, easing: `Easing.in(Easing.ease)`)  | Custom exiting animation                        |

#### Dialog.Close

Dialog.Close extends [CloseButton](./close-button) and automatically handles dialog dismissal when pressed.

#### Dialog.Title

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Title content                                      |
| `className`    | `string`          | -       | Additional CSS classes for title                   |
| `...TextProps` | `TextProps`       | -       | All standard React Native Text props are supported |

#### Dialog.Description

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Description content                                |
| `className`    | `string`          | -       | Additional CSS classes for description             |
| `...TextProps` | `TextProps`       | -       | All standard React Native Text props are supported |

### Hooks

#### useDialog

Hook to access dialog primitive context.

```tsx
const { isOpen, onOpenChange } = useDialog();
```

| property       | type                       | description                   |
| -------------- | -------------------------- | ----------------------------- |
| `isOpen`       | `boolean`                  | Current open state            |
| `onOpenChange` | `(value: boolean) => void` | Function to change open state |

#### useDialogAnimation

Hook to access dialog animation context for advanced customization.

```tsx
const { progress, isDragging, isGestureReleaseAnimationRunning } =
  useDialogAnimation();
```

| property                           | type                   | description                                  |
| ---------------------------------- | ---------------------- | -------------------------------------------- |
| `progress`                         | `SharedValue<number>`  | Animation progress (0=idle, 1=open, 2=close) |
| `isDragging`                       | `SharedValue<boolean>` | Whether dialog is being dragged              |
| `isGestureReleaseAnimationRunning` | `SharedValue<boolean>` | Whether gesture release animation is running |

### Special Notes

#### Element Inspector (iOS)

Dialog uses FullWindowOverlay on iOS. To enable the React Native element inspector during development, set `disableFullWindowOverlay={true}` on `Dialog.Portal`. Tradeoff: the dialog will not appear above native modals when disabled.

### Examples from dialog.md

#### Example: Basic Dialog

```tsx
import { Dialog, Button } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';
import { FloppyDiscIcon } from './icons';

function BasicDialogExample() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <View className="flex-1 items-center justify-center">
      <Dialog isOpen={isOpen} onOpenChange={setIsOpen}>
        <Dialog.Trigger asChild>
          <Button variant="secondary">Basic dialog</Button>
        </Dialog.Trigger>
        <Dialog.Portal>
          <Dialog.Overlay />
          <Dialog.Content>
            <Dialog.Close
              variant="ghost"
              className="absolute top-3 right-2.5 z-50"
            />
            <View className="size-9 items-center justify-center rounded-full bg-overlay-foreground/5 mb-4">
              <FloppyDiscIcon size={16} colorClassName="accent-warning" />
            </View>
            <View className="mb-8 gap-1.5">
              <Dialog.Title>Low Disk Space</Dialog.Title>
              <Dialog.Description>
                You are running low on disk space. Delete unnecessary files to
                free up space.
              </Dialog.Description>
            </View>
            <View className="flex-row justify-end gap-3">
              <Button
                variant="tertiary"
                className="bg-overlay-foreground/5"
                onPress={() => setIsOpen(false)}
              >
                Confirm
              </Button>
            </View>
          </Dialog.Content>
        </Dialog.Portal>
      </Dialog>
    </View>
  );
}
```

#### Example: Dialog with Blur Backdrop (iOS)

```tsx
import { Dialog, Button } from 'heroui-native';
import { Platform } from 'react-native';
import { TrashIcon } from './icons';

function BlurBackdropDialogExample() {
  const [isOpen, setIsOpen] = useState(false);

  if (Platform.OS !== 'ios') {
    return null;
  }

  return (
    <View className="flex-1 items-center justify-center">
      <Dialog isOpen={isOpen} onOpenChange={setIsOpen}>
        <Dialog.Trigger asChild>
          <Button variant="secondary">Dialog with blur backdrop</Button>
        </Dialog.Trigger>
        <Dialog.Portal>
          <DialogBlurBackdrop />
          <Dialog.Content className="max-w-sm mx-auto">
            <View className="size-10 items-center justify-center rounded-full bg-overlay-foreground/5 mb-4">
              <TrashIcon size={16} colorClassName="accent-danger" />
            </View>
            <View className="mb-8 gap-1">
              <Dialog.Title>Delete product</Dialog.Title>
              <Dialog.Description>
                Are you sure you want to delete this product? This action
                cannot be undone.
              </Dialog.Description>
            </View>
            <View className="gap-3">
              <Button variant="danger" onPress={simulatePress}>
                Delete
              </Button>
              <Button
                variant="tertiary"
                className="bg-overlay-foreground/5"
                onPress={() => setIsOpen(false)}
              >
                Cancel
              </Button>
            </View>
          </Dialog.Content>
        </Dialog.Portal>
      </Dialog>
    </View>
  );
}
```

#### Example: Dialog with Text Input

```tsx
import { Dialog, Button, FieldError, Input, Label, TextField } from 'heroui-native';
import { useState } from 'react';
import { useWindowDimensions } from 'react-native';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import { FadeInDown, FadeOutDown, Easing } from 'react-native-reanimated';

function TextInputDialogExample() {
  const [isOpen, setIsOpen] = useState(false);
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [nameError, setNameError] = useState('');
  const [emailError, setEmailError] = useState('');

  const { height } = useWindowDimensions();
  const insets = useSafeAreaInsets();
  const insetTop = insets.top + 12;
  const maxHeight = (height - insetTop) / 2;

  const validateEmail = (emailValue: string) => {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(emailValue);
  };

  const handleSubmit = () => {
    let hasError = false;

    if (!name.trim()) {
      setNameError('Name is required');
      hasError = true;
    } else if (name.trim().length < 2) {
      setNameError('Name must be at least 2 characters');
      hasError = true;
    } else {
      setNameError('');
    }

    if (!email.trim()) {
      setEmailError('Email is required');
      hasError = true;
    } else if (!validateEmail(email)) {
      setEmailError('Please enter a valid email address');
      hasError = true;
    } else {
      setEmailError('');
    }

    if (!hasError) {
      setName('');
      setEmail('');
      setIsOpen(false);
    }
  };

  return (
    <View className="flex-1 items-center justify-center">
      <Dialog isOpen={isOpen} onOpenChange={setIsOpen}>
        <Dialog.Trigger asChild>
          <Button variant="secondary">Dialog with text input</Button>
        </Dialog.Trigger>
        <Dialog.Portal className="justify-start">
          <DialogBlurBackdrop />
          <Dialog.Content
            className="bg-surface"
            animation={{
              entering: FadeInDown.duration(200).easing(Easing.out(Easing.ease)),
              exiting: FadeOutDown.duration(150).easing(Easing.in(Easing.ease)),
            }}
            style={{
              marginTop: insetTop,
              height: maxHeight,
            }}
          >
            <View className="items-end">
              <Dialog.Close />
            </View>
            <Dialog.Title className="mb-6">Update Profile</Dialog.Title>

            <View className="flex-1">
              <ScrollView contentContainerClassName="gap-5">
                <TextField isRequired isInvalid={!!nameError}>
                  <Label>Full Name</Label>
                  <Input
                    variant="secondary"
                    placeholder="Enter your name"
                    value={name}
                    onChangeText={(text) => {
                      setName(text);
                      if (nameError) setNameError('');
                    }}
                    autoCapitalize="words"
                    autoFocus
                  />
                  <FieldError>{nameError}</FieldError>
                </TextField>

                <TextField isRequired isInvalid={!!emailError}>
                  <Label>Email Address</Label>
                  <Input
                    variant="secondary"
                    placeholder="email@example.com"
                    value={email}
                    onChangeText={(text) => {
                      setEmail(text);
                      if (emailError) setEmailError('');
                    }}
                    autoCapitalize="none"
                  />
                  <FieldError>{emailError}</FieldError>
                </TextField>
              </ScrollView>
            </View>

            <View className="flex-row justify-end gap-3 pt-3">
              <Button variant="ghost" size="sm" onPress={() => setIsOpen(false)}>
                Cancel
              </Button>
              <Button size="sm" onPress={handleSubmit}>
                Update Profile
              </Button>
            </View>
          </Dialog.Content>
        </Dialog.Portal>
      </Dialog>
    </View>
  );
}
```

#### Complete Example Code

```tsx
import { Dialog, Button } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';
import { FloppyDiscIcon, TrashIcon } from './icons';

const BasicDialogContent = () => {
  const [basicDialogOpen, setBasicDialogOpen] = useState(false);

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <Dialog isOpen={basicDialogOpen} onOpenChange={setBasicDialogOpen}>
          <Dialog.Trigger asChild>
            <Button variant="secondary">Basic dialog</Button>
          </Dialog.Trigger>
          <Dialog.Portal>
            <Dialog.Overlay />
            <Dialog.Content>
              <Dialog.Close
                variant="ghost"
                className="absolute top-3 right-2.5 z-50"
              />
              <View className="size-9 items-center justify-center rounded-full bg-overlay-foreground/5 mb-4">
                <FloppyDiscIcon size={16} colorClassName="accent-warning" />
              </View>
              <View className="mb-8 gap-1.5">
                <Dialog.Title>Low Disk Space</Dialog.Title>
                <Dialog.Description>
                  You are running low on disk space.
                </Dialog.Description>
              </View>
              <View className="flex-row justify-end gap-3">
                <Button
                  variant="tertiary"
                  className="bg-overlay-foreground/5"
                  onPress={() => setBasicDialogOpen(false)}
                >
                  Confirm
                </Button>
              </View>
            </Dialog.Content>
          </Dialog.Portal>
        </Dialog>
      </View>
    </View>
  );
};

export default function DialogExample() {
  return <BasicDialogContent />;
}
```

---

## BottomSheet

Displays a bottom sheet that slides up from the bottom with animated transitions and swipe-to-dismiss gestures.

### Import

```tsx
import { BottomSheet } from 'heroui-native';
```

### Anatomy

```tsx
<BottomSheet>
  <BottomSheet.Trigger>...</BottomSheet.Trigger>
  <BottomSheet.Portal>
    <BottomSheet.Overlay>...</BottomSheet.Overlay>
    <BottomSheet.Content>
      <BottomSheet.Close />
      <BottomSheet.Title>...</BottomSheet.Title>
      <BottomSheet.Description>...</BottomSheet.Description>
    </BottomSheet.Content>
  </BottomSheet.Portal>
</BottomSheet>
```

- **BottomSheet**: Root component that manages open state and provides context to child components.
- **BottomSheet.Trigger**: Pressable element that opens the bottom sheet when pressed.
- **BottomSheet.Portal**: Renders bottom sheet content in a portal with full window overlay.
- **BottomSheet.Overlay**: Background overlay that covers the screen, typically closes bottom sheet when pressed.
- **BottomSheet.Content**: Main bottom sheet container using @gorhom/bottom-sheet for rendering with gesture support.
- **BottomSheet.Close**: Close button for the bottom sheet. Can accept custom children or uses default close icon.
- **BottomSheet.Title**: Bottom sheet title text with semantic heading role and accessibility linking.
- **BottomSheet.Description**: Bottom sheet description text that provides additional context with accessibility linking.

### Usage

#### Basic Bottom Sheet

Simple bottom sheet with title, description, and close button.

```tsx
<BottomSheet>
  <BottomSheet.Trigger asChild>
    <Button>Open Bottom Sheet</Button>
  </BottomSheet.Trigger>
  <BottomSheet.Portal>
    <BottomSheet.Overlay />
    <BottomSheet.Content>
      <BottomSheet.Close />
      <BottomSheet.Title>...</BottomSheet.Title>
      <BottomSheet.Description>...</BottomSheet.Description>
    </BottomSheet.Content>
  </BottomSheet.Portal>
</BottomSheet>
```

#### Detached Bottom Sheet

Bottom sheet that appears detached from the bottom edge with custom spacing.

```tsx
<BottomSheet>
  <BottomSheet.Trigger>...</BottomSheet.Trigger>
  <BottomSheet.Portal>
    <BottomSheet.Overlay />
    <BottomSheet.Content
      detached={true}
      bottomInset={insets.bottom + 12}
      className="mx-4"
      backgroundClassName="rounded-[32px]"
    >
      ...
    </BottomSheet.Content>
  </BottomSheet.Portal>
</BottomSheet>
```

#### Scrollable with Snap Points

Bottom sheet with multiple snap points and scrollable content.

```tsx
<BottomSheet>
  <BottomSheet.Trigger>...</BottomSheet.Trigger>
  <BottomSheet.Portal>
    <BottomSheet.Overlay />
    <BottomSheet.Content snapPoints={['25%', '50%', '90%']}>
      <ScrollView>...</ScrollView>
    </BottomSheet.Content>
  </BottomSheet.Portal>
</BottomSheet>
```

#### Custom Overlay

Replace the default overlay with custom content like blur effects.

```tsx
import { useBottomSheet, useBottomSheetAnimation } from 'heroui-native';
import { StyleSheet, Pressable } from 'react-native';
import { interpolate, useDerivedValue } from 'react-native-reanimated';
import { AnimatedBlurView } from './animated-blur-view';
import { useUniwind } from 'uniwind';

export const BottomSheetBlurOverlay = () => {
  const { theme } = useUniwind();
  const { onOpenChange } = useBottomSheet();
  const { progress } = useBottomSheetAnimation();

  const blurIntensity = useDerivedValue(() => {
    return interpolate(progress.get(), [0, 1, 2], [0, 40, 0]);
  });

  return (
    <Pressable
      style={StyleSheet.absoluteFill}
      onPress={() => onOpenChange(false)}
    >
      <AnimatedBlurView
        blurIntensity={blurIntensity}
        tint={theme === 'dark' ? 'dark' : 'systemUltraThinMaterialDark'}
        style={StyleSheet.absoluteFill}
      />
    </Pressable>
  );
};
```

```tsx
<BottomSheet>
  <BottomSheet.Trigger>...</BottomSheet.Trigger>
  <BottomSheet.Portal>
    <BottomSheetBlurOverlay />
    <BottomSheet.Content>...</BottomSheet.Content>
  </BottomSheet.Portal>
</BottomSheet>
```

### Docs Example

```tsx
import { BottomSheet, Button } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';
import { withUniwind } from 'uniwind';
import Ionicons from '@expo/vector-icons/Ionicons';

const StyledIonicons = withUniwind(Ionicons);

export default function BottomSheetExample() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
      <BottomSheet.Trigger asChild>
        <Button variant="secondary">Open Bottom Sheet</Button>
      </BottomSheet.Trigger>
      <BottomSheet.Portal>
        <BottomSheet.Overlay />
        <BottomSheet.Content>
          <View className="items-center mb-5">
            <View className="size-20 items-center justify-center rounded-full bg-green-500/10">
              <StyledIonicons
                name="shield-checkmark"
                size={40}
                className="text-green-500"
              />
            </View>
          </View>
          <View className="mb-8 gap-2 items-center">
            <BottomSheet.Title className="text-center">
              Keep yourself safe
            </BottomSheet.Title>
            <BottomSheet.Description className="text-center">
              Update your software to the latest version for better security and
              performance.
            </BottomSheet.Description>
          </View>
          <View className="gap-3">
            <Button onPress={() => setIsOpen(false)}>Update Now</Button>
            <Button variant="tertiary" onPress={() => setIsOpen(false)}>
              Later
            </Button>
          </View>
        </BottomSheet.Content>
      </BottomSheet.Portal>
    </BottomSheet>
  );
}
```

### API Reference

#### BottomSheet

| prop            | type                       | default | description                                        |
| --------------- | -------------------------- | ------- | -------------------------------------------------- |
| `children`      | `React.ReactNode`          | -       | Bottom sheet content and trigger elements          |
| `isOpen`        | `boolean`                  | -       | Controlled open state of the bottom sheet          |
| `isDefaultOpen` | `boolean`                  | `false` | Initial open state when uncontrolled               |
| `animation`     | `AnimationRootDisableAll`  | -       | Animation configuration                            |
| `onOpenChange`  | `(value: boolean) => void` | -       | Callback when open state changes                   |
| `...ViewProps`  | `ViewProps`                | -       | All standard React Native View props are supported |

##### Animation Configuration

Animation configuration for bottom sheet root component. Can be:

- `"disable-all"`: Disable all animations including children
- `undefined`: Use default animations

#### BottomSheet.Trigger

| prop                       | type                    | default | description                                                    |
| -------------------------- | ----------------------- | ------- | -------------------------------------------------------------- |
| `children`                 | `React.ReactNode`       | -       | Trigger element content                                        |
| `asChild`                  | `boolean`               | -       | Render as child element without wrapper                        |
| `...TouchableOpacityProps` | `TouchableOpacityProps` | -       | All standard React Native TouchableOpacity props are supported |

#### BottomSheet.Portal

| prop                       | type                   | default | description                                                                                                                   |
| -------------------------- | ---------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `children`                 | `React.ReactNode`      | -       | Portal content (overlay and bottom sheet)                                                                                     |
| `disableFullWindowOverlay` | `boolean`              | `false` | When true on iOS, uses View instead of FullWindowOverlay. Enables element inspector; overlay won't appear above native modals |
| `className`                | `string`               | -       | Additional CSS classes for portal container                                                                                   |
| `style`                    | `StyleProp<ViewStyle>` | -       | Additional styles for portal container                                                                                        |
| `hostName`                 | `string`               | -       | Optional portal host name for specific container                                                                              |
| `forceMount`               | `boolean`              | -       | Force mount when closed for animation purposes                                                                                |

#### BottomSheet.Overlay

| prop                    | type                                                   | default | description                                                  |
| ----------------------- | ------------------------------------------------------ | ------- | ------------------------------------------------------------ |
| `children`              | `React.ReactNode`                                      | -       | Custom overlay content                                       |
| `className`             | `string`                                               | -       | Additional CSS classes for overlay                           |
| `style`                 | `ViewStyle`                                            | -       | Additional styles for overlay container                      |
| `animation`             | `Omit<PopupOverlayAnimation, 'entering' \| 'exiting'>` | -       | Animation configuration                                      |
| `isAnimatedStyleActive` | `boolean`                                              | `true`  | Whether animated styles (react-native-reanimated) are active |
| `isCloseOnPress`        | `boolean`                                              | `true`  | Whether pressing overlay closes bottom sheet                 |
| `...PressableProps`     | `PressableProps`                                       | -       | All standard React Native Pressable props are supported      |

##### Animation Configuration

Animation configuration for bottom sheet overlay component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration (excluding `entering` and `exiting` properties)

| prop            | type                       | default     | description                                     |
| --------------- | -------------------------- | ----------- | ----------------------------------------------- |
| `state`         | `'disabled' \| boolean`    | -           | Disable animations while customizing properties |
| `opacity.value` | `[number, number, number]` | `[0, 1, 0]` | Opacity values [idle, open, close]              |

#### BottomSheet.Content

| prop                        | type                                     | default | description                                                                                        |
| --------------------------- | ---------------------------------------- | ------- | -------------------------------------------------------------------------------------------------- |
| `children`                  | `React.ReactNode`                        | -       | Bottom sheet content                                                                               |
| `className`                 | `string`                                 | -       | Additional CSS classes for bottom sheet container                                                  |
| `containerClassName`        | `string`                                 | -       | Additional CSS classes for container                                                               |
| `contentContainerClassName` | `string`                                 | -       | Additional CSS classes for content container                                                       |
| `backgroundClassName`       | `string`                                 | -       | Additional CSS classes for background                                                              |
| `handleClassName`           | `string`                                 | -       | Additional CSS classes for handle                                                                  |
| `handleIndicatorClassName`  | `string`                                 | -       | Additional CSS classes for handle indicator                                                        |
| `contentContainerProps`     | `Omit<BottomSheetViewProps, 'children'>` | -       | Props for the content container                                                                    |
| `animation`                 | `AnimationDisabled`                      | -       | Animation configuration                                                                            |
| `...GorhomBottomSheetProps` | `Partial<GorhomBottomSheetProps>`        | -       | All [@gorhom/bottom-sheet props](https://gorhom.dev/react-native-bottom-sheet/props) are supported |

**Note**: You can use all components from [@gorhom/bottom-sheet](https://gorhom.dev/react-native-bottom-sheet/components/bottomsheetview) inside the content, such as `BottomSheetView`, `BottomSheetScrollView`, `BottomSheetFlatList`, etc.

#### BottomSheet.Close

BottomSheet.Close extends [CloseButton](./close-button) and automatically handles bottom sheet dismissal when pressed.

#### BottomSheet.Title

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Title content                                      |
| `className`    | `string`          | -       | Additional CSS classes for title                   |
| `...TextProps` | `TextProps`       | -       | All standard React Native Text props are supported |

#### BottomSheet.Description

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Description content                                |
| `className`    | `string`          | -       | Additional CSS classes for description             |
| `...TextProps` | `TextProps`       | -       | All standard React Native Text props are supported |

### Hooks

#### useBottomSheet

Hook to access bottom sheet primitive context.

```tsx
const { isOpen, onOpenChange } = useBottomSheet();
```

| property       | type                       | description                   |
| -------------- | -------------------------- | ----------------------------- |
| `isOpen`       | `boolean`                  | Current open state            |
| `onOpenChange` | `(value: boolean) => void` | Function to change open state |

#### useBottomSheetAnimation

Hook to access bottom sheet animation context for advanced customization.

```tsx
const { progress } = useBottomSheetAnimation();
```

| property   | type                  | description                                  |
| ---------- | --------------------- | -------------------------------------------- |
| `progress` | `SharedValue<number>` | Animation progress (0=idle, 1=open, 2=close) |

### Special Notes

#### Element Inspector (iOS)

BottomSheet uses FullWindowOverlay on iOS, which renders in a separate native window. This breaks the React Native element inspector. To enable the inspector during development, set `disableFullWindowOverlay={true}` on `BottomSheet.Portal`. Tradeoff: the bottom sheet will not appear above native modals when disabled.

#### Handling Close Callbacks

It is recommended to use `BottomSheet`'s `onOpenChange` prop for handling close callbacks, as it reliably fires for all close scenarios (swiping down, pressing overlay, pressing close button, programmatic close, etc.).

```tsx
<BottomSheet
  isOpen={isOpen}
  onOpenChange={(value) => {
    setIsOpen(value);
    if (!value) {
      // This callback runs whenever the bottom sheet closes
      // regardless of how it was closed
      yourCallbackOnClose();
    }
  }}
>
  ...
</BottomSheet>
```

**Note**: `BottomSheet.Content`'s `onClose` prop (from @gorhom/bottom-sheet) has limitations and will only fire when the bottom sheet is closed by swiping down. It will not fire when closing via overlay press, close button, or programmatic close. For reliable close callbacks, always use `BottomSheet`'s `onOpenChange` prop instead.

### Examples from bottom-sheet.md

#### Example 1: Basic Bottom Sheet

A simple bottom sheet with title, description, and action buttons.

```tsx
import Ionicons from '@expo/vector-icons/Ionicons';
import { BottomSheet, Button } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';
import { withUniwind } from 'uniwind';

const StyledIonicons = withUniwind(Ionicons);

export const BasicBottomSheetContent = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
          <BottomSheet.Trigger asChild>
            <Button variant="secondary" isDisabled={isOpen}>
              Basic bottom sheet
            </Button>
          </BottomSheet.Trigger>
          <BottomSheet.Portal>
            <BottomSheet.Overlay />
            <BottomSheet.Content>
              <View className="items-center mb-5">
                <View className="size-20 items-center justify-center rounded-full bg-green-500/10">
                  <StyledIonicons
                    name="shield-checkmark"
                    size={40}
                    className="text-green-500"
                  />
                </View>
              </View>
              <View className="mb-8 gap-2 items-center">
                <BottomSheet.Title className="text-center">
                  Keep yourself safe
                </BottomSheet.Title>
                <BottomSheet.Description className="text-center">
                  Update your software to the latest version for better security
                  and performance.
                </BottomSheet.Description>
              </View>
              <View className="gap-3">
                <Button onPress={() => setIsOpen(false)}>Update Now</Button>
                <Button variant="tertiary" onPress={() => setIsOpen(false)}>
                  Later
                </Button>
              </View>
            </BottomSheet.Content>
          </BottomSheet.Portal>
        </BottomSheet>
      </View>
    </View>
  );
};
```

#### Example 2: Detached Bottom Sheet

A detached bottom sheet with custom styling and payment options.

```tsx
import FontAwesome5 from '@expo/vector-icons/FontAwesome5';
import { BottomSheet, Button } from 'heroui-native';
import { useState } from 'react';
import { Platform, View } from 'react-native';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import { withUniwind } from 'uniwind';
import { AppText } from '../../../components/app-text';

const StyledFontAwesome5 = withUniwind(FontAwesome5);

const DetachedBottomSheetContent = () => {
  const [isOpen, setIsOpen] = useState(false);
  const insets = useSafeAreaInsets();

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
          <BottomSheet.Trigger asChild>
            <Button variant="secondary" isDisabled={isOpen}>
              Detached bottom sheet
            </Button>
          </BottomSheet.Trigger>
          <BottomSheet.Portal>
            <BottomSheet.Overlay />
            <BottomSheet.Content
              detached={true}
              bottomInset={insets.bottom + 12}
              className="mx-4"
              backgroundClassName="rounded-4xl"
              contentContainerClassName="pb-4"
            >
              <View className="items-center mb-5">
                <View className="">
                  <StyledFontAwesome5
                    name="bitcoin"
                    size={48}
                    className="text-green-500"
                  />
                </View>
              </View>
              <View className="mb-6 items-center">
                <BottomSheet.Title className="text-center text-xl font-bold">
                  Oh! Your wallet is empty
                </BottomSheet.Title>
                <BottomSheet.Description className="text-center">
                  You'll need to top up to buy
                </BottomSheet.Description>
              </View>
              <Button
                variant="primary"
                className="bg-green-500 mb-2"
                onPress={() => setIsOpen(false)}
                feedbackVariant="scale"
              >
                <Button.Label className="text-white font-semibold">
                  Add Cash
                </Button.Label>
              </Button>
              <View className="flex-row items-center justify-center">
                {['Apple Pay', 'Mastercard', 'Visa', 'Amex'].map(
                  (label, index, array) => (
                    <View key={label} className="flex-row items-center">
                      <AppText className="text-xs font-normal text-muted">
                        {label}
                      </AppText>
                      {index < array.length - 1 && (
                        <AppText className="text-xs font-normal text-muted mx-1.5">
                          {'\u2022'}
                        </AppText>
                      )}
                    </View>
                  )
                )}
              </View>
            </BottomSheet.Content>
          </BottomSheet.Portal>
        </BottomSheet>
      </View>
    </View>
  );
};
```

#### Example 3: Bottom Sheet with Blur Overlay

A bottom sheet with a custom blur overlay effect.

```tsx
import { BottomSheet, Button } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';
import { BottomSheetBlurOverlay } from '../../../components/bottom-sheet-blur-overlay';

const WithBlurOverlayContent = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
          <BottomSheet.Trigger asChild>
            <Button variant="secondary" isDisabled={isOpen}>
              Bottom sheet with blur overlay
            </Button>
          </BottomSheet.Trigger>
          <BottomSheet.Portal>
            <BottomSheetBlurOverlay />
            <BottomSheet.Content>
              <View className="mb-10 gap-3 px-2">
                <BottomSheet.Title className="text-2xl font-semibold">
                  Delete account?
                </BottomSheet.Title>
                <BottomSheet.Description>
                  If you delete your account, you won't be able to restore it or
                  receive support.
                </BottomSheet.Description>
                <BottomSheet.Description>
                  Our app will no longer be able to provide support for any of
                  your trips, such as providing a refund or locking for lost
                  items.
                </BottomSheet.Description>
                <BottomSheet.Description>
                  For other deletion options, see our Privacy Policy.
                </BottomSheet.Description>
              </View>
              <View className="gap-3">
                <Button variant="danger" onPress={() => setIsOpen(false)}>
                  Delete forever
                </Button>
                <Button variant="tertiary" onPress={() => setIsOpen(false)}>
                  Cancel
                </Button>
              </View>
            </BottomSheet.Content>
          </BottomSheet.Portal>
        </BottomSheet>
      </View>
    </View>
  );
};
```

#### Example 4: Scrollable Bottom Sheet with Snap Points

A scrollable bottom sheet with snap points and a custom footer.

```tsx
import Ionicons from '@expo/vector-icons/Ionicons';
import { BottomSheetFooter, BottomSheetScrollView } from '@gorhom/bottom-sheet';
import { BottomSheet, Button, Card, cn, Separator } from 'heroui-native';
import { useCallback, useMemo, useState } from 'react';
import { View } from 'react-native';
import { withUniwind } from 'uniwind';
import { useAppTheme } from '../../../contexts/app-theme-context';
import { AppText } from '../../../components/app-text';

const StyledIonicons = withUniwind(Ionicons);

export const ScrollableWithSnapPointsContent = () => {
  const [isOpen, setIsOpen] = useState(false);
  const { isDark } = useAppTheme();

  const snapPoints = useMemo(() => ['40%', '80%'], []);

  const taxiOptions = [
    {
      id: 'priority',
      name: 'Taxi Priority',
      icon: 'flash-outline' as const,
      time: '1 min',
      capacity: '4',
      price: 'c. \u20ac12-19',
      highlight: false,
    },
    {
      id: 'comfort',
      name: 'Taxi Comfort',
      icon: 'star-outline' as const,
      time: '6 min',
      capacity: '4',
      price: 'c. \u20ac11-18',
      highlight: false,
    },
    {
      id: 'green',
      name: 'Green Taxi',
      icon: 'leaf-outline' as const,
      time: '6 min',
      capacity: '4',
      price: 'c. \u20ac11-17',
      highlight: false,
    },
    {
      id: 'premium',
      name: 'Premium',
      icon: 'car-sport-outline' as const,
      time: '10 min',
      capacity: '4',
      description: 'High end cars, top drivers',
      price: '\u20ac30.89',
      highlight: true,
    },
    {
      id: 'xl',
      name: 'Taxi XL',
      icon: 'car-outline' as const,
      time: '2 min',
      capacity: '5-8',
      price: 'c. \u20ac12-19',
      highlight: false,
    },
  ];

  const renderFooter = useCallback(
    (props: { animatedFooterPosition: any }) => (
      <BottomSheetFooter {...props}>
        <View className="px-4 pb-safe-offset-3 bg-overlay">
          <Separator className="-mx-4 mb-3" />
          <Button variant="danger" onPress={() => setIsOpen(false)}>
            Order Premium now
          </Button>
        </View>
      </BottomSheetFooter>
    ),
    []
  );

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
          <BottomSheet.Trigger asChild>
            <Button variant="secondary" isDisabled={isOpen}>
              Scrollable with snap points
            </Button>
          </BottomSheet.Trigger>
          <BottomSheet.Portal>
            <BottomSheet.Overlay />
            <BottomSheet.Content
              snapPoints={snapPoints}
              enableOverDrag={false}
              enableDynamicSizing={false}
              footerComponent={renderFooter}
              contentContainerClassName="h-full px-0"
              handleComponent={() => null}
            >
              <View className="flex-row items-center justify-between pl-7 pr-5 pb-3">
                <BottomSheet.Title className="text-xl font-bold">
                  Select a way to travel
                </BottomSheet.Title>
                <BottomSheet.Close />
              </View>
              <Separator className="-mx-5" />
              <BottomSheetScrollView
                contentContainerClassName="pb-safe-offset-12"
                showsVerticalScrollIndicator={false}
              >
                <View className="mb-4 px-3">
                  {taxiOptions.map((option) => (
                    <Card
                      key={option.id}
                      className={cn(
                        'flex-row items-center mb-2 shadow-none',
                        !option.highlight
                          ? 'bg-transparent'
                          : isDark
                            ? 'bg-green-900/40'
                            : 'bg-green-50'
                      )}
                    >
                      <View
                        className={cn(
                          'size-12 items-center justify-center rounded-full mr-3',
                          option.highlight
                            ? 'bg-black'
                            : option.icon === 'leaf-outline'
                              ? 'bg-green-500'
                              : 'bg-black'
                        )}
                      >
                        <StyledIonicons
                          name={option.icon}
                          size={24}
                          className="text-white"
                        />
                      </View>
                      <Card.Body className="flex-1">
                        <Card.Title className="text-base font-semibold">
                          {option.name}
                        </Card.Title>
                        <Card.Description className="text-sm">
                          in {option.time} {'\u2022'} {option.capacity}
                        </Card.Description>
                        {option.description && (
                          <Card.Description className="text-xs text-success mt-1">
                            {option.description}
                          </Card.Description>
                        )}
                      </Card.Body>
                      <View className="flex-row items-center">
                        <AppText className="text-base font-semibold text-foreground">
                          {option.price}
                        </AppText>
                        {option.highlight && (
                          <StyledIonicons
                            name="chevron-forward"
                            size={20}
                            className="text-muted ml-2"
                          />
                        )}
                      </View>
                    </Card>
                  ))}
                </View>
              </BottomSheetScrollView>
            </BottomSheet.Content>
          </BottomSheet.Portal>
        </BottomSheet>
      </View>
    </View>
  );
};
```

#### Example 5: Bottom Sheet with Text Input

A bottom sheet with a search input and user list.

```tsx
import Ionicons from '@expo/vector-icons/Ionicons';
import { BottomSheetScrollView } from '@gorhom/bottom-sheet';
import { LinearGradient } from 'expo-linear-gradient';
import {
  Avatar,
  BottomSheet,
  Button,
  Input,
  ScrollShadow,
  TextField,
  useThemeColor,
} from 'heroui-native';
import { useMemo, useState } from 'react';
import { Pressable, View } from 'react-native';
import { withUniwind } from 'uniwind';
import { AppText } from '../app-text';
import { MagnifierIcon } from '../icons/magnifier';

const StyledIonicons = withUniwind(Ionicons);

type User = {
  id: string;
  name: string;
  email: string;
};

const MOCK_USERS: User[] = [
  { id: '1', name: 'John Doe', email: 'john.doe@example.com' },
  { id: '2', name: 'Jane Smith', email: 'jane.smith@example.com' },
  { id: '3', name: 'Alice Johnson', email: 'alice.johnson@example.com' },
  { id: '4', name: 'Bob Williams', email: 'bob.williams@example.com' },
  { id: '5', name: 'Charlie Brown', email: 'charlie.brown@example.com' },
  { id: '6', name: 'Diana Prince', email: 'diana.prince@example.com' },
  { id: '7', name: 'Edward Norton', email: 'edward.norton@example.com' },
  { id: '8', name: 'Fiona Apple', email: 'fiona.apple@example.com' },
  { id: '9', name: 'George Lucas', email: 'george.lucas@example.com' },
  { id: '10', name: 'Helen Keller', email: 'helen.keller@example.com' },
];

const getInitials = (name: string): string => {
  const parts = name.trim().split(' ');
  if (parts.length >= 2) {
    const first = parts[0];
    const last = parts[parts.length - 1];
    if (first && last && first[0] && last[0]) {
      return `${first[0]}${last[0]}`.toUpperCase();
    }
  }
  return name.substring(0, 2).toUpperCase();
};

const UserSearchItem = ({ user }: { user: User }) => {
  const initials = getInitials(user.name);

  return (
    <View className="flex-row items-center mb-2 py-2.5">
      <Avatar size="md" className="mr-3" alt={user.name}>
        <Avatar.Fallback>{initials}</Avatar.Fallback>
      </Avatar>
      <View className="flex-1">
        <AppText className="text-base font-semibold text-foreground">
          {user.name}
        </AppText>
        <AppText className="text-sm text-muted">{user.email}</AppText>
      </View>
    </View>
  );
};

const BottomSheetTextInput = ({
  searchQuery,
  setSearchQuery,
}: {
  searchQuery: string;
  setSearchQuery: (text: string) => void;
}) => {
  return (
    <TextField className="absolute top-0 left-0 right-0 px-5 pt-2">
      <View className="w-full flex-row items-center">
        <Input
          variant="secondary"
          placeholder="Search by name or email..."
          value={searchQuery}
          onChangeText={setSearchQuery}
          className="flex-1 px-10"
          autoCapitalize="none"
          autoCorrect={false}
        />
        <View className="absolute left-3.5" pointerEvents="none">
          <MagnifierIcon colorClassName="accent-field-placeholder" />
        </View>
        {searchQuery.length > 0 && (
          <Pressable
            className="absolute right-3 p-1"
            onPress={() => setSearchQuery('')}
            hitSlop={12}
          >
            <StyledIonicons
              name="close-circle"
              size={20}
              className="text-muted"
            />
          </Pressable>
        )}
      </View>
    </TextField>
  );
};

const UserSearchBottomSheetContent = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const themeColorOverlay = useThemeColor('overlay');
  const snapPoints = useMemo(() => ['50%', '90%'], []);

  const filteredUsers = useMemo(() => {
    if (!searchQuery.trim()) {
      return MOCK_USERS;
    }
    const query = searchQuery.toLowerCase();
    return MOCK_USERS.filter(
      (user) =>
        user.name.toLowerCase().includes(query) ||
        user.email.toLowerCase().includes(query)
    );
  }, [searchQuery]);

  return (
    <BottomSheet.Content
      snapPoints={snapPoints}
      enableOverDrag={false}
      enableDynamicSizing={false}
      contentContainerClassName="h-full pt-16 pb-2"
      keyboardBehavior="extend"
    >
      <BottomSheetTextInput
        searchQuery={searchQuery}
        setSearchQuery={setSearchQuery}
      />
      <ScrollShadow
        LinearGradientComponent={LinearGradient}
        color={themeColorOverlay}
      >
        <BottomSheetScrollView
          showsVerticalScrollIndicator={false}
          contentContainerClassName="pt-3"
          keyboardShouldPersistTaps="handled"
        >
          {filteredUsers.length > 0 ? (
            <View>
              {filteredUsers.map((user) => (
                <UserSearchItem key={user.id} user={user} />
              ))}
            </View>
          ) : (
            <View className="items-center justify-center py-8">
              <StyledIonicons
                name="search-outline"
                size={48}
                className="text-muted mb-3"
              />
              <AppText className="text-base text-muted">No users found</AppText>
              <AppText className="text-sm text-muted mt-1">
                Try a different search term
              </AppText>
            </View>
          )}
        </BottomSheetScrollView>
      </ScrollShadow>
    </BottomSheet.Content>
  );
};

export const WithTextInputContent = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
          <BottomSheet.Trigger asChild>
            <Button variant="secondary" isDisabled={isOpen}>
              Bottom sheet with text input
            </Button>
          </BottomSheet.Trigger>
          <BottomSheet.Portal>
            <BottomSheet.Overlay />
            <UserSearchBottomSheetContent />
          </BottomSheet.Portal>
        </BottomSheet>
      </View>
    </View>
  );
};
```

#### Example 6: Bottom Sheet with OTP Input

A bottom sheet with phone verification OTP input.

```tsx
import { BottomSheetScrollView } from '@gorhom/bottom-sheet';
import {
  Avatar,
  BottomSheet,
  Button,
  Description,
  InputOTP,
  Label,
  useToast,
  type InputOTPRef,
} from 'heroui-native';
import { memo, useCallback, useEffect, useRef, useState } from 'react';
import { View } from 'react-native';
import { KeyboardController } from 'react-native-keyboard-controller';
import { AppText } from '../app-text';

const formatPhoneNumber = (phone: string): string => {
  const cleaned = phone.replace(/\D/g, '');
  const match = cleaned.match(/^(\d{1})(\d{3})(\d{3})(\d{4})$/);
  if (match) {
    return `+${match[1]} ${match[2]} ${match[3]} ${match[4]}`;
  }
  return phone;
};

const BottomSheetInputOTP = memo(
  ({
    otpRef,
    value,
    onChange,
    onComplete,
    phoneNumber,
  }: {
    otpRef: React.RefObject<InputOTPRef | null>;
    value: string;
    onChange: (value: string) => void;
    onComplete: (code: string) => void;
    phoneNumber: string;
  }) => {
    return (
      <View className="gap-6 items-center">
        <Avatar size="lg" color="accent" variant="soft" alt="Verification icon">
          <Avatar.Fallback>
            <AppText className="text-2xl font-semibold">{'\u2713'}</AppText>
          </Avatar.Fallback>
        </Avatar>

        <View className="gap-1 items-center px-2">
          <Label className="text-2xl font-semibold text-center">
            Verify your phone number
          </Label>
          <Description className="text-base text-center text-muted">
            We sent a verification code to
          </Description>
          <AppText className="text-base font-semibold text-center text-foreground mt-1">
            {formatPhoneNumber(phoneNumber)}
          </AppText>
        </View>

        <View className="w-full items-center">
          <InputOTP
            ref={otpRef}
            variant="secondary"
            maxLength={6}
            value={value}
            onChange={onChange}
            onComplete={onComplete}
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
          </InputOTP>
        </View>
      </View>
    );
  }
);

const OTPBottomSheetContent = () => {
  const otpRef = useRef<InputOTPRef>(null);
  const [otpValue, setOtpValue] = useState('');
  const [isVerifying, setIsVerifying] = useState(false);
  const [resendTimer, setResendTimer] = useState(0);
  const { toast } = useToast();

  const phoneNumber = '+12345678900';

  useEffect(() => {
    if (resendTimer > 0) {
      const timer = setTimeout(() => {
        setResendTimer(resendTimer - 1);
      }, 1000);
      return () => clearTimeout(timer);
    }
    return undefined;
  }, [resendTimer]);

  const handleComplete = useCallback(
    async (_code: string) => {
      setIsVerifying(true);
      await new Promise((resolve) => setTimeout(resolve, 1500));
      setIsVerifying(false);
      toast.show({
        variant: 'success',
        label: 'Phone verified',
        description: 'Your phone number has been successfully verified.',
      });
      setTimeout(() => {
        setOtpValue('');
        otpRef.current?.clear();
      }, 2000);
    },
    [toast]
  );

  const handleResend = useCallback(() => {
    if (resendTimer > 0) return;
    setResendTimer(60);
    setOtpValue('');
    otpRef.current?.clear();
    toast.show({
      variant: 'success',
      label: 'Code sent',
      description: 'A new verification code has been sent to your phone.',
    });
  }, [resendTimer, toast]);

  const handleVerify = useCallback(() => {
    if (otpValue.length === 6) {
      handleComplete(otpValue);
    } else {
      toast.show({
        variant: 'warning',
        label: 'Incomplete code',
        description: 'Please enter all 6 digits to continue.',
      });
    }
  }, [otpValue, handleComplete, toast]);

  return (
    <BottomSheet.Content
      onClose={() => {
        KeyboardController.dismiss();
        setOtpValue('');
        setResendTimer(0);
      }}
    >
      <BottomSheetScrollView
        showsVerticalScrollIndicator={false}
        keyboardShouldPersistTaps="handled"
        contentContainerClassName="pb-safe-offset-3"
      >
        <BottomSheetInputOTP
          otpRef={otpRef}
          value={otpValue}
          onChange={setOtpValue}
          onComplete={handleComplete}
          phoneNumber={phoneNumber}
        />

        <View className="mt-8 gap-3">
          <Button
            variant="primary"
            onPress={handleVerify}
            isDisabled={isVerifying || otpValue.length !== 6}
          >
            {isVerifying ? 'Verifying...' : 'Verify Code'}
          </Button>

          <View className="flex-row items-center justify-center mt-2">
            <AppText className="text-sm text-muted text-center">
              Didn't receive the code?
            </AppText>
            <Button
              variant="ghost"
              size="sm"
              onPress={handleResend}
              isDisabled={resendTimer > 0}
            >
              {resendTimer > 0 ? `Resend in ${resendTimer}s` : 'Resend'}
            </Button>
          </View>
        </View>
        <View className="mt-8 px-2">
          <AppText className="text-xs text-muted text-center leading-5">
            By continuing, you agree to receive SMS messages for verification.
            Message and data rates may apply.
          </AppText>
        </View>
      </BottomSheetScrollView>
    </BottomSheet.Content>
  );
};

export const WithOTPInputContent = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
          <BottomSheet.Trigger asChild>
            <Button variant="secondary" isDisabled={isOpen}>
              Bottom sheet with OTP input
            </Button>
          </BottomSheet.Trigger>
          <BottomSheet.Portal>
            <BottomSheet.Overlay onPress={() => KeyboardController.dismiss()} />
            <BottomSheet.Title className="sr-only">
              Phone Number Verification
            </BottomSheet.Title>
            <OTPBottomSheetContent />
          </BottomSheet.Portal>
        </BottomSheet>
      </View>
    </View>
  );
};
```

#### Example 7: Multi-Step Detached Bottom Sheet

A multi-step bottom sheet with animated content transitions.

```tsx
import Feather from '@expo/vector-icons/Feather';
import { BottomSheet, Button } from 'heroui-native';
import { useCallback, useState } from 'react';
import { StyleSheet, View } from 'react-native';
import Animated, {
  FadeIn,
  FadeOut,
  LinearTransition,
  useSharedValue,
  withSequence,
  withTiming,
} from 'react-native-reanimated';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import { withUniwind } from 'uniwind';
import { AnimatedBlurView } from '../animated-blur-view';

const StyledFeather = withUniwind(Feather);

type Step = 1 | 2 | 3;

const PREVIOUS_STEP: Record<Step, Step> = { 1: 1, 2: 1, 3: 2 };
const NEXT_STEP: Record<Step, Step> = { 1: 2, 2: 3, 3: 3 };

const layoutTransition = LinearTransition.springify();
const fadeIn = FadeIn.duration(200);
const fadeOut = FadeOut.duration(150);

const STEP_1_BODY = [
  'Lorem ipsum dolor sit amet, consectetur adipiscing elit.',
  'Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.',
  'Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris.',
  'Nisi ut aliquip ex ea commodo consequat.',
  'Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore.',
  'Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit.',
].join(' ');

const STEP_2_BODY = [
  'Lorem ipsum dolor sit amet, consectetur adipiscing elit.',
  'Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.',
  'Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris.',
  'Nisi ut aliquip ex ea commodo consequat.',
  'Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore.',
  'Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit.',
  'Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium.',
  'Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit.',
  'Neque porro quisquam est, qui dolorem ipsum quia dolor sit amet.',
  'At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis.',
].join(' ');

const STEP_3_BODY = [
  'Lorem ipsum dolor sit amet, consectetur adipiscing elit.',
  'Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.',
  'Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris.',
  'Nisi ut aliquip ex ea commodo consequat.',
  'Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore.',
  'Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit.',
  'Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium.',
  'Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit.',
].join(' ');

export const MultiStepDetachedContent = () => {
  const [isOpen, setIsOpen] = useState(false);
  const [step, setStep] = useState<Step>(1);
  const insets = useSafeAreaInsets();
  const blurIntensity = useSharedValue(0);

  const triggerBlurPulse = useCallback(() => {
    blurIntensity.value = withSequence(
      withTiming(10, { duration: 150 }),
      withTiming(0, { duration: 200 })
    );
  }, [blurIntensity]);

  const handleOpenChange = useCallback(
    (open: boolean) => {
      if (open) {
        setStep(1);
        blurIntensity.value = 0;
      }
      setIsOpen(open);
    },
    [blurIntensity]
  );

  const handleBack = useCallback(() => {
    triggerBlurPulse();
    setStep((prev) => PREVIOUS_STEP[prev]);
  }, [triggerBlurPulse]);

  const handleContinue = useCallback(() => {
    triggerBlurPulse();
    setStep((prev) => NEXT_STEP[prev]);
  }, [triggerBlurPulse]);

  const handleClose = useCallback(() => {
    setIsOpen(false);
  }, []);

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <BottomSheet isOpen={isOpen} onOpenChange={handleOpenChange}>
          <BottomSheet.Trigger asChild>
            <Button variant="secondary" isDisabled={isOpen}>
              Multi-step detached
            </Button>
          </BottomSheet.Trigger>

          <BottomSheet.Portal>
            <BottomSheet.Overlay />
            <BottomSheet.Content
              detached
              bottomInset={insets.bottom + 12}
              className="mx-4"
              backgroundClassName="rounded-4xl"
              contentContainerClassName="pb-4 pt-0"
              handleIndicatorClassName="h-0"
            >
              {/* Header */}
              <Animated.View layout={layoutTransition}>
                <View className="flex-row items-center justify-between mb-4">
                  {step > 1 && (
                    <Animated.View entering={fadeIn} exiting={fadeOut}>
                      <Button
                        variant="tertiary"
                        isIconOnly
                        onPress={handleBack}
                        size="sm"
                        className="size-8"
                      >
                        <StyledFeather
                          name="chevron-left"
                          size={20}
                          className="text-foreground"
                        />
                      </Button>
                    </Animated.View>
                  )}

                  <Animated.View layout={layoutTransition}>
                    <View className="flex-1">
                      <BottomSheet.Title>
                        {step === 1 ? 'Content One' : 'Content Two'}
                      </BottomSheet.Title>
                    </View>
                  </Animated.View>

                  <BottomSheet.Close />
                </View>
              </Animated.View>

              {/* Content */}
              <Animated.View layout={layoutTransition}>
                <View className="mb-6 overflow-hidden">
                  {step === 1 && (
                    <Animated.View entering={fadeIn} exiting={fadeOut}>
                      <BottomSheet.Title>This is a test title</BottomSheet.Title>
                      <BottomSheet.Description>{STEP_1_BODY}</BottomSheet.Description>
                    </Animated.View>
                  )}

                  {step === 2 && (
                    <Animated.View entering={fadeIn} exiting={fadeOut}>
                      <BottomSheet.Title>Different title</BottomSheet.Title>
                      <BottomSheet.Description>{STEP_2_BODY}</BottomSheet.Description>
                    </Animated.View>
                  )}

                  {step === 3 && (
                    <Animated.View entering={fadeIn} exiting={fadeOut}>
                      <BottomSheet.Title>Another title</BottomSheet.Title>
                      <BottomSheet.Description>{STEP_3_BODY}</BottomSheet.Description>
                    </Animated.View>
                  )}

                  <AnimatedBlurView
                    blurIntensity={blurIntensity}
                    style={StyleSheet.absoluteFill}
                    pointerEvents="none"
                  />
                </View>
              </Animated.View>

              {/* Footer */}
              <Animated.View layout={layoutTransition}>
                {step === 1 && (
                  <Animated.View entering={fadeIn} exiting={fadeOut}>
                    <Button onPress={handleContinue}>Continue</Button>
                  </Animated.View>
                )}

                {step === 2 && (
                  <Animated.View entering={fadeIn} exiting={fadeOut}>
                    <View className="flex-row gap-3">
                      <View className="flex-1">
                        <Button variant="tertiary" onPress={handleClose}>
                          Cancel
                        </Button>
                      </View>
                      <View className="flex-1">
                        <Button onPress={handleContinue}>Continue</Button>
                      </View>
                    </View>
                  </Animated.View>
                )}

                {step === 3 && (
                  <Animated.View entering={fadeIn} exiting={fadeOut}>
                    <Button onPress={handleClose}>Finish</Button>
                  </Animated.View>
                )}
              </Animated.View>
            </BottomSheet.Content>
          </BottomSheet.Portal>
        </BottomSheet>
      </View>
    </View>
  );
};
```

#### Complete Usage Example

```tsx
import { BottomSheet, Button } from 'heroui-native';
import { useState } from 'react';
import { View } from 'react-native';

export default function BottomSheetExample() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <View className="flex-1 items-center justify-center">
      <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
        <BottomSheet.Trigger asChild>
          <Button variant="secondary">Open Bottom Sheet</Button>
        </BottomSheet.Trigger>
        <BottomSheet.Portal>
          <BottomSheet.Overlay />
          <BottomSheet.Content>
            <BottomSheet.Title>Sheet Title</BottomSheet.Title>
            <BottomSheet.Description>
              This is the bottom sheet content.
            </BottomSheet.Description>
            <Button onPress={() => setIsOpen(false)}>Close</Button>
          </BottomSheet.Content>
        </BottomSheet.Portal>
      </BottomSheet>
    </View>
  );
}
```

### Full Example Code (bottom-sheet-example.tsx)

```tsx
import FontAwesome5 from '@expo/vector-icons/FontAwesome5';
import { useRouter } from 'expo-router';
import { BottomSheet, Button } from 'heroui-native';
import { useState } from 'react';
import { Platform, View } from 'react-native';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import { withUniwind } from 'uniwind';
import { AppText } from '../../../components/app-text';
import { BottomSheetBlurOverlay } from '../../../components/bottom-sheet-blur-overlay';
import { BasicBottomSheetContent } from '../../../components/bottom-sheet/basic';
import { ScrollableWithSnapPointsContent } from '../../../components/bottom-sheet/scrollable-with-snap-points';
import { WithOTPInputContent } from '../../../components/bottom-sheet/with-otp-input';
import { WithTextInputContent } from '../../../components/bottom-sheet/with-text-input';
import type { UsageVariant } from '../../../components/component-presentation/types';
import { UsageVariantFlatList } from '../../../components/component-presentation/usage-variant-flatlist';

const StyledFontAwesome5 = withUniwind(FontAwesome5);

// ------------------------------------------------------------------------------

const DetachedBottomSheetContent = () => {
  const [isOpen, setIsOpen] = useState(false);

  const insets = useSafeAreaInsets();

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
          <BottomSheet.Trigger asChild>
            <Button variant="secondary" isDisabled={isOpen}>
              Detached bottom sheet
            </Button>
          </BottomSheet.Trigger>
          <BottomSheet.Portal>
            <BottomSheet.Overlay />
            <BottomSheet.Content
              detached={true}
              bottomInset={insets.bottom + 12}
              className="mx-4"
              backgroundClassName="rounded-4xl"
              contentContainerClassName="pb-4"
            >
              <View className="items-center mb-5">
                <View className="">
                  <StyledFontAwesome5
                    name="bitcoin"
                    size={48}
                    className="text-green-500"
                  />
                </View>
              </View>
              <View className="mb-6 items-center">
                <BottomSheet.Title className="text-center text-xl font-bold">
                  Oh! Your wallet is empty
                </BottomSheet.Title>
                <BottomSheet.Description className="text-center">
                  You'll need to top up to buy
                </BottomSheet.Description>
              </View>
              <Button
                variant="primary"
                className="bg-green-500 mb-2"
                onPress={() => setIsOpen(false)}
                feedbackVariant="scale"
              >
                <Button.Label className="text-white font-semibold">
                  Add Cash
                </Button.Label>
              </Button>
              <View className="flex-row items-center justify-center">
                {['Apple Pay', 'Mastercard', 'Visa', 'Amex'].map(
                  (label, index, array) => (
                    <View key={label} className="flex-row items-center">
                      <AppText className="text-xs font-normal text-muted">
                        {label}
                      </AppText>
                      {index < array.length - 1 && (
                        <AppText className="text-xs font-normal text-muted mx-1.5">
                          {'\u2022'}
                        </AppText>
                      )}
                    </View>
                  )
                )}
              </View>
            </BottomSheet.Content>
          </BottomSheet.Portal>
        </BottomSheet>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const WithBlurOverlayContent = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <BottomSheet isOpen={isOpen} onOpenChange={setIsOpen}>
          <BottomSheet.Trigger asChild>
            <Button variant="secondary" isDisabled={isOpen}>
              Bottom sheet with blur overlay
            </Button>
          </BottomSheet.Trigger>
          <BottomSheet.Portal>
            <BottomSheetBlurOverlay />
            <BottomSheet.Content>
              <View className="mb-10 gap-3 px-2">
                <BottomSheet.Title className="text-2xl font-semibold">
                  Delete account?
                </BottomSheet.Title>
                <BottomSheet.Description>
                  If you delete your account, you won't be able to restore it or
                  receive support.
                </BottomSheet.Description>
                <BottomSheet.Description>
                  Our app will no longer be able to provide support for any of
                  your trips, such as providing a refund or locking for lost
                  items.
                </BottomSheet.Description>
                <BottomSheet.Description>
                  For other deletion options, see our Privacy Policy.
                </BottomSheet.Description>
              </View>
              <View className="gap-3">
                <Button variant="danger" onPress={() => setIsOpen(false)}>
                  Delete forever
                </Button>
                <Button variant="tertiary" onPress={() => setIsOpen(false)}>
                  Cancel
                </Button>
              </View>
            </BottomSheet.Content>
          </BottomSheet.Portal>
        </BottomSheet>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const NativeModalBottomSheetContent = () => {
  const router = useRouter();

  if (Platform.OS !== 'ios') {
    return null;
  }

  return (
    <View className="flex-1">
      <View className="flex-1 items-center justify-center">
        <Button
          variant="secondary"
          onPress={() => router.push('components/bottom-sheet-native-modal')}
        >
          Bottom sheet from native modal
        </Button>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const BOTTOM_SHEET_VARIANTS_IOS: UsageVariant[] = [
  {
    value: 'basic-bottom-sheet',
    label: 'Basic bottom sheet',
    content: <BasicBottomSheetContent />,
  },
  {
    value: 'detached-bottom-sheet',
    label: 'Detached bottom sheet',
    content: <DetachedBottomSheetContent />,
  },
  {
    value: 'with-blur-overlay',
    label: 'With blur overlay',
    content: <WithBlurOverlayContent />,
  },
  {
    value: 'scrollable-with-snap-points',
    label: 'Scrollable with snap points',
    content: <ScrollableWithSnapPointsContent />,
  },
  {
    value: 'native-modal-bottom-sheet',
    label: 'Bottom sheet from native modal',
    content: <NativeModalBottomSheetContent />,
  },
  {
    value: 'with-text-input',
    label: 'Bottom sheet with text input',
    content: <WithTextInputContent />,
  },
  {
    value: 'with-otp-input',
    label: 'Bottom sheet with OTP input',
    content: <WithOTPInputContent />,
  },
];

const BOTTOM_SHEET_VARIANTS_ANDROID: UsageVariant[] = [
  {
    value: 'basic-bottom-sheet',
    label: 'Basic bottom sheet',
    content: <BasicBottomSheetContent />,
  },
  {
    value: 'detached-bottom-sheet',
    label: 'Detached bottom sheet',
    content: <DetachedBottomSheetContent />,
  },
  {
    value: 'scrollable-with-snap-points',
    label: 'Scrollable with snap points',
    content: <ScrollableWithSnapPointsContent />,
  },
  {
    value: 'with-text-input',
    label: 'Bottom sheet with text input',
    content: <WithTextInputContent />,
  },
  {
    value: 'with-otp-input',
    label: 'Bottom sheet with OTP input',
    content: <WithOTPInputContent />,
  },
];

export default function BottomSheetScreen() {
  return (
    <UsageVariantFlatList
      data={
        Platform.OS === 'ios'
          ? BOTTOM_SHEET_VARIANTS_IOS
          : BOTTOM_SHEET_VARIANTS_ANDROID
      }
    />
  );
}
```

---
