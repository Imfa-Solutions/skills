# Data Display & Utility Components

## Table of Contents
- [Avatar](#avatar)
- [Chip](#chip)
- [ScrollShadow](#scrollshadow)
- [PressableFeedback](#pressablefeedback)

---

## Avatar

Displays a user avatar with support for images, text initials, or fallback icons.

**Source:** avatar/avatar.tsx
**Styles:** avatar/avatar.styles.ts

### Import

```tsx
import { Avatar } from 'heroui-native';
```

### Anatomy

```tsx
<Avatar>
  <Avatar.Image />
  <Avatar.Fallback />
</Avatar>
```

- **Avatar**: Main container that manages avatar display state. Provides size and color context to child components. Supports animation configuration to control all child animations.
- **Avatar.Image**: Optional image component that displays the avatar image. Handles loading states and errors automatically with opacity-based fade-in animation.
- **Avatar.Fallback**: Optional fallback component shown when image fails to load or is unavailable. Displays a default person icon when no children are provided. Supports configurable entering animations with delay support.

### Usage

#### Basic Usage

The Avatar component displays a default person icon when no image or text is provided.

```tsx
<Avatar>
  <Avatar.Fallback />
</Avatar>
```

#### With Image

Display an avatar image with automatic fallback handling.

```tsx
<Avatar>
  <Avatar.Image source={{ uri: 'https://example.com/avatar.jpg' }} />
  <Avatar.Fallback>JD</Avatar.Fallback>
</Avatar>
```

#### With Text Initials

Show text initials as the avatar content.

```tsx
<Avatar>
  <Avatar.Fallback>AB</Avatar.Fallback>
</Avatar>
```

#### With Custom Icon

Provide a custom icon as fallback content.

```tsx
<Avatar>
  <Avatar.Fallback>
    <Ionicons name="person" size={18} />
  </Avatar.Fallback>
</Avatar>
```

#### Sizes

Control the avatar size with the size prop.

```tsx
<Avatar size="sm">
  <Avatar.Fallback />
</Avatar>

<Avatar size="md">
  <Avatar.Fallback />
</Avatar>

<Avatar size="lg">
  <Avatar.Fallback />
</Avatar>
```

#### Variants

Choose between different visual styles with the `variant` prop.

```tsx
<Avatar variant="default">
  <Avatar.Fallback>DF</Avatar.Fallback>
</Avatar>

<Avatar variant="soft">
  <Avatar.Fallback>SF</Avatar.Fallback>
</Avatar>
```

#### Colors

Apply different color variants to the avatar.

```tsx
<Avatar color="default">
  <Avatar.Fallback>DF</Avatar.Fallback>
</Avatar>

<Avatar color="accent">
  <Avatar.Fallback>AC</Avatar.Fallback>
</Avatar>

<Avatar color="success">
  <Avatar.Fallback>SC</Avatar.Fallback>
</Avatar>

<Avatar color="warning">
  <Avatar.Fallback>WR</Avatar.Fallback>
</Avatar>

<Avatar color="danger">
  <Avatar.Fallback>DG</Avatar.Fallback>
</Avatar>
```

#### Delayed Fallback

Show fallback after a delay to prevent flashing during image load.

```tsx
<Avatar>
  <Avatar.Image source={{ uri: imageUrl }} />
  <Avatar.Fallback delayMs={600}>NA</Avatar.Fallback>
</Avatar>
```

#### Custom Image Component

Use a custom image component with the asChild prop.

```tsx
import { Image } from 'expo-image';

<Avatar>
  <Avatar.Image source={{ uri: imageUrl }} asChild>
    <Image style={{ width: '100%', height: '100%' }} contentFit="cover" />
  </Avatar.Image>
  <Avatar.Fallback>EI</Avatar.Fallback>
</Avatar>;
```

#### Animation Control

Control animations at different levels of the Avatar component.

##### Disable All Animations

Disable all animations including children from the root component:

```tsx
<Avatar animation="disable-all">
  <Avatar.Image source={{ uri: imageUrl }} />
  <Avatar.Fallback>JD</Avatar.Fallback>
</Avatar>
```

##### Custom Image Animation

Customize the image opacity animation:

```tsx
<Avatar>
  <Avatar.Image
    source={{ uri: imageUrl }}
    animation={{
      opacity: {
        value: [0.3, 1],
        timingConfig: { duration: 300 },
      },
    }}
  />
  <Avatar.Fallback>JD</Avatar.Fallback>
</Avatar>
```

##### Custom Fallback Animation

Customize the fallback entering animation:

```tsx
import { FadeInDown } from 'react-native-reanimated';

<Avatar>
  <Avatar.Image source={{ uri: imageUrl }} />
  <Avatar.Fallback
    animation={{
      entering: {
        value: FadeInDown.duration(400),
      },
    }}
  >
    JD
  </Avatar.Fallback>
</Avatar>;
```

##### Disable Individual Animations

Disable animations for specific components:

```tsx
<Avatar>
  <Avatar.Image source={{ uri: imageUrl }} animation={false} />
  <Avatar.Fallback animation="disabled">JD</Avatar.Fallback>
</Avatar>
```

### Docs Example

```tsx
import { Avatar } from 'heroui-native';
import { View } from 'react-native';

export default function AvatarExample() {
  const users = [
    { id: 1, image: 'https://example.com/user1.jpg', name: 'John Doe' },
    { id: 2, image: 'https://example.com/user2.jpg', name: 'Jane Smith' },
    { id: 3, image: 'https://example.com/user3.jpg', name: 'Bob Johnson' },
  ];

  return (
    <View className="flex-row gap-4">
      {users.map((user) => (
        <Avatar key={user.id} size="lg" color="accent">
          <Avatar.Image source={{ uri: user.image }} />
          <Avatar.Fallback>
            {user.name
              .split(' ')
              .map((n) => n[0])
              .join('')}
          </Avatar.Fallback>
        </Avatar>
      ))}
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/avatar.tsx).

### API Reference

#### Avatar

| prop           | type                                                          | default     | description                                                                               |
| -------------- | ------------------------------------------------------------- | ----------- | ----------------------------------------------------------------------------------------- |
| `children`     | `React.ReactNode`                                             | -           | Avatar content (Image and/or Fallback components)                                         |
| `size`         | `'sm' \| 'md' \| 'lg'`                                        | `'md'`      | Size of the avatar                                                                        |
| `variant`      | `'default' \| 'soft'`                                         | `'default'` | Visual variant of the avatar                                                              |
| `color`        | `'default' \| 'accent' \| 'success' \| 'warning' \| 'danger'` | `'accent'`  | Color variant of the avatar                                                               |
| `className`    | `string`                                                      | -           | Additional CSS classes to apply                                                           |
| `animation`    | `"disable-all"` \| `undefined`                                | `undefined` | Animation configuration. Use `"disable-all"` to disable all animations including children |
| `alt`          | `string`                                                      | -           | Alternative text description for accessibility                                            |
| `...ViewProps` | `ViewProps`                                                   | -           | All standard React Native View props are supported                                        |

#### Avatar.Image

Props extend different base types depending on the `asChild` prop value:

- When `asChild={false}` (default): extends `AnimatedProps<ImageProps>` from React Native Reanimated
- When `asChild={true}`: extends primitive image props for custom image components

**Note:** When using `asChild={true}` with custom image components, the `className` prop may not be applied in some cases depending on the custom component's implementation. Ensure your custom component properly handles style props.

| prop                    | type                                           | default | description                                                  |
| ----------------------- | ---------------------------------------------- | ------- | ------------------------------------------------------------ |
| `source`                | `ImageSourcePropType`                          | -       | Image source (required when `asChild={false}`)               |
| `asChild`               | `boolean`                                      | `false` | Whether to use a custom image component as child             |
| `className`             | `string`                                       | -       | Additional CSS classes to apply                              |
| `animation`             | `AvatarImageAnimation`                         | -       | Animation configuration                                      |
| `isAnimatedStyleActive` | `boolean`                                      | `true`  | Whether animated styles (react-native-reanimated) are active |
| `...AnimatedProps`      | `AnimatedProps<ImageProps>` or primitive props | -       | Additional props based on `asChild` value                    |

##### AvatarImageAnimation

Animation configuration for avatar image component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                   | type                    | default                                             | description                                          |
| ---------------------- | ----------------------- | --------------------------------------------------- | ---------------------------------------------------- |
| `state`                | `'disabled' \| boolean` | -                                                   | Disable animations while customizing properties      |
| `opacity.value`        | `[number, number]`      | `[0, 1]`                                            | Opacity values [initial, loaded] for image animation |
| `opacity.timingConfig` | `WithTimingConfig`      | `{ duration: 200, easing: Easing.in(Easing.ease) }` | Animation timing configuration                       |

**Note:** Animation is automatically disabled when `asChild={true}`

#### Avatar.Fallback

| prop                    | type                                                          | default               | description                                                                       |
| ----------------------- | ------------------------------------------------------------- | --------------------- | --------------------------------------------------------------------------------- |
| `children`              | `React.ReactNode`                                             | -                     | Fallback content (text, icon, or custom element)                                  |
| `delayMs`               | `number`                                                      | `0`                   | Delay in milliseconds before showing the fallback (applied to entering animation) |
| `color`                 | `'default' \| 'accent' \| 'success' \| 'warning' \| 'danger'` | inherited from parent | Color variant of the fallback                                                     |
| `className`             | `string`                                                      | -                     | Additional CSS classes for the container                                          |
| `classNames`            | `ElementSlots<AvatarFallbackSlots>`                           | -                     | Additional CSS classes for different parts                                        |
| `styles`                | `{ container?: ViewStyle; text?: TextStyle }`                 | -                     | Styles for different parts of the avatar fallback                                 |
| `textProps`             | `TextProps`                                                   | -                     | Props to pass to Text component when children is a string                         |
| `iconProps`             | `PersonIconProps`                                             | -                     | Props to customize the default person icon                                        |
| `animation`             | `AvatarFallbackAnimation`                                     | -                     | Animation configuration                                                           |
| `...Animated.ViewProps` | `Animated.ViewProps`                                          | -                     | All Reanimated Animated.View props are supported                                  |

**classNames prop:** `ElementSlots<AvatarFallbackSlots>` provides type-safe CSS classes for different parts of the fallback component. Available slots: `container`, `text`.

##### `styles`

| prop        | type        | description                 |
| ----------- | ----------- | --------------------------- |
| `container` | `ViewStyle` | Styles for the container    |
| `text`      | `TextStyle` | Styles for the text content |

##### AvatarFallbackAnimation

Animation configuration for avatar fallback component. Can be:

- `false` or `"disabled"`: Disable all animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop             | type                    | default                                                                             | description                                     |
| ---------------- | ----------------------- | ----------------------------------------------------------------------------------- | ----------------------------------------------- |
| `state`          | `'disabled' \| boolean` | -                                                                                   | Disable animations while customizing properties |
| `entering.value` | `EntryOrExitLayoutType` | `FadeIn`<br/>`.duration(200)`<br/>`.easing(Easing.in(Easing.ease))`<br/>`.delay(0)` | Custom entering animation for fallback          |

##### PersonIconProps

| prop    | type     | description                           |
| ------- | -------- | ------------------------------------- |
| `size`  | `number` | Size of the icon in pixels (optional) |
| `color` | `string` | Color of the icon (optional)          |

### Hooks

#### useAvatar Hook

Hook to access Avatar primitive root context. Provides access to avatar status.

**Note:** The `status` property is particularly useful for adding a skeleton loader while the image is loading.

```tsx
import { Avatar, useAvatar, Skeleton } from 'heroui-native';

function AvatarWithSkeleton() {
  return (
    <Avatar>
      <Avatar.Image source={{ uri: imageUrl }} />
      <AvatarContent />
      <Avatar.Fallback>JD</Avatar.Fallback>
    </Avatar>
  );
}

function AvatarContent() {
  const { status } = useAvatar();

  if (status === 'loading') {
    return <Skeleton className="absolute inset-0 rounded-full" />;
  }

  return null;
}
```

| property    | type                                                 | description                                                 |
| ----------- | ---------------------------------------------------- | ----------------------------------------------------------- |
| `status`    | `'loading' \| 'loaded' \| 'error'`                   | Current loading state of the avatar image.                  |
| `setStatus` | `(status: 'loading' \| 'loaded' \| 'error') => void` | Function to manually set the avatar status (advanced usage) |

**Status Values:**

- `'loading'`: Image is currently being loaded. Use this state to show a skeleton loader.
- `'loaded'`: Image has successfully loaded.
- `'error'`: Image failed to load or source is invalid. The fallback component is automatically shown in this state.

### Examples from avatar.md

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { Avatar, cn } from 'heroui-native';
```

#### Components

- `Avatar` - Root container component
- `Avatar.Image` - User profile image
- `Avatar.Fallback` - Fallback content when image fails to load

#### Avatar Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `size` | `'sm' \| 'md' \| 'lg'` | `'md'` | Avatar size |
| `variant` | `'default' \| 'soft'` | `'default'` | Visual style variant |
| `color` | `'accent' \| 'default' \| 'success' \| 'warning' \| 'danger'` | `'default'` | Fallback color |
| `alt` | `string` | required | Accessibility label |
| `className` | `string` | - | Additional CSS classes |

#### Avatar.Image Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `source` | `ImageSourcePropType` | - | Image source URI |
| `className` | `string` | - | Additional CSS classes |

#### Avatar.Fallback Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `className` | `string` | - | Additional CSS classes |
| `classNames` | `object` | - | Object with `container` and `text` class names |

#### Example: Sizes

```tsx
import { Avatar } from 'heroui-native';
import { View } from 'react-native';

function SizesExample() {
  return (
    <View className="flex-row items-center justify-center gap-4">
      <Avatar size="sm" alt="Small Avatar">
        <Avatar.Image
          source={{
            uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/blue.jpg',
          }}
        />
        <Avatar.Fallback />
      </Avatar>
      <Avatar size="md" alt="Medium Avatar">
        <Avatar.Image
          source={{
            uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/purple.jpg',
          }}
        />
        <Avatar.Fallback>MD</Avatar.Fallback>
      </Avatar>
      <Avatar size="lg" alt="Large Avatar">
        <Avatar.Image
          source={{
            uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/red.jpg',
          }}
        />
        <Avatar.Fallback>LG</Avatar.Fallback>
      </Avatar>
    </View>
  );
}
```

#### Example: Default Text Fallback with Colors

```tsx
<View className="flex-row items-center justify-center gap-3">
  <Avatar color="accent" alt="Accent">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback>AC</Avatar.Fallback>
  </Avatar>
  <Avatar color="default" alt="Default">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback>DF</Avatar.Fallback>
  </Avatar>
  <Avatar color="success" alt="Success">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback>SC</Avatar.Fallback>
  </Avatar>
  <Avatar color="warning" alt="Warning">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback>WR</Avatar.Fallback>
  </Avatar>
  <Avatar color="danger" alt="Danger">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback>DG</Avatar.Fallback>
  </Avatar>
</View>
```

#### Example: Soft Text Fallback with Colors

```tsx
<View className="flex-row items-center justify-center gap-3">
  <Avatar variant="soft" color="accent" alt="Accent">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback>AC</Avatar.Fallback>
  </Avatar>
  <Avatar variant="soft" color="default" alt="Default">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback>DF</Avatar.Fallback>
  </Avatar>
  <Avatar variant="soft" color="success" alt="Success">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback>SC</Avatar.Fallback>
  </Avatar>
  <Avatar variant="soft" color="warning" alt="Warning">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback>WR</Avatar.Fallback>
  </Avatar>
  <Avatar variant="soft" color="danger" alt="Danger">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback>DG</Avatar.Fallback>
  </Avatar>
</View>
```

#### Example: Default Icon Fallback

```tsx
<View className="flex-row items-center justify-center gap-3">
  <Avatar color="accent" alt="Accent">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback />
  </Avatar>
  <Avatar color="default" alt="Default">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback />
  </Avatar>
  <Avatar color="success" alt="Success">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback />
  </Avatar>
  <Avatar color="warning" alt="Warning">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback />
  </Avatar>
  <Avatar color="danger" alt="Danger">
    <Avatar.Image source={undefined} />
    <Avatar.Fallback />
  </Avatar>
</View>
```

#### Example: Custom Fallback

```tsx
import { LinearGradient } from 'expo-linear-gradient';
import { PersonFillIcon } from './icons/person-fill';

<View className="flex-row items-center justify-center gap-4">
  <Avatar alt="John Doe">
    <Avatar.Fallback>🎉</Avatar.Fallback>
  </Avatar>
  <Avatar alt="Custom">
    <Avatar.Fallback>
      <LinearGradient
        colors={['#ec4899', '#a855f7']}
        start={{ x: 0, y: 0 }}
        end={{ x: 1, y: 1 }}
        style={{
          flex: 1,
          width: '100%',
          height: '100%',
          alignItems: 'center',
          justifyContent: 'center',
        }}
      >
        <Text className="text-white font-medium">GB</Text>
      </LinearGradient>
    </Avatar.Fallback>
  </Avatar>
  <Avatar alt="User">
    <Avatar.Fallback>
      <PersonFillIcon colorClassName="accent-muted" />
    </Avatar.Fallback>
  </Avatar>
</View>
```

#### Example: Avatar Group

```tsx
import { Avatar, cn } from 'heroui-native';

const avatarGroupData = [
  {
    id: 1,
    image: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/blue.jpg',
    name: 'John Doe',
  },
  {
    id: 2,
    image: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/green.jpg',
    name: 'Kate Wilson',
  },
  {
    id: 3,
    image: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/purple.jpg',
    name: 'Emily Chen',
  },
  {
    id: 4,
    image: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/orange.jpg',
    name: 'Michael Brown',
  },
];

function AvatarGroupExample() {
  return (
    <View className="flex-row">
      {avatarGroupData.map((user, index) => (
        <Avatar
          key={user.id}
          className={cn('border-background border-2', index !== 0 && '-ml-4')}
          alt={user.name}
        >
          <Avatar.Image source={{ uri: user.image }} />
          <Avatar.Fallback
            classNames={{
              container: 'bg-warning',
              text: 'text-warning-foreground',
            }}
          >
            {user.name
              .split(' ')
              .map((n) => n[0])
              .join('')}
          </Avatar.Fallback>
        </Avatar>
      ))}
    </View>
  );
}
```

#### Example: Custom Styles

```tsx
import { LinearGradient } from 'expo-linear-gradient';
import { StyleSheet } from 'react-native';

<View className="flex-row items-center justify-center gap-4">
  <Avatar className="h-16 w-16" alt="Extra Large">
    <Avatar.Image
      source={{
        uri: 'https://img.heroui.chat/image/avatar?w=400&h=400&u=3',
      }}
    />
    <Avatar.Fallback>XL</Avatar.Fallback>
  </Avatar>

  <Avatar className="rounded-lg" alt="Square Avatar">
    <Avatar.Image
      source={{
        uri: 'https://img.heroui.chat/image/avatar?w=400&h=400&u=5',
      }}
    />
    <Avatar.Fallback className="rounded-lg">SQ</Avatar.Fallback>
  </Avatar>

  <Avatar className="p-[2.5px]" size="lg" alt="Gradient Border">
    <LinearGradient
      colors={['#ec4899', '#f59e0b']}
      start={{ x: 0, y: 0 }}
      end={{ x: 1, y: 0 }}
      style={StyleSheet.absoluteFill}
    />
    <Avatar.Image
      className="border-[0.5px] border-background rounded-full"
      source={{
        uri: 'https://img.heroui.chat/image/avatar?w=400&h=400&u=20',
      }}
    />
    <Avatar.Fallback className="border-none">GB</Avatar.Fallback>
  </Avatar>

  <View className="relative">
    <Avatar size="lg" alt="Online User">
      <Avatar.Image
        source={{
          uri: 'https://img.heroui.chat/image/avatar?w=400&h=400&u=23',
        }}
      />
      <Avatar.Fallback>ON</Avatar.Fallback>
    </Avatar>
    <View className="absolute bottom-0.5 right-0.5 size-3.5 rounded-full bg-green-500 border border-background" />
  </View>
</View>
```

#### Complete Example Code from avatar.md

```tsx
import { Image } from 'expo-image';
import { LinearGradient } from 'expo-linear-gradient';
import { Avatar, cn } from 'heroui-native';
import { StyleSheet, Text, View } from 'react-native';
import { PersonFillIcon } from './icons/person-fill';

const SizesContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row items-center justify-center gap-4">
        <Avatar size="sm" alt="Small Avatar">
          <Avatar.Image
            source={{
              uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/blue.jpg',
            }}
          />
          <Avatar.Fallback />
        </Avatar>
        <Avatar size="md" alt="Medium Avatar">
          <Avatar.Image
            source={{
              uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/purple.jpg',
            }}
          />
          <Avatar.Fallback>MD</Avatar.Fallback>
        </Avatar>
        <Avatar size="lg" alt="Large Avatar">
          <Avatar.Image
            source={{
              uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/red.jpg',
            }}
          />
          <Avatar.Fallback>LG</Avatar.Fallback>
        </Avatar>
      </View>
    </View>
  );
};

const DefaultTextFallbackContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row items-center justify-center gap-3">
        <Avatar color="accent" alt="Accent">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>AC</Avatar.Fallback>
        </Avatar>
        <Avatar color="default" alt="Default">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>DF</Avatar.Fallback>
        </Avatar>
        <Avatar color="success" alt="Success">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>SC</Avatar.Fallback>
        </Avatar>
        <Avatar color="warning" alt="Warning">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>WR</Avatar.Fallback>
        </Avatar>
        <Avatar color="danger" alt="Danger">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>DG</Avatar.Fallback>
        </Avatar>
      </View>
    </View>
  );
};

const avatarGroupData = [
  {
    id: 1,
    image: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/blue.jpg',
    name: 'John Doe',
  },
  {
    id: 2,
    image: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/green.jpg',
    name: 'Kate Wilson',
  },
  {
    id: 3,
    image: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/purple.jpg',
    name: 'Emily Chen',
  },
  {
    id: 4,
    image: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/orange.jpg',
    name: 'Michael Brown',
  },
];

const AvatarGroupContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row mb-6">
        {avatarGroupData.map((user, index) => (
          <Avatar
            key={user.id}
            className={cn('border-background border-2', index !== 0 && '-ml-4')}
            alt={user.name}
          >
            <Avatar.Image source={{ uri: user.image }} />
            <Avatar.Fallback
              classNames={{
                container: 'bg-warning',
                text: 'text-warning-foreground',
              }}
            >
              {user.name
                .split(' ')
                .map((n) => n[0])
                .join('')}
            </Avatar.Fallback>
          </Avatar>
        ))}
      </View>
      <View className="flex-row">
        {avatarGroupData.slice(0, 3).map((user, index) => (
          <Avatar
            key={user.id}
            className={cn('border-background border-2', index !== 0 && '-ml-4')}
            alt={user.name}
          >
            <Avatar.Image source={{ uri: user.image }} />
            <Avatar.Fallback
              classNames={{
                container: 'bg-warning',
                text: 'text-warning-foreground',
              }}
            >
              {user.name
                .split(' ')
                .map((n) => n[0])
                .join('')}
            </Avatar.Fallback>
          </Avatar>
        ))}
        <Avatar className="border-background border-2 -ml-4" alt="More">
          <Avatar.Fallback>+2</Avatar.Fallback>
        </Avatar>
      </View>
    </View>
  );
};

export default function AvatarExample() {
  return <SizesContent />;
}
```

### Full Example Code (avatar-example.tsx)

```tsx
/* eslint-disable react-native/no-inline-styles */
import { Image } from 'expo-image';
import { LinearGradient } from 'expo-linear-gradient';
import { Avatar, cn } from 'heroui-native';
import { StyleSheet, Text, View } from 'react-native';
import type { UsageVariant } from '../../../components/component-presentation/types';
import { UsageVariantFlatList } from '../../../components/component-presentation/usage-variant-flatlist';
import { PersonFillIcon } from '../../../components/icons/person-fill';

const SizesContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row items-center justify-center gap-4">
        <Avatar size="sm" alt="Small Avatar">
          <Avatar.Image
            source={{
              uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/blue.jpg',
            }}
          />
          <Avatar.Fallback />
        </Avatar>
        <Avatar size="md" alt="Medium Avatar">
          <Avatar.Image
            source={{
              uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/purple.jpg',
            }}
          />
          <Avatar.Fallback>MD</Avatar.Fallback>
        </Avatar>
        <Avatar size="lg" alt="Large Avatar">
          <Avatar.Image
            source={{
              uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/red.jpg',
            }}
          />
          <Avatar.Fallback>LG</Avatar.Fallback>
        </Avatar>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const DefaultTextFallbackContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row items-center justify-center gap-3">
        <Avatar color="accent" alt="Accent">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>AC</Avatar.Fallback>
        </Avatar>
        <Avatar color="default" alt="Default">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>DF</Avatar.Fallback>
        </Avatar>
        <Avatar color="success" alt="Success">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>SC</Avatar.Fallback>
        </Avatar>
        <Avatar color="warning" alt="Warning">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>WR</Avatar.Fallback>
        </Avatar>
        <Avatar color="danger" alt="Danger">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>DG</Avatar.Fallback>
        </Avatar>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const SoftTextFallbackContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row items-center justify-center gap-3">
        <Avatar variant="soft" color="accent" alt="Accent">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>AC</Avatar.Fallback>
        </Avatar>
        <Avatar variant="soft" color="default" alt="Default">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>DF</Avatar.Fallback>
        </Avatar>
        <Avatar variant="soft" color="success" alt="Success">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>SC</Avatar.Fallback>
        </Avatar>
        <Avatar variant="soft" color="warning" alt="Warning">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>WR</Avatar.Fallback>
        </Avatar>
        <Avatar variant="soft" color="danger" alt="Danger">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback>DG</Avatar.Fallback>
        </Avatar>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const DefaultIconFallbackContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row items-center justify-center gap-3">
        <Avatar color="accent" alt="Accent">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback />
        </Avatar>
        <Avatar color="default" alt="Default">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback />
        </Avatar>
        <Avatar color="success" alt="Success">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback />
        </Avatar>
        <Avatar color="warning" alt="Warning">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback />
        </Avatar>
        <Avatar color="danger" alt="Danger">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback />
        </Avatar>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const SoftIconFallbackContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row items-center justify-center gap-3">
        <Avatar variant="soft" color="accent" alt="Accent">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback />
        </Avatar>
        <Avatar variant="soft" color="default" alt="Default">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback />
        </Avatar>
        <Avatar variant="soft" color="success" alt="Success">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback />
        </Avatar>
        <Avatar variant="soft" color="warning" alt="Warning">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback />
        </Avatar>
        <Avatar variant="soft" color="danger" alt="Danger">
          <Avatar.Image source={undefined} />
          <Avatar.Fallback />
        </Avatar>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const CustomFallbackContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row items-center justify-center gap-4">
        <Avatar alt="John Doe">
          <Avatar.Fallback>🎉</Avatar.Fallback>
        </Avatar>
        <Avatar alt="Custom">
          <Avatar.Fallback>
            <LinearGradient
              colors={['#ec4899', '#a855f7']}
              start={{ x: 0, y: 0 }}
              end={{ x: 1, y: 1 }}
              style={{
                flex: 1,
                width: '100%',
                height: '100%',
                alignItems: 'center',
                justifyContent: 'center',
              }}
            >
              <Text className="text-white font-medium">GB</Text>
            </LinearGradient>
          </Avatar.Fallback>
        </Avatar>
        <Avatar alt="User">
          <Avatar.Fallback>
            <PersonFillIcon colorClassName="accent-muted" />
          </Avatar.Fallback>
        </Avatar>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const avatarGroupData = [
  {
    id: 1,
    image:
      'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/blue.jpg',
    name: 'John Doe',
  },
  {
    id: 2,
    image:
      'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/green.jpg',
    name: 'Kate Wilson',
  },
  {
    id: 3,
    image:
      'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/purple.jpg',
    name: 'Emily Chen',
  },
  {
    id: 4,
    image:
      'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/avatars/orange.jpg',
    name: 'Michael Brown',
  },
];

const AvatarGroupContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row mb-6">
        {avatarGroupData.map((user, index) => (
          <Avatar
            key={user.id}
            className={cn('border-background border-2', index !== 0 && '-ml-4')}
            alt={user.name}
          >
            <Avatar.Image source={{ uri: user.image }} />
            <Avatar.Fallback
              classNames={{
                container: 'bg-warning',
                text: 'text-warning-foreground',
              }}
            >
              {user.name
                .split(' ')
                .map((n) => n[0])
                .join('')}
            </Avatar.Fallback>
          </Avatar>
        ))}
      </View>
      <View className="flex-row">
        {avatarGroupData.slice(0, 3).map((user, index) => (
          <Avatar
            key={user.id}
            className={cn('border-background border-2', index !== 0 && '-ml-4')}
            alt={user.name}
          >
            <Avatar.Image source={{ uri: user.image }} />
            <Avatar.Fallback
              classNames={{
                container: 'bg-warning',
                text: 'text-warning-foreground',
              }}
            >
              {user.name
                .split(' ')
                .map((n) => n[0])
                .join('')}
            </Avatar.Fallback>
          </Avatar>
        ))}
        <Avatar className="border-background border-2 -ml-4" alt="More">
          <Avatar.Fallback>+2</Avatar.Fallback>
        </Avatar>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const CustomStylesContent = () => {
  return (
    <View className="flex-1 px-5 items-center justify-center">
      <View className="flex-row items-center justify-center gap-4">
        <Avatar className="h-16 w-16" alt="Extra Large">
          <Avatar.Image
            source={{
              uri: 'https://img.heroui.chat/image/avatar?w=400&h=400&u=3',
            }}
          />
          <Avatar.Fallback>XL</Avatar.Fallback>
        </Avatar>
        <Avatar className="rounded-lg" alt="Square Avatar">
          <Avatar.Image
            source={{
              uri: 'https://img.heroui.chat/image/avatar?w=400&h=400&u=5',
            }}
          />
          <Avatar.Fallback className="rounded-lg">SQ</Avatar.Fallback>
        </Avatar>
        <Avatar className="p-[2.5px]" size="lg" alt="Gradient Border">
          <LinearGradient
            colors={['#ec4899', '#f59e0b']}
            start={{ x: 0, y: 0 }}
            end={{ x: 1, y: 0 }}
            style={StyleSheet.absoluteFill}
          />
          <Avatar.Image
            className="border-[0.5px] border-background rounded-full"
            source={{
              uri: 'https://img.heroui.chat/image/avatar?w=400&h=400&u=20',
            }}
          />
          <Avatar.Fallback className="border-none">GB</Avatar.Fallback>
        </Avatar>
        <View className="relative">
          <Avatar size="lg" alt="Online User">
            <Avatar.Image
              source={{
                uri: 'https://img.heroui.chat/image/avatar?w=400&h=400&u=23',
              }}
              asChild
            >
              <Image
                style={{ width: '100%', height: '100%' }}
                contentFit="cover"
              />
            </Avatar.Image>
            <Avatar.Fallback>ON</Avatar.Fallback>
          </Avatar>
          <View className="absolute bottom-0.5 right-0.5 size-3.5 rounded-full bg-green-500 border border-background" />
        </View>
      </View>
    </View>
  );
};

// ------------------------------------------------------------------------------

const AVATAR_VARIANTS: UsageVariant[] = [
  {
    value: 'sizes',
    label: 'Sizes',
    content: <SizesContent />,
  },
  {
    value: 'default-text-fallback',
    label: 'Default text fallback',
    content: <DefaultTextFallbackContent />,
  },
  {
    value: 'soft-text-fallback',
    label: 'Soft text fallback',
    content: <SoftTextFallbackContent />,
  },
  {
    value: 'default-icon-fallback',
    label: 'Default icon fallback',
    content: <DefaultIconFallbackContent />,
  },
  {
    value: 'soft-icon-fallback',
    label: 'Soft icon fallback',
    content: <SoftIconFallbackContent />,
  },
  {
    value: 'custom-fallback',
    label: 'Custom fallback',
    content: <CustomFallbackContent />,
  },
  {
    value: 'avatar-group',
    label: 'Avatar group',
    content: <AvatarGroupContent />,
  },
  {
    value: 'custom-styles',
    label: 'Custom styles',
    content: <CustomStylesContent />,
  },
];

export default function AvatarScreen() {
  return <UsageVariantFlatList data={AVATAR_VARIANTS} />;
}
```

---

## Chip

Displays a compact element in a capsule shape.

**Source:** chip/chip.tsx
**Styles:** chip/chip.styles.ts

### Import

```tsx
import { Chip } from 'heroui-native';
```

### Anatomy

```tsx
<Chip>
  <Chip.Label>...</Chip.Label>
</Chip>
```

- **Chip**: Main container that displays a compact element
- **Chip.Label**: Text content of the chip

### Usage

#### Basic Usage

The Chip component displays text or custom content in a capsule shape.

```tsx
<Chip>Basic Chip</Chip>
```

#### Sizes

Control the chip size with the `size` prop.

```tsx
<Chip size="sm">Small</Chip>
<Chip size="md">Medium</Chip>
<Chip size="lg">Large</Chip>
```

#### Variants

Choose between different visual styles with the `variant` prop.

```tsx
<Chip variant="primary">Primary</Chip>
<Chip variant="secondary">Secondary</Chip>
<Chip variant="tertiary">Tertiary</Chip>
<Chip variant="soft">Soft</Chip>
```

#### Colors

Apply different color themes with the `color` prop.

```tsx
<Chip color="accent">Accent</Chip>
<Chip color="default">Default</Chip>
<Chip color="success">Success</Chip>
<Chip color="warning">Warning</Chip>
<Chip color="danger">Danger</Chip>
```

#### With Icons

Add icons or custom content alongside text using compound components.

```tsx
<Chip>
  <Icon name="star" size={12} />
  <Chip.Label>Featured</Chip.Label>
</Chip>

<Chip>
  <Chip.Label>Close</Chip.Label>
  <Icon name="close" size={12} />
</Chip>
```

#### Custom Styling

Apply custom styles using className or style props.

```tsx
<Chip className="bg-purple-600 px-6">
  <Chip.Label className="text-white">Custom</Chip.Label>
</Chip>
```

#### Disable All Animations

Disable all animations including children by using the `"disable-all"` value for the `animation` prop.

```tsx
{
  /* Disable all animations including children */
}
<Chip animation="disable-all">No Animations</Chip>;
```

### Docs Example

```tsx
import { Chip } from 'heroui-native';
import { View, Text } from 'react-native';
import { Ionicons } from '@expo/vector-icons';

export default function ChipExample() {
  return (
    <View className="gap-4 p-4">
      <View className="flex-row flex-wrap gap-2">
        <Chip size="sm">Small</Chip>
        <Chip size="md">Medium</Chip>
        <Chip size="lg">Large</Chip>
      </View>

      <View className="flex-row flex-wrap gap-2">
        <Chip variant="primary" color="accent">
          Primary
        </Chip>
        <Chip variant="secondary" color="success">
          <View className="size-1.5 rounded-full bg-success" />
          <Chip.Label>Success</Chip.Label>
        </Chip>
        <Chip variant="tertiary" color="warning">
          <Ionicons name="star" size={12} color="#F59E0B" />
          <Chip.Label>Premium</Chip.Label>
        </Chip>
      </View>

      <View className="flex-row gap-2">
        <Chip variant="secondary">
          <Chip.Label>Remove</Chip.Label>
          <Ionicons name="close" size={14} color="#6B7280" />
        </Chip>
        <Chip className="bg-purple-600">
          <Chip.Label className="text-white font-semibold">Custom</Chip.Label>
        </Chip>
      </View>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/chip.tsx).

### API Reference

#### Chip

| prop                | type                                                          | default     | description                                                                               |
| ------------------- | ------------------------------------------------------------- | ----------- | ----------------------------------------------------------------------------------------- |
| `children`          | `React.ReactNode`                                             | -           | Content to render inside the chip                                                         |
| `size`              | `'sm' \| 'md' \| 'lg'`                                        | `'md'`      | Size of the chip                                                                          |
| `variant`           | `'primary' \| 'secondary' \| 'tertiary' \| 'soft'`            | `'primary'` | Visual variant of the chip                                                                |
| `color`             | `'accent' \| 'default' \| 'success' \| 'warning' \| 'danger'` | `'accent'`  | Color theme of the chip                                                                   |
| `className`         | `string`                                                      | -           | Additional CSS classes to apply                                                           |
| `animation`         | `"disable-all" \| undefined`                                  | `undefined` | Animation configuration. Use `"disable-all"` to disable all animations including children |
| `...PressableProps` | `PressableProps`                                              | -           | All Pressable props are supported                                                         |

#### Chip.Label

| prop           | type              | default | description                            |
| -------------- | ----------------- | ------- | -------------------------------------- |
| `children`     | `React.ReactNode` | -       | Text or content to render as the label |
| `className`    | `string`          | -       | Additional CSS classes to apply        |
| `...TextProps` | `TextProps`       | -       | All standard Text props are supported  |

### Hooks

#### useChip

Hook to access the Chip context values. Returns the chip's size, variant, and color.

```tsx
import { useChip } from 'heroui-native';

const { size, variant, color } = useChip();
```

##### Return Value

| property  | type                                                          | description                |
| --------- | ------------------------------------------------------------- | -------------------------- |
| `size`    | `'sm' \| 'md' \| 'lg'`                                        | Size of the chip           |
| `variant` | `'primary' \| 'secondary' \| 'tertiary' \| 'soft'`            | Visual variant of the chip |
| `color`   | `'accent' \| 'default' \| 'success' \| 'warning' \| 'danger'` | Color theme of the chip    |

**Note:** This hook must be used within a `Chip` component. It will throw an error if called outside of the chip context.

### Examples from chip.md

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { Chip } from 'heroui-native';
```

#### Components

- `Chip` - Root container component
- `Chip.Label` - Chip text label

#### Chip Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'primary' \| 'secondary' \| 'tertiary' \| 'soft'` | `'primary'` | Visual style variant |
| `size` | `'sm' \| 'md' \| 'lg'` | `'md'` | Chip size |
| `color` | `'accent' \| 'default' \| 'success' \| 'warning' \| 'danger'` | `'default'` | Chip color |
| `className` | `string` | - | Additional CSS classes |

#### Example: Sizes

```tsx
import { Chip } from 'heroui-native';
import { View } from 'react-native';

function SizesExample() {
  return (
    <View className="flex-row items-center gap-4">
      <Chip size="sm">Small</Chip>
      <Chip size="md">Medium</Chip>
      <Chip size="lg">Large</Chip>
    </View>
  );
}
```

#### Example: Variants

```tsx
<View className="gap-4">
  <Chip variant="primary" className="self-center">
    Primary
  </Chip>
  <Chip variant="secondary" className="self-center">
    Secondary
  </Chip>
  <Chip variant="tertiary" className="self-center">
    Tertiary
  </Chip>
  <Chip variant="soft" className="self-center">
    Soft
  </Chip>
</View>
```

#### Example: Primary Variant Colors

```tsx
<View className="gap-4">
  <View className="flex-row gap-4 justify-center">
    <Chip variant="primary" color="accent">
      Accent
    </Chip>
    <Chip variant="primary" color="default">
      Default
    </Chip>
    <Chip variant="primary" color="success">
      Success
    </Chip>
  </View>
  <View className="flex-row gap-4 justify-center">
    <Chip variant="primary" color="warning">
      Warning
    </Chip>
    <Chip variant="primary" color="danger">
      Danger
    </Chip>
  </View>
</View>
```

#### Example: Secondary Variant Colors

```tsx
<View className="gap-4">
  <View className="flex-row gap-4 justify-center">
    <Chip variant="secondary" color="accent">
      Accent
    </Chip>
    <Chip variant="secondary" color="default">
      Default
    </Chip>
    <Chip variant="secondary" color="success">
      Success
    </Chip>
  </View>
  <View className="flex-row gap-4 justify-center">
    <Chip variant="secondary" color="warning">
      Warning
    </Chip>
    <Chip variant="secondary" color="danger">
      Danger
    </Chip>
  </View>
</View>
```

#### Example: With Start Content

```tsx
import { PlusIcon, StarFillIcon } from './icons';

<View className="gap-8">
  <View className="flex-row flex-wrap gap-4 justify-center">
    <Chip variant="tertiary">
      <Text className="text-xs">📌</Text>
      <Chip.Label>Featured</Chip.Label>
    </Chip>
    <Chip size="md" variant="secondary" color="success">
      <PlusIcon size={12} colorClassName="accent-success" />
      <Chip.Label>New</Chip.Label>
    </Chip>
    <Chip size="lg" variant="tertiary" color="warning">
      <StarFillIcon size={11} colorClassName="accent-warning" />
      <Chip.Label>Premium</Chip.Label>
    </Chip>
  </View>

  <View className="flex-row flex-wrap gap-4 justify-center">
    <Chip size="md" variant="secondary">
      <View className="size-1.5 mr-1.5 rounded-full bg-accent" />
      <Chip.Label>Information</Chip.Label>
    </Chip>
    <Chip size="md" variant="secondary" color="success">
      <View className="size-1.5 mr-1.5 rounded-full bg-success" />
      <Chip.Label>Completed</Chip.Label>
    </Chip>
    <Chip size="md" variant="secondary" color="warning">
      <View className="size-1.5 mr-1.5 rounded-full bg-warning" />
      <Chip.Label>Pending</Chip.Label>
    </Chip>
    <Chip size="md" variant="secondary" color="danger">
      <View className="size-1.5 mr-1.5 rounded-full bg-danger" />
      <Chip.Label>Failed</Chip.Label>
    </Chip>
  </View>
</View>
```

#### Example: With End Content

```tsx
import { XMarkIcon } from './icons';

<View className="flex-row gap-4 justify-center">
  <Chip size="sm" variant="secondary">
    <Chip.Label className="text-muted">Close</Chip.Label>
    <XMarkIcon size={12} colorClassName="accent-muted" />
  </Chip>
  <Chip size="md" variant="primary" color="danger" className="pr-1.5">
    <Chip.Label>Remove</Chip.Label>
    <XMarkIcon size={14} colorClassName="accent-danger-foreground" />
  </Chip>
  <Chip
    size="lg"
    variant="secondary"
    color="default"
    className="pr-1.5 p-0.5 pl-2 gap-2"
  >
    <Chip.Label className="text-muted">Clear</Chip.Label>
    <View className="rounded-full p-1 bg-muted/20">
      <XMarkIcon size={12} colorClassName="accent-muted" />
    </View>
  </Chip>
</View>
```

#### Example: Custom Styling

```tsx
import { LinearGradient } from 'expo-linear-gradient';
import { StyleSheet } from 'react-native';

<View className="flex-row flex-wrap gap-4 justify-center">
  <Chip className="bg-purple-600 px-6">
    <Chip.Label className="text-background text-base">Custom</Chip.Label>
  </Chip>
  <Chip
    variant="secondary"
    className="border-purple-600 bg-purple-100 rounded-sm"
  >
    <Chip.Label className="text-purple-800">Purple</Chip.Label>
  </Chip>

  <Chip>
    <LinearGradient
      colors={['#ec4899', '#8b5cf6']}
      start={{ x: 0, y: 0 }}
      end={{ x: 1, y: 0 }}
      style={StyleSheet.absoluteFill}
    />
    <Chip.Label className="text-white font-semibold">Gradient</Chip.Label>
  </Chip>

  <Chip size="lg">
    <LinearGradient
      colors={['#10b981', '#3b82f6']}
      start={{ x: 0, y: 0 }}
      end={{ x: 1, y: 1 }}
      style={StyleSheet.absoluteFill}
    />
    <Chip.Label className="text-white font-bold">Premium</Chip.Label>
  </Chip>

  <Chip>
    <LinearGradient
      colors={['#f59e0b', '#ef4444']}
      start={{ x: 0, y: 0.5 }}
      end={{ x: 1, y: 0.5 }}
      style={StyleSheet.absoluteFill}
    />
    <Chip.Label className="text-white font-semibold">Hot</Chip.Label>
  </Chip>
</View>
```

#### Complete Example Code

```tsx
import { LinearGradient } from 'expo-linear-gradient';
import { Chip } from 'heroui-native';
import { StyleSheet, View, Text } from 'react-native';
import { PlusIcon, StarFillIcon, XMarkIcon } from './icons';

const SizesContent = () => {
  return (
    <View className="flex-row items-center gap-4">
      <Chip size="sm">Small</Chip>
      <Chip size="md">Medium</Chip>
      <Chip size="lg">Large</Chip>
    </View>
  );
};

const VariantsContent = () => {
  return (
    <View className="gap-4">
      <Chip variant="primary" className="self-center">
        Primary
      </Chip>
      <Chip variant="secondary" className="self-center">
        Secondary
      </Chip>
      <Chip variant="tertiary" className="self-center">
        Tertiary
      </Chip>
      <Chip variant="soft" className="self-center">
        Soft
      </Chip>
    </View>
  );
};

const WithStartContentContent = () => {
  return (
    <View className="gap-8">
      <View className="flex-row flex-wrap gap-4 justify-center">
        <Chip variant="tertiary">
          <Text className="text-xs">📌</Text>
          <Chip.Label>Featured</Chip.Label>
        </Chip>
        <Chip size="md" variant="secondary" color="success">
          <PlusIcon size={12} colorClassName="accent-success" />
          <Chip.Label>New</Chip.Label>
        </Chip>
        <Chip size="lg" variant="tertiary" color="warning">
          <StarFillIcon size={11} colorClassName="accent-warning" />
          <Chip.Label>Premium</Chip.Label>
        </Chip>
      </View>
    </View>
  );
};

const CustomStylingContent = () => {
  return (
    <View className="flex-row flex-wrap gap-4 justify-center">
      <Chip className="bg-purple-600 px-6">
        <Chip.Label className="text-background text-base">Custom</Chip.Label>
      </Chip>
      <Chip
        variant="secondary"
        className="border-purple-600 bg-purple-100 rounded-sm"
      >
        <Chip.Label className="text-purple-800">Purple</Chip.Label>
      </Chip>

      <Chip>
        <LinearGradient
          colors={['#ec4899', '#8b5cf6']}
          start={{ x: 0, y: 0 }}
          end={{ x: 1, y: 0 }}
          style={StyleSheet.absoluteFill}
        />
        <Chip.Label className="text-white font-semibold">Gradient</Chip.Label>
      </Chip>
    </View>
  );
};

export default function ChipExample() {
  return <VariantsContent />;
}
```

---

## ScrollShadow

Adds dynamic gradient shadows to scrollable content based on scroll position and overflow.

**Source:** scroll-shadow/scroll-shadow.tsx
**Styles:** scroll-shadow/scroll-shadow.styles.ts

### Import

```tsx
import { ScrollShadow } from 'heroui-native';
```

### Anatomy

```tsx
<ScrollShadow LinearGradientComponent={LinearGradient}>
  <ScrollView>...</ScrollView>
</ScrollShadow>
```

- **ScrollShadow**: Main container that wraps scrollable components and adds dynamic gradient shadows at the edges based on scroll position and content overflow. Automatically detects scroll orientation (horizontal/vertical) and manages shadow visibility.
- **LinearGradientComponent**: Required prop that accepts a LinearGradient component from compatible libraries (expo-linear-gradient, react-native-linear-gradient, etc.) to render the gradient shadows.

### Usage

#### Basic Usage

Wrap any scrollable component to automatically add edge shadows.

```tsx
<ScrollShadow LinearGradientComponent={LinearGradient}>
  <ScrollView>...</ScrollView>
</ScrollShadow>
```

#### Horizontal Scrolling

The component auto-detects horizontal scrolling from the child's `horizontal` prop.

```tsx
<ScrollShadow LinearGradientComponent={LinearGradient}>
  <FlatList horizontal data={data} renderItem={...} />
</ScrollShadow>
```

#### Custom Shadow Size

Control the gradient shadow height/width with the `size` prop.

```tsx
<ScrollShadow size={100} LinearGradientComponent={LinearGradient}>
  <ScrollView>...</ScrollView>
</ScrollShadow>
```

#### Visibility Control

Specify which shadows to display using the `visibility` prop.

```tsx
<ScrollShadow visibility="top" LinearGradientComponent={LinearGradient}>
  <ScrollView>...</ScrollView>
</ScrollShadow>

<ScrollShadow visibility="bottom" LinearGradientComponent={LinearGradient}>
  <ScrollView>...</ScrollView>
</ScrollShadow>

<ScrollShadow visibility="none" LinearGradientComponent={LinearGradient}>
  <ScrollView>...</ScrollView>
</ScrollShadow>
```

#### Custom Shadow Color

Override the default shadow color which uses the theme's background.

```tsx
<ScrollShadow color="#ffffff" LinearGradientComponent={LinearGradient}>
  <ScrollView>...</ScrollView>
</ScrollShadow>
```

#### With Custom Scroll Handler

**Important:** ScrollShadow internally converts the child to a Reanimated animated component. If you need to use the `onScroll` prop, you must use `useAnimatedScrollHandler` from react-native-reanimated.

```tsx
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useAnimatedScrollHandler } from 'react-native-reanimated';

const scrollHandler = useAnimatedScrollHandler({
  onScroll: (event) => {
    console.log(event.contentOffset.y);
  },
});

<ScrollShadow LinearGradientComponent={LinearGradient}>
  <Animated.ScrollView onScroll={scrollHandler}>...</Animated.ScrollView>
</ScrollShadow>;
```

### Docs Example

```tsx
import { ScrollShadow, Surface } from 'heroui-native';
import { LinearGradient } from 'expo-linear-gradient';
import { FlatList, ScrollView, Text, View } from 'react-native';

export default function ScrollShadowExample() {
  const horizontalData = Array.from({ length: 10 }, (_, i) => ({
    id: i,
    title: `Card ${i + 1}`,
  }));

  return (
    <View className="flex-1 bg-background">
      <Text className="px-5 py-3 text-lg font-semibold">Horizontal List</Text>
      <ScrollShadow LinearGradientComponent={LinearGradient}>
        <FlatList
          data={horizontalData}
          horizontal
          renderItem={({ item }) => (
            <Surface variant="2" className="w-32 h-24 justify-center px-4">
              <Text>{item.title}</Text>
            </Surface>
          )}
          showsHorizontalScrollIndicator={false}
          contentContainerClassName="p-5 gap-4"
        />
      </ScrollShadow>

      <Text className="px-5 py-3 text-lg font-semibold">Vertical Content</Text>
      <ScrollShadow
        size={80}
        className="h-48"
        LinearGradientComponent={LinearGradient}
      >
        <ScrollView
          contentContainerClassName="p-5"
          showsVerticalScrollIndicator={false}
        >
          <Text className="mb-4 text-2xl font-bold">Long Content</Text>
          <Text className="mb-4 text-base leading-6">
            Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
            eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim
            ad minim veniam, quis nostrud exercitation ullamco laboris.
          </Text>
          <Text className="mb-4 text-base leading-6">
            Sed ut perspiciatis unde omnis iste natus error sit voluptatem
            accusantium doloremque laudantium, totam rem aperiam, eaque ipsa
            quae ab illo inventore veritatis et quasi architecto beatae vitae.
          </Text>
        </ScrollView>
      </ScrollShadow>
    </View>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/scroll-shadow.tsx).

### API Reference

#### ScrollShadow

| prop                      | type                                                                   | default      | description                                                                                                     |
| ------------------------- | ---------------------------------------------------------------------- | ------------ | --------------------------------------------------------------------------------------------------------------- |
| `children`                | `React.ReactElement`                                                   | -            | The scrollable component to enhance with shadows. Must be a single React element (ScrollView, FlatList, etc.)   |
| `LinearGradientComponent` | `ComponentType<LinearGradientProps>`                                   | **required** | LinearGradient component from any compatible library (expo-linear-gradient, react-native-linear-gradient, etc.) |
| `size`                    | `number`                                                               | `50`         | Size (height/width) of the gradient shadow in pixels                                                            |
| `orientation`             | `'horizontal' \| 'vertical'`                                           | auto-detect  | Orientation of the scroll shadow. If not provided, will auto-detect from child's `horizontal` prop              |
| `visibility`              | `'auto' \| 'top' \| 'bottom' \| 'left' \| 'right' \| 'both' \| 'none'` | `'auto'`     | Visibility mode for the shadows. 'auto' shows shadows based on scroll position and content overflow             |
| `color`                   | `string`                                                               | theme color  | Custom color for the gradient shadow. If not provided, uses the theme's background color                        |
| `isEnabled`               | `boolean`                                                              | `true`       | Whether the shadow effect is enabled                                                                            |
| `animation`               | `ScrollShadowRootAnimation`                                            | -            | Animation configuration                                                                                         |
| `className`               | `string`                                                               | -            | Additional CSS classes to apply to the container                                                                |
| `...ViewProps`            | `ViewProps`                                                            | -            | All standard React Native View props are supported                                                              |

##### ScrollShadowRootAnimation

Animation configuration for ScrollShadow component. Can be:

- `false` or `"disabled"`: Disable only root animations
- `"disable-all"`: Disable all animations including children
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop            | type                                     | default  | description                                                                         |
| --------------- | ---------------------------------------- | -------- | ----------------------------------------------------------------------------------- |
| `state`         | `'disabled' \| 'disable-all' \| boolean` | -        | Disable animations while customizing properties                                     |
| `opacity.value` | `[number, number]`                       | `[0, 1]` | `Opacity values [initial, active].` `For bottom/right shadow, this is reversed`     |

#### LinearGradientProps

The `LinearGradientComponent` prop expects a component that accepts these props:

| prop        | type                              | description                                                        |
| ----------- | --------------------------------- | ------------------------------------------------------------------ |
| `colors`    | `any`                             | Array of colors for the gradient                                   |
| `locations` | `any` (optional)                  | Array of numbers defining the location of each gradient color stop |
| `start`     | `any` (optional)                  | Start point of the gradient (e.g., `{ x: 0, y: 0 }`)               |
| `end`       | `any` (optional)                  | End point of the gradient (e.g., `{ x: 1, y: 0 }`)                 |
| `style`     | `StyleProp<ViewStyle>` (optional) | Style to apply to the gradient view                                |

### Special Notes

**Important:** ScrollShadow internally converts the child to a Reanimated animated component. If you need to use the `onScroll` prop on your scrollable component, you must use `useAnimatedScrollHandler` from react-native-reanimated instead of the standard `onScroll` prop.

### Examples from scroll-shadow.md

#### Installation

```bash
npm install heroui-native
```

#### Import

```tsx
import { ScrollShadow } from 'heroui-native';
```

#### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `color` | `string` | - | Shadow color |
| `className` | `string` | - | Additional CSS classes |

#### Example: Basic Scroll Shadow

```tsx
import { ScrollShadow } from 'heroui-native';
import { LinearGradient } from 'expo-linear-gradient';
import { View, Text, ScrollView } from 'react-native';

function BasicScrollShadowExample() {
  return (
    <View className="p-5 h-64">
      <ScrollShadow
        LinearGradientComponent={LinearGradient}
        color="#ffffff"
      >
        <ScrollView>
          <Text className="text-foreground">
            Lorem ipsum dolor sit amet...
            {/* Long content */}
          </Text>
        </ScrollView>
      </ScrollShadow>
    </View>
  );
}
```

#### Complete Example Code

```tsx
import { ScrollShadow } from 'heroui-native';
import { LinearGradient } from 'expo-linear-gradient';
import { View, Text, ScrollView } from 'react-native';

export default function ScrollShadowExample() {
  return (
    <View className="flex-1 p-5">
      <ScrollShadow
        LinearGradientComponent={LinearGradient}
        color="#ffffff"
        className="h-64"
      >
        <ScrollView>
          <Text className="text-foreground p-4">
            Lorem ipsum dolor sit amet, consectetur adipiscing elit.
            Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
            {/* More content */}
          </Text>
        </ScrollView>
      </ScrollShadow>
    </View>
  );
}
```

---

## PressableFeedback

Container component that provides visual feedback for press interactions with automatic scale animation.

**Source:** pressable-feedback/pressable-feedback.tsx
**Styles:** pressable-feedback/pressable-feedback.styles.ts

### Import

```tsx
import { PressableFeedback } from 'heroui-native';
```

### Anatomy

```tsx
<PressableFeedback>
  <PressableFeedback.Highlight />
  <PressableFeedback.Ripple />
  <PressableFeedback.Scale>...</PressableFeedback.Scale>
</PressableFeedback>
```

- **PressableFeedback**: Pressable container with built-in scale animation. Manages press state and container dimensions, providing them to child compound parts via context. Use `animation={false}` to disable the built-in scale when using `PressableFeedback.Scale` instead.
- **PressableFeedback.Scale**: Scale animation wrapper for applying scale to a specific child element. Use this instead of the root's built-in scale when you need control over which element scales or need to apply `className` / `style` to the scale wrapper.
- **PressableFeedback.Highlight**: Highlight overlay for iOS-style press feedback. Renders an absolute-positioned layer that fades in on press.
- **PressableFeedback.Ripple**: Ripple overlay for Android-style press feedback. Renders a radial gradient circle that expands from the touch point.

### Usage

#### Basic

PressableFeedback provides press-down scale feedback out of the box. This is the recommended way to use it in most cases.

```tsx
<PressableFeedback>...</PressableFeedback>
```

#### With Highlight

Add a highlight overlay for iOS-style feedback effect alongside the built-in scale.

```tsx
<PressableFeedback>
  <PressableFeedback.Highlight />
  ...
</PressableFeedback>
```

#### With Ripple

Add a ripple overlay for Android-style feedback effect alongside the built-in scale.

```tsx
<PressableFeedback>
  <PressableFeedback.Ripple />
  ...
</PressableFeedback>
```

#### Custom Scale Animation

Customize the built-in scale animation via the `animation.scale` prop. Accepts `value`, `timingConfig`, and `ignoreScaleCoefficient`.

```tsx
<PressableFeedback
  animation={{
    scale: {
      value: 0.9,
      timingConfig: { duration: 150 },
      ignoreScaleCoefficient: true,
    },
  }}
>
  ...
</PressableFeedback>
```

#### Custom Highlight Animation

Configure highlight overlay opacity and background color.

```tsx
<PressableFeedback>
  <PressableFeedback.Highlight
    animation={{
      backgroundColor: { value: '#3b82f6' },
      opacity: { value: [0, 0.2] },
    }}
  />
  ...
</PressableFeedback>
```

#### Custom Ripple Animation

Configure ripple effect color, opacity, and duration.

```tsx
<PressableFeedback>
  <PressableFeedback.Ripple
    animation={{
      backgroundColor: { value: '#ec4899' },
      opacity: { value: [0, 0.1, 0] },
      progress: { baseDuration: 600 },
    }}
  />
  ...
</PressableFeedback>
```

#### Scale on a Specific Child (PressableFeedback.Scale)

When you need to apply the scale animation to a specific element inside the container rather than the root itself, disable the root's built-in scale with `animation={false}` and use the `PressableFeedback.Scale` compound part. This gives you full control over which element scales and lets you apply `className` / `style` directly to the scale wrapper.

```tsx
<PressableFeedback animation={false}>
  <PressableFeedback.Scale>...</PressableFeedback.Scale>
</PressableFeedback>
```

You can combine it with Highlight or Ripple inside the Scale wrapper:

```tsx
<PressableFeedback animation={false}>
  <PressableFeedback.Scale>
    <PressableFeedback.Highlight />
    ...
  </PressableFeedback.Scale>
</PressableFeedback>
```

#### Disable All Animations

Set `animation="disable-all"` on the root to cascade-disable all animations including the built-in scale and any child compound parts (Scale, Highlight, Ripple).

```tsx
<PressableFeedback animation="disable-all">...</PressableFeedback>
```

You can also disable all animations while keeping a scale config (e.g. for toggling at runtime):

```tsx
<PressableFeedback animation={{ scale: { value: 0.97 }, state: 'disable-all' }}>
  ...
</PressableFeedback>
```

### Docs Example

```tsx
import { PressableFeedback, Card, Button } from 'heroui-native';
import { Image } from 'expo-image';
import { LinearGradient } from 'expo-linear-gradient';
import { StyleSheet, View, Text } from 'react-native';

export default function PressableFeedbackExample() {
  return (
    <PressableFeedback className="w-full aspect-square overflow-auto">
      <Card className="flex-1">
        <Image
          source={{
            uri: 'https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/neo2.jpeg',
          }}
          style={StyleSheet.absoluteFill}
          contentFit="cover"
        />
        <LinearGradient
          colors={['rgba(0,0,0,0.1)', 'rgba(0,0,0,0.4)']}
          style={StyleSheet.absoluteFill}
        />
        <PressableFeedback.Ripple
          animation={{
            backgroundColor: { value: 'white' },
            opacity: { value: [0, 0.3, 0] },
          }}
        />
        <View className="flex-1 gap-4" pointerEvents="box-none">
          <Card.Body className="flex-1" pointerEvents="none">
            <Card.Title className="text-base text-zinc-50 uppercase mb-0.5">
              Neo
            </Card.Title>
            <Card.Description className="text-zinc-50 font-medium text-base">
              Home robot
            </Card.Description>
          </Card.Body>
          <Card.Footer className="gap-3">
            <View className="flex-row items-center justify-between">
              <View pointerEvents="none">
                <Text className="text-base text-white">Available soon</Text>
                <Text className="text-base text-zinc-300">Get notified</Text>
              </View>
              <Button size="sm" className="bg-white">
                <Button.Label className="text-black">Notify me</Button.Label>
              </Button>
            </View>
          </Card.Footer>
        </View>
      </Card>
    </PressableFeedback>
  );
}
```

You can find more examples in the [GitHub repository](https://github.com/heroui-inc/heroui-native/blob/rc/example/src/app/(home)/components/pressable-feedback.tsx).

### API Reference

#### PressableFeedback

| prop                    | type                             | default | description                                                                                                 |
| ----------------------- | -------------------------------- | ------- | ----------------------------------------------------------------------------------------------------------- |
| `children`              | `React.ReactNode`                | -       | Content to be wrapped with press feedback                                                                   |
| `isDisabled`            | `boolean`                        | `false` | Whether the pressable component is disabled                                                                 |
| `className`             | `string`                         | -       | Additional CSS classes                                                                                      |
| `animation`             | `PressableFeedbackRootAnimation` | -       | Customize scale via `{ scale: ... }`, `false` to disable root scale, `'disable-all'` to cascade-disable all |
| `isAnimatedStyleActive` | `boolean`                        | `true`  | Whether the root's built-in animated styles are active                                                      |
| `...rest`               | `AnimatedProps<PressableProps>`  | -       | All Reanimated Animated Pressable props are supported                                                       |

##### PressableFeedbackRootAnimation

The root animation prop supports the standard `AnimationRoot` control flow:

- `true` or `undefined`: Use the default built-in scale animation
- `false` or `"disabled"`: Disable the root's built-in scale (use this when applying scale via `PressableFeedback.Scale` instead)
- `"disable-all"`: Cascade-disable all animations including the built-in scale and children (Scale, Highlight, Ripple)
- `object`: Custom configuration for the built-in scale

| prop    | type                                     | default | description                                                                     |
| ------- | ---------------------------------------- | ------- | ------------------------------------------------------------------------------- |
| `scale` | `PressableFeedbackScaleAnimation`        | -       | Customize the built-in scale animation (value, timingConfig, etc.)              |
| `state` | `'disabled' \| 'disable-all' \| boolean` | -       | Control animation state while keeping configuration (e.g. for runtime toggling) |

#### PressableFeedback.Scale

Use this compound part when you need to apply scale to a specific child element inside the container, instead of scaling the root itself. Set `animation={false}` on the root to disable its built-in scale when using this component.

| prop                    | type                              | default | description                                                  |
| ----------------------- | --------------------------------- | ------- | ------------------------------------------------------------ |
| `className`             | `string`                          | -       | Additional CSS classes                                       |
| `animation`             | `PressableFeedbackScaleAnimation` | -       | Animation configuration for scale effect                     |
| `isAnimatedStyleActive` | `boolean`                         | `true`  | Whether animated styles (react-native-reanimated) are active |
| `style`                 | `ViewStyle`                       | -       | Additional styles                                            |
| `...AnimatedProps`      | `AnimatedProps<ViewProps>`        | -       | All Reanimated Animated View props are supported             |

##### PressableFeedbackScaleAnimation

Animation configuration for scale effect. Can be:

- `false` or `"disabled"`: Disable scale animation
- `true` or `undefined`: Use default scale animation
- `object`: Custom scale configuration

| prop                     | type                    | default                                              | description                                                                |
| ------------------------ | ----------------------- | ---------------------------------------------------- | -------------------------------------------------------------------------- |
| `state`                  | `'disabled' \| boolean` | -                                                    | Disable animations while customizing properties                            |
| `value`                  | `number`                | `0.985`                                              | Scale value when pressed (automatically adjusted based on container width) |
| `timingConfig`           | `WithTimingConfig`      | `{ duration: 300, easing: Easing.out(Easing.ease) }` | Animation timing configuration                                             |
| `ignoreScaleCoefficient` | `boolean`               | `false`                                              | Ignore automatic scale coefficient and use the scale value directly        |

#### PressableFeedback.Highlight

| prop                    | type                                  | default | description                                                  |
| ----------------------- | ------------------------------------- | ------- | ------------------------------------------------------------ |
| `className`             | `string`                              | -       | Additional CSS classes                                       |
| `animation`             | `PressableFeedbackHighlightAnimation` | -       | Animation configuration for highlight overlay                |
| `isAnimatedStyleActive` | `boolean`                             | `true`  | Whether animated styles (react-native-reanimated) are active |
| `style`                 | `ViewStyle`                           | -       | Additional styles                                            |
| `...AnimatedProps`      | `AnimatedProps<ViewProps>`            | -       | All Reanimated Animated View props are supported             |

##### PressableFeedbackHighlightAnimation

Animation configuration for highlight overlay. Can be:

- `false` or `"disabled"`: Disable highlight animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                    | type                    | default             | description                                     |
| ----------------------- | ----------------------- | ------------------- | ----------------------------------------------- |
| `state`                 | `'disabled' \| boolean` | -                   | Disable animations while customizing properties |
| `opacity.value`         | `[number, number]`      | `[0, 0.1]`          | Opacity values [unpressed, pressed]             |
| `opacity.timingConfig`  | `WithTimingConfig`      | `{ duration: 200 }` | Animation timing configuration                  |
| `backgroundColor.value` | `string`                | Theme-aware gray    | Background color of highlight overlay           |

#### PressableFeedback.Ripple

| prop                    | type                                      | default | description                                                  |
| ----------------------- | ----------------------------------------- | ------- | ------------------------------------------------------------ |
| `className`             | `string`                                  | -       | Additional CSS classes for container slot                    |
| `classNames`            | `ElementSlots<RippleSlots>`               | -       | Additional CSS classes for slots (container, ripple)         |
| `styles`                | `Partial<Record<RippleSlots, ViewStyle>>` | -       | Styles for different parts of the ripple overlay             |
| `animation`             | `PressableFeedbackRippleAnimation`        | -       | Animation configuration for ripple overlay                   |
| `isAnimatedStyleActive` | `boolean`                                 | `true`  | Whether animated styles (react-native-reanimated) are active |
| `...ViewProps`          | `Omit<ViewProps, 'style'>`                | -       | All View props except style are supported                    |

##### `styles`

| prop        | type        | description                   |
| ----------- | ----------- | ----------------------------- |
| `container` | `ViewStyle` | Styles for the container slot |
| `ripple`    | `ViewStyle` | Styles for the ripple slot    |

##### PressableFeedbackRippleAnimation

Animation configuration for ripple overlay. Can be:

- `false` or `"disabled"`: Disable ripple animations
- `true` or `undefined`: Use default animations
- `object`: Custom animation configuration

| prop                                 | type                       | default                 | description                                                                  |
| ------------------------------------ | -------------------------- | ----------------------- | ---------------------------------------------------------------------------- |
| `state`                              | `'disabled' \| boolean`    | -                       | Disable animations while customizing properties                              |
| `backgroundColor.value`              | `string`                   | Computed based on theme | Background color of ripple effect                                            |
| `progress.baseDuration`              | `number`                   | `1000`                  | Base duration for ripple progress (automatically adjusted based on diagonal) |
| `progress.minBaseDuration`           | `number`                   | `750`                   | Minimum base duration for the ripple progress animation                      |
| `progress.ignoreDurationCoefficient` | `boolean`                  | `false`                 | Ignore automatic duration coefficient and use base duration directly         |
| `opacity.value`                      | `[number, number, number]` | `[0, 0.1, 0]`           | Opacity values [start, peak, end] for ripple animation                       |
| `opacity.timingConfig`               | `WithTimingConfig`         | `{ duration: 200 }`     | Animation timing configuration                                               |
| `scale.value`                        | `[number, number, number]` | `[0, 1, 1]`             | Scale values [start, peak, end] for ripple animation                         |
| `scale.timingConfig`                 | `WithTimingConfig`         | `{ duration: 200 }`     | Animation timing configuration                                               |

##### `ElementSlots<RippleSlots>`

Additional CSS classes for ripple slots:

| slot        | description                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------------------- |
| `container` | Outer container slot (`absolute inset-0`) - styles can be fully customized                                          |
| `ripple`    | Inner ripple slot (`absolute top-0 left-0 rounded-full`) - has animated properties that cannot be set via className |
