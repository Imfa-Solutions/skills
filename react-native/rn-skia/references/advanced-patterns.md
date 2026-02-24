# Advanced Patterns & Real-World Examples

## Architecture Patterns

### 1. Component Composition

```tsx
const DrawingContext = createContext(null);

const DrawingProvider = ({ children }) => {
  const theme = useTheme();
  const dimensions = useWindowDimensions();
  return (
    <DrawingContext.Provider value={{ theme, dimensions }}>
      {children}
    </DrawingContext.Provider>
  );
};

// Atomic component
const AtomCircle = ({ cx, cy, r, color }) => {
  const { theme } = useContext(DrawingContext);
  return <Circle cx={cx} cy={cy} r={r} color={color || theme.primary} />;
};

// Molecular component
const MoleculeButton = ({ x, y, label }) => {
  const width = 100, height = 40;
  return (
    <Group transform={[{ translateX: x }, { translateY: y }]}>
      <RoundedRect x={0} y={0} width={width} height={height} r={8} color="#007AFF">
        <Shadow dx={0} dy={2} blur={4} color="rgba(0,0,0,0.2)" />
      </RoundedRect>
      <Text x={width / 2 - 20} y={height / 2 + 6} text={label} font={font} color="white" />
    </Group>
  );
};
```

### 2. Custom Hook Pattern

```tsx
export const useAnimatedChart = (data, options) => {
  const progress = useSharedValue(0);
  const selectedIndex = useSharedValue(-1);

  const animatedData = useDerivedValue(() => {
    return data.map((point, i) => ({
      ...point,
      value: point.value * progress.value,
      opacity: selectedIndex.value === -1 || selectedIndex.value === i ? 1 : 0.3,
    }));
  });

  const animate = useCallback(() => {
    progress.value = withSpring(1, { damping: 15 });
  }, []);

  const select = useCallback((index) => {
    selectedIndex.value = withSpring(index);
  }, []);

  return { animatedData, animate, select, progress };
};
```

### 3. Render Props Pattern

```tsx
const InteractiveCanvas = ({ children, onTouch }) => {
  const touchPosition = useSharedValue({ x: 0, y: 0 });

  const gesture = Gesture.Pan()
    .onBegin((e) => { touchPosition.value = { x: e.x, y: e.y }; onTouch?.('begin', touchPosition.value); })
    .onChange((e) => { touchPosition.value = { x: e.x, y: e.y }; onTouch?.('move', touchPosition.value); })
    .onEnd(() => { onTouch?.('end', touchPosition.value); });

  return (
    <GestureDetector gesture={gesture}>
      <Canvas style={{ flex: 1 }}>
        {children(touchPosition)}
      </Canvas>
    </GestureDetector>
  );
};

// Usage
<InteractiveCanvas onTouch={(phase, pos) => console.log(phase, pos)}>
  {(touchPos) => <Circle cx={touchPos.x} cy={touchPos.y} r={20} color="red" />}
</InteractiveCanvas>
```

## Real-World Examples

### Interactive Bar Chart

```tsx
const InteractiveBarChart = ({ data }) => {
  const selectedIndex = useSharedValue(-1);
  const barHeights = data.map(() => useSharedValue(0));

  useEffect(() => {
    barHeights.forEach((h, i) => {
      h.value = withDelay(i * 100, withSpring(data[i].value));
    });
  }, []);

  return (
    <Canvas style={{ height: 300 }}>
      <Group>
        {data.map((item, i) => {
          const x = i * 60 + 20;
          const isSelected = useDerivedValue(() => selectedIndex.value === i);
          const color = useDerivedValue(() =>
            isSelected.value ? "#007AFF" : "#C7C7CC"
          );

          return (
            <Group key={i} transform={[{ translateX: x }]}>
              <Rect x={0} y={300 - barHeights[i]} width={40} height={barHeights[i]} color={color}>
                <BlurMask blur={isSelected.value ? 10 : 0} />
              </Rect>
              <Text x={5} y={280} text={item.label} font={labelFont} color="#333" />
            </Group>
          );
        })}
      </Group>
    </Canvas>
  );
};
```

