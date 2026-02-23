---
name: rn-heroui
description: >
  HeroUI Native component usage examples and implementation patterns for React Native.
  Use when building UI with HeroUI Native components, implementing specific component patterns,
  or needing code examples for: Button, CloseButton, Input, TextField, Label, Description,
  FieldError, Checkbox, RadioGroup, Select, SearchField, TextArea, InputOTP, ControlField,
  Alert, Spinner, Skeleton, SkeletonGroup, Accordion, Tabs, ListGroup, Dialog, BottomSheet,
  Popover, Toast, Card, Surface, Separator, Switch, Slider, Avatar, Chip, ScrollShadow,
  PressableFeedback. Also use for HeroUI Native setup, provider config, theming, styling,
  animation, colors, composition patterns, and portal usage.
  Triggers on: heroui-native component examples, "how to use Button/Card/Dialog/etc in heroui-native",
  HeroUI Native UI patterns, building screens with heroui-native, component API reference,
  form building with heroui-native, overlay/modal/sheet patterns.
---

# HeroUI Native Examples

Reference-based skill for HeroUI Native component implementation. Load only the reference file needed for the task.

## Component Index

Find the right reference file by component name:

| Component | Reference File |
|-----------|---------------|
| Button, CloseButton | [references/buttons.md](references/buttons.md) |
| Input, TextField, Label, Description, FieldError, InputOTP, ControlField | [references/forms-input.md](references/forms-input.md) |
| Checkbox, RadioGroup, Select, SearchField, TextArea | [references/forms-selection.md](references/forms-selection.md) |
| Alert, Spinner, Skeleton, SkeletonGroup | [references/feedback.md](references/feedback.md) |
| Accordion, Tabs, ListGroup | [references/navigation.md](references/navigation.md) |
| Dialog, BottomSheet, Popover, Toast | [references/overlays.md](references/overlays.md) |
| Card, Surface, Separator | [references/layout.md](references/layout.md) |
| Switch, Slider | [references/controls.md](references/controls.md) |
| Avatar, Chip, ScrollShadow | [references/data-display.md](references/data-display.md) |
| Provider, Setup, global.css | [references/setup-guide.md](references/setup-guide.md) |
| Theming, Colors, Styling, Animation | [references/theming-styling.md](references/theming-styling.md) |

## How to Use

1. Identify which component(s) the user needs
2. Read ONLY the reference file(s) for those components
3. Use the examples as-is or adapt them to the user's specific needs
4. For multi-component UIs, read multiple reference files as needed

## Key Patterns

All HeroUI Native components follow these patterns:

```tsx
// Import from heroui-native
import { Button, Card, Dialog } from 'heroui-native';

// Compound component pattern with dot notation
<Card>
  <Card.Header />
  <Card.Body>
    <Card.Title>Title</Card.Title>
    <Card.Description>Desc</Card.Description>
  </Card.Body>
  <Card.Footer />
</Card>

// Styling with Tailwind className
<Button className="bg-accent px-6" variant="primary">
  <Button.Label className="text-accent-foreground">Click</Button.Label>
</Button>

// asChild for composition
<Dialog.Trigger asChild>
  <Button variant="secondary">Open</Button>
</Dialog.Trigger>

// Controlled state
const [isOpen, setIsOpen] = useState(false);
<Dialog isOpen={isOpen} onOpenChange={setIsOpen}>...</Dialog>
```

## Critical Rules

- Always wrap app with `HeroUINativeProvider` inside `GestureHandlerRootView`
- Import styles in global.css: `@import 'heroui-native/styles'`
- Text colors go on `Button.Label` / `Chip.Label`, NOT on root component
- Use `withUniwind(IconComponent)` to style third-party icons with className
- Overlay components (Dialog, BottomSheet, Popover) need Portal + Overlay pattern
- Use `cn()` utility from heroui-native for conditional className merging
- Variants: `primary` (main action), `secondary` (alternative), `tertiary` (dismissive), `danger` (destructive)
- Sizes: `sm`, `md`, `lg` across all components
