# React Native Reanimated - Worklets Deep Dive

> **Complete Guide for LLMs** | Version: Reanimated 4.x / Worklets 0.7.x | Last Updated: 2026-02-20

---

## Table of Contents

1. [What are Worklets?](#what-are-worklets)
2. [The 'worklet' Directive](#the-worklet-directive)
3. [Worklet Runtime](#worklet-runtime)
4. [Thread Communication](#thread-communication)
5. [Closure Capture](#closure-capture)
6. [Worklet Scopes](#worklet-scopes)
7. [Creating Custom Worklets](#creating-custom-worklets)
8. [Worklet Best Practices](#worklet-best-practices)
9. [Common Pitfalls](#common-pitfalls)
10. [Advanced Topics](#advanced-topics)

---

## What are Worklets?

Worklets are **small JavaScript functions that run synchronously on the UI thread**. They are the foundation of Reanimated's performance, enabling smooth 60fps animations by bypassing the React Native bridge.

### Key Characteristics

| Feature | Description |
|---------|-------------|
| Thread | Runs on UI thread, not JS thread |
| Synchronous | Executes immediately without bridge delay |
| Lightweight | Minimal overhead for each execution |
| Closure Capture | Automatically captures referenced variables |
| Babel Transformed | Converted to worklets at build time |

### Visual Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    JAVASCRIPT THREAD                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  React       │  │  Worklet     │  │  Babel       │      │
│  │  Components  │  │  Definitions │  │  Plugin      │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ Build Time Transformation
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      UI THREAD (WORKLET)                    │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Worklet     │  │  Shared      │  │  Animation   │      │
│  │  Runtime     │  │  Values      │  │  Engine      │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

---

## The 'worklet' Directive

The `'worklet'` directive marks a function to be processed by the Babel plugin.

### Basic Syntax

```typescript
function myWorklet() {
  'worklet';
  // This code runs on UI thread
  console.log('Hello from UI thread!');
}
```

### Rules

1. **Must be first statement**
```typescript
// ✅ Correct
function worklet() {
  'worklet';
  // ...
}

// ❌ Incorrect
function notWorklet() {
  console.log('first');
  'worklet'; // Too late!
}
```

2. **Must be a string literal**
```typescript
// ✅ Correct
'worklet';

// ❌ Incorrect
const directive = 'worklet'; // Won't work
```

3. **Arrow functions supported**
```typescript
const myWorklet = () => {
  'worklet';
  // ...
};
```

### Where 'worklet' is Automatically Added

The Babel plugin automatically adds `'worklet'` to:

```typescript
// 1. useAnimatedStyle callback
const style = useAnimatedStyle(() => {
  // 'worklet' added automatically
  return { transform: [{ translateX: offset.value }] };
});

// 2. useAnimatedProps callback
const props = useAnimatedProps(() => {
  // 'worklet' added automatically
  return { opacity: opacity.value };
});

// 3. Gesture handler callbacks
const gesture = Gesture.Tap()
  .onBegin(() => {
    // 'worklet' added automatically
    scale.value = withSpring(1.2);
  });

// 4. useAnimatedScrollHandler callbacks
const scrollHandler = useAnimatedScrollHandler({
  onScroll: (event) => {
    // 'worklet' added automatically
    scrollY.value = event.contentOffset.y;
  },
});

// 5. useDerivedValue callback
const derived = useDerivedValue(() => {
  // 'worklet' added automatically
  return offset.value * 2;
});

// 6. useAnimatedReaction callbacks
useAnimatedReaction(
  () => {
    // 'worklet' added automatically
    return offset.value;
  },
  (current) => {
    // 'worklet' added automatically
    if (current > 100) {
      runOnJS(callback)();
    }
  }
);
```

### Manual 'worklet' Required

```typescript
// Standalone worklet functions
function calculatePosition() {
  'worklet';
  return { x: offset.value, y: offset.value };
}

// Functions passed to runOnUI
runOnUI(() => {
  'worklet';
  offset.value = withSpring(100);
})();

// Object methods (if workletized)
const calculator = {
  add(a: number, b: number) {
    'worklet';
    return a + b;
  },
};
```

---

## Worklet Runtime

### Runtime Kinds

```typescript
enum RuntimeKind {
  RN_RUNTIME = 1,    // React Native JS thread
  UI_RUNTIME = 2,    // Reanimated UI thread
  WORKLET_RUNTIME = 3, // Custom worklet runtime
}
```

### Checking Runtime

```typescript
import { getRuntimeKind, isUIRuntime, isRNRuntime } from 'react-native-worklets';

function checkRuntime() {
  'worklet';
  
  if (isUIRuntime()) {
    console.log('Running on UI thread');
  }
  
  if (isRNRuntime()) {
    console.log('Running on JS thread');
  }
  
  const kind = getRuntimeKind();
  console.log('Runtime kind:', kind); // 1, 2, or 3
}
```

### Runtime Capabilities

| Feature | JS Runtime | UI Runtime |
|---------|------------|------------|
| React Hooks | ✅ | ❌ |
| Shared Values | ✅ (read/write) | ✅ (read/write) |
| Animation Functions | ✅ | ✅ |
| setState | ✅ | ❌ (use runOnJS) |
| console.log | ✅ | ✅ |
| Date, Math, JSON | ✅ | ✅ |
| Timers (setTimeout) | ✅ | Limited |
| requestAnimationFrame | ❌ | ✅ |

---

## Thread Communication

### JS to UI Thread

```typescript
import { runOnUI } from 'react-native-reanimated';

// Define worklet
const uiWorklet = (value: number) => {
  'worklet';
  offset.value = withSpring(value);
  console.log('Executed on UI thread');
};

// Execute from JS thread
runOnUI(uiWorklet)(100);

// Inline worklet
runOnUI(() => {
  'worklet';
  offset.value = withTiming(200);
})();
```

### UI to JS Thread

```typescript
import { runOnJS } from 'react-native-reanimated';

// JS function
const jsFunction = (result: number) => {
  // Runs on JS thread
  console.log('Result:', result);
  setState(result);
};

// In worklet
const worklet = () => {
  'worklet';
  const result = someCalculation();
  runOnJS(jsFunction)(result);
};
```

### Synchronous Execution

```typescript
import { runOnUISync, executeOnUIRuntimeSync } from 'react-native-worklets';

// Synchronously run on UI thread
const result = runOnUISync(() => {
  'worklet';
  return sharedValue.value;
});

// Execute on UI runtime
executeOnUIRuntimeSync(() => {
  'worklet';
  // Do something synchronously
});
```

### Scheduling

```typescript
import { 
  scheduleOnUI, 
  scheduleOnRN,
  scheduleOnRuntime 
} from 'react-native-worklets';

// Schedule on UI thread
scheduleOnUI(() => {
  'worklet';
  // Runs on next UI frame
});

// Schedule on JS thread
scheduleOnRN(() => {
  // Runs on next JS frame
});

// Schedule on specific runtime
const runtime = createWorkletRuntime('my-runtime');
scheduleOnRuntime(runtime, () => {
  'worklet';
  // Runs on custom runtime
});
```

---

## Closure Capture

Worklets automatically capture referenced variables from the outer scope.

### Automatic Capture

```typescript
function Example() {
  const offset = useSharedValue(0);
  const config = { damping: 10, stiffness: 100 };
  const multiplier = 2;

  const style = useAnimatedStyle(() => {
    // offset, config, and multiplier are automatically captured
    return {
      transform: [
        { translateX: offset.value * multiplier },
      ],
    };
  });
}
```

### What Gets Captured

```typescript
// ✅ Captured automatically
const sharedValue = useSharedValue(0);
const number = 42;
const string = 'hello';
const boolean = true;
const object = { x: 0, y: 0 };
const array = [1, 2, 3];
const functionRef = someFunction;

// ❌ Not captured (or causes issues)
let mutable = 0; // Mutable variables may not update
const component = <View />; // JSX not supported
const classInstance = new MyClass(); // Class instances limited
```

### Capture by Copy vs Reference

```typescript
function Example() {
  let counter = 0; // Primitive - captured by copy
  const obj = { count: 0 }; // Object - captured by reference
  const sv = useSharedValue(0); // SharedValue - synchronized

  const worklet = () => {
    'worklet';
    
    counter++; // Won't affect outer counter!
    obj.count++; // Will affect outer object
    sv.value++; // Will affect shared value
  };
}
```

### Forcing Capture with `worklet`

```typescript
// Use IIFE to capture current value
const currentValue = (() => {
  const value = someVariable;
  return () => {
    'worklet';
    return value; // Captures the value at creation time
  };
})();
```

---

## Worklet Scopes

### Component-Level Worklets

```typescript
function MyComponent() {
  const offset = useSharedValue(0);

  // Worklet defined in component scope
  const animateTo = (target: number) => {
    'worklet';
    offset.value = withSpring(target);
  };

  // Use in gesture
  const gesture = Gesture.Tap()
    .onEnd(() => {
      'worklet';
      animateTo(100);
    });
}
```

### Module-Level Worklets

```typescript
// worklets.ts
export const easingWorklet = (t: number) => {
  'worklet';
  return t * t * (3 - 2 * t); // Smoothstep
};

export const lerp = (a: number, b: number, t: number) => {
  'worklet';
  return a + (b - a) * t;
};

// Component.tsx
import { easingWorklet, lerp } from './worklets';

const style = useAnimatedStyle(() => ({
  transform: [
    { 
      translateX: lerp(0, 100, easingWorklet(progress.value)) 
    },
  ],
}));
```

### Object with Worklets

```typescript
const animations = {
  fadeIn(opacity: SharedValue<number>) {
    'worklet';
    opacity.value = withTiming(1, { duration: 300 });
  },
  
  fadeOut(opacity: SharedValue<number>) {
    'worklet';
    opacity.value = withTiming(0, { duration: 300 });
  },
  
  slideIn(offset: SharedValue<number>, target: number) {
    'worklet';
    offset.value = withSpring(target, { damping: 15 });
  },
};

// Usage
animations.fadeIn(opacity);
animations.slideIn(offset, 100);
```

---

## Creating Custom Worklets

### Standalone Worklet Function

```typescript
// math.worklets.ts

export const clamp = (value: number, min: number, max: number) => {
  'worklet';
  return Math.min(Math.max(value, min), max);
};

export const mapRange = (
  value: number,
  inMin: number,
  inMax: number,
  outMin: number,
  outMax: number
) => {
  'worklet';
  return ((value - inMin) * (outMax - outMin)) / (inMax - inMin) + outMin;
};

export const smoothstep = (edge0: number, edge1: number, x: number) => {
  'worklet';
  const t = clamp((x - edge0) / (edge1 - edge0), 0, 1);
  return t * t * (3 - 2 * t);
};
```

### Worklet Class (Pattern)

```typescript
class AnimationController {
  private offset: SharedValue<number>;
  private velocity: SharedValue<number>;

  constructor(initialValue: number) {
    this.offset = useSharedValue(initialValue);
    this.velocity = useSharedValue(0);
  }

  animateTo(target: number) {
    'worklet';
    this.offset.value = withSpring(target, {
      velocity: this.velocity.value,
    });
  }

  decay(velocity: number) {
    'worklet';
    this.offset.value = withDecay({
      velocity,
      clamp: [0, 300],
    });
  }

  getValue() {
    'worklet';
    return this.offset.value;
  }
}
```

### Higher-Order Worklet

```typescript
// Create a reusable animation worklet
const createSpringAnimation = (
  stiffness: number,
  damping: number
) => {
  return (value: SharedValue<number>, target: number) => {
    'worklet';
    value.value = withSpring(target, { stiffness, damping });
  };
};

// Usage
const gentleSpring = createSpringAnimation(50, 15);
const snappySpring = createSpringAnimation(300, 20);

gentleSpring(offset, 100);
snappySpring(scale, 1.5);
```

---

## Worklet Best Practices

### DO's

✅ **Keep worklets small and focused**
```typescript
// Good - single responsibility
const calculatePosition = () => {
  'worklet';
  return { x: offsetX.value, y: offsetY.value };
};
```

✅ **Use shared values for state**
```typescript
// Good - shared values are synchronized
const progress = useSharedValue(0);

const worklet = () => {
  'worklet';
  progress.value = withTiming(1);
};
```

✅ **Use runOnJS for JS-only operations**
```typescript
// Good - delegate to JS thread
const worklet = () => {
  'worklet';
  if (condition) {
    runOnJS(updateReactState)(newValue);
  }
};
```

✅ **Memoize worklet functions**
```typescript
// Good - stable reference
const animate = useCallback(() => {
  'worklet';
  offset.value = withSpring(100);
}, []);
```

✅ **Type your worklets**
```typescript
// Good - explicit types
const animateTo: (target: number) => void = (target) => {
  'worklet';
  offset.value = withSpring(target);
};
```

### DON'Ts

❌ **Don't use React hooks in worklets**
```typescript
// Bad - hooks don't work in worklets
const worklet = () => {
  'worklet';
  const [state, setState] = useState(0); // ❌ ERROR!
};
```

❌ **Don't mutate captured primitives**
```typescript
// Bad - won't update outer scope
let counter = 0;

const worklet = () => {
  'worklet';
  counter++; // ❌ Only increments local copy
};
```

❌ **Don't access DOM or native APIs**
```typescript
// Bad - not available in worklet
const worklet = () => {
  'worklet';
  document.getElementById('id'); // ❌ Not available!
  localStorage.getItem('key');   // ❌ Not available!
};
```

❌ **Don't create closures over large objects**
```typescript
// Bad - large closure overhead
const largeData = new Array(10000).fill(0);

const worklet = () => {
  'worklet';
  console.log(largeData[0]); // ❌ Copies entire array!
};
```

---

## Common Pitfalls

### Pitfall 1: Stale Closures

```typescript
function Example() {
  const [state, setState] = useState(0);
  const offset = useSharedValue(0);

  // ❌ Problem: state is captured at creation time
  const badWorklet = () => {
    'worklet';
    console.log(state); // Always logs initial value!
  };

  // ✅ Solution: Use shared values for reactive data
  const stateSv = useSharedValue(state);
  
  useEffect(() => {
    stateSv.value = state;
  }, [state]);

  const goodWorklet = () => {
    'worklet';
    console.log(stateSv.value); // Always current!
  };
}
```

### Pitfall 2: Calling JS Functions

```typescript
// ❌ Problem: Trying to call JS function from worklet
const jsFunction = () => {
  console.log('JS function');
};

const worklet = () => {
  'worklet';
  jsFunction(); // ❌ ERROR - function not workletized
};

// ✅ Solution: Use runOnJS
const worklet = () => {
  'worklet';
  runOnJS(jsFunction)();
};
```

### Pitfall 3: Async Operations

```typescript
// ❌ Problem: Async not supported in worklets
const worklet = async () => {
  'worklet';
  await something(); // ❌ Not supported!
};

// ✅ Solution: Move async to JS thread
const asyncWork = async () => {
  const result = await fetchData();
  runOnUI(() => {
    'worklet';
    offset.value = result;
  })();
};
```

### Pitfall 4: Error Handling

```typescript
// ❌ Problem: Errors in worklets can crash the app
const worklet = () => {
  'worklet';
  const result = someArray[100]; // May be undefined
  result.toString(); // ❌ Crash if undefined!
};

// ✅ Solution: Add guards
const worklet = () => {
  'worklet';
  const result = someArray[100];
  if (result !== undefined) {
    return result.toString();
  }
  return '';
};
```

---

## Advanced Topics

### Custom Worklet Runtime

```typescript
import { createWorkletRuntime } from 'react-native-worklets';

// Create custom runtime
const customRuntime = createWorkletRuntime('my-custom-runtime');

// Run code on custom runtime
runOnRuntime(customRuntime, () => {
  'worklet';
  // Runs on custom runtime
  console.log('Hello from custom runtime');
})();

// Schedule on custom runtime
scheduleOnRuntime(customRuntime, () => {
  'worklet';
  // Scheduled execution
});
```

### Worklet Event Loop

```typescript
import { callMicrotasks } from 'react-native-worklets';

// Manually process microtasks
const worklet = () => {
  'worklet';
  
  Promise.resolve().then(() => {
    console.log('Microtask');
  });
  
  callMicrotasks(); // Process pending microtasks
};
```

### Debugging Worklets

```typescript
// Add console logs
const worklet = () => {
  'worklet';
  console.log('Worklet executing');
  console.log('Offset:', offset.value);
  
  // Use console.warn for visibility
  console.warn('Warning from worklet');
};

// Check runtime
const worklet = () => {
  'worklet';
  if (__DEV__) {
    console.log('Runtime:', getRuntimeKind());
  }
};
```

### Performance Optimization

```typescript
// Pre-calculate constants
const PI_2 = Math.PI * 2;

const optimizedWorklet = () => {
  'worklet';
  // Use pre-calculated constant
  return Math.sin(progress.value * PI_2);
};

// Minimize closure capture
const efficientWorklet = (() => {
  const config = { damping: 10, stiffness: 100 };
  return () => {
    'worklet';
    offset.value = withSpring(target.value, config);
  };
})();
```

---

## Quick Reference Card

```typescript
// Basic worklet
const worklet = () => {
  'worklet';
  // UI thread code
};

// Thread communication
runOnUI(worklet)();
runOnJS(jsFunction)(args);
runOnUISync(() => { 'worklet'; return value; });

// Scheduling
scheduleOnUI(() => { 'worklet'; });
scheduleOnRN(() => { });

// Runtime info
isUIRuntime();
isRNRuntime();
getRuntimeKind();

// Custom runtime
const runtime = createWorkletRuntime('name');
runOnRuntime(runtime, () => { 'worklet'; })();

// Auto-worklet contexts
useAnimatedStyle(() => { });
useAnimatedProps(() => { });
useDerivedValue(() => { });
useAnimatedReaction(() => { }, () => { });
useAnimatedScrollHandler({ onScroll: () => { } });
Gesture.Tap().onBegin(() => { });
```

---

## Resources

- [Worklets Documentation](https://docs.swmansion.com/react-native-worklets/)
- [Reanimated Documentation](https://docs.swmansion.com/react-native-reanimated/)
- [Babel Plugin Configuration](https://docs.swmansion.com/react-native-reanimated/docs/guides/worklets/)