### Particle System with Atlas

```tsx
const ParticleSystem = ({ count = 100 }) => {
  const particles = useRef(
    Array.from({ length: count }, () => ({
      x: Math.random() * 400, y: Math.random() * 400,
      vx: (Math.random() - 0.5) * 2, vy: (Math.random() - 0.5) * 2,
      size: Math.random() * 4 + 2,
      color: `hsl(${Math.random() * 360}, 70%, 50%)`,
    }))
  ).current;

  const time = useClock();
  const sprite = useMemo(
    () => drawAsImage(<Circle cx={5} cy={5} r={5} color="white" />, { width: 10, height: 10 }),
    []
  );

  const transforms = useRSXformBuffer(count, (i, transform) => {
    "worklet";
    const p = particles[i];
    const t = time.value / 1000;
    p.x += p.vx; p.y += p.vy;
    if (p.x < 0 || p.x > 400) p.vx *= -1;
    if (p.y < 0 || p.y > 400) p.vy *= -1;
    const offsetX = Math.sin(t * 2 + i) * 10;
    const offsetY = Math.cos(t * 3 + i) * 10;
    transform.set(1, 0, p.x + offsetX, p.y + offsetY);
  });

  return (
    <Canvas style={{ width: 400, height: 400 }}>
      <Atlas image={sprite} sprites={[rect(0, 0, 10, 10)]} transforms={transforms}
        colors={particles.map(p => Skia.Color(p.color))} />
    </Canvas>
  );
};
```

### Drawing Canvas

```tsx
const DrawingCanvas = () => {
  const paths = useSharedValue([]);
  const currentPath = useSharedValue(null);
  const strokeWidth = useSharedValue(5);
  const color = useSharedValue("black");

  const gesture = Gesture.Pan()
    .onBegin((e) => {
      const path = Skia.Path.Make();
      path.moveTo(e.x, e.y);
      currentPath.value = { path, color: color.value, strokeWidth: strokeWidth.value };
    })
    .onChange((e) => {
      if (currentPath.value) {
        currentPath.value.path.lineTo(e.x, e.y);
        currentPath.value = { ...currentPath.value };
      }
    })
    .onEnd(() => {
      if (currentPath.value) {
        paths.value = [...paths.value, currentPath.value];
        currentPath.value = null;
      }
    });

  return (
    <View style={{ flex: 1 }}>
      <GestureDetector gesture={gesture}>
        <Canvas style={{ flex: 1 }}>
          {paths.value.map((p, i) => (
            <Path key={i} path={p.path} style="stroke" strokeWidth={p.strokeWidth}
              color={p.color} strokeCap="round" strokeJoin="round" />
          ))}
          {currentPath.value && (
            <Path path={currentPath.value.path} style="stroke"
              strokeWidth={currentPath.value.strokeWidth}
              color={currentPath.value.color} strokeCap="round" strokeJoin="round" />
          )}
        </Canvas>
      </GestureDetector>
      <View style={styles.toolbar}>
        {['black', 'red', 'blue', 'green'].map(c => (
          <TouchableOpacity key={c} onPress={() => color.value = c}
            style={[styles.colorButton, { backgroundColor: c }]} />
        ))}
      </View>
    </View>
  );
};
```

### Progress Ring

