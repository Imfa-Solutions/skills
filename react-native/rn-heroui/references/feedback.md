# Feedback Components

## Table of Contents
- [Alert](#alert)
- [Spinner](#spinner)
- [Skeleton](#skeleton)
- [SkeletonGroup](#skeletongroup)

---

## Alert

---
title: Alert
description: Displays important messages and notifications to users with status indicators.
links:
  source_native: alert/alert.tsx
  styles_native: alert/alert.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/alert-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/alert-docs-dark.mp4"
/>

### Import

```tsx
import { Alert } from 'heroui-native';
```

### Anatomy

```tsx
<Alert>
  <Alert.Indicator />
  <Alert.Content>
    <Alert.Title>...</Alert.Title>
    <Alert.Description>...</Alert.Description>
  </Alert.Content>
</Alert>
```

- **Alert**: Main container with `role="alert"` and status-based styling. Provides status context to sub-components via a primitive context.
- **Alert.Indicator**: Renders a status-appropriate icon by default. Accepts custom children to override the default icon. Supports `iconProps` for customising size and color.
- **Alert.Content**: Wrapper for the title and description. Provides layout structure for text content.
- **Alert.Title**: Heading text with status-based color. Connected to root via `aria-labelledby`.
- **Alert.Description**: Body text rendered with muted color. Connected to root via `aria-describedby`.

### Usage

#### Basic Usage

The Alert component uses compound parts to display a notification with an icon, title, and description.

```tsx
<Alert>
  <Alert.Indicator />
  <Alert.Content>
    <Alert.Title>New features available</Alert.Title>
    <Alert.Description>
      Check out our latest updates including dark mode support and improved
      accessibility features.
    </Alert.Description>
  </Alert.Content>
</Alert>
```

#### Status Variants

Set the `status` prop to control the icon and title color. Available statuses are `default`, `accent`, `success`, `warning`, and `danger`.

```tsx
<Alert status="success">
  <Alert.Indicator />
  <Alert.Content>
    <Alert.Title>Success</Alert.Title>
    <Alert.Description>...</Alert.Description>
  </Alert.Content>
</Alert>

<Alert status="warning">
  <Alert.Indicator />
  <Alert.Content>
    <Alert.Title>Scheduled maintenance</Alert.Title>
    <Alert.Description>...</Alert.Description>
  </Alert.Content>
</Alert>

<Alert status="danger">
  <Alert.Indicator />
  <Alert.Content>
    <Alert.Title>Unable to connect</Alert.Title>
    <Alert.Description>...</Alert.Description>
  </Alert.Content>
</Alert>
```

#### Title Only

Omit `Alert.Description` for a compact single-line alert.

```tsx
<Alert status="success" className="items-center">
  <Alert.Indicator className="pt-0" />
  <Alert.Content>
    <Alert.Title>Profile updated successfully</Alert.Title>
  </Alert.Content>
</Alert>
```

#### With Action Buttons

Place additional elements like buttons alongside the content.

```tsx
<Alert status="accent">
  <Alert.Indicator />
  <Alert.Content>
    <Alert.Title>Update available</Alert.Title>
    <Alert.Description>
      A new version of the application is available.
    </Alert.Description>
  </Alert.Content>
  <Button size="sm" variant="primary">
    Refresh
  </Button>
</Alert>
```

#### Custom Indicator

Replace the default status icon by passing custom children to `Alert.Indicator`.

```tsx
<Alert status="accent">
  <Alert.Indicator>
    <Spinner>
      <Spinner.Indicator iconProps={{ width: 20, height: 20 }} />
    </Spinner>
  </Alert.Indicator>
  <Alert.Content>
    <Alert.Title>Processing your request</Alert.Title>
    <Alert.Description>Please wait while we sync your data.</Alert.Description>
  </Alert.Content>
</Alert>
```

#### Custom Styling

Apply custom styles using the `className` prop on the root and compound parts.

```tsx
<Alert className="bg-accent/10 rounded-xl">
  <Alert.Indicator className="pt-1" />
  <Alert.Content className="gap-1">
    <Alert.Title className="text-lg">...</Alert.Title>
    <Alert.Description className="text-base">...</Alert.Description>
  </Alert.Content>
</Alert>
```

### Example

```tsx
import { Alert, Button, CloseButton } from 'heroui-native';
import { View } from 'react-native';

export default function AlertExample() {
  return (
    <View className="w-full gap-4">
      <Alert status="accent">
        <Alert.Indicator />
        <Alert.Content>
          <Alert.Title>Update available</Alert.Title>
          <Alert.Description>
            A new version of the application is available. Please refresh to get
            the latest features and bug fixes.
          </Alert.Description>
        </Alert.Content>
        <Button size="sm" variant="primary">
          Refresh
        </Button>
      </Alert>

      <Alert status="danger">
        <Alert.Indicator />
        <Alert.Content>
          <Alert.Title>Unable to connect to server</Alert.Title>
          <Alert.Description>
            Unable to connect to the server. Check your internet connection and
            try again.
          </Alert.Description>
        </Alert.Content>
        <Button size="sm" variant="danger">
          Retry
        </Button>
      </Alert>

      <Alert status="success" className="items-center">
        <Alert.Indicator className="pt-0" />
        <Alert.Content>
          <Alert.Title>Profile updated successfully</Alert.Title>
        </Alert.Content>
        <CloseButton />
      </Alert>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](<https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/alert.tsx>).

### API Reference

#### Alert

| prop           | type                                                          | default     | description                                                       |
| -------------- | ------------------------------------------------------------- | ----------- | ----------------------------------------------------------------- |
| `children`     | `React.ReactNode`                                             | -           | Children elements to render inside the alert                      |
| `status`       | `'default' \| 'accent' \| 'success' \| 'warning' \| 'danger'` | `'default'` | Status controlling the icon and color treatment                   |
| `id`           | `string \| number`                                            | -           | Unique identifier for the alert. Auto-generated when not provided |
| `className`    | `string`                                                      | -           | Additional CSS classes                                            |
| `style`        | `ViewStyle`                                                   | -           | Additional styles applied to the root container                   |
| `...ViewProps` | `ViewProps`                                                   | -           | All standard React Native View props are supported                |

#### Alert.Indicator

| prop           | type              | default | description                                                        |
| -------------- | ----------------- | ------- | ------------------------------------------------------------------ |
| `children`     | `React.ReactNode` | -       | Custom children to render instead of the default status icon       |
| `className`    | `string`          | -       | Additional CSS classes                                             |
| `iconProps`    | `AlertIconProps`  | -       | Props passed to the default status icon (size and color overrides) |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported                 |

#### AlertIconProps

| prop    | type     | default      | description            |
| ------- | -------- | ------------ | ---------------------- |
| `size`  | `number` | `18`         | Icon size in pixels    |
| `color` | `string` | status color | Icon color as a string |

#### Alert.Content

| prop           | type              | default | description                                                     |
| -------------- | ----------------- | ------- | --------------------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Children elements (typically Alert.Title and Alert.Description) |
| `className`    | `string`          | -       | Additional CSS classes                                          |
| `...ViewProps` | `ViewProps`       | -       | All standard React Native View props are supported              |

#### Alert.Title

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Title text content                                 |
| `className`    | `string`          | -       | Additional CSS classes                             |
| `...TextProps` | `TextProps`       | -       | All standard React Native Text props are supported |

#### Alert.Description

| prop           | type              | default | description                                        |
| -------------- | ----------------- | ------- | -------------------------------------------------- |
| `children`     | `React.ReactNode` | -       | Description text content                           |
| `className`    | `string`          | -       | Additional CSS classes                             |
| `...TextProps` | `TextProps`       | -       | All standard React Native Text props are supported |

### Hooks

#### useAlert

Hook to access the alert root context. Must be used within an `Alert` component.

```tsx
import { useAlert } from 'heroui-native';

const { status, nativeID } = useAlert();
```

##### Returns

| property   | type                                                          | description                                                  |
| ---------- | ------------------------------------------------------------- | ------------------------------------------------------------ |
| `status`   | `'default' \| 'accent' \| 'success' \| 'warning' \| 'danger'` | Current alert status for sub-component styling               |
| `nativeID` | `string`                                                      | Unique identifier used for accessibility and ARIA attributes |

### Examples from alert.md

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { Alert, Button, CloseButton, Spinner } from 'heroui-native';
```

#### Components

- `Alert` - Root container component
- `Alert.Indicator` - Visual status indicator icon
- `Alert.Content` - Content wrapper
- `Alert.Title` - Alert title text
- `Alert.Description` - Alert description text

#### Props

##### Alert Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `status` | `'default' \| 'accent' \| 'success' \| 'warning' \| 'danger'` | `'default'` | Alert severity status |
| `className` | `string` | - | Additional CSS classes |

##### Alert.Indicator Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `className` | `string` | - | Additional CSS classes |
| `children` | `ReactNode` | - | Custom indicator content |

#### Example: Default & Accent Status

```tsx
import { Alert } from 'heroui-native';
import { View } from 'react-native';

function DefaultAndAccentExample() {
  return (
    <View className="w-full gap-4">
      <Alert>
        <Alert.Indicator />
        <Alert.Content>
          <Alert.Title>New features available</Alert.Title>
          <Alert.Description>
            Check out our latest updates including dark mode support and
            improved accessibility features.
          </Alert.Description>
        </Alert.Content>
      </Alert>

      <Alert status="accent">
        <Alert.Indicator />
        <Alert.Content>
          <Alert.Title>Update available</Alert.Title>
          <Alert.Description>
            A new version of the application is available.
          </Alert.Content>
        </Alert>
      </Alert>
    </View>
  );
}
```

#### Example: Success, Warning, Danger Status

```tsx
<View className="w-full gap-4">
  <Alert status="success">
    <Alert.Indicator />
    <Alert.Content>
      <Alert.Title>Success</Alert.Title>
      <Alert.Description>
        Your profile information has been updated.
      </Alert.Description>
    </Alert.Content>
  </Alert>

  <Alert status="warning">
    <Alert.Indicator />
    <Alert.Content>
      <Alert.Title>Scheduled maintenance</Alert.Title>
      <Alert.Description>
        Our services will be unavailable on Sunday, March 15th.
      </Alert.Description>
    </Alert.Content>
  </Alert>

  <Alert status="danger">
    <Alert.Indicator />
    <Alert.Content>
      <Alert.Title>Unable to connect to server</Alert.Title>
      <Alert.Description>
        Unable to connect to the server. Check your internet connection.
      </Alert.Description>
    </Alert.Content>
  </Alert>
</View>
```

#### Example: With Buttons

```tsx
import { Alert, Button } from 'heroui-native';

function AlertWithButtonsExample() {
  return (
    <View className="w-full gap-4">
      <Alert status="accent">
        <Alert.Indicator />
        <Alert.Content>
          <Alert.Title>Update available</Alert.Title>
          <Alert.Description>
            A new version of the application is available.
          </Alert.Description>
        </Alert.Content>
        <Button size="sm" variant="primary">
          Refresh
        </Button>
      </Alert>

      <Alert status="danger">
        <Alert.Indicator />
        <Alert.Content>
          <Alert.Title>Unable to connect to server</Alert.Title>
          <Alert.Description>
            Check your internet connection and try again.
          </Alert.Description>
        </Alert.Content>
        <Button size="sm" variant="danger">
          Retry
        </Button>
      </Alert>

      <Alert status="success" className="items-center">
        <Alert.Indicator className="pt-0" />
        <Alert.Content>
          <Alert.Title>Profile updated successfully</Alert.Title>
        </Alert.Content>
        <CloseButton />
      </Alert>
    </View>
  );
}
```

#### Example: Custom Indicator

```tsx
import { Alert, Spinner } from 'heroui-native';

function CustomIndicatorExample() {
  return (
    <Alert status="accent">
      <Alert.Indicator className="pt-px">
        <Spinner>
          <Spinner.Indicator iconProps={{ width: 20, height: 20 }} />
        </Spinner>
      </Alert.Indicator>
      <Alert.Content>
        <Alert.Title>Processing your request</Alert.Title>
        <Alert.Description>
          Please wait while we sync your data.
        </Alert.Description>
      </Alert.Content>
    </Alert>
  );
}
```

#### Complete Example Code

```tsx
import { Alert, Button, CloseButton, Spinner } from 'heroui-native';
import { View } from 'react-native';

const DefaultAndAccentContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <View className="w-full gap-4">
        <Alert>
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>New features available</Alert.Title>
            <Alert.Description>
              Check out our latest updates including dark mode support and
              improved accessibility features.
            </Alert.Description>
          </Alert.Content>
        </Alert>
        <Alert status="accent">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Update available</Alert.Title>
            <Alert.Description>
              A new version of the application is available.
            </Alert.Description>
          </Alert.Content>
        </Alert>
      </View>
    </View>
  );
};

const SuccessWarningDangerContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <View className="w-full gap-4">
        <Alert status="success">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Success</Alert.Title>
            <Alert.Description>
              Your profile information has been updated.
            </Alert.Description>
          </Alert.Content>
        </Alert>
        <Alert status="warning">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Scheduled maintenance</Alert.Title>
            <Alert.Description>
              Our services will be unavailable on Sunday.
            </Alert.Description>
          </Alert.Content>
        </Alert>
        <Alert status="danger">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Unable to connect to server</Alert.Title>
            <Alert.Description>
              Check your internet connection and try again.
            </Alert.Description>
          </Alert.Content>
        </Alert>
      </View>
    </View>
  );
};

const WithButtonsContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <View className="w-full gap-4">
        <Alert status="accent">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Update available</Alert.Title>
            <Alert.Description>
              A new version is available. Please refresh.
            </Alert.Description>
          </Alert.Content>
          <Button size="sm" variant="primary">
            Refresh
          </Button>
        </Alert>

        <Alert status="danger">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Connection error</Alert.Title>
            <Alert.Description>
              Unable to connect to the server.
            </Alert.Description>
          </Alert.Content>
          <Button size="sm" variant="danger">
            Retry
          </Button>
        </Alert>
      </View>
    </View>
  );
};

const WithCustomIndicatorContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <View className="w-full gap-4">
        <Alert status="accent">
          <Alert.Indicator className="pt-px">
            <Spinner>
              <Spinner.Indicator iconProps={{ width: 20, height: 20 }} />
            </Spinner>
          </Alert.Indicator>
          <Alert.Content>
            <Alert.Title>Processing your request</Alert.Title>
            <Alert.Description>
              Please wait while we sync your data.
            </Alert.Description>
          </Alert.Content>
        </Alert>
      </View>
    </View>
  );
};

export default function AlertExample() {
  return <WithButtonsContent />;
}
```

### Full Example Code (alert-example.tsx)

```tsx
import { Alert, Button, CloseButton, Spinner } from 'heroui-native';
import { View } from 'react-native';
import type { UsageVariant } from '../../../components/component-presentation/types';
import { UsageVariantFlatList } from '../../../components/component-presentation/usage-variant-flatlist';

const DefaultAndAccentContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <View className="w-full gap-4">
        <Alert>
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>New features available</Alert.Title>
            <Alert.Description>
              Check out our latest updates including dark mode support and
              improved accessibility features.
            </Alert.Description>
          </Alert.Content>
        </Alert>
        <Alert status="accent">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Update available</Alert.Title>
            <Alert.Description>
              A new version of the application is available. Please refresh to
              get the latest features and bug fixes.
            </Alert.Description>
          </Alert.Content>
        </Alert>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const SuccessWarningDangerContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <View className="w-full gap-4">
        <Alert status="success">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Success</Alert.Title>
            <Alert.Description>
              Your profile information has been updated. Review the changes in
              your account settings.
            </Alert.Description>
          </Alert.Content>
        </Alert>
        <Alert status="warning">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Scheduled maintenance</Alert.Title>
            <Alert.Description>
              Our services will be unavailable on Sunday, March 15th from 2:00
              AM to 6:00 AM UTC for scheduled maintenance.
            </Alert.Description>
          </Alert.Content>
        </Alert>

        <Alert status="danger">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Unable to connect to server</Alert.Title>
            <Alert.Description>
              Unable to connect to the server. Check your internet connection
              and try again.
            </Alert.Description>
          </Alert.Content>
        </Alert>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const WithButtonsContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <View className="w-full gap-4">
        <Alert status="accent">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Update available</Alert.Title>
            <Alert.Description>
              A new version of the application is available. Please refresh to
              get the latest features and bug fixes.
            </Alert.Description>
          </Alert.Content>
          <Button size="sm" variant="primary">
            Refresh
          </Button>
        </Alert>

        <Alert status="danger">
          <Alert.Indicator />
          <Alert.Content>
            <Alert.Title>Unable to connect to server</Alert.Title>
            <Alert.Description>
              Unable to connect to the server. Check your internet connection
              and try again.
            </Alert.Description>
          </Alert.Content>
          <Button size="sm" variant="danger">
            Retry
          </Button>
        </Alert>

        <Alert status="success" className="items-center">
          <Alert.Indicator className="pt-0" />
          <Alert.Content>
            <Alert.Title>Profile updated successfully</Alert.Title>
          </Alert.Content>
          <CloseButton />
        </Alert>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const WithCustomIndicatorContent = () => {
  return (
    <View className="flex-1 items-center justify-center px-5">
      <View className="w-full gap-4">
        <Alert status="accent">
          <Alert.Indicator className="pt-px">
            <Spinner>
              <Spinner.Indicator iconProps={{ width: 20, height: 20 }} />
            </Spinner>
          </Alert.Indicator>
          <Alert.Content>
            <Alert.Title>Processing your request</Alert.Title>
            <Alert.Description>
              Please wait while we sync your data. This may take a few moments.
            </Alert.Description>
          </Alert.Content>
        </Alert>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const ALERT_VARIANTS: UsageVariant[] = [
  {
    value: 'default',
    label: 'Default & Accent',
    content: <DefaultAndAccentContent />,
  },
  {
    value: 'success-warning-danger',
    label: 'Success, Warning, Danger',
    content: <SuccessWarningDangerContent />,
  },
  {
    value: 'title-only',
    label: 'With buttons',
    content: <WithButtonsContent />,
  },
  {
    value: 'with-custom-indicator',
    label: 'With custom indicator',
    content: <WithCustomIndicatorContent />,
  },
];

export default function AlertScreen() {
  return <UsageVariantFlatList data={ALERT_VARIANTS} />;
}
```

---

## Spinner

---
title: Spinner
description: Displays an animated loading indicator.
links:
  source_native: spinner/spinner.tsx
  styles_native: spinner/spinner.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/spinner-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/spinner-docs-dark.mp4"
/>

### Import

```tsx
import { Spinner } from 'heroui-native';
```

### Anatomy

```tsx
<Spinner>
  <Spinner.Indicator>...</Spinner.Indicator>
</Spinner>
```

- **Spinner**: Main container that controls loading state, size, and color. Renders a default animated indicator if no children provided.
- **Spinner.Indicator**: Optional sub-component for customizing animation configuration and icon appearance. Accepts custom children to replace the default icon.

### Usage

#### Basic Usage

The Spinner component displays a rotating loading indicator.

```tsx
<Spinner />
```

#### Sizes

Control the spinner size with the `size` prop.

```tsx
<Spinner size="sm" />
<Spinner size="md" />
<Spinner size="lg" />
```

#### Colors

Use predefined color variants or custom colors.

```tsx
<Spinner color="default" />
<Spinner color="success" />
<Spinner color="warning" />
<Spinner color="danger" />
<Spinner color="#8B5CF6" />
```

#### Loading State

Control the visibility of the spinner with the `isLoading` prop.

```tsx
<Spinner isLoading={true} />
<Spinner isLoading={false} />
```

#### Animation Speed

Customize the rotation speed using the `animation` prop on the Indicator component.

```tsx
<Spinner>
  <Spinner.Indicator animation={{ rotation: { speed: 0.5 } }} />
</Spinner>

<Spinner>
  <Spinner.Indicator animation={{ rotation: { speed: 2 } }} />
</Spinner>
```

#### Custom Icon

Replace the default spinner icon with custom content.

```tsx
const themeColorForeground = useThemeColor('foreground')

<Spinner>
  <Spinner.Indicator>
    <Ionicons name="refresh" size={24} color={themeColorForeground} />
  </Spinner.Indicator>
</Spinner>

<Spinner>
  <Spinner.Indicator>
    <Text>⏳</Text>
  </Spinner.Indicator>
</Spinner>
```

### Example

```tsx
import { Spinner } from 'heroui-native';
import { Ionicons } from '@expo/vector-icons';
import React from 'react';
import { Text, TouchableOpacity, View } from 'react-native';

export default function SpinnerExample() {
  const [isLoading, setIsLoading] = React.useState(true);

  return (
    <View className="gap-4 p-4 bg-background">
      <View className="flex-row items-center gap-2 p-4 rounded-lg bg-stone-200">
        <Spinner size="sm" color="default" />
        <Text className="text-stone-500">Loading content...</Text>
      </View>

      <View className="items-center p-8 rounded-2xl bg-stone-200">
        <Spinner size="lg" color="success" isLoading={isLoading} />
        <Text className="text-stone-500 mt-4">Processing...</Text>
        <TouchableOpacity onPress={() => setIsLoading(!isLoading)}>
          <Text className="text-primary mt-2 text-sm">
            {isLoading ? 'Tap to stop' : 'Tap to start'}
          </Text>
        </TouchableOpacity>
      </View>

      <View className="flex-row gap-4 items-center justify-center">
        <Spinner size="md" color="#EC4899">
          <Spinner.Indicator animation={{ rotation: { speed: 0.7 } }}>
            <Ionicons name="refresh" size={24} color="#EC4899" />
          </Spinner.Indicator>
        </Spinner>
      </View>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/spinner.tsx).

### API Reference

#### Spinner

| prop           | type                                                        | default     | description                                        |
| -------------- | ----------------------------------------------------------- | ----------- | -------------------------------------------------- |
| `children`     | `React.ReactNode`                                           | `undefined` | Content to render inside the spinner               |
| `size`         | `'sm' \| 'md' \| 'lg'`                                      | `'md'`      | Size of the spinner                                |
| `color`        | `'default' \| 'success' \| 'warning' \| 'danger' \| string` | `'default'` | Color theme of the spinner                         |
| `isLoading`    | `boolean`                                                   | `true`      | Whether the spinner is loading                     |
| `className`    | `string`                                                    | `undefined` | Custom class name for the spinner                  |
| `animation`    | `SpinnerRootAnimation`                                      | -           | Animation configuration                            |
| `...ViewProps` | `ViewProps`                                                 | -           | All standard React Native View props are supported |

#### SpinnerRootAnimation

Animation configuration for Spinner component. Can be:

- `false` or `"disabled"`: Disable only root animations
- `"disable-all"`: Disable all animations including children
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop             | type                                     | default                                                              | description                                     |
| ---------------- | ---------------------------------------- | -------------------------------------------------------------------- | ----------------------------------------------- |
| `state`          | `'disabled' \| 'disable-all' \| boolean` | -                                                                    | Disable animations while customizing properties |
| `entering.value` | `EntryOrExitLayoutType`                  | `FadeIn`<br/>`.duration(200)`<br/>`.easing(Easing.out(Easing.ease))` | Custom entering animation                       |
| `exiting.value`  | `EntryOrExitLayoutType`                  | `FadeOut`<br/>`.duration(100)`                                       | Custom exiting animation                        |

#### Spinner.Indicator

| prop                    | type                        | default     | description                                                  |
| ----------------------- | --------------------------- | ----------- | ------------------------------------------------------------ |
| `children`              | `React.ReactNode`           | `undefined` | Content to render inside the indicator                       |
| `iconProps`             | `SpinnerIconProps`          | `undefined` | Props for the default icon                                   |
| `className`             | `string`                    | `undefined` | Custom class name for the indicator element                  |
| `animation`             | `SpinnerIndicatorAnimation` | -           | Animation configuration                                      |
| `isAnimatedStyleActive` | `boolean`                   | `true`      | Whether animated styles (react-native-reanimated) are active |
| `...Animated.ViewProps` | `Animated.ViewProps`        | -           | All Reanimated Animated.View props are supported             |

#### SpinnerIndicatorAnimation

Animation configuration for Spinner.Indicator component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop              | type                         | default         | description                                     |
| ----------------- | ---------------------------- | --------------- | ----------------------------------------------- |
| `state`           | `'disabled' \| boolean`      | -               | Disable animations while customizing properties |
| `rotation.speed`  | `number`                     | `1.1`           | Rotation speed multiplier                       |
| `rotation.easing` | `WithTimingConfig['easing']` | `Easing.linear` | Animation easing configuration                  |

#### SpinnerIconProps

| prop     | type               | default          | description        |
| -------- | ------------------ | ---------------- | ------------------ |
| `width`  | `number \| string` | `24`             | Width of the icon  |
| `height` | `number \| string` | `24`             | Height of the icon |
| `color`  | `string`           | `'currentColor'` | Color of the icon  |

### Examples from spinner.md

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { Spinner } from 'heroui-native';
```

#### Components

- `Spinner` - Root container component
- `Spinner.Indicator` - Spinner indicator icon

#### Props

##### Spinner Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `size` | `'sm' \| 'md' \| 'lg'` | `'md'` | Spinner size |
| `color` | `string` | - | Spinner color |

##### Spinner.Indicator Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `iconProps` | `object` | - | Icon customization props |

#### Example: Basic Spinner

```tsx
import { Spinner } from 'heroui-native';
import { View } from 'react-native';

function BasicSpinnerExample() {
  return (
    <View className="flex-1 items-center justify-center">
      <Spinner />
    </View>
  );
}
```

#### Example: Spinner Sizes

```tsx
import { Spinner } from 'heroui-native';
import { View } from 'react-native';

function SpinnerSizesExample() {
  return (
    <View className="flex-row gap-8 items-center justify-center">
      <Spinner size="sm" />
      <Spinner size="md" />
      <Spinner size="lg" />
    </View>
  );
}
```

#### Example: Spinner with Custom Color

```tsx
import { Spinner } from 'heroui-native';
import { View } from 'react-native';

function SpinnerColorExample() {
  return (
    <View className="flex-row gap-8 items-center justify-center">
      <Spinner color="red" />
      <Spinner color="blue" />
      <Spinner color="green" />
    </View>
  );
}
```

#### Example: Spinner with Animation

```tsx
import { Spinner } from 'heroui-native';
import { FadeIn } from 'react-native-reanimated';
import { View } from 'react-native';

function SpinnerWithAnimationExample() {
  return (
    <View className="flex-1 items-center justify-center">
      <Spinner entering={FadeIn.delay(100)} />
    </View>
  );
}
```

#### Complete Example Code

```tsx
import { Spinner } from 'heroui-native';
import { View } from 'react-native';

export default function SpinnerExample() {
  return (
    <View className="flex-1 items-center justify-center">
      <Spinner size="md" />
    </View>
  );
}
```

---

## Skeleton

---
title: Skeleton
description: Displays a loading placeholder with shimmer or pulse animation effects.
links:
  source_native: skeleton/skeleton.tsx
  styles_native: skeleton/skeleton.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/skeleton-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/skeleton-docs-dark.mp4"
/>

### Import

```tsx
import { Skeleton } from 'heroui-native';
```

### Anatomy

The Skeleton component is a simple wrapper that renders a placeholder for content that is loading. It does not have any child components.

```tsx
<Skeleton />
```

### Usage

#### Basic Usage

The Skeleton component creates an animated placeholder while content is loading.

```tsx
<Skeleton className="h-20 w-full rounded-lg" />
```

#### With Content

Show skeleton while loading, then display content when ready.

```tsx
<Skeleton isLoading={isLoading} className="h-20 rounded-lg">
  <View className="h-20 bg-primary rounded-lg">
    <Text>Loaded Content</Text>
  </View>
</Skeleton>
```

#### Animation Variants

Control the animation style with the `variant` prop.

```tsx
<Skeleton variant="shimmer" className="h-20 w-full rounded-lg" />
<Skeleton variant="pulse" className="h-20 w-full rounded-lg" />
<Skeleton variant="none" className="h-20 w-full rounded-lg" />
```

#### Custom Shimmer Configuration

Customize the shimmer effect with duration, speed, and highlight color.

```tsx
<Skeleton
  className="h-16 w-full rounded-lg"
  variant="shimmer"
  animation={{
    shimmer: {
      duration: 2000,
      speed: 2,
      highlightColor: 'rgba(59, 130, 246, 0.3)',
    },
  }}
>
  ...
</Skeleton>
```

#### Custom Pulse Configuration

Configure pulse animation with duration and opacity range.

```tsx
<Skeleton
  className="h-16 w-full rounded-lg"
  variant="pulse"
  animation={{
    pulse: {
      duration: 500,
      minOpacity: 0.1,
      maxOpacity: 0.8,
    },
  }}
>
  ...
</Skeleton>
```

#### Shape Variations

Create different skeleton shapes using className for styling.

```tsx
<Skeleton className="h-4 w-full rounded-md" />
<Skeleton className="h-4 w-3/4 rounded-md" />
<Skeleton className="h-4 w-1/2 rounded-md" />
<Skeleton className="size-12 rounded-full" />
```

#### Custom Enter/Exit Animations

Apply custom Reanimated transitions when skeleton appears or disappears.

```tsx
<Skeleton
  entering={FadeIn.duration(300)}
  exiting={FadeOut.duration(300)}
  isLoading={isLoading}
  className="h-20 w-full rounded-lg"
>
  ...
</Skeleton>
```

### Example

```tsx
import { Avatar, Card, Skeleton } from 'heroui-native';
import { useState } from 'react';
import { Image, Text, View } from 'react-native';

export default function SkeletonExample() {
  const [isLoading, setIsLoading] = useState(true);

  return (
    <Card className="p-4">
      <View className="flex-row items-center gap-3 mb-4">
        <Skeleton isLoading={isLoading} className="h-10 w-10 rounded-full">
          <Avatar size="sm" alt="Avatar">
            <Avatar.Image source={{ uri: 'https://i.pravatar.cc/150?img=4' }} />
            <Avatar.Fallback />
          </Avatar>
        </Skeleton>

        <View className="flex-1 gap-1">
          <Skeleton isLoading={isLoading} className="h-3 w-32 rounded-md">
            <Text className="font-semibold text-foreground">John Doe</Text>
          </Skeleton>
          <Skeleton isLoading={isLoading} className="h-3 w-24 rounded-md">
            <Text className="text-sm text-muted">@johndoe</Text>
          </Skeleton>
        </View>
      </View>

      <Skeleton
        isLoading={isLoading}
        className="h-48 w-full rounded-lg"
        animation={{
          shimmer: {
            duration: 1500,
            speed: 1,
          },
        }}
      >
        <View className="h-48 bg-surface-tertiary rounded-lg overflow-hidden">
          <Image
            source={{
              uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/backgrounds/cards/car1.jpg',
            }}
            className="h-full w-full"
          />
        </View>
      </Skeleton>
    </Card>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/skeleton.tsx).

### API Reference

#### Skeleton

| prop                    | type                             | default     | description                                                  |
| ----------------------- | -------------------------------- | ----------- | ------------------------------------------------------------ |
| `children`              | `React.ReactNode`                | -           | Content to show when not loading                             |
| `isLoading`             | `boolean`                        | `true`      | Whether the skeleton is currently loading                    |
| `variant`               | `'shimmer' \| 'pulse' \| 'none'` | `'shimmer'` | Animation variant                                            |
| `animation`             | `SkeletonRootAnimation`          | -           | Animation configuration                                      |
| `isAnimatedStyleActive` | `boolean`                        | `true`      | Whether animated styles (react-native-reanimated) are active |
| `className`             | `string`                         | -           | Additional CSS classes for styling                           |
| `...Animated.ViewProps` | `AnimatedProps<ViewProps>`       | -           | All Reanimated Animated.View props are supported             |

#### SkeletonRootAnimation

Animation configuration for Skeleton component. Can be:

- `false` or `"disabled"`: Disable only root animations
- `"disable-all"`: Disable all animations including children
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                     | type                                     | default                     | description                                     |
| ------------------------ | ---------------------------------------- | --------------------------- | ----------------------------------------------- |
| `state`                  | `'disabled' \| 'disable-all' \| boolean` | -                           | Disable animations while customizing properties |
| `entering.value`         | `EntryOrExitLayoutType`                  | `FadeIn`                    | Custom entering animation                       |
| `exiting.value`          | `EntryOrExitLayoutType`                  | `FadeOut`                   | Custom exiting animation                        |
| `shimmer.duration`       | `number`                                 | `1500`                      | Animation duration in milliseconds              |
| `shimmer.speed`          | `number`                                 | `1`                         | Speed multiplier for the animation              |
| `shimmer.highlightColor` | `string`                                 | -                           | Highlight color for the shimmer effect          |
| `shimmer.easing`         | `EasingFunction`                         | `Easing.linear`             | Easing function for the animation               |
| `pulse.duration`         | `number`                                 | `1000`                      | Animation duration in milliseconds              |
| `pulse.minOpacity`       | `number`                                 | `0.5`                       | Minimum opacity value                           |
| `pulse.maxOpacity`       | `number`                                 | `1`                         | Maximum opacity value                           |
| `pulse.easing`           | `EasingFunction`                         | `Easing.inOut(Easing.ease)` | Easing function for the animation               |

### Examples from skeleton.md

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { Skeleton, SkeletonGroup } from 'heroui-native';
```

#### Components

- `Skeleton` - Individual skeleton placeholder
- `SkeletonGroup` - Container for multiple skeletons

#### Props

##### Skeleton Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `className` | `string` | - | Additional CSS classes |

#### Example: Basic Skeleton

```tsx
import { Skeleton } from 'heroui-native';
import { View } from 'react-native';

function BasicSkeletonExample() {
  return (
    <View className="flex-1 items-center justify-center p-5">
      <Skeleton className="h-4 w-3/4 mb-4" />
      <Skeleton className="h-4 w-1/2" />
    </View>
  );
}
```

#### Example: Skeleton Card

```tsx
import { Skeleton, SkeletonGroup } from 'heroui-native';
import { View } from 'react-native';

function SkeletonCardExample() {
  return (
    <View className="p-5">
      <SkeletonGroup>
        <Skeleton className="h-48 w-full rounded-xl mb-4" />
        <Skeleton className="h-6 w-3/4 mb-2" />
        <Skeleton className="h-4 w-1/2 mb-2" />
        <Skeleton className="h-4 w-full" />
      </SkeletonGroup>
    </View>
  );
}
```

#### Example: Avatar Skeleton

```tsx
import { Skeleton } from 'heroui-native';
import { View } from 'react-native';

function AvatarSkeletonExample() {
  return (
    <View className="flex-row items-center p-5 gap-4">
      <Skeleton className="h-12 w-12 rounded-full" />
      <View className="flex-1">
        <Skeleton className="h-4 w-32 mb-2" />
        <Skeleton className="h-3 w-48" />
      </View>
    </View>
  );
}
```

#### Complete Example Code

```tsx
import { Skeleton, SkeletonGroup } from 'heroui-native';
import { View } from 'react-native';

export default function SkeletonExample() {
  return (
    <View className="flex-1 p-5">
      <SkeletonGroup>
        <Skeleton className="h-48 w-full rounded-xl mb-4" />
        <Skeleton className="h-6 w-3/4 mb-2" />
        <Skeleton className="h-4 w-1/2 mb-2" />
        <Skeleton className="h-4 w-full" />
      </SkeletonGroup>
    </View>
  );
}
```

---

## SkeletonGroup

---
title: SkeletonGroup
description: Coordinates multiple skeleton loading placeholders with centralized animation control.
links:
  source_native: skeleton-group/skeleton-group.tsx
  styles_native: skeleton-group/skeleton-group.styles.ts
  figma: true
---

<NativeVideoPlayerView
  srcLight="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/skeleton-docs-light.mp4"
  srcDark="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/native/components/videos/skeleton-docs-dark.mp4"
/>

### Import

```tsx
import { SkeletonGroup } from 'heroui-native';
```

### Anatomy

```tsx
<SkeletonGroup>
  <SkeletonGroup.Item />
</SkeletonGroup>
```

- **SkeletonGroup**: Root container that provides centralized control for all skeleton items
- **SkeletonGroup.Item**: Individual skeleton item that inherits props from the parent group

### Usage

#### Basic Usage

The SkeletonGroup component manages multiple skeleton items with shared loading state and animation.

```tsx
<SkeletonGroup isLoading={isLoading}>
  <SkeletonGroup.Item className="h-4 w-full rounded-md" />
  <SkeletonGroup.Item className="h-4 w-3/4 rounded-md" />
  <SkeletonGroup.Item className="h-4 w-1/2 rounded-md" />
</SkeletonGroup>
```

#### With Container Layout

Use className on the group to control layout of skeleton items.

```tsx
<SkeletonGroup isLoading={isLoading} className="flex-row items-center gap-3">
  <SkeletonGroup.Item className="h-12 w-12 rounded-lg" />
  <View className="flex-1 gap-1.5">
    <SkeletonGroup.Item className="h-4 w-full rounded-md" />
    <SkeletonGroup.Item className="h-3 w-2/3 rounded-md" />
  </View>
</SkeletonGroup>
```

#### With isSkeletonOnly for Pure Skeleton Layouts

Use `isSkeletonOnly` when the group contains only skeleton placeholders with layout wrappers (like View) that have no content to render in the loaded state. This prop hides the entire group when `isLoading` is false, preventing empty containers from affecting your layout.

```tsx
<SkeletonGroup
  isLoading={isLoading}
  isSkeletonOnly // Hides entire group when isLoading is false
  className="flex-row items-center gap-3"
>
  <SkeletonGroup.Item className="h-12 w-12 rounded-lg" />
  {/* This View is only for layout, no content */}
  <View className="flex-1 gap-1.5">
    <SkeletonGroup.Item className="h-4 w-full rounded-md" />
    <SkeletonGroup.Item className="h-3 w-2/3 rounded-md" />
  </View>
</SkeletonGroup>
```

#### With Animation Variants

Control animation style for all items in the group.

```tsx
<SkeletonGroup isLoading={isLoading} variant="pulse">
  <SkeletonGroup.Item className="h-10 w-10 rounded-full" />
  <SkeletonGroup.Item className="h-4 w-32 rounded-md" />
  <SkeletonGroup.Item className="h-3 w-24 rounded-md" />
</SkeletonGroup>
```

#### With Custom Animation Configuration

Configure shimmer or pulse animations for the entire group.

```tsx
<SkeletonGroup
  isLoading={isLoading}
  variant="shimmer"
  animation={{
    shimmer: {
      duration: 2000,
      highlightColor: 'rgba(59, 130, 246, 0.3)',
    },
  }}
>
  <SkeletonGroup.Item className="h-16 w-full rounded-lg" />
  <SkeletonGroup.Item className="h-4 w-3/4 rounded-md" />
</SkeletonGroup>
```

#### With Enter/Exit Animations

Apply Reanimated transitions when the group appears or disappears.

```tsx
<SkeletonGroup
  entering={FadeInLeft}
  exiting={FadeOutRight}
  isLoading={isLoading}
  className="w-full gap-2"
>
  <SkeletonGroup.Item className="h-4 w-full rounded-md" />
  <SkeletonGroup.Item className="h-4 w-3/4 rounded-md" />
</SkeletonGroup>
```

### Example

```tsx
import { Card, SkeletonGroup, Avatar } from 'heroui-native';
import { useState } from 'react';
import { Text, View, Image } from 'react-native';

export default function SkeletonGroupExample() {
  const [isLoading, setIsLoading] = useState(true);

  return (
    <SkeletonGroup isLoading={isLoading}>
      <Card className="p-4">
        <Card.Header>
          <View className="flex-row items-center gap-3 mb-4">
            <SkeletonGroup.Item className="h-10 w-10 rounded-full">
              <Avatar size="sm" alt="Avatar">
                <Avatar.Image
                  source={{ uri: 'https://i.pravatar.cc/150?img=4' }}
                />
                <Avatar.Fallback />
              </Avatar>
            </SkeletonGroup.Item>

            <View className="flex-1 gap-1">
              <SkeletonGroup.Item className="h-3 w-32 rounded-md">
                <Text className="font-semibold text-foreground">John Doe</Text>
              </SkeletonGroup.Item>
              <SkeletonGroup.Item className="h-3 w-24 rounded-md">
                <Text className="text-sm text-muted">@johndoe</Text>
              </SkeletonGroup.Item>
            </View>
          </View>

          <View className="mb-4 gap-1.5">
            <SkeletonGroup.Item className="h-4 w-full rounded-md">
              <Text className="text-base text-foreground">
                This is the first line of the post content.
              </Text>
            </SkeletonGroup.Item>
            <SkeletonGroup.Item className="h-4 w-full rounded-md">
              <Text className="text-base text-foreground">
                Second line with more interesting content to read.
              </Text>
            </SkeletonGroup.Item>
            <SkeletonGroup.Item className="h-4 w-2/3 rounded-md">
              <Text className="text-base text-foreground">
                Last line is shorter.
              </Text>
            </SkeletonGroup.Item>
          </View>
        </Card.Header>

        <SkeletonGroup.Item className="h-48 w-full rounded-lg">
          <View className="h-48 bg-surface-tertiary rounded-lg overflow-hidden">
            <Image
              source={{
                uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/backgrounds/cards/car1.jpg',
              }}
              className="h-full w-full"
            />
          </View>
        </SkeletonGroup.Item>
      </Card>
    </SkeletonGroup>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/skeleton-group.tsx).

### API Reference

#### SkeletonGroup

| prop                    | type                             | default     | description                                                            |
| ----------------------- | -------------------------------- | ----------- | ---------------------------------------------------------------------- |
| `children`              | `React.ReactNode`                | -           | SkeletonGroup.Item components and layout elements                      |
| `isLoading`             | `boolean`                        | `true`      | Whether the skeleton items are currently loading                       |
| `isSkeletonOnly`        | `boolean`                        | `false`     | Hides entire group when isLoading is false (for skeleton-only layouts) |
| `variant`               | `'shimmer' \| 'pulse' \| 'none'` | `'shimmer'` | Animation variant for all items in the group                           |
| `animation`             | `SkeletonRootAnimation`          | -           | Animation configuration                                                |
| `className`             | `string`                         | -           | Additional CSS classes for the group container                         |
| `style`                 | `StyleProp<ViewStyle>`           | -           | Custom styles for the group container                                  |
| `...Animated.ViewProps` | `AnimatedProps<ViewProps>`       | -           | All Reanimated Animated.View props are supported                       |

#### SkeletonRootAnimation

Animation configuration for SkeletonGroup component. Can be:

- `false` or `"disabled"`: Disable only root animations
- `"disable-all"`: Disable all animations including children
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                     | type                                     | default                     | description                                     |
| ------------------------ | ---------------------------------------- | --------------------------- | ----------------------------------------------- |
| `state`                  | `'disabled' \| 'disable-all' \| boolean` | -                           | Disable animations while customizing properties |
| `entering.value`         | `EntryOrExitLayoutType`                  | `FadeIn`                    | Custom entering animation                       |
| `exiting.value`          | `EntryOrExitLayoutType`                  | `FadeOut`                   | Custom exiting animation                        |
| `shimmer.duration`       | `number`                                 | `1500`                      | Animation duration in milliseconds              |
| `shimmer.speed`          | `number`                                 | `1`                         | Speed multiplier for the animation              |
| `shimmer.highlightColor` | `string`                                 | -                           | Highlight color for the shimmer effect          |
| `shimmer.easing`         | `EasingFunction`                         | `Easing.linear`             | Easing function for the animation               |
| `pulse.duration`         | `number`                                 | `1000`                      | Animation duration in milliseconds              |
| `pulse.minOpacity`       | `number`                                 | `0.5`                       | Minimum opacity value                           |
| `pulse.maxOpacity`       | `number`                                 | `1`                         | Maximum opacity value                           |
| `pulse.easing`           | `EasingFunction`                         | `Easing.inOut(Easing.ease)` | Easing function for the animation               |

#### SkeletonGroup.Item

| prop                    | type                             | default   | description                                                         |
| ----------------------- | -------------------------------- | --------- | ------------------------------------------------------------------- |
| `children`              | `React.ReactNode`                | -         | Content to show when not loading                                    |
| `isLoading`             | `boolean`                        | inherited | Whether the skeleton is currently loading (overrides group setting) |
| `variant`               | `'shimmer' \| 'pulse' \| 'none'` | inherited | Animation variant (overrides group setting)                         |
| `animation`             | `SkeletonRootAnimation`          | inherited | Animation configuration (overrides group setting)                   |
| `className`             | `string`                         | -         | Additional CSS classes for styling the item                         |
| `...Animated.ViewProps` | `AnimatedProps<ViewProps>`       | -         | All Reanimated Animated.View props are supported                    |

### Special Notes

#### Props Inheritance

SkeletonGroup.Item components inherit all animation-related props from their parent SkeletonGroup:

- `isLoading`
- `variant`
- `animation`

Individual items can override any inherited prop by providing their own value.
