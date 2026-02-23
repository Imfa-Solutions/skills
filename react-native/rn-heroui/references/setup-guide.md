# Setup & Architecture Guide

## Table of Contents
- [Quick Start](#quick-start)
- [Provider](#provider)
- [Portal](#portal)
- [Composition](#composition)
- [Design Principles](#design-principles)

---

## Quick Start

---
title: Quick Start
description: Get started with HeroUI Native in minutes
icon: rocket
---

### Getting Started

#### 1. Install HeroUI Native

<Tabs items={["npm", "pnpm", "yarn", "bun"]}>
  <Tab value="npm">
    ```bash
    npm install heroui-native
    ```
  </Tab>
  <Tab value="pnpm">
    ```bash
    pnpm add heroui-native
    ```
  </Tab>
  <Tab value="yarn">
    ```bash
    yarn add heroui-native
    ```
  </Tab>
  <Tab value="bun">
    ```bash
    bun add heroui-native
    ```
  </Tab>
</Tabs>

#### 2. Install Mandatory Peer Dependencies

<Tabs items={["npm", "pnpm", "yarn", "bun"]}>
  <Tab value="npm">
    ```bash
    npm install react-native-screens@~4.16.0 react-native-reanimated@~4.1.1 react-native-gesture-handler@^2.28.0 react-native-worklets@0.5.1 react-native-safe-area-context@~5.6.0 react-native-svg@15.12.1 tailwind-variants@^3.2.2 tailwind-merge@^3.4.0 @gorhom/bottom-sheet@^5
    ```
  </Tab>
  <Tab value="pnpm">
    ```bash
    pnpm add react-native-screens@~4.16.0 react-native-reanimated@~4.1.1 react-native-gesture-handler@^2.28.0 react-native-worklets@0.5.1 react-native-safe-area-context@~5.6.0 react-native-svg@15.12.1 tailwind-variants@^3.2.2 tailwind-merge@^3.4.0 @gorhom/bottom-sheet@^5
    ```
  </Tab>
  <Tab value="yarn">
    ```bash
    yarn add react-native-screens@~4.16.0 react-native-reanimated@~4.1.1 react-native-gesture-handler@^2.28.0 react-native-worklets@0.5.1 react-native-safe-area-context@~5.6.0 react-native-svg@15.12.1 tailwind-variants@^3.2.2 tailwind-merge@^3.4.0 @gorhom/bottom-sheet@^5
    ```
  </Tab>
  <Tab value="bun">
    ```bash
    bun add react-native-screens@~4.16.0 react-native-reanimated@~4.1.1 react-native-gesture-handler@^2.28.0 react-native-worklets@0.5.1 react-native-safe-area-context@~5.6.0 react-native-svg@15.12.1 tailwind-variants@^3.2.2 tailwind-merge@^3.4.0 @gorhom/bottom-sheet@^5
    ```
  </Tab>
</Tabs>

<Callout type="warning">
  It's recommended to use the exact versions specified above to avoid compatibility issues. Version mismatches may cause unexpected bugs.
</Callout>

#### 3. Set Up Uniwind

Follow the [Uniwind installation guide](https://docs.uniwind.dev/quickstart) to set up Tailwind CSS for React Native.

If you're migrating from NativeWind, see the [migration guide](https://docs.uniwind.dev/migration-from-nativewind).

#### 4. Configure global.css

Inside your `global.css` file add the following imports:

```css
@import 'tailwindcss';
@import 'uniwind';

@import 'heroui-native/styles';

/* Path to the heroui-native lib inside node_modules relative to global.css */
/* Examples:
 *   - If global.css is at project root: ./node_modules/heroui-native/lib
 *   - If global.css is in app/: ../node_modules/heroui-native/lib
 *   - If global.css is in src/styles/: ../../node_modules/heroui-native/lib
 */
@source './node_modules/heroui-native/lib';
```

#### 5. Wrap Your App with Provider

Wrap your application with `HeroUINativeProvider`. You must wrap it with `GestureHandlerRootView`:

```tsx
import { HeroUINativeProvider } from 'heroui-native';
import { GestureHandlerRootView } from 'react-native-gesture-handler';

export default function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <HeroUINativeProvider>{/* Your app content */}</HeroUINativeProvider>
    </GestureHandlerRootView>
  );
}
```

> **Note**: For advanced configuration options including text props, animation settings, and toast configuration, see the [Provider documentation](/docs/native/getting-started/provider).

#### 6. Use Your First Component

```tsx
import { Button } from 'heroui-native';
import { View } from 'react-native';

export default function MyComponent() {
  return (
    <View className="flex-1 justify-center items-center bg-background">
      <Button onPress={() => console.log('Pressed!')}>Get Started</Button>
    </View>
  );
}
```

#### 7. Reduce Bundle Size with Granular Exports

If you want to reduce bundle size and import only the components you need, our library provides granular exports for each component:

```tsx
// Granular imports - use when you need only a few components
import { HeroUINativeProvider } from "heroui-native/provider";
import { Button } from "heroui-native/button";
import { Card } from "heroui-native/card";

// General import - imports the whole library, use when you're using many components
import { Button, Card } from "heroui-native";
```

Granular imports are ideal when you only need a few components, as they help keep your bundle size smaller. General imports from `heroui-native` will include the entire library, which is convenient when you're using many components throughout your app.

**Available granular exports:**

- `heroui-native/provider` - Provider component
- `heroui-native/provider-raw` - Lightweight provider (keeps bare minimum to start)
- `heroui-native/[component-name]` - Individual components
- `heroui-native/portal` - Portal utilities
- `heroui-native/toast` - Toast provider and utilities
- `heroui-native/utils` - Utility functions
- `heroui-native/hooks` - Custom hooks

<Callout type="warning">
  **Important**: To keep the bundle size under control, you must follow the pattern with granular imports consistently. Even one general import from `heroui-native` will break this optimization strategy.
</Callout>

> **Tip**: For even more control over your bundle, consider using [`HeroUINativeProviderRaw`](/docs/native/getting-started/provider#raw-provider) -- a lightweight provider that excludes `ToastProvider` and `PortalHost`, making dependencies like `react-native-screens`, `@gorhom/bottom-sheet`, and `react-native-svg` fully optional.

### What's Next?

- [HeroUI Native Provider](/docs/native/getting-started/provider)
- [Styling Guide](/docs/native/getting-started/styling)
- [Theming Documentation](/docs/native/getting-started/theming)

### Running on Web (Expo)

<Callout type="warning">
  HeroUI Native is currently not recommended for web use. We are focusing on mobile platforms (iOS and Android) at this time. For web development, please use [HeroUI React](/docs/react/getting-started/quick-start) instead.
</Callout>

---

## Provider

---
title: Provider
description: Configure HeroUI Native provider with text, animation, and toast settings
icon: chevrons-expand-horizontal
---

The `HeroUINativeProvider` is the root provider component that configures and initializes HeroUI Native in your React Native application. It provides global configuration and portal management for your application.

### Overview

The provider serves as the main entry point for HeroUI Native, wrapping your application with essential contexts and configurations:

- **Safe Area Insets**: Automatically handles safe area insets updates via `SafeAreaListener` and syncs them with Uniwind for use in Tailwind classes (e.g., `pb-safe-offset-3`)
- **Text Configuration**: Global text component settings for consistency across all HeroUI components
- **Animation Configuration**: Global animation control to disable all animations across the application
- **Toast Configuration**: Global toast system configuration including insets, default props, and wrapper components
- **Portal Management**: Handles overlays, modals, and other components that render on top of the app hierarchy

### Basic Setup

Wrap your application root with the provider:

```tsx
import { HeroUINativeProvider } from 'heroui-native';
import { GestureHandlerRootView } from 'react-native-gesture-handler';

export default function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <HeroUINativeProvider>{/* Your app content */}</HeroUINativeProvider>
    </GestureHandlerRootView>
  );
}
```

### Configuration Options

The provider accepts a `config` prop with the following options:

#### Text Component Configuration

Global settings for all Text components within HeroUI Native. These props are carefully selected to include only those that make sense to configure globally across all Text components in the application:

```tsx
import { HeroUINativeProvider } from 'heroui-native';
import type { HeroUINativeConfig } from 'heroui-native';

const config: HeroUINativeConfig = {
  textProps: {
    // Disable font scaling for accessibility
    allowFontScaling: false,

    // Auto-adjust font size to fit container
    adjustsFontSizeToFit: false,

    // Maximum font size multiplier when scaling
    maxFontSizeMultiplier: 1.5,

    // Minimum font scale (iOS only, 0.01-1.0)
    minimumFontScale: 0.5,
  },
};

export default function App() {
  return (
    <HeroUINativeProvider config={config}>
      {/* Your app content */}
    </HeroUINativeProvider>
  );
}
```

#### Animation Configuration

Global animation configuration for the entire application:

```tsx
const config: HeroUINativeConfig = {
  // Disable all animations across the application (cascades to all children)
  animation: 'disable-all',
};
```

<Callout type="warning">
  **Note**: When set to `'disable-all'`, all animations across the application will be disabled. This is useful for accessibility or performance optimization.
</Callout>

#### Developer Information Configuration

Control developer-facing informational messages displayed in the console:

```tsx
const config: HeroUINativeConfig = {
  devInfo: {
    // Disable styling principles information message
    stylingPrinciples: false,
  },
};
```

<Callout type="info">
  **Note**: By default, informational messages are enabled. Set `stylingPrinciples: false` to disable the styling principles message that appears in the console during development.
</Callout>

#### Toast Configuration

Configure the global toast system including insets, default props, and wrapper components. You can also disable the toast provider entirely:

**Option 1: Disable Toast Provider**

```tsx
const config: HeroUINativeConfig = {
  // Disable toast provider entirely
  toast: false,
  // or
  toast: 'disabled',
};
```

<Callout type="info">
  **Note**: When toast is disabled (`false` or `'disabled'`), the `ToastProvider` will not be rendered, and toast functionality will not be available in your application.
</Callout>

**Option 2: Configure Toast Provider**

```tsx
import { KeyboardAvoidingView } from 'react-native';

const config: HeroUINativeConfig = {
  toast: {
    // Global toast configuration (used as defaults for all toasts)
    defaultProps: {
      variant: 'default',
      placement: 'top',
      isSwipeable: true,
      animation: true,
    },
    // Insets for spacing from screen edges (added to safe area insets)
    insets: {
      top: 0,      // Default: iOS = 0, Android = 12
      bottom: 6,   // Default: iOS = 6, Android = 12
      left: 12,    // Default: 12
      right: 12,   // Default: 12
    },
    // Maximum number of visible toasts before opacity starts fading
    maxVisibleToasts: 3,
    // Custom wrapper function to wrap the toast content
    contentWrapper: (children) => (
      <KeyboardAvoidingView
        behavior="padding"
        keyboardVerticalOffset={24}
        className="flex-1"
      >
        {children}
      </KeyboardAvoidingView>
    ),
  },
};
```

### Complete Example

Here's a comprehensive example showing all configuration options:

```tsx
import { HeroUINativeProvider } from 'heroui-native';
import type { HeroUINativeConfig } from 'heroui-native';
import { GestureHandlerRootView } from 'react-native-gesture-handler';

const config: HeroUINativeConfig = {
  // Global text configuration
  textProps: {
    minimumFontScale: 0.5,
    maxFontSizeMultiplier: 1.5,
    allowFontScaling: true,
    adjustsFontSizeToFit: false,
  },
  // Global animation configuration
  animation: 'disable-all', // Optional: disable all animations
  // Developer information messages configuration
  devInfo: {
    stylingPrinciples: true, // Optional: disable styling principles message
  },
  // Global toast configuration
  // Option 1: Configure toast with custom settings
  toast: {
    defaultProps: {
      variant: 'default',
      placement: 'top',
    },
    insets: {
      top: 0,
      bottom: 6,
      left: 12,
      right: 12,
    },
    maxVisibleToasts: 3,
  },
  // Option 2: Disable toast entirely
  // toast: false,
  // or
  // toast: 'disabled',
};

export default function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <HeroUINativeProvider config={config}>
        <YourApp />
      </HeroUINativeProvider>
    </GestureHandlerRootView>
  );
}
```

### Integration with Expo Router

When using Expo Router, wrap your root layout:

```tsx
// app/_layout.tsx
import { HeroUINativeProvider } from 'heroui-native';
import type { HeroUINativeConfig } from 'heroui-native';
import { Stack } from 'expo-router';

const config: HeroUINativeConfig = {
  textProps: {
    minimumFontScale: 0.5,
    maxFontSizeMultiplier: 1.5,
  },
};

export default function RootLayout() {
  return (
    <HeroUINativeProvider config={config}>
      <Stack />
    </HeroUINativeProvider>
  );
}
```

### Architecture

#### Provider Hierarchy

The `HeroUINativeProvider` internally composes multiple providers:

```
HeroUINativeProvider
├── SafeAreaListener (handles safe area insets updates)
│   └── GlobalAnimationSettingsProvider (animation configuration)
│       └── TextComponentProvider (text configuration)
│           └── ToastProvider (toast configuration, conditionally rendered)
│               └── Your App
│               └── PortalHost (for overlays)
```

<Callout type="info">
  **Note**: The `ToastProvider` is conditionally rendered based on the `toast` configuration. If `toast` is set to `false` or `'disabled'`, the `ToastProvider` will not be rendered, and the app content and `PortalHost` will be rendered directly under `TextComponentProvider`.
</Callout>

#### Safe Area Insets Handling

The provider automatically wraps your application with [`SafeAreaListener`](https://appandflow.github.io/react-native-safe-area-context/api/safe-area-listener) from `react-native-safe-area-context`. This component listens to safe area insets and frame changes without triggering re-renders, and automatically updates Uniwind with the latest insets via the `onChange` callback.

### Raw Provider

`HeroUINativeProviderRaw` is a lightweight variant of `HeroUINativeProvider` designed for bundle optimization. It excludes `ToastProvider` and `PortalHost`, giving you a bare minimum starting point where you only install and add what you actually need.

#### When to Use

Use `HeroUINativeProviderRaw` when you want full control over which dependencies are included in your bundle. With the raw provider imported from `heroui-native/provider-raw`, the following dependencies are optional and only required if you use the corresponding components:

- **react-native-screens** -- required for overlay components (Popover, Dialog)
- **@gorhom/bottom-sheet** -- required for BottomSheet component
- **react-native-svg** -- required for components that use icons (Accordion, Alert, Checkbox, etc.)

#### Setup

```tsx
import {
  HeroUINativeProviderRaw,
  type HeroUINativeConfigRaw,
} from 'heroui-native/provider-raw';
import { GestureHandlerRootView } from 'react-native-gesture-handler';

const config: HeroUINativeConfigRaw = {
  textProps: {
    maxFontSizeMultiplier: 1.5,
  },
};

export default function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <HeroUINativeProviderRaw config={config}>
        {/* Your app content */}
      </HeroUINativeProviderRaw>
    </GestureHandlerRootView>
  );
}
```

#### Adding Toast and Portal Manually

If you need toast or portal functionality with the raw provider, add them yourself:

```tsx
import { HeroUINativeProviderRaw } from 'heroui-native/provider-raw';
import { PortalHost } from 'heroui-native/portal';
import { ToastProvider } from 'heroui-native/toast';

export default function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <HeroUINativeProviderRaw>
        <ToastProvider>
          {/* Your app content */}
          <PortalHost />
        </ToastProvider>
      </HeroUINativeProviderRaw>
    </GestureHandlerRootView>
  );
}
```

#### Provider Hierarchy

```
HeroUINativeProviderRaw
├── SafeAreaListener (handles safe area insets updates)
│   └── GlobalAnimationSettingsProvider (animation configuration)
│       └── TextComponentProvider (text configuration)
│           └── Your App
```

### Best Practices

#### 1. Single Provider Instance

Always use a single `HeroUINativeProvider` at the root of your app. Don't nest multiple providers:

```tsx
// Bad
<HeroUINativeProvider>
  <SomeComponent>
    <HeroUINativeProvider> {/* Don't do this */}
      <AnotherComponent />
    </HeroUINativeProvider>
  </SomeComponent>
</HeroUINativeProvider>

// Good
<HeroUINativeProvider>
  <SomeComponent>
    <AnotherComponent />
  </SomeComponent>
</HeroUINativeProvider>
```

#### 2. Configuration Object

Define your configuration outside the component to prevent recreating on each render:

```tsx
// Bad
function App() {
  return (
    <HeroUINativeProvider
      config={{
        textProps: {
          /* inline config */
        },
      }}
    >
      {/* ... */}
    </HeroUINativeProvider>
  );
}

// Good
const config: HeroUINativeConfig = {
  textProps: {
    maxFontSizeMultiplier: 1.5,
  },
};

function App() {
  return (
    <HeroUINativeProvider config={config}>{/* ... */}</HeroUINativeProvider>
  );
}
```

#### 3. Text Configuration

Consider accessibility when configuring text props:

```tsx
const config: HeroUINativeConfig = {
  textProps: {
    // Allow font scaling for accessibility
    allowFontScaling: true,
    // But limit maximum scale
    maxFontSizeMultiplier: 1.5,
  },
};
```

### TypeScript Support

The provider is fully typed. Import types for better IDE support:

```tsx
import { HeroUINativeProvider, type HeroUINativeConfig } from 'heroui-native';

const config: HeroUINativeConfig = {
  // Full type safety and autocomplete
  textProps: {
    allowFontScaling: true,
    maxFontSizeMultiplier: 1.5,
  },
  animation: 'disable-all', // Optional: disable all animations
  devInfo: {
    stylingPrinciples: true, // Optional: disable styling principles message
  },
  // Toast configuration options:
  // - false or 'disabled': Disable toast provider
  // - ToastProviderProps object: Configure toast settings
  toast: {
    defaultProps: {
      variant: 'default',
      placement: 'top',
    },
    insets: {
      top: 0,
      bottom: 6,
      left: 12,
      right: 12,
    },
  },
};
```

### Related

- [Quick Start](/docs/native/getting-started/quick-start) - Basic setup guide
- [Theming](/docs/native/getting-started/theming) - Customize colors and themes
- [Styling](/docs/native/getting-started/styling) - Style components with Tailwind

---

## Portal

---
title: Portal
icon: windows
---

Portals let you render its children into a different part of your app. This is particularly useful for components that need to render above other content, such as modals, overlays, and popups.

### Default Setup

By default, the `PortalHost` is included in the `HeroUINativeProvider`, so there is no need to add it manually. The provider automatically sets up the portal system for all components that use portals.

### Advanced Use Cases

For advanced use cases, you can import `Portal` and `PortalHost` directly from `heroui-native` to create custom portal implementations:

```tsx
import { Portal, PortalHost } from "heroui-native";
import { View, Text } from "react-native";

function AppLayout() {
  return (
    <View className="flex-1">
      <View className="p-5">
        <Text>Header Content</Text>
      </View>

      <View className="flex-1 p-5">
        <Text>Main Content Area</Text>
        <CustomNotification />
      </View>

      {/* Portal host positioned at the top of the screen */}
      <PortalHost name="notification-host" />
    </View>
  );
}

function CustomNotification() {
  return (
    <Portal name="notification-portal" hostName="notification-host">
      <View className="absolute top-0 left-0 right-0 bg-blue-500 p-4">
        <Text>This notification appears at the top via Portal</Text>
      </View>
    </Portal>
  );
}
```

In this example, the `CustomNotification` component uses a `Portal` to render its content at the location of the `PortalHost`, which is positioned at the top of the screen. This allows the notification to appear above all other content regardless of where it's defined in the component tree.

### State Management Considerations

State changes in parent components can cause unexpected issues with components rendered inside portals. For example, when a text input is placed directly inside a portal and the parent component re-renders, it can reset the input's auto-suggestions or cause other UI disruptions.

To avoid this, keep the state of interactive components (like text inputs) inside the portal by creating a separate component for the portal content. This isolates the state from parent re-renders.

#### Example Pattern

```tsx
// Problematic: State in parent causes re-renders that affect portal content
function ParentComponent() {
  const [dialogOpen, setDialogOpen] = useState(false);
  const [inputValue, setInputValue] = useState(""); // State in parent

  return (
    <Dialog isOpen={dialogOpen} onOpenChange={setDialogOpen}>
      <Dialog.Trigger>
        <Button>Open Dialog</Button>
      </Dialog.Trigger>
      <Dialog.Portal>
        <Input
          value={inputValue}
          onChangeText={setInputValue}
          // Parent re-renders reset auto-suggestions
        />
      </Dialog.Portal>
    </Dialog>
  );
}

// Correct: State managed inside separate component within portal
function ParentComponent() {
  const [dialogOpen, setDialogOpen] = useState(false);

  return (
    <Dialog isOpen={dialogOpen} onOpenChange={setDialogOpen}>
      <Dialog.Trigger>
        <Button>Open Dialog</Button>
      </Dialog.Trigger>
      <Dialog.Portal>
        <DialogFormContent
          onClose={() => setDialogOpen(false)}
          // Form state isolated from parent
        />
      </Dialog.Portal>
    </Dialog>
  );
}

function DialogFormContent({ onClose }: { onClose: () => void }) {
  const [inputValue, setInputValue] = useState(""); // State inside portal
  const [error, setError] = useState("");

  return (
    <Dialog.Content>
      <Input
        value={inputValue}
        onChangeText={setInputValue}
        // Auto-suggestions preserved during parent re-renders
      />
      <FieldError>{error}</FieldError>
      <Button onPress={onClose}>Close</Button>
    </Dialog.Content>
  );
}
```

In the correct pattern, the `DialogFormContent` component manages its own state independently of the parent component. This ensures that parent re-renders (such as when `dialogOpen` changes) don't affect the input's internal state, preserving auto-suggestions and other input behaviors.

### API Reference

#### PortalHost

By default, children of all Portal components will be rendered as its own children.

| Prop | Type     | Note                                                      |
|------|----------|-----------------------------------------------------------|
| name | `string` | Provide when it is used as a custom host (optional)       |

#### Portal

| Prop      | Type     | Note                                                      |
|-----------|----------|-----------------------------------------------------------|
| name*     | `string` | Unique value otherwise the portal with the same name will replace the original portal |
| hostName  | `string` | Provide when its children are to be rendered in a custom host (optional) |
| children  | `React.ReactNode` | The content to render in the portal |

\* Required prop

### Related

- [Quick Start](/docs/native/getting-started/quick-start) - Basic setup guide
- View [Provider](/docs/native/getting-started/provider) documentation

---

## Composition

---
title: Composition
description: Build flexible UI with component composition patterns
icon: layers
---

HeroUI Native uses composition patterns to create flexible, customizable components. Change the rendered element, compose components together, and maintain full control over markup.

### Compound Components

HeroUI Native components use a compound component pattern with dot notation--components export sub-components as properties (e.g., `Button.Label`, `Dialog.Trigger`, `Accordion.Item`) that work together to form complete UI elements.

```tsx
import { Button, Dialog } from 'heroui-native';

function DialogExample() {
  return (
    <Dialog>
      <Dialog.Trigger>
        Open Dialog
      </Dialog.Trigger>
      <Dialog.Portal>
        <Dialog.Overlay />
        <Dialog.Content>
          <Dialog.Close />
          <Dialog.Title>Dialog Title</Dialog.Title>
          <Dialog.Description>Dialog description</Dialog.Description>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog>
  );
}
```

### The asChild Prop

The `asChild` prop lets you change what element a component renders. When `asChild` is true, HeroUI Native clones the child element and merges props instead of rendering its default element.

```tsx
import { Button, Dialog } from 'heroui-native';

function DialogExample() {
  return (
    <Dialog>
      {/* With asChild: Button becomes the trigger directly, no wrapper element */}
      <Dialog.Trigger asChild>
        <Button variant="primary">Open Dialog</Button>
      </Dialog.Trigger>
      <Dialog.Portal>
        <Dialog.Overlay />
        <Dialog.Content>
          {/* Dialog.Close can also use asChild */}
          <Dialog.Close asChild>
            <Button variant="ghost" size="sm">Cancel</Button>
          </Dialog.Close>
          <Dialog.Title>Dialog Title</Dialog.Title>
          <Dialog.Description>Dialog description</Dialog.Description>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog>
  );
}
```

### Custom Components

Create your own components by composing HeroUI Native primitives:

```tsx
import { Button, Card, Popover } from 'heroui-native';
import { View } from 'react-native';

// Product card component
function ProductCard({ title, description, price, onBuy, ...props }) {
  return (
    <Card {...props}>
      <Card.Body>
        <Card.Title>{price}</Card.Title>
        <Card.Title>{title}</Card.Title>
        <Card.Description>{description}</Card.Description>
      </Card.Body>
      <Card.Footer>
        <Button variant="primary" onPress={onBuy}>
          <Button.Label>Buy now</Button.Label>
        </Button>
      </Card.Footer>
    </Card>
  );
}

// Popover button component
function PopoverButton({ children, popoverContent, ...props }) {
  return (
    <Popover>
      <Popover.Trigger asChild>
        <Button {...props}>
          <Button.Label>{children}</Button.Label>
        </Button>
      </Popover.Trigger>
      <Popover.Portal>
        <Popover.Overlay />
        <Popover.Content>
          <Popover.Close />
          {popoverContent}
        </Popover.Content>
      </Popover.Portal>
    </Popover>
  );
}

// Usage
<ProductCard
  title="Living room Sofa"
  description="Perfect for modern spaces"
  price="$450"
  onBuy={() => console.log('Buy')}
/>

<PopoverButton variant="tertiary" popoverContent={
  <View>
    <Popover.Title>Information</Popover.Title>
    <Popover.Description>Additional details here</Popover.Description>
  </View>
}>
  Show Info
</PopoverButton>
```

### Custom Variants

Create custom variants using `tailwind-variants` to extend component styling. Note that text color classes must be applied to `Button.Label`, not the parent `Button`:

```tsx
import { Button } from 'heroui-native';
import type { ButtonRootProps } from 'heroui-native';
import { tv, type VariantProps } from 'tailwind-variants';

const customButtonVariants = tv({
  base: 'font-semibold rounded-lg',
  variants: {
    intent: {
      primary: 'bg-blue-500',
      secondary: 'bg-gray-200',
      danger: 'bg-red-500',
    },
  },
  defaultVariants: {
    intent: 'primary',
  },
});

const customLabelVariants = tv({
  base: '',
  variants: {
    intent: {
      primary: 'text-white',
      secondary: 'text-gray-800',
      danger: 'text-white',
    },
  },
  defaultVariants: {
    intent: 'primary',
  },
});

type CustomButtonVariants = VariantProps<typeof customButtonVariants>;

interface CustomButtonProps
  extends Omit<ButtonRootProps, 'className' | 'variant'>,
    CustomButtonVariants {
  className?: string;
  labelClassName?: string;
}

export function CustomButton({
  intent,
  className,
  labelClassName,
  children,
  ...props
}: CustomButtonProps) {
  return (
    <Button
      className={customButtonVariants({ intent, className })}
      {...props}
    >
      <Button.Label
        className={customLabelVariants({ intent, className: labelClassName })}
      >
        {children}
      </Button.Label>
    </Button>
  );
}
```

### Next Steps

- Learn about [Styling](/docs/native/getting-started/styling) system
- Explore [Theming](/docs/native/getting-started/theming) documentation
- Explore [Animation](/docs/native/getting-started/animation) options

---

## Design Principles

---
title: Design Principles
description: Core principles that guide HeroUI v3's design and development
icon: book
---

HeroUI Native follows 9 core principles that prioritize clarity, accessibility, customization, and developer experience.

### Core Principles

#### 1. Semantic Intent Over Visual Style

Use semantic naming (primary, secondary, tertiary) instead of visual descriptions (solid, flat, bordered). Inspired by [Uber's Base design system](https://base.uber.com/6d2425e9f/p/756216-button), variants follow a clear hierarchy:

<DocsImage
  src="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/emphasis.jpg"
  darkSrc="https://heroui-assets.nyc3.cdn.digitaloceanspaces.com/docs/emphasis-dark.jpg"
  alt="Semantic Intent Hierarchy"
/>


```tsx
// Semantic variants communicate hierarchy
<Button variant="primary">Save</Button>
<Button variant="secondary">Edit</Button>
<Button variant="tertiary">Cancel</Button>
```

| Variant | Purpose | Usage |
|---------|---------|-------|
| **Primary** | Main action to move forward | 1 per context |
| **Secondary** | Alternative actions | Multiple allowed |
| **Tertiary** | Dismissive actions (cancel, skip) | Sparingly |
| **Danger** | Destructive actions | When needed |

#### 2. Accessibility as Foundation

Accessibility follows mobile development best practices with proper touch accessibility, focus management, and screen reader support built into every component. All components include proper accessibility labels and semantic structure for VoiceOver (iOS) and TalkBack (Android).

```tsx
import { Tabs } from 'heroui-native';

<Tabs value="profile" onValueChange={setActiveTab}>
  <Tabs.List>
    <Tabs.Indicator />
    <Tabs.Trigger value="profile">
      <Tabs.Label>Profile</Tabs.Label>
    </Tabs.Trigger>
    <Tabs.Trigger value="security">
      <Tabs.Label>Security</Tabs.Label>
    </Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="profile">Content</Tabs.Content>
  <Tabs.Content value="security">Content</Tabs.Content>
</Tabs>
```

#### 3. Composition Over Configuration

Compound components let you rearrange, customize, or omit parts as needed. Use dot notation to compose components exactly as you need them.

```tsx
// Compose parts to build exactly what you need
import { Accordion } from 'heroui-native';

<Accordion>
  <Accordion.Item value="1">
    <Accordion.Trigger>
      Question Text
      <Accordion.Indicator />
    </Accordion.Trigger>
    <Accordion.Content>Answer content</Accordion.Content>
  </Accordion.Item>
</Accordion>
```

#### 4. Progressive Disclosure

Start simple, add complexity only when needed. Components work with minimal props and scale up as requirements grow.

```tsx
import { Button, Spinner } from 'heroui-native';
import { Feather } from '@expo/vector-icons';

// Level 1: Minimal
<Button>Click me</Button>

// Level 2: Enhanced
<Button variant="primary" size="lg">
  <Feather name="check" size={20} />
  <Button.Label>Submit</Button.Label>
</Button>

// Level 3: Advanced
<Button variant="primary" isDisabled={isLoading}>
  {isLoading ? (
    <>
      <Spinner size="sm" />
      <Button.Label>Loading...</Button.Label>
    </>
  ) : (
    <Button.Label>Submit</Button.Label>
  )}
</Button>
```

#### 5. Predictable Behavior

Consistent patterns across all components: sizes (`sm`, `md`, `lg`), variants, and className support. Same API, same behavior.

```tsx
import { Button, Chip, Avatar } from 'heroui-native';

// All components follow the same patterns
<Button size="lg" variant="primary" className="custom">
  <Button.Label>Click me</Button.Label>
</Button>
<Chip size="lg" color="success" className="custom">
  <Chip.Label>Success</Chip.Label>
</Chip>
<Avatar size="lg" className="custom">
  <Avatar.Fallback>JD</Avatar.Fallback>
</Avatar>
```

#### 6. Type Safety First

Full TypeScript support with IntelliSense, auto-completion, and compile-time error detection. Extend types for custom components.

```tsx
import type { ButtonRootProps } from 'heroui-native';

// Type-safe props and event handlers
<Button
  variant="primary"  // Autocomplete: primary | secondary | tertiary | ghost | danger | danger-soft
  size="md"          // Type checked: sm | md | lg
  onPress={() => {   // Properly typed press handler
    console.log('Button pressed');
  }}
>
  <Button.Label>Click me</Button.Label>
</Button>

// Extend types for custom components
interface CustomButtonProps extends Omit<ButtonRootProps, 'variant'> {
  intent: 'save' | 'cancel' | 'delete';
}
```

#### 7. Developer Experience Excellence

Clear APIs, descriptive errors, IntelliSense and AI-friendly markdown docs.

#### 8. Complete Customization

Beautiful defaults out-of-the-box. Transform the entire look with CSS variables through [Uniwind's theming system](https://docs.uniwind.dev/theming/basics). Every slot is customizable.

```css
/* Custom colors using Uniwind's theme layer */
@layer theme {
  @variant light {
    --accent: oklch(0.65 0.25 270); /* Custom indigo accent */
    --background: oklch(0.98 0 0);  /* Custom background */
  }

  @variant dark {
    --accent: oklch(0.65 0.25 270);
    --background: oklch(0.15 0 0);
  }
}

/* Radius customization */
@theme {
  --radius: 0.75rem; /* Increase for rounder components */
}
```

#### 9. Open and Extensible

Wrap, extend, and customize components to match your needs. Create custom wrappers or apply custom styles using className.

```tsx
import { Button } from 'heroui-native';
import type { ButtonRootProps } from 'heroui-native';

// Custom wrapper component
interface CTAButtonProps extends Omit<ButtonRootProps, 'variant'> {
  intent?: 'primary-cta' | 'secondary-cta' | 'minimal';
}

const CTAButton = ({
  intent = 'primary-cta',
  children,
  ...props
}: CTAButtonProps) => {
  const variantMap = {
    'primary-cta': 'primary',
    'secondary-cta': 'secondary',
    'minimal': 'ghost'
  } as const;

  return (
    <Button variant={variantMap[intent]} {...props}>
      <Button.Label>{children}</Button.Label>
    </Button>
  );
};

// Usage
<CTAButton intent="primary-cta">Get Started</CTAButton>
<CTAButton intent="secondary-cta">Learn More</CTAButton>
```

**Extend with Tailwind Variants:**

```tsx
import { Button } from 'heroui-native';
import { tv } from 'tailwind-variants';

// Extend button styles with custom variants
const myButtonVariants = tv({
  base: 'px-4 py-2 rounded-lg',
  variants: {
    variant: {
      'primary-cta': 'bg-accent px-8 py-4 shadow-lg',
      'secondary-cta': 'border-2 border-accent px-6 py-3',
    }
  },
  defaultVariants: {
    variant: 'primary-cta',
  }
});

// Label variants for text colors (must be applied to Button.Label)
const myLabelVariants = tv({
  base: '',
  variants: {
    variant: {
      'primary-cta': 'text-accent-foreground',
      'secondary-cta': 'text-accent',
    }
  },
  defaultVariants: {
    variant: 'primary-cta',
  }
});

// Use the custom variants
function CustomButton({ variant, className, labelClassName, children, ...props }) {
  return (
    <Button className={myButtonVariants({ variant, className })} {...props}>
      <Button.Label className={myLabelVariants({ variant, className: labelClassName })}>
        {children}
      </Button.Label>
    </Button>
  );
}

// Usage
<CustomButton variant="primary-cta">Get Started</CustomButton>
<CustomButton variant="secondary-cta">Learn More</CustomButton>
```