```tsx
const ProgressRing = ({ progress, radius = 50, strokeWidth = 10 }) => {
  const animatedProgress = useSharedValue(0);
  useEffect(() => { animatedProgress.value = withSpring(progress); }, [progress]);

  const path = useMemo(() => {
    const p = Skia.Path.Make();
    p.addCircle(radius + strokeWidth, radius + strokeWidth, radius);
    return p;
  }, [radius, strokeWidth]);

  return (
    <Canvas style={{ width: (radius + strokeWidth) * 2, height: (radius + strokeWidth) * 2 }}>
      <Path path={path} style="stroke" strokeWidth={strokeWidth} color="#E5E5EA" />
      <Path path={path} style="stroke" strokeWidth={strokeWidth} color="#007AFF"
        strokeCap="round" start={0} end={animatedProgress}>
        <BlurMask blur={5} style="normal" />
      </Path>
      <Text x={radius + strokeWidth - 15} y={radius + strokeWidth + 8}
        text={useDerivedValue(() => `${Math.round(animatedProgress.value * 100)}%`)}
        font={font} color="#007AFF" />
    </Canvas>
  );
};
```

### Waveform Visualizer

```tsx
const WaveformVisualizer = ({ audioData }) => {
  const bars = 50;
  const barWidth = 6, gap = 2;
  const amplitudes = Array.from({ length: bars }, () => useSharedValue(0));

  useEffect(() => {
    const interval = setInterval(() => {
      amplitudes.forEach((amp, i) => {
        const target = audioData[i] || Math.random() * 100;
        amp.value = withSpring(target, { damping: 20 });
      });
    }, 50);
    return () => clearInterval(interval);
  }, []);

  return (
    <Canvas style={{ height: 200 }}>
      <Group>
        {amplitudes.map((amp, i) => {
          const x = i * (barWidth + gap);
          const height = useDerivedValue(() => amp.value);
          const y = useDerivedValue(() => 100 - height.value / 2);
          const color = useDerivedValue(() =>
            interpolateColors(height.value / 100, [0, 0.5, 1], ["#34C759", "#FF9500", "#FF3B30"])
          );
          return (
            <RoundedRect key={i} x={x} y={y} width={barWidth} height={height} r={3} color={color}>
              <BlurMask blur={height.value > 80 ? 5 : 0} />
            </RoundedRect>
          );
        })}
      </Group>
    </Canvas>
  );
};
```

## Design System Integration

```tsx
const skiaTheme = {
  colors: {
    primary: Skia.Color("#007AFF"),
    secondary: Skia.Color("#5856D6"),
    success: Skia.Color("#34C759"),
    warning: Skia.Color("#FF9500"),
    error: Skia.Color("#FF3B30"),
    background: Skia.Color("#F2F2F7"),
    surface: Skia.Color("#FFFFFF"),
    text: Skia.Color("#000000"),
    textSecondary: Skia.Color("#8E8E93"),
  },
  spacing: { xs: 4, sm: 8, md: 16, lg: 24, xl: 32 },
  borderRadius: { sm: 4, md: 8, lg: 16, full: 9999 },
  shadows: {
    sm: { dx: 0, dy: 1, blur: 2, color: "rgba(0,0,0,0.1)" },
    md: { dx: 0, dy: 4, blur: 8, color: "rgba(0,0,0,0.15)" },
    lg: { dx: 0, dy: 8, blur: 16, color: "rgba(0,0,0,0.2)" },
  },
};

const ThemedButton = ({ children }) => {
  const theme = useSkiaTheme();
  return (
    <RoundedRect r={theme.borderRadius.md} color={theme.colors.primary}>
      <Shadow {...theme.shadows.md} />
    </RoundedRect>
  );
};
```

## Testing

### Unit Tests

```tsx
import { renderSkia } from "@shopify/react-native-skia/testing";

describe("Chart", () => {
  it("renders correctly", () => {
    const { toJSON } = renderSkia(<BarChart data={[{ value: 10 }, { value: 20 }]} />);
    expect(toJSON()).toMatchSnapshot();
  });

  it("animates on mount", () => {
    const { getByType } = renderSkia(<AnimatedCircle />);
    const circle = getByType(Circle);
    expect(circle.props.r).toBe(0);
    jest.advanceTimersByTime(1000);
    expect(circle.props.r).toBe(50);
  });
});
```

