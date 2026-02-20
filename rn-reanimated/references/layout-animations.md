# React Native Reanimated - Layout Animations

> **Complete Guide for LLMs** | Version: Reanimated 4.x | Last Updated: 2026-02-20

---

## Table of Contents

1. [Overview](#overview)
2. [Entering Animations](#entering-animations)
3. [Exiting Animations](#exiting-animations)
4. [Layout Transitions](#layout-transitions)
5. [Default Animations](#default-animations)
6. [Custom Animations](#custom-animations)
7. [Keyframe Animations](#keyframe-animations)
8. [List Layout Animations](#list-layout-animations)
9. [Shared Element Transitions](#shared-element-transitions)
10. [Best Practices](#best-practices)

---

## Overview

Layout Animations in Reanimated allow you to animate:
- **Entering**: How components appear when mounted
- **Exiting**: How components disappear when unmounted
- **Layout Transitions**: How components animate when their layout changes

```typescript
import Animated, {
  // Default animations
  FadeIn, FadeInUp, FadeInDown, FadeInLeft, FadeInRight,
  FadeOut, FadeOutUp, FadeOutDown, FadeOutLeft, FadeOutRight,
  
  // Slide animations
  SlideInLeft, SlideInRight, SlideInUp, SlideInDown,
  SlideOutLeft, SlideOutRight, SlideOutUp, SlideOutDown,
  
  // Zoom animations
  ZoomIn, ZoomInRotate, ZoomInEasyUp, ZoomInEasyDown,
  ZoomOut, ZoomOutRotate, ZoomOutEasyUp, ZoomOutEasyDown,
  
  // Bounce animations
  BounceIn, BounceInUp, BounceInDown, BounceInLeft, BounceInRight,
  BounceOut, BounceOutUp, BounceOutDown, BounceOutLeft, BounceOutRight,
  
  // Flip animations
  FlipInX, FlipInY, FlipInEasyX, FlipInEasyY,
  FlipOutX, FlipOutY, FlipOutEasyX, FlipOutEasyY,
  
  // Light speed animations
  LightSpeedInLeft, LightSpeedInRight,
  LightSpeedOutLeft, LightSpeedOutRight,
  
  // Pinwheel animations
  PinwheelIn, PinwheelOut,
  
  // Roll animations
  RollInLeft, RollInRight,
  RollOutLeft, RollOutRight,
  
  // Rotate animations
  RotateIn, RotateInDownLeft, RotateInDownRight,
  RotateOut, RotateOutDownLeft, RotateOutDownRight,
  
  // Stretch animations
  StretchInX, StretchInY,
  StretchOutX, StretchOutY,
  
  // Layout transitions
  Layout,
  CurvedTransition,
  EntryExitTransition,
  FadingTransition,
  JumpingTransition,
  LinearTransition,
  SequencedTransition,
} from 'react-native-reanimated';
```

---

## Entering Animations

Applied when a component is first rendered.

### Basic Usage

```typescript
import Animated, { FadeIn, SlideInUp } from 'react-native-reanimated';

function App() {
  const [visible, setVisible] = useState(false);

  return (
    <View>
      <Button title="Show" onPress={() => setVisible(true)} />
      {visible && (
        <Animated.View entering={FadeIn} style={styles.box}>
          <Text>Hello!</Text>
        </Animated.View>
      )}
    </View>
  );
}
```

### With Configuration

```typescript
<Animated.View
  entering={FadeIn.duration(500).delay(200).springify()}
  style={styles.box}
>
  <Text>Animated Entry!</Text>
</Animated.View>
```

### Available Modifiers

```typescript
// Duration (ms)
FadeIn.duration(500)

// Delay (ms)
FadeIn.delay(200)

// Spring physics
FadeIn.springify().damping(10).stiffness(100)

// With callback
FadeIn.withCallback((finished) => {
  console.log('Animation finished:', finished);
})

// Random delay
FadeIn.randomDelay()

// Combine modifiers
FadeIn
  .duration(500)
  .delay(200)
  .springify()
  .damping(15)
```

### Complete Example

```typescript
import React, { useState } from 'react';
import { View, Button, StyleSheet } from 'react-native';
import Animated, { 
  FadeIn, 
  SlideInUp, 
  ZoomIn,
  BounceIn 
} from 'react-native-reanimated';

function EnteringAnimationsDemo() {
  const [items, setItems] = useState<string[]>([]);

  const addItem = () => {
    setItems([...items, `Item ${items.length + 1}`]);
  };

  const clearItems = () => {
    setItems([]);
  };

  return (
    <View style={styles.container}>
      <View style={styles.buttonRow}>
        <Button title="Add Item" onPress={addItem} />
        <Button title="Clear" onPress={clearItems} />
      </View>
      
      {items.map((item, index) => (
        <Animated.View
          key={item}
          entering={SlideInUp
            .delay(index * 100)
            .springify()
            .damping(12)
          }
          style={styles.item}
        >
          <Text>{item}</Text>
        </Animated.View>
      ))}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },
  buttonRow: {
    flexDirection: 'row',
    gap: 10,
    marginBottom: 20,
  },
  item: {
    padding: 16,
    backgroundColor: '#E8E8E8',
    borderRadius: 8,
    marginBottom: 8,
  },
});
```

---

## Exiting Animations

Applied when a component is removed from the tree.

### Basic Usage

```typescript
import Animated, { FadeOut, SlideOutDown } from 'react-native-reanimated';

function App() {
  const [visible, setVisible] = useState(true);

  return (
    <View>
      <Button title="Hide" onPress={() => setVisible(false)} />
      {visible && (
        <Animated.View exiting={FadeOut} style={styles.box}>
          <Text>Goodbye!</Text>
        </Animated.View>
      )}
    </View>
  );
}
```

### With Configuration

```typescript
<Animated.View
  exiting={SlideOutDown.duration(300).springify()}
  style={styles.box}
>
  <Text>Animated Exit!</Text>
</Animated.View>
```

### Complete Example

```typescript
import React, { useState } from 'react';
import { View, Button, StyleSheet } from 'react-native';
import Animated, { 
  FadeOut, 
  SlideOutRight, 
  ZoomOut,
  BounceOut 
} from 'react-native-reanimated';

function ExitingAnimationsDemo() {
  const [items, setItems] = useState(['Item 1', 'Item 2', 'Item 3']);

  const removeItem = (index: number) => {
    const newItems = [...items];
    newItems.splice(index, 1);
    setItems(newItems);
  };

  return (
    <View style={styles.container}>
      {items.map((item, index) => (
        <Animated.View
          key={item}
          entering={FadeIn.delay(index * 100)}
          exiting={SlideOutRight.duration(300)}
          style={styles.item}
        >
          <Text>{item}</Text>
          <Button title="Remove" onPress={() => removeItem(index)} />
        </Animated.View>
      ))}
    </View>
  );
}
```

---

## Layout Transitions

Animate layout changes (position, size) automatically.

### Basic Usage

```typescript
import Animated, { Layout } from 'react-native-reanimated';

<Animated.View layout={Layout} style={styles.box}>
  {/* Content that changes size */}
</Animated.View>
```

### With Configuration

```typescript
<Animated.View
  layout={Layout
    .springify()
    .damping(10)
    .stiffness(100)
  }
  style={styles.box}
>
  {/* Content */}
</Animated.View>
```

### Complete Example

```typescript
import React, { useState } from 'react';
import { View, Button, StyleSheet } from 'react-native';
import Animated, { Layout } from 'react-native-reanimated';

function LayoutTransitionDemo() {
  const [expanded, setExpanded] = useState(false);

  return (
    <View style={styles.container}>
      <Button
        title={expanded ? 'Collapse' : 'Expand'}
        onPress={() => setExpanded(!expanded)}
      />
      
      <Animated.View
        layout={Layout.springify().damping(12)}
        style={[
          styles.box,
          { height: expanded ? 300 : 100 }
        ]}
      >
        <Text>This box animates its layout changes!</Text>
        {expanded && (
          <Animated.View
            entering={FadeIn}
            exiting={FadeOut}
            style={styles.extraContent}
          >
            <Text>Extra content that appears</Text>
          </Animated.View>
        )}
      </Animated.View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },
  box: {
    backgroundColor: '#B58DF1',
    borderRadius: 16,
    padding: 20,
    marginTop: 20,
  },
  extraContent: {
    marginTop: 20,
    padding: 16,
    backgroundColor: 'rgba(255,255,255,0.3)',
    borderRadius: 8,
  },
});
```

---

## Default Animations

### Fade Animations

```typescript
// Entering
FadeIn           // Opacity 0 → 1
FadeInUp         // Fade + translate from bottom
FadeInDown       // Fade + translate from top
FadeInLeft       // Fade + translate from left
FadeInRight      // Fade + translate from right

// Exiting
FadeOut          // Opacity 1 → 0
FadeOutUp        // Fade + translate up
FadeOutDown      // Fade + translate down
FadeOutLeft      // Fade + translate left
FadeOutRight     // Fade + translate right
```

### Slide Animations

```typescript
// Entering
SlideInLeft      // Slide from left
SlideInRight     // Slide from right
SlideInUp        // Slide from bottom
SlideInDown      // Slide from top

// Exiting
SlideOutLeft     // Slide to left
SlideOutRight    // Slide to right
SlideOutUp       // Slide to top
SlideOutDown     // Slide to bottom
```

### Zoom Animations

```typescript
// Entering
ZoomIn           // Scale 0 → 1
ZoomInRotate     // Scale + rotate
ZoomInEasyUp     // Scale with ease up
ZoomInEasyDown   // Scale with ease down

// Exiting
ZoomOut          // Scale 1 → 0
ZoomOutRotate    // Scale + rotate
ZoomOutEasyUp    // Scale with ease up
ZoomOutEasyDown  // Scale with ease down
```

### Bounce Animations

```typescript
// Entering
BounceIn         // Bounce scale
BounceInUp       // Bounce from bottom
BounceInDown     // Bounce from top
BounceInLeft     // Bounce from left
BounceInRight    // Bounce from right

// Exiting
BounceOut        // Bounce out
BounceOutUp      // Bounce out up
BounceOutDown    // Bounce out down
BounceOutLeft    // Bounce out left
BounceOutRight   // Bounce out right
```

### Flip Animations

```typescript
// Entering
FlipInX          // Flip around X axis
FlipInY          // Flip around Y axis
FlipInEasyX      // Easy flip X
FlipInEasyY      // Easy flip Y

// Exiting
FlipOutX         // Flip out X
FlipOutY         // Flip out Y
FlipOutEasyX     // Easy flip out X
FlipOutEasyY     // Easy flip out Y
```

### Rotate Animations

```typescript
// Entering
RotateIn         // Rotate in
RotateInDownLeft // Rotate in from down-left
RotateInDownRight// Rotate in from down-right

// Exiting
RotateOut        // Rotate out
RotateOutDownLeft// Rotate out to down-left
RotateOutDownRight// Rotate out to down-right
```

---

## Custom Animations

Create custom entering/exiting animations using the animation builder.

### Using BaseAnimationBuilder

```typescript
import { 
  BaseAnimationBuilder,
  EntryExitAnimationType 
} from 'react-native-reanimated';

const CustomEntering = BaseAnimationBuilder
  .duration(500)
  .withCallback((finished) => {
    console.log('Custom animation finished:', finished);
  })
  .buildEntryExitAnimation(
    // Initial values
    {
      initialValues: {
        opacity: 0,
        transform: [
          { translateY: 100 },
          { scale: 0.5 },
          { rotate: '-45deg' },
        ],
      },
      // Animation to final values
      animations: {
        opacity: 1,
        transform: [
          { translateY: 0 },
          { scale: 1 },
          { rotate: '0deg' },
        ],
      },
    },
    EntryExitAnimationType.ENTRY
  );

// Usage
<Animated.View entering={CustomEntering}>
  <Text>Custom Animation!</Text>
</Animated.View>
```

### Custom Exiting Animation

```typescript
const CustomExiting = BaseAnimationBuilder
  .duration(300)
  .buildEntryExitAnimation(
    {
      initialValues: {
        opacity: 1,
        transform: [{ scale: 1 }],
      },
      animations: {
        opacity: 0,
        transform: [{ scale: 1.5 }], // Grow while fading
      },
    },
    EntryExitAnimationType.EXIT
  );
```

### Custom Layout Transition

```typescript
import { CurvedTransition } from 'react-native-reanimated';

const CustomLayout = CurvedTransition
  .duration(500)
  .easingX(Easing.inOut(Easing.ease))
  .easingY(Easing.inOut(Easing.ease));

// Usage
<Animated.View layout={CustomLayout}>
  {/* Content */}
</Animated.View>
```

---

## Keyframe Animations

Create complex multi-step animations.

### Basic Usage

```typescript
import { Keyframe } from 'react-native-reanimated';

const keyframe = new Keyframe({
  0: {
    opacity: 0,
    transform: [{ translateY: 50 }, { scale: 0.5 }],
  },
  50: {
    opacity: 0.5,
    transform: [{ translateY: -10 }, { scale: 1.1 }],
  },
  100: {
    opacity: 1,
    transform: [{ translateY: 0 }, { scale: 1 }],
  },
});

// Usage
<Animated.View entering={keyframe.duration(1000)}>
  <Text>Keyframe Animation!</Text>
</Animated.View>
```

### With Spring

```typescript
const springKeyframe = new Keyframe({
  0: {
    opacity: 0,
    transform: [{ translateX: -100 }],
  },
  100: {
    opacity: 1,
    transform: [{ translateX: 0 }],
  },
}).springify().damping(10);
```

### Complete Example

```typescript
import { Keyframe } from 'react-native-reanimated';

const wobbleKeyframe = new Keyframe({
  0: {
    transform: [{ rotate: '0deg' }],
  },
  25: {
    transform: [{ rotate: '-5deg' }],
  },
  50: {
    transform: [{ rotate: '5deg' }],
  },
  75: {
    transform: [{ rotate: '-3deg' }],
  },
  100: {
    transform: [{ rotate: '0deg' }],
  },
}).duration(500);

function WobbleButton() {
  const [wobble, setWobble] = useState(false);

  return (
    <Animated.View
      entering={wobble ? wobbleKeyframe : undefined}
      style={styles.button}
    >
      <Button
        title="Wobble"
        onPress={() => {
          setWobble(false);
          setTimeout(() => setWobble(true), 0);
        }}
      />
    </Animated.View>
  );
}
```

---

## List Layout Animations

Special handling for FlatList and ScrollView items.

### Basic FlatList Animation

```typescript
import Animated, { Layout } from 'react-native-reanimated';

function AnimatedList() {
  const [items, setItems] = useState([1, 2, 3, 4, 5]);

  const renderItem = ({ item }: { item: number }) => (
    <Animated.View
      entering={FadeInUp}
      exiting={FadeOut}
      layout={Layout.springify()}
      style={styles.item}
    >
      <Text>Item {item}</Text>
    </Animated.View>
  );

  return (
    <Animated.FlatList
      data={items}
      renderItem={renderItem}
      keyExtractor={(item) => item.toString()}
      itemLayoutAnimation={Layout.springify()} // List-level layout
    />
  );
}
```

### Skipping Items

```typescript
<Animated.FlatList
  data={items}
  renderItem={renderItem}
  skipEnteringExitingAnimations // Skip for initial render
/>
```

### Custom Cell Renderer

```typescript
const CellRenderer = (props) => {
  return (
    <Animated.View
      entering={FadeIn}
      exiting={FadeOut}
      layout={Layout.springify()}
      {...props}
    />
  );
};

<FlatList
  data={items}
  renderItem={renderItem}
  CellRendererComponent={CellRenderer}
/>
```

---

## Shared Element Transitions

Animate elements between screens (requires React Navigation).

### Setup

```typescript
import { createNativeStackNavigator } from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

function App() {
  return (
    <Stack.Navigator>
      <Stack.Screen
        name="List"
        component={ListScreen}
        options={{ headerShown: false }}
      />
      <Stack.Screen
        name="Detail"
        component={DetailScreen}
        options={{ headerShown: false }}
      />
    </Stack.Navigator>
  );
}
```

### Basic Usage

```typescript
// ListScreen.tsx
import Animated from 'react-native-reanimated';

function ListScreen({ navigation }) {
  return (
    <View>
      {items.map((item) => (
        <TouchableOpacity
          key={item.id}
          onPress={() => navigation.navigate('Detail', { item })}
        >
          <Animated.View
            sharedTransitionTag={`item-${item.id}`}
            style={styles.card}
          >
            <Animated.Image
              sharedTransitionTag={`image-${item.id}`}
              source={item.image}
              style={styles.image}
            />
            <Text>{item.title}</Text>
          </Animated.View>
        </TouchableOpacity>
      ))}
    </View>
  );
}

// DetailScreen.tsx
function DetailScreen({ route }) {
  const { item } = route.params;

  return (
    <View>
      <Animated.View
        sharedTransitionTag={`item-${item.id}`}
        style={styles.detailCard}
      >
        <Animated.Image
          sharedTransitionTag={`image-${item.id}`}
          source={item.image}
          style={styles.detailImage}
        />
        <Text style={styles.title}>{item.title}</Text>
        <Text>{item.description}</Text>
      </Animated.View>
    </View>
  );
}
```

### Custom Transition

```typescript
import { SharedTransition } from 'react-native-reanimated';

const customTransition = SharedTransition.custom((values) => {
  'worklet';
  return {
    height: withSpring(values.targetHeight),
    width: withSpring(values.targetWidth),
    originX: withSpring(values.targetOriginX),
    originY: withSpring(values.targetOriginY),
  };
});

// Usage
<Animated.View
  sharedTransitionTag="item-1"
  sharedTransitionStyle={customTransition}
>
  {/* Content */}
</Animated.View>
```

---

## Best Practices

### DO's

✅ **Use layout animations for list reordering**
```typescript
<Animated.FlatList
  data={sortedItems}
  renderItem={renderItem}
  itemLayoutAnimation={Layout.springify()}
/>
```

✅ **Combine entering/exiting with layout**
```typescript
<Animated.View
  entering={FadeIn}
  exiting={FadeOut}
  layout={Layout.springify()}
>
  {/* Content */}
</Animated.View>
```

✅ **Use staggered delays for lists**
```typescript
{items.map((item, index) => (
  <Animated.View
    key={item.id}
    entering={FadeIn.delay(index * 50)}
  >
    {/* Content */}
  </Animated.View>
))}
```

✅ **Handle reduced motion**
```typescript
import { useReducedMotion } from 'react-native-reanimated';

function Component() {
  const reducedMotion = useReducedMotion();
  
  return (
    <Animated.View
      entering={reducedMotion ? undefined : FadeIn}
    >
      {/* Content */}
    </Animated.View>
  );
}
```

### DON'Ts

❌ **Don't use layout animations on every render**
```typescript
// Bad - causes unnecessary animations
<Animated.View layout={Layout}>
  <Text>{dynamicValue}</Text> {/* Changes every render */}
</Animated.View>
```

❌ **Don't forget unique keys**
```typescript
// Bad - React can't track items properly
{items.map((item) => (
  <Animated.View key={Math.random()}> {/* ❌ Random key! */}
    {/* Content */}
  </Animated.View>
))}

// Good - stable unique keys
{items.map((item) => (
  <Animated.View key={item.id}> {/* ✅ Stable ID */}
    {/* Content */}
  </Animated.View>
))}
```

❌ **Don't animate too many items simultaneously**
```typescript
// Bad - performance issue with many items
{thousandsOfItems.map((item, index) => (
  <Animated.View entering={FadeIn.delay(index * 10)}>
    {/* Too many simultaneous animations */}
  </Animated.View>
))}
```

---

## Quick Reference Card

```typescript
// Entering animations
<Animated.View entering={FadeIn} />
<Animated.View entering={SlideInUp.duration(500)} />
<Animated.View entering={ZoomIn.springify()} />

// Exiting animations
<Animated.View exiting={FadeOut} />
<Animated.View exiting={SlideOutDown} />

// Layout transitions
<Animated.View layout={Layout} />
<Animated.View layout={Layout.springify().damping(10)} />

// Combined
<Animated.View
  entering={FadeIn}
  exiting={FadeOut}
  layout={Layout}
>
  {/* Content */}
</Animated.View>

// Keyframes
const keyframe = new Keyframe({
  0: { opacity: 0 },
  100: { opacity: 1 },
});

// Shared elements
<Animated.View sharedTransitionTag="unique-id" />
```
