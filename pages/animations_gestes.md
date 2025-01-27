---
routeAlias: 'animations-gestes'
---

# Animations de base

## Animated Value - Configuration

```jsx
// Création de la valeur animée
const fadeAnim = useRef(new Animated.Value(0)).current;
```

---

## Animated Value - Animation

```jsx
// Configuration et démarrage de l'animation
Animated.timing(fadeAnim, {
  toValue: 1,
  duration: 1000,
  useNativeDriver: true,
}).start();
```

---

## Animations parallèles - Configuration

```jsx
// Configuration des animations multiples
const fadeAnim = useRef(new Animated.Value(0)).current;
const scaleAnim = useRef(new Animated.Value(1)).current;
```

---

## Animations parallèles - Exécution

```jsx
// Exécution des animations en parallèle
Animated.parallel([
  Animated.timing(fadeAnim, {
    toValue: 1,
    duration: 1000,
  }),
  Animated.spring(scaleAnim, {
    toValue: 1.2,
    friction: 2,
  }),
]).start();
```

---

## Animations séquentielles

```jsx
// Animations l'une après l'autre
Animated.sequence([
  Animated.timing(fadeAnim, { 
    toValue: 1,
    duration: 500,
  }),
  Animated.timing(slideAnim, { 
    toValue: 100,
    duration: 500,
  }),
]).start();
```

---

## Interpolation de valeurs

```jsx
// Transformation d'une valeur en une autre
const rotation = animValue.interpolate({
  inputRange: [0, 1],
  outputRange: ['0deg', '360deg'],
});

return (
  <Animated.View
    style={{
      transform: [{ rotate: rotation }]
    }}
  />
);
```

---

# Gestion des gestes

## Configuration PanResponder

```jsx
const panResponder = PanResponder.create({
  onStartShouldSetPanResponder: () => true,
  onPanResponderMove: (evt, gestureState) => {
    // Gestion du déplacement
    console.log(gestureState.dx, gestureState.dy);
  },
  onPanResponderRelease: () => {
    // Gestion du relâchement
  },
});
```

---

## Gestion du tap (appui simple)

```jsx
const tapGesture = {
  onStartShouldSetPanResponder: () => true,
  onPanResponderRelease: (e, gestureState) => {
    if (Math.abs(gestureState.dx) < 5 && 
        Math.abs(gestureState.dy) < 5) {
      // C'est un tap
      handleTap();
    }
  },
};
```

---

## Gestion du swipe

```jsx
const isSwipe = (gestureState) => {
  return Math.abs(gestureState.dx) > 50;
};

const handleSwipe = (gestureState) => {
  if (gestureState.dx > 50) {
    // Swipe vers la droite
    handleRightSwipe();
  } else if (gestureState.dx < -50) {
    // Swipe vers la gauche
    handleLeftSwipe();
  }
};
```

---

## Gestion du pinch (zoom)

```jsx
const calculatePinchDistance = (evt) => {
  const touches = evt.nativeEvent.touches;
  if (touches.length !== 2) return 0;
  
  const [touch1, touch2] = touches;
  return Math.sqrt(
    Math.pow(touch2.pageX - touch1.pageX, 2) +
    Math.pow(touch2.pageY - touch1.pageY, 2)
  );
};
```

---

# Animations avancées

## LayoutAnimation

```jsx
const toggleLayout = () => {
  LayoutAnimation.configureNext(
    LayoutAnimation.Presets.spring
  );
  setExpanded(!expanded);
};
```

---

## Animations de liste

```jsx
<FlatList
  data={items}
  renderItem={({ item, index }) => (
    <Animated.View
      style={{
        opacity: fadeAnim,
        transform: [{
          translateY: slideAnim.interpolate({
            inputRange: [0, 1],
            outputRange: [50 * index, 0]
          })
        }]
      }}
    >
      <ListItem item={item} />
    </Animated.View>
  )}
/>
```

---

# Bibliothèques tierces

## React Native Reanimated

```jsx
import Animated, {
  withSpring,
  useAnimatedStyle,
} from 'react-native-reanimated';

const animatedStyle = useAnimatedStyle(() => {
  return {
    transform: [{ scale: withSpring(1.2) }],
  };
});
```

---

## React Native Gesture Handler

```jsx
import { PanGestureHandler } from 'react-native-gesture-handler';

const onGestureEvent = useAnimatedGestureHandler({
  onStart: (_, ctx) => {
    ctx.startX = translateX.value;
  },
  onActive: (event, ctx) => {
    translateX.value = ctx.startX + event.translationX;
  },
});
```

---
routeAlias: 'exercice-carte-swipeable'
---

# Exercice : Carte swipeable

## Configuration initiale

```jsx
// Installation
import { PanGestureHandler } from 'react-native-gesture-handler';

const SCREEN_WIDTH = Dimensions.get('window').width;
const SWIPE_THRESHOLD = 0.25 * SCREEN_WIDTH;
```

---

## Logique de base du swipe

```jsx
const SwipeableCard = ({ profile, onSwipeLeft, onSwipeRight }) => {
  const position = useRef(new Animated.ValueXY()).current;
  
  const panResponder = PanResponder.create({
    onStartShouldSetPanResponder: () => true,
    onPanResponderMove: (_, gesture) => {
      position.setValue({ 
        x: gesture.dx, 
        y: gesture.dy 
      });
    },
  });
```

---

## Gestion des swipes

```jsx
  const forceSwipe = (direction) => {
    const x = direction === 'right' ? 
      SCREEN_WIDTH : -SCREEN_WIDTH;
    
    Animated.timing(position, {
      toValue: { x, y: 0 },
      duration: 250,
      useNativeDriver: false,
    }).start(() => onSwipeComplete(direction));
  };

  const onSwipeComplete = (direction) => {
    direction === 'right' ? onSwipeRight() : onSwipeLeft();
    position.setValue({ x: 0, y: 0 });
  };
```

---

## Styles et animations - Partie 1

```jsx
// Configuration du style de la carte
const getCardStyle = () => {
  const rotate = position.x.interpolate({
    inputRange: [-SCREEN_WIDTH * 1.5, 0, SCREEN_WIDTH * 1.5],
    outputRange: ['-120deg', '0deg', '120deg'],
  });

  return {
    ...position.getLayout(),
    transform: [{ rotate }],
  };
};
```

---

## Styles et animations - Partie 2

```jsx
// Rendu du composant
return (
  <Animated.View 
    style={[styles.card, getCardStyle()]} 
    {...panResponder.panHandlers}
  >
    <Image 
      source={{ uri: profile.imageUrl }} 
      style={styles.image} 
    />
    <View style={styles.textContainer}>
      <Text style={styles.name}>{profile.name}</Text>
      <Text style={styles.bio}>{profile.bio}</Text>
    </View>
  </Animated.View>
);
```

---

# Résultat de l'exercice

Cet exercice vous permet de créer une interface de swipe interactive et fluide, similaire à celle de Tinder, en utilisant les animations et gestes de React Native.