### Visual Regression

```tsx
const screenshot = await renderAndCapture(<Chart />);
const diff = await compareScreenshot(screenshot, "chart-baseline.png");
expect(diff).toBeLessThan(0.01);
```

## Debugging

### Visual Grid Overlay

```tsx
const DebugCanvas = ({ children, showBounds = true }) => (
  <Canvas>
    {showBounds && <Grid spacing={50} />}
    {children}
  </Canvas>
);

const Grid = ({ spacing }) => (
  <Group>
    {Array.from({ length: 10 }, (_, i) => (
      <React.Fragment key={i}>
        <Line p1={{ x: i * spacing, y: 0 }} p2={{ x: i * spacing, y: 500 }} color="rgba(255,0,0,0.1)" strokeWidth={1} />
        <Line p1={{ x: 0, y: i * spacing }} p2={{ x: 500, y: i * spacing }} color="rgba(255,0,0,0.1)" strokeWidth={1} />
      </React.Fragment>
    ))}
  </Group>
);
```

### Performance Monitor Hook

```tsx
const usePerformanceMonitor = (componentName) => {
  const renderCount = useRef(0);
  const lastRenderTime = useRef(performance.now());

  useEffect(() => {
    renderCount.current++;
    const now = performance.now();
    const timeSince = now - lastRenderTime.current;
    if (timeSince < 16) {
      console.warn(`${componentName} rendering too fast: ${timeSince.toFixed(2)}ms`);
    }
    lastRenderTime.current = now;
  });
};
```

## Common Patterns Library

### Skeleton Loading

```tsx
const Skeleton = ({ width, height }) => {
  const opacity = useSharedValue(0.3);
  useEffect(() => {
    opacity.value = withRepeat(
      withSequence(withTiming(0.7, { duration: 800 }), withTiming(0.3, { duration: 800 })),
      -1
    );
  }, []);
  return <Rect x={0} y={0} width={width} height={height} r={4} color="#E5E5EA" opacity={opacity} />;
};
```

### Ripple Effect

```tsx
const Ripple = ({ x, y, onComplete }) => {
  const radius = useSharedValue(0);
  const opacity = useSharedValue(1);
  useEffect(() => {
    radius.value = withTiming(100, { duration: 600 });
    opacity.value = withTiming(0, { duration: 600 }, () => { onComplete?.(); });
  }, []);
  return <Circle cx={x} cy={y} r={radius} opacity={opacity} color="rgba(0,122,255,0.3)" />;
};
```

### Confetti

```tsx
const Confetti = ({ count = 50, onComplete }) => {
  const particles = useRef(
    Array.from({ length: count }, () => ({
      x: useSharedValue(200), y: useSharedValue(200),
      vx: useSharedValue((Math.random() - 0.5) * 20),
      vy: useSharedValue(-Math.random() * 20 - 10),
      rotation: useSharedValue(Math.random() * Math.PI * 2),
      color: ['#FF0080', '#7928CA', '#FF4D4D', '#F9CB28'][Math.floor(Math.random() * 4)],
    }))
  ).current;

  useEffect(() => {
    const gravity = 0.8;
    const interval = setInterval(() => {
      particles.forEach((p) => {
        p.vy.value += gravity;
        p.x.value += p.vx.value;
        p.y.value += p.vy.value;
        p.rotation.value += 0.1;
      });
    }, 16);
    setTimeout(() => { clearInterval(interval); onComplete?.(); }, 3000);
  }, []);

  return (
    <Canvas style={StyleSheet.absoluteFillObject}>
      {particles.map((p, i) => (
        <Group key={i} transform={[{ translateX: p.x }, { translateY: p.y }, { rotate: p.rotation }]}>
          <Rect x={-5} y={-5} width={10} height={10} color={p.color} />
        </Group>
      ))}
    </Canvas>
  );
};
```
