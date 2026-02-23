# Theming, Styling, Colors & Animation

## Table of Contents
- [Theming](#theming)
- [Styling](#styling)
- [Colors](#colors)
- [Animation](#animation)

---

## Theming

HeroUI Native uses CSS variables for theming. Customize everything from colors to component styles using standard CSS.

### How It Works

HeroUI Native's theming system is built on top of [Tailwind CSS v4](https://tailwindcss.com/docs/theme)'s theme via [Uniwind](https://uniwind.dev/). When you import `heroui-native/styles`, it uses Tailwind's built-in color palettes, maps them to semantic variables, automatically switches between light and dark themes, and uses CSS layers and the `@theme` directive for organization.

**Naming pattern:**

- Colors without a suffix are backgrounds (e.g., `--accent`)
- Colors with `-foreground` are for text on that background (e.g., `--accent-foreground`)

### Quick Start

**Apply colors in your components:**

```tsx
import { View, Text } from 'react-native';

<View className="bg-background flex-1">
  <Text className="text-foreground">Your app content</Text>
</View>;
```

**Switch themes:**

HeroUI Native automatically supports dark mode through [Uniwind](https://docs.uniwind.dev/theming/basics). The theme switches between light and dark variants based on system preferences or manual selection:

```tsx
import { Uniwind, useUniwind } from 'uniwind';
import { Button } from 'heroui-native';

function ThemeToggle() {
  const { theme } = useUniwind();

  return (
    <Button
      onPress={() => Uniwind.setTheme(theme === 'light' ? 'dark' : 'light')}
    >
      <Button.Label>
        Toggle {theme === 'light' ? 'Dark' : 'Light'} Mode
      </Button.Label>
    </Button>
  );
}
```

**Override colors:**

```css
/* global.css */
@layer theme {
  @variant light {
    /* Override any color variable */
    --accent: oklch(0.65 0.25 270); /* Custom indigo accent */
    --success: oklch(0.65 0.15 155);
  }

  @variant dark {
    --accent: oklch(0.65 0.25 270);
    --success: oklch(0.75 0.12 155);
  }
}
```

> **Note**: See [Colors](/docs/native/getting-started/colors) for the complete color palette and visual reference.

**Create your own theme:**

Create multiple themes using Uniwind's variant system. For complete custom theme documentation, see the [Uniwind Custom Themes Guide](https://docs.uniwind.dev/theming/custom-themes).

> **Important:** All themes must define the same variables. See the [Default Theme](/docs/native/getting-started/colors#default-theme) section for a complete list of all required variables.

```css
/* global.css */
@layer theme {
  :root {
    @variant ocean-light {
      /* Base Colors */
      --background: oklch(0.95 0.02 230);
      --foreground: oklch(0.25 0.04 230);

     /* Surface: Used for non-overlay components (cards, accordions, disclosure groups) */
      --surface: oklch(0.98 0.01 230);
      --surface-foreground: oklch(0.3 0.045 230);

      --surface-secondary: oklch(0.96 0.012 230);
      --surface-secondary-foreground: oklch(0.3 0.045 230);

      --surface-tertiary: oklch(0.94 0.015 230);
      --surface-tertiary-foreground: oklch(0.3 0.045 230);

      /* Overlay: Used for floating/overlay components (dialogs, popovers, modals, menus) */
      --overlay: oklch(0.998 0.003 230);
      --overlay-foreground: oklch(0.3 0.045 230);

      --muted: oklch(0.55 0.035 230);

      --default: oklch(0.94 0.018 230);
      --default-foreground: oklch(0.4 0.05 230);

      /* Accent */
      --accent: oklch(0.6 0.2 230);
      --accent-foreground: oklch(0.98 0.005 230);

      /* Form Field Defaults - Colors */
      --field-background: oklch(0.98 0.01 230);
      --field-foreground: oklch(0.25 0.04 230);
      --field-placeholder: var(--muted);
      --field-border: transparent;

      /* Status Colors */
      --success: oklch(0.72 0.14 165);
      --success-foreground: oklch(0.25 0.08 165);

      --warning: oklch(0.78 0.12 85);
      --warning-foreground: oklch(0.3 0.08 85);

      --danger: oklch(0.68 0.18 15);
      --danger-foreground: oklch(0.98 0.005 15);

      /* Component Colors */
      --segment: oklch(0.98 0.01 230);
      --segment-foreground: oklch(0.25 0.04 230);

      /* Misc Colors */
      --border: oklch(0 0 0 / 0%);
      --separator: oklch(0.91 0.015 230);
      --focus: var(--accent);
      --link: oklch(0.62 0.17 230);

      /* Shadows */
      --surface-shadow:
        0 2px 4px 0 rgba(0, 0, 0, 0.04), 0 1px 2px 0 rgba(0, 0, 0, 0.06),
        0 0 1px 0 rgba(0, 0, 0, 0.06);
      --overlay-shadow:
        0 2px 8px 0 rgba(0, 0, 0, 0.02), 0 -6px 12px 0 rgba(0, 0, 0, 0.01),
        0 14px 28px 0 rgba(0, 0, 0, 0.03);
      --field-shadow:
        0 2px 4px 0 rgba(0, 0, 0, 0.04), 0 1px 2px 0 rgba(0, 0, 0, 0.06),
        0 0 1px 0 rgba(0, 0, 0, 0.06);
    }

    @variant ocean-dark {
      /* Base Colors */
      --background: oklch(0.15 0.04 230);
      --foreground: oklch(0.94 0.01 230);

      /* Surface: Used for non-overlay components (cards, accordions, disclosure groups) */
      --surface: oklch(0.2 0.048 230);
      --surface-foreground: oklch(0.9 0.015 230);

      --surface-secondary: oklch(0.24 0.046 230);
      --surface-secondary-foreground: oklch(0.9 0.015 230);

      --surface-tertiary: oklch(0.27 0.044 230);
      --surface-tertiary-foreground: oklch(0.9 0.015 230);

      /* Overlay: Used for floating/overlay components (dialogs, popovers, modals, menus) */
      --overlay: oklch(0.23 0.045 230);
      --overlay-foreground: oklch(0.9 0.015 230);

      --muted: oklch(0.5 0.04 230);

      --default: oklch(0.25 0.05 230);
      --default-foreground: oklch(0.88 0.018 230);

      /* Accent */
      --accent: oklch(0.72 0.21 230);
      --accent-foreground: oklch(0.15 0.04 230);

      /* Form Field Defaults - Colors */
      --field-background: var(--default);
      --field-foreground: var(--foreground);
      --field-placeholder: var(--muted);
      --field-border: transparent;

      /* Status Colors */
      --success: oklch(0.68 0.16 165);
      --success-foreground: oklch(0.95 0.008 165);

      --warning: oklch(0.75 0.14 90);
      --warning-foreground: oklch(0.2 0.04 90);

      --danger: oklch(0.65 0.2 20);
      --danger-foreground: oklch(0.95 0.008 20);

      /* Component Colors */
      --segment: oklch(0.22 0.046 230);
      --segment-foreground: oklch(0.9 0.015 230);

      /* Misc Colors */
      --border: oklch(0 0 0 / 0%);
      --separator: oklch(0.28 0.045 230);
      --focus: var(--accent);
      --link: oklch(0.75 0.18 230);

      /* Shadows */
      --surface-shadow: 0 0 0 0 transparent inset; /* No shadow on dark mode */
      --overlay-shadow: 0 0 1px 0 rgba(255, 255, 255, 0.3) inset;
      --field-shadow: 0 0 0 0 transparent inset; /* Transparent shadow to allow ring utilities to work */
    }
  }
}
```

**Important:** When adding custom themes, you must register them in your Metro config:

```js
// metro.config.js
const { withUniwindConfig } = require('uniwind/metro');
const {
  wrapWithReanimatedMetroConfig,
} = require('react-native-reanimated/metro-config');

const config = {
  // ... your existing config
};

module.exports = withUniwindConfig(wrapWithReanimatedMetroConfig(config), {
  cssEntryFile: './global.css',
  dtsFile: './src/uniwind.d.ts',
  extraThemes: ['ocean-light', 'ocean-dark'],
});
```

Apply themes in your app:

```tsx
import { Uniwind } from 'uniwind';
import { Button } from 'heroui-native';

function App() {
  return (
    <Button onPress={() => Uniwind.setTheme('ocean-light')}>
      <Button.Label>Ocean Theme</Button.Label>
    </Button>
  );
}
```

### Adding Custom Colors

Add your own semantic colors to the theme:

```css
@layer theme {
  @variant light {
    --info: oklch(0.6 0.15 210);
    --info-foreground: oklch(0.98 0 0);
  }

  @variant dark {
    --info: oklch(0.7 0.12 210);
    --info-foreground: oklch(0.15 0 0);
  }
}

/* Make the color available to Tailwind */
@theme inline {
  --color-info: var(--info);
  --color-info-foreground: var(--info-foreground);
}
```

Now use it in your components:

```tsx
import { View, Text } from 'react-native';

<View className="bg-info p-4 rounded-lg">
  <Text className="text-info-foreground">Info message</Text>
</View>;
```

### Custom Fonts

To use a custom font family in your app, you need to load the fonts and then override the font CSS variables.

#### 1. Load Fonts in Your App

First, load your custom fonts (using Expo's `useFonts` hook for example):

```tsx
import { useFonts } from 'expo-font';
import { HeroUINativeProvider } from 'heroui-native';
import {
  YourFont_400Regular,
  YourFont_500Medium,
  YourFont_600SemiBold,
} from '@expo-google-fonts/your-font';

export default function App() {
  const [fontsLoaded] = useFonts({
    YourFont_400Regular,
    YourFont_500Medium,
    YourFont_600SemiBold,
  });

  if (!fontsLoaded) {
    return null; // Or return a loading screen
  }

  return <HeroUINativeProvider>{/* Your app content */}</HeroUINativeProvider>;
}
```

#### 2. Configure Font CSS Variables

After loading the fonts, override the font CSS variables in your `global.css` file:

```css
@theme {
  --font-normal: 'YourFont-400Regular';
  --font-medium: 'YourFont-500Medium';
  --font-semibold: 'YourFont-600SemiBold';
}
```

**Note:** The font names in CSS variables should match the PostScript names of your loaded fonts. Check your font package documentation or use the font names exactly as they appear in your `useFonts` hook.

All HeroUI Native components automatically use these font variables, ensuring consistent typography throughout your app.

### Variables Reference

HeroUI defines three types of variables:

1. **Base Variables** -- Non-changing values like `--white`, `--black`
2. **Theme Variables** -- Colors that change between light/dark themes
3. **Calculated Variables** -- Automatically generated hover (pressed) states and size variants

For a complete reference, see: [Colors Documentation](/docs/native/getting-started/colors), [Default Theme Variables](https://github.com/heroui-inc/heroui-native/blob/rc/src/styles/variables.css), [Shared Theme Utilities](https://github.com/heroui-inc/heroui-native/blob/rc/src/styles/theme.css)

**Calculated variables (Tailwind):**

We use Tailwind's `@theme` directive to automatically create calculated variables for hover (pressed) states and radius variants. These are defined in [theme.css](https://github.com/heroui-inc/heroui-native/blob/rc/src/styles/theme.css):

```css
@theme inline static {
  --color-background: var(--background);
  --color-foreground: var(--foreground);

  --color-surface: var(--surface);
  --color-surface-foreground: var(--surface-foreground);
  --color-surface-hover: color-mix(in oklab, var(--surface) 92%, var(--surface-foreground) 8%);

  --color-surface-secondary: var(--surface-secondary);
  --color-surface-secondary-foreground: var(--surface-secondary-foreground);

  --color-surface-tertiary: var(--surface-tertiary);
  --color-surface-tertiary-foreground: var(--surface-tertiary-foreground);

  --color-overlay: var(--overlay);
  --color-overlay-foreground: var(--overlay-foreground);

  --color-muted: var(--muted);

  --color-accent: var(--accent);
  --color-accent-foreground: var(--accent-foreground);

  --color-segment: var(--segment);
  --color-segment-foreground: var(--segment-foreground);

  --color-border: var(--border);
  --color-separator: var(--separator);
  --color-focus: var(--focus);
  --color-link: var(--link);

  --color-default: var(--default);
  --color-default-foreground: var(--default-foreground);

  --color-success: var(--success);
  --color-success-foreground: var(--success-foreground);

  --color-warning: var(--warning);
  --color-warning-foreground: var(--warning-foreground);

  --color-danger: var(--danger);
  --color-danger-foreground: var(--danger-foreground);

  /* Form Field Tokens */
  --color-field: var(--field-background, var(--default));
  --color-field-foreground: var(--field-foreground, var(--foreground));
  --color-field-placeholder: var(--field-placeholder, var(--muted));
  --color-field-border: var(--field-border, var(--border));
  --radius-field: var(--field-radius, var(--radius-xl));
  --border-width-field: var(--field-border-width, var(--border-width));

  --shadow-surface: var(--surface-shadow);
  --shadow-overlay: var(--overlay-shadow);
  --shadow-field: var(--field-shadow);

  /* Calculated Variables */

  /* Colors */

  /* --- background shades --- */
  --color-background-secondary: color-mix(in oklab, var(--background) 96%, var(--foreground) 4%);
  --color-background-tertiary: color-mix(in oklab, var(--background) 92%, var(--foreground) 8%);
  --color-background-inverse: var(--foreground);

  /* ------------------------- */
  --color-default-hover: color-mix(in oklab, var(--default) 96%, var(--default-foreground) 4%);
  --color-accent-hover: color-mix(in oklab, var(--accent) 90%, var(--accent-foreground) 10%);
  --color-success-hover: color-mix(in oklab, var(--success) 90%, var(--success-foreground) 10%);
  --color-warning-hover: color-mix(in oklab, var(--warning) 90%, var(--warning-foreground) 10%);
  --color-danger-hover: color-mix(in oklab, var(--danger) 90%, var(--danger-foreground) 10%);

  /* Form Field Colors */
  --color-field-hover: color-mix(in oklab, var(--field-background, var(--default)) 90%, var(--field-foreground, var(--foreground)) 2%);
  --color-field-focus: var(--field-background, var(--default));
  --color-field-border-hover: color-mix(in oklab, var(--field-border, var(--border)) 88%, var(--field-foreground, var(--foreground)) 10%);
  --color-field-border-focus: color-mix(in oklab, var(--field-border, var(--border)) 74%, var(--field-foreground, var(--foreground)) 22%);

  /* Soft Colors */
  --color-accent-soft: color-mix(in oklab, var(--accent) 15%, transparent);
  --color-accent-soft-foreground: var(--accent);
  --color-accent-soft-hover: color-mix(in oklab, var(--accent) 20%, transparent);

  --color-danger-soft: color-mix(in oklab, var(--danger) 15%, transparent);
  --color-danger-soft-foreground: var(--danger);
  --color-danger-soft-hover: color-mix(in oklab, var(--danger) 20%, transparent);

  --color-warning-soft: color-mix(in oklab, var(--warning) 15%, transparent);
  --color-warning-soft-foreground: var(--warning);
  --color-warning-soft-hover: color-mix(in oklab, var(--warning) 20%, transparent);

  --color-success-soft: color-mix(in oklab, var(--success) 15%, transparent);
  --color-success-soft-foreground: var(--success);
  --color-success-soft-hover: color-mix(in oklab, var(--success) 20%, transparent);

  /* Separator Colors - Levels */
  --color-separator-secondary: color-mix(in oklab, var(--surface) 85%, var(--surface-foreground) 15%);
  --color-separator-tertiary: color-mix(in oklab, var(--surface) 81%, var(--surface-foreground) 19%);

  /* Border Colors - Levels (progressive contrast: default -> secondary -> tertiary) */
  /* Light mode: lighter -> darker | Dark mode: darker -> lighter */
  --color-border-secondary: color-mix(in oklab, var(--surface) 78%, var(--surface-foreground) 22%);
  --color-border-tertiary: color-mix(in oklab, var(--surface) 66%, var(--surface-foreground) 34%);

  /* Radius and default sizes - defaults can change by just changing the --radius */
  --radius-xs: calc(var(--radius) * 0.25); /* 0.125rem (2px) */
  --radius-sm: calc(var(--radius) * 0.5); /* 0.25rem (4px) */
  --radius-md: calc(var(--radius) * 0.75); /* 0.375rem (6px) */
  --radius-lg: calc(var(--radius) * 1); /* 0.5rem (8px) */
  --radius-xl: calc(var(--radius) * 1.5); /* 0.75rem (12px) */
  --radius-2xl: calc(var(--radius) * 2); /* 1rem (16px) */
  --radius-3xl: calc(var(--radius) * 3); /* 1.5rem (24px) */
  --radius-4xl: calc(var(--radius) * 4); /* 2rem (32px) */
}
```

Form controls now rely on the `--field-*` variables and their calculated hover/focus variants. Update them in your theme to restyle inputs, checkboxes, radios, and OTP slots without impacting surfaces like buttons or cards.

### Resources

- [Colors Documentation](/docs/native/getting-started/colors)
- [Styling Guide](/docs/native/getting-started/styling)
- [Tailwind CSS v4 Theming](https://tailwindcss.com/docs/theme)
- [OKLCH Color Tool](https://oklch.com)

---

## Styling

HeroUI Native components provide flexible styling options: Tailwind CSS utilities, StyleSheet API, and render props for dynamic styling.

### Styling Principles

HeroUI Native is built with `className` as the go-to styling solution. You can use Tailwind CSS classes via the `className` prop on all components.

**StyleSheet precedence:** The `style` prop (StyleSheet API) can be used and has precedence over `className` when both are provided. This allows you to override Tailwind classes when needed.

**Animated styles:** Some style properties are animated using `react-native-reanimated` and, like StyleSheet styles, they have precedence over `className`. To identify which styles are animated and cannot be used via `className`:

- **Hover over `className` in your IDE** - The TypeScript definitions will show which properties are available
- **Check component documentation** - Each component page includes a link to the component's style source at the top, which contains notes about animated properties

**Customizing animated styles:** If styles are occupied by animation, you can modify them via the `animation` prop on components that support it.

### Basic Styling

**Using className:** All HeroUI Native components accept `className` props:

```tsx
import { Button } from 'heroui-native';

<Button className="bg-accent px-6 py-3 rounded-lg">
  <Button.Label>Custom Button</Button.Label>
</Button>;
```

**Using style:** Components also accept inline styles via the `style` prop:

```tsx
import { Button } from 'heroui-native';

<Button style={{ backgroundColor: '#8B5CF6', paddingHorizontal: 24 }}>
  <Button.Label>Styled Button</Button.Label>
</Button>;
```

### Render Props

Use a render function to access component state and customize content dynamically:

```tsx
import { RadioGroup, Label, cn } from 'heroui-native';

<RadioGroup value={value} onValueChange={setValue}>
  <RadioGroup.Item value="option1">
    {({ isSelected, isInvalid, isDisabled }) => (
      <>
        <Label
          className={cn(
            'text-foreground',
            isSelected && 'text-accent font-semibold'
          )}
        >
          Option 1
        </Label>
        <Radio
          className={cn(
            'border-2 rounded-full',
            isSelected && 'border-accent bg-accent'
          )}
        >
          {isSelected && <CustomIcon />}
        </Radio>
      </>
    )}
  </RadioGroup.Item>
</RadioGroup>;
```

### Creating Wrapper Components

Create reusable custom components using [tailwind-variants](https://tailwind-variants.org/) -- a Tailwind CSS first-class variant API:

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

### Using Component classNames

Each HeroUI Native component exports a `classNames` object that contains the same styling functions used internally by the component. This is particularly useful when you want to style your own custom components to match the appearance of HeroUI Native components.

For example, you can style a custom `Link` component to look like a `Button`:

```tsx
import { buttonClassNames, cn } from 'heroui-native';
import { Pressable, Text } from 'react-native';

interface LinkProps {
  href: string;
  variant?: 'primary' | 'secondary' | 'outline' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
  className?: string;
}

export function Link({
  href,
  variant = 'primary',
  size = 'md',
  children,
  className,
}: LinkProps) {
  return (
    <Pressable
      className={cn(
        buttonClassNames.root({ variant, size }),
        className
      )}
      onPress={() => {
        // Handle navigation
      }}
    >
      <Text className={buttonClassNames.label({ variant, size })}>
        {children}
      </Text>
    </Pressable>
  );
}
```

**Available classNames exports:**

Each component exports its `classNames` object. For example:
- `buttonClassNames` - Contains `root` and `label` functions
- `cardClassNames` - Contains `root`, `header`, `body`, `footer`, `label`, and `description` functions
- `chipClassNames` - Contains `root` and `label` functions
- And many more...

**Usage pattern:**

```tsx
import { buttonClassNames } from 'heroui-native';

// Use with variant and size options
const rootClasses = buttonClassNames.root({
  variant: 'primary',
  size: 'md',
  className: 'custom-class', // Optional: merge with your own classes
});

const labelClasses = buttonClassNames.label({
  variant: 'primary',
  size: 'md',
});
```

The `classNames` functions accept the same variant props as the components themselves, allowing you to maintain visual consistency across your custom components and HeroUI Native components.

### Responsive Design

HeroUI Native supports Tailwind's responsive breakpoint system via [Uniwind](https://docs.uniwind.dev/breakpoints). Use breakpoint prefixes like `sm:`, `md:`, `lg:`, and `xl:` to apply styles conditionally based on screen width.

**Mobile-first approach:** Start with mobile styles (no prefix), then use breakpoints to enhance for larger screens.

#### Responsive Typography and Spacing

```tsx
import { Button } from 'heroui-native';
import { View, Text } from 'react-native';

<View className="px-4 sm:px-6 lg:px-8 py-6 sm:py-8">
  <Text className="text-2xl sm:text-3xl lg:text-4xl font-bold mb-4 sm:mb-6">
    Responsive Heading
  </Text>
  <Button className="px-3 sm:px-4 lg:px-6">
    <Button.Label className="text-sm sm:text-base lg:text-lg">
      Responsive Button
    </Button.Label>
  </Button>
</View>;
```

#### Responsive Layouts

```tsx
import { View, Text } from 'react-native';

<View className="flex-row flex-wrap">
  {/* Mobile: 1 column, Tablet: 2 columns, Desktop: 3 columns */}
  <View className="w-full sm:w-1/2 lg:w-1/3 p-2">
    <View className="bg-accent p-4 rounded-lg">
      <Text className="text-accent-foreground">Item 1</Text>
    </View>
  </View>
</View>;
```

**Default breakpoints:**
- `sm`: 640px
- `md`: 768px
- `lg`: 1024px
- `xl`: 1280px
- `2xl`: 1536px

For custom breakpoints and more details, see the [Uniwind breakpoints documentation](https://docs.uniwind.dev/breakpoints).

### Utilities

HeroUI Native provides utility functions to assist with styling components.

#### cn Utility

The `cn` utility function merges Tailwind CSS classes with proper conflict resolution. It's particularly useful when combining conditional classes or merging classes from props:

```tsx
import { cn } from 'heroui-native';
import { View } from 'react-native';

function MyComponent({ className, isActive }) {
  return (
    <View
      className={cn(
        'bg-background p-4 rounded-lg',
        'border border-separator',
        isActive && 'bg-accent',
        className
      )}
    />
  );
}
```

The `cn` utility is powered by `tailwind-variants` and includes:

- Automatic Tailwind class merging (`twMerge: true`)
- Custom opacity class group support
- Proper conflict resolution (later classes override earlier ones)

**Example with conflicts:**

```tsx
// 'bg-accent' overrides 'bg-background'
cn('bg-background p-4', 'bg-accent');
// Result: 'p-4 bg-accent'
```

#### useThemeColor Hook

Retrieves theme color values from CSS variables. Supports both single color and multiple colors for efficient batch retrieval.

**Single color usage:**

```tsx
import { useThemeColor } from 'heroui-native';

function MyComponent() {
  const accentColor = useThemeColor('accent');
  const dangerColor = useThemeColor('danger');

  return (
    <View style={{ borderColor: accentColor }}>
      <Text style={{ color: dangerColor }}>Error message</Text>
    </View>
  );
}
```

**Multiple colors usage (more efficient):**

```tsx
import { useThemeColor } from 'heroui-native';

function MyComponent() {
  const [accentColor, backgroundColor, dangerColor] = useThemeColor([
    'accent',
    'background',
    'danger',
  ]);

  return (
    <View style={{ borderColor: accentColor, backgroundColor }}>
      <Text style={{ color: dangerColor }}>Error message</Text>
    </View>
  );
}
```

**Type signatures:**

```tsx
// Single color
useThemeColor(themeColor: ThemeColor): string

// Multiple colors (with type inference for tuples)
useThemeColor<T extends readonly [ThemeColor, ...ThemeColor[]]>(
  themeColor: T
): CreateStringTuple<T['length']>

// Multiple colors (array)
useThemeColor(themeColor: ThemeColor[]): string[]
```

Available theme colors include: `background`, `foreground`, `surface`, `accent`, `default`, `success`, `warning`, `danger`, and all their variants (hover, soft, foreground, etc.), plus semantic colors like `muted`, `border`, `separator`, `field`, `overlay`, and more.

### Next Steps

- Learn about [Animation](/docs/native/getting-started/animation) techniques
- Explore [Theming](/docs/native/getting-started/theming) system
- Explore [Colors](/docs/native/getting-started/colors) documentation

---

## Colors

HeroUI Native uses CSS variables for colors that automatically switch between light and dark themes. All colors use the `oklch` color space for better color transitions.

### How It Works

HeroUI Native's color system is built on top of [Tailwind CSS v4](https://tailwindcss.com/docs/theme)'s theme via [Uniwind](https://uniwind.dev/). When you import `heroui-native/styles`, it uses Tailwind's built-in color palettes and maps them to semantic variables.

**Naming pattern:**

- Colors without a suffix are backgrounds (e.g., `--accent`)
- Colors with `-foreground` are for text on that background (e.g., `--accent-foreground`)

**Usage:**

```tsx
import { View, Text } from 'react-native';

// This gives you the right background and text colors
<View className="bg-background flex-1">
  <View className="bg-accent p-4 rounded-lg">
    <Text className="text-accent-foreground">Hello</Text>
  </View>
</View>;
```

### Base Colors

These four colors stay the same in all themes:

- **White** (`--white`)
- **Black** (`--black`)
- **Snow** (`--snow`)
- **Eclipse** (`--eclipse`)

### Background & Surface

- **Background** (`--background`, foreground: `--foreground`)
- **Foreground** (`--foreground`)
- **Surface** (`--surface`, foreground: `--surface-foreground`)
- **Surface Foreground** (`--surface-foreground`)
- **Overlay** (`--overlay`, foreground: `--overlay-foreground`)
- **Overlay Foreground** (`--overlay-foreground`)

### Primary Colors

**Accent** -- Your main brand color (used for primary actions)
**Accent Soft** -- A lighter version for secondary actions

- **Accent** (`--accent`, foreground: `--accent-foreground`)
- **Accent Foreground** (`--accent-foreground`)
- **Accent Soft** (`--accent-soft`, foreground: `--accent-soft-foreground`)
- **Accent Soft Foreground** (`--accent-soft-foreground`)

### Status Colors

For alerts, validation, and status messages:

- **Success** (`--success`, foreground: `--success-foreground`)
- **Success Foreground** (`--success-foreground`)
- **Warning** (`--warning`, foreground: `--warning-foreground`)
- **Warning Foreground** (`--warning-foreground`)
- **Danger** (`--danger`, foreground: `--danger-foreground`)
- **Danger Foreground** (`--danger-foreground`)

### Form Field Colors

For consistent form field styling across input components:

- **Field Background** (`--field-background`, foreground: `--field-foreground`)
- **Field Foreground** (`--field-foreground`)
- **Field Placeholder** (`--field-placeholder`)
- **Field Border** (`--field-border`)

### Other Colors

- **Default** (`--default`, foreground: `--default-foreground`)
- **Default Foreground** (`--default-foreground`)
- **Muted** (`--muted`, foreground: `--foreground`)
- **Border** (`--border`)
- **Focus** (`--focus`)
- **Link** (`--link`)

### How to Use Colors

**In your components:**

```tsx
import { View, Text } from 'react-native';
import { Button } from 'heroui-native';

<View className="bg-background flex-1 p-4">
  <Text className="text-foreground mb-4">Content</Text>
  <Button variant="primary" className="bg-accent">
    <Button.Label className="text-accent-foreground">Click me</Button.Label>
  </Button>
</View>;
```

**In CSS files:**

```css
/* global.css */
/* Direct CSS variables */
.container {
  flex: 1;
  background-color: var(--accent);
  width: 50px;
  height: 50px;
  border-radius: var(--radius);
}
```

### Default Theme

The complete theme definition can be found in ([variables.css](https://github.com/heroui-inc/heroui-native/blob/rc/src/styles/variables.css)). This theme automatically switches between light and dark modes through [Uniwind's theming system](https://docs.uniwind.dev/theming/basics), which supports system preferences and programmatic theme switching.

```css
@theme {
    /* Primitive Colors (Do not change between light and dark) */
    --white: oklch(100% 0 0);
    --black: oklch(0% 0 0);
    --snow: oklch(0.9911 0 0);
    --eclipse: oklch(0.2103 0.0059 285.89);

    /* Border */
    --border-width: 1px;
    --field-border-width: 0px;

    /* Base radius */
    --radius: 0.5rem;
    --field-radius: calc(var(--radius) * 1.5);

    /* Opacity */
    --opacity-disabled: 0.5;
}

@layer theme {
    :root {
      @variant light {
        /* Base Colors */
        --background: oklch(0.9702 0 0);
        --foreground: var(--eclipse);

        /* Surface: Used for non-overlay components (cards, accordions, disclosure groups) */
        --surface: var(--white);
        --surface-foreground: var(--foreground);

        --surface-secondary: oklch(0.9524 0.0013 286.37);
        --surface-secondary-foreground: var(--foreground);

        --surface-tertiary: oklch(0.9373 0.0013 286.37);
        --surface-tertiary-foreground: var(--foreground);

        /* Overlay: Used for floating/overlay components (dialogs, popovers, modals, menus) */
        --overlay: var(--white);
        --overlay-foreground: var(--foreground);

        --muted: oklch(0.5517 0.0138 285.94);

        --default: oklch(94% 0.001 286.375);
        --default-foreground: var(--eclipse);

        --accent: oklch(0.6204 0.195 253.83);
        --accent-foreground: var(--snow);

        /* Form Fields */
        --field-background: var(--white);
        --field-foreground: oklch(0.2103 0.0059 285.89);
        --field-placeholder: var(--muted);
        --field-border: transparent; /* no border by default on form fields */

        /* Status Colors */
        --success: oklch(0.7329 0.1935 150.81);
        --success-foreground: var(--eclipse);

        --warning: oklch(0.7819 0.1585 72.33);
        --warning-foreground: var(--eclipse);

        --danger: oklch(0.6532 0.2328 25.74);
        --danger-foreground: var(--snow);

        /* Component Colors */
        --segment: var(--white);
        --segment-foreground: var(--eclipse);

        /* Misc Colors */
        --border: oklch(90% 0.004 286.32);
        --separator: oklch(74% 0.004 286.32);
        --focus: var(--accent);
        --link: var(--foreground);

        /* Shadows */
        --surface-shadow:
          0 2px 4px 0 rgba(0, 0, 0, 0.04), 0 1px 2px 0 rgba(0, 0, 0, 0.06),
          0 0 1px 0 rgba(0, 0, 0, 0.06);
        --overlay-shadow:
          0 2px 8px 0 rgba(0, 0, 0, 0.02), 0 -6px 12px 0 rgba(0, 0, 0, 0.01),
          0 14px 28px 0 rgba(0, 0, 0, 0.03);
        --field-shadow:
          0 2px 4px 0 rgba(0, 0, 0, 0.04), 0 1px 2px 0 rgba(0, 0, 0, 0.06),
          0 0 1px 0 rgba(0, 0, 0, 0.06);
      }

      @variant dark {
        /* Base Colors */
        --background: oklch(12% 0.005 285.823);
        --foreground: var(--snow);

        /* Surface: Used for non-overlay components (cards, accordions, disclosure groups) */
        --surface: oklch(0.2103 0.0059 285.89);
        --surface-foreground: var(--foreground);

        --surface-secondary: oklch(0.257 0.0037 286.14);
        --surface-secondary-foreground: var(--foreground);

        --surface-tertiary: oklch(0.2721 0.0024 247.91);
        --surface-tertiary-foreground: var(--foreground);

        /* Overlay: Used for floating/overlay components (dialogs, popovers, modals, menus) - lighter for contrast */
        --overlay: oklch(0.2103 0.0059 285.89);
        --overlay-foreground: var(--foreground);

        --muted: oklch(70.5% 0.015 286.067);

        --default: oklch(27.4% 0.006 286.033);
        --default-foreground: var(--snow);

        --accent: oklch(0.6204 0.195 253.83);
        --accent-foreground: var(--snow);

        /* Form Field Defaults - Colors (only the ones that are different from light theme) */
        --field-background: oklch(0.2103 0.0059 285.89);
        --field-foreground: var(--foreground);
        --field-placeholder: var(--muted);
        --field-border: transparent; /* no border by default on form fields */

        /* Status Colors */
        --success: oklch(0.7329 0.1935 150.81);
        --success-foreground: var(--eclipse);

        --warning: oklch(0.8203 0.1388 76.34);
        --warning-foreground: var(--eclipse);

        --danger: oklch(0.594 0.1967 24.63);
        --danger-foreground: var(--snow);

        /* Component Colors */
        --segment: oklch(0.3964 0.01 285.93);
        --segment-foreground: var(--foreground);

        /* Misc Colors */
        --border: oklch(28% 0.006 286.033);
        --separator: oklch(40% 0.006 286.033);
        --focus: var(--accent);
        --link: var(--foreground);

        /* Shadows */
        --surface-shadow: 0 0 0 0 transparent inset; /* No shadow on dark mode */
        --overlay-shadow: 0 0 1px 0 rgba(255, 255, 255, 0.2) inset;
        --field-shadow: 0 0 0 0 transparent inset; /* Transparent shadow to allow ring utilities to work */
      }
    }
  }
```

### Customizing Colors

**Override existing colors:**

```css
@layer theme {
  @variant light {
    /* Override default colors */
    --accent: oklch(0.65 0.25 270); /* Custom indigo accent */
    --success: oklch(0.65 0.15 155);
  }

  @variant dark {
    /* Override dark theme colors */
    --accent: oklch(0.65 0.25 270);
    --success: oklch(0.75 0.12 155);
  }
}
```

**Tip:** Convert colors at [oklch.com](https://oklch.com)

**Add your own colors:**

```css
@layer theme {
  @variant light {
    --info: oklch(0.6 0.15 210);
    --info-foreground: oklch(0.98 0 0);
  }

  @variant dark {
    --info: oklch(0.7 0.12 210);
    --info-foreground: oklch(0.15 0 0);
  }
}

@theme inline {
  --color-info: var(--info);
  --color-info-foreground: var(--info-foreground);
}
```

Now you can use it:

```tsx
import { View, Text } from 'react-native';

<View className="bg-info p-4 rounded-lg">
  <Text className="text-info-foreground">Info message</Text>
</View>;
```

> **Note**: To learn more about theme variables and how they work in Tailwind CSS v4, see the [Tailwind CSS Theme documentation](https://tailwindcss.com/docs/theme).

### useThemeColor Hook

The `useThemeColor` hook has been enhanced to support multiple colors selection, making it more flexible for complex theming scenarios.

**Multiple Colors Selection:**

You can now select multiple colors at once, which is useful when you need to work with related color values together:

```tsx
import { useThemeColor } from 'heroui-native';

// Select multiple colors at once
const { accent, accentForeground, success, danger } = useThemeColor([
  'accent',
  'accentForeground',
  'success',
  'danger',
]);

// Use the selected colors
<View style={{ backgroundColor: accent }}>
  <Text style={{ color: accentForeground }}>Accent Text</Text>
</View>;
```

This enhancement improves performance when working with multiple color values and makes it easier to manage complex theming scenarios where multiple colors need to be selected and applied together.

### Quick Tips

- Always use color variables, not hard-coded values
- Use foreground/background pairs for good contrast
- Test in both light and dark modes
- The system respects user's theme preference automatically

### Related

- [Theming](/docs/native/getting-started/theming) - Learn about the theming system
- [Styling](/docs/native/getting-started/styling) - Styling components with CSS
- [Design Principles](/docs/native/getting-started/design-principles) - Understanding HeroUI's design philosophy

---

## Animation

All animations in HeroUI Native are built with [react-native-reanimated](https://docs.swmansion.com/react-native-reanimated/) and gesture control is handled by [react-native-gesture-handler](https://docs.swmansion.com/react-native-gesture-handler/). It's worth familiarizing yourself with these libraries if you want more control over animations.

### The `animation` Prop

Every animated component in HeroUI Native exposes a single `animation` prop that controls all animations for that component. This prop allows you to modify animation values, timing configurations, layout animations, or completely disable animations.

**Approach**: If you're working with animations, first look for the `animation` prop on the component you're using.

### Modifying Animations

You can customize animations by passing an object to the `animation` prop. Each component exposes different animation properties that you can modify. The approach is simple: if you want to slightly change the animation behavior of already written animations in components, we provide all necessary values for modification. If you want to write your own animations without relying on our written ones, you must create your own custom components with animations.

#### Example 1: Simple Value Modification

Modify animation values like scale, opacity, or colors:

```tsx
import {Switch} from 'heroui-native';

<Switch
  animation={{
    scale: {
      value: [1, 0.9], // [unpressed, pressed]
    },
    backgroundColor: {
      value: ['#172554', '#eab308'], // [unselected, selected]
    },
  }}>
  <Switch.Thumb />
</Switch>;
```

#### Example 2: Timing Configuration

Customize animation timing and easing:

```tsx
import {Accordion} from 'heroui-native';

<Accordion>
  <Accordion.Item value="1">
    <Accordion.Trigger>
      <Accordion.Indicator
        animation={{
          rotation: {
            value: [0, -180],
            springConfig: {
              damping: 60,
              stiffness: 900,
              mass: 3,
            },
          },
        }}
      />
    </Accordion.Trigger>
  </Accordion.Item>
</Accordion>;
```

#### Example 3: Layout Animations (Entering/Exiting)

Customize entering and exiting animations using Reanimated's layout animations:

```tsx
import {Accordion} from 'heroui-native';
import {FadeInRight, FadeInLeft, ZoomIn} from 'react-native-reanimated';
import {Easing} from 'react-native-reanimated';

<Accordion>
  <Accordion.Item value="1">
    <Accordion.Content
      animation={{
        entering: {
          value: FadeInRight.delay(50).easing(Easing.inOut(Easing.ease)),
        },
      }}>
      Content here
    </Accordion.Content>
  </Accordion.Item>
</Accordion>;
```

#### Example 4: State Prop for Granular Control

The `state` prop allows you to disable animations while still customizing animation properties. This is useful when you want to fine-tune component behavior without enabling animations:

```tsx
import {Switch} from 'heroui-native';

<Switch
  animation={{
    state: 'disabled', // Disable animations but allow property customization
    scale: {
      value: 0.95, // This value is still respected even though animations are disabled
    },
    backgroundColor: {
      value: ['#172554', '#eab308'],
    },
  }}>
  <Switch.Thumb />
</Switch>
```

The `state` prop accepts:
- `'disabled'`: Disable animations while allowing property customization
- `'disable-all'`: Disable all animations including children (only available at root level)
- `boolean`: Simple enable/disable control (`true` enables, `false` disables)

This provides more granular control over animation behavior, allowing you to customize properties without enabling animations.

### Disabling Animations

You can disable animations at different levels using the `animation` prop.

#### Disable Options

- `animation={false}` or `animation="disabled"`: Disable animations for the specific component only
- `animation="disable-all"`: Disable all animations including children (only available at root level)
- `animation={true}` or `animation={undefined}`: Use default animations

#### Component-Level Disabling

Disable animations for a specific component:

```tsx
<Switch>
  <Switch.Thumb animation={false} />
</Switch>
```

#### Root-Level Disabling (`disable-all`)

The `"disable-all"` option is only available at the root level of compound components. When used, it disables all animations including children, even if those children are not part of the compound component structure:

```tsx
// Disables all animations including Button components inside Card
<Card animation="disable-all">
  <Card.Body>
    <Card.Title>$450</Card.Title>
    <Card.Description>Living room Sofa</Card.Description>
  </Card.Body>
  <Card.Footer className="gap-3">
    <Button variant="primary">Buy now</Button>
    <Button variant="ghost">Add to cart</Button>
  </Card.Footer>
</Card>
```

**Important**: `"disable-all"` cascades down to all child components, including standalone components like `Button`, `Spinner`, etc., not just compound component parts.

### Global Animation Configuration

You can disable all HeroUI Native animations globally using the `HeroUINativeProvider`:

```tsx
import {HeroUINativeProvider} from 'heroui-native';

<HeroUINativeProvider
  config={{
    animation: 'disable-all',
  }}>
  <App />
</HeroUINativeProvider>;
```

This will disable all animations across your entire application, regardless of individual component `animation` prop settings.

### Accessibility

Reduce motion is handled automatically under the hood. When a user enables "Reduce Motion" in their device accessibility settings, all animations are automatically disabled globally. This is handled by the `GlobalAnimationSettingsProvider` which checks `useReducedMotion()` from react-native-reanimated.

You don't need to do anything - the library respects the user's accessibility preferences automatically.

### Animation State Management

We keep disabled state of animations under control internally to ensure they look nice without unpredictable lags or jumps. When animations are disabled, components immediately jump to their final state rather than animating, preventing visual glitches or intermediate states.

### Children Render Function

Many components support a render function pattern for children, which is particularly handy when working with state like `isSelected`:

```tsx
import {Switch} from 'heroui-native';

<Switch
  isSelected={isSelected}
  onSelectedChange={setIsSelected}>
  {({isSelected, isDisabled}) => (
    <Switch.Thumb>{isSelected ? <CheckIcon /> : <XIcon />}</Switch.Thumb>
  )}
</Switch>;
```

This pattern allows you to conditionally render content based on component state, making it easy to create dynamic UIs that respond to selection, disabled states, and other component properties.

### Next Steps

- Learn about [Styling](/docs/native/getting-started/styling) approaches
- View [Theming](/docs/native/getting-started/theming) documentation
- Explore [Colors](/docs/native/getting-started/colors) documentation
