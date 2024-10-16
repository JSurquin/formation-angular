---
routeAlias: 'animations-gestes'
---

# Animations et gestes

- **Animations avec React Native Animated**
  - Création d'animations de base
  - Animations parallèles et séquentielles
  - Interpolation de valeurs

- **Gestes avec PanResponder**
  - Détection des gestes de base (tap, swipe, pinch)
  - Création d'interactions personnalisées

---

# Animations et gestes (suite)

- **Animations de mise en page**
  - LayoutAnimation pour des transitions fluides
  - Animations de liste avec FlatList

- **Bibliothèques tierces populaires**
  - React Native Reanimated pour des animations performantes
  - React Native Gesture Handler pour une gestion avancée des gestes

---
routeAlias: 'exercice-carte-swipeable'
---

## Exercice : Création d'une carte swipeable

Créons une carte de profil swipeable pour notre application TinderLikeApp, similaire à l'interface de Tinder.

1. Installez react-native-gesture-handler : `npm install react-native-gesture-handler`
2. Créez un nouveau composant `SwipeableCard.js`
3. Implémentez la logique de swipe avec des animations

---

## Exercice : Création d'une carte swipeable (suite)

Voici le début du code pour `SwipeableCard.js` :

```jsx
import React, { useRef } from 'react';
import { Animated, PanResponder, View, Text, Image, StyleSheet, Dimensions } from 'react-native';

const SCREEN_WIDTH = Dimensions.get('window').width;
const SWIPE_THRESHOLD = 0.25 * SCREEN_WIDTH;

const SwipeableCard = ({ profile, onSwipeLeft, onSwipeRight }) => {
  const position = useRef(new Animated.ValueXY()).current;

  const panResponder = PanResponder.create({
    onStartShouldSetPanResponder: () => true,
    onPanResponderMove: (_, gesture) => {
      position.setValue({ x: gesture.dx, y: gesture.dy });
    },
    onPanResponderRelease: (_, gesture) => {
      if (gesture.dx > SWIPE_THRESHOLD) {
        forceSwipe('right');
      } else if (gesture.dx < -SWIPE_THRESHOLD) {
        forceSwipe('left');
      } else {
        resetPosition();
      }
    },
  });

  // ... (suite dans la prochaine slide)
```

---

## Exercice : Création d'une carte swipeable (suite)

Suite du code pour `SwipeableCard.js` :

```jsx
  const forceSwipe = (direction) => {
    const x = direction === 'right' ? SCREEN_WIDTH : -SCREEN_WIDTH;
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

  const resetPosition = () => {
    Animated.spring(position, {
      toValue: { x: 0, y: 0 },
      useNativeDriver: false,
    }).start();
  };

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

  // ... (suite dans la prochaine slide)
```

---

## Exercice : Création d'une carte swipeable (suite)

Fin du code pour `SwipeableCard.js` :

```jsx
  return (
    <Animated.View style={[styles.card, getCardStyle()]} {...panResponder.panHandlers}>
      <Image source={{ uri: profile.imageUrl }} style={styles.image} />
      <View style={styles.textContainer}>
        <Text style={styles.name}>{profile.name}</Text>
        <Text style={styles.bio}>{profile.bio}</Text>
      </View>
    </Animated.View>
  );
};

const styles = StyleSheet.create({
  card: {
    position: 'absolute',
    width: SCREEN_WIDTH * 0.9,
    height: SCREEN_WIDTH * 1.2,
    borderRadius: 20,
    backgroundColor: 'white',
    shadowColor: '#000',
    shadowOpacity: 0.1,
    shadowRadius: 5,
    shadowOffset: { width: 0, height: 0 },
    elevation: 3,
  },
  image: {
    width: '100%',
    height: '70%',
    borderTopLeftRadius: 20,
    borderTopRightRadius: 20,
  },
  textContainer: {
    padding: 20,
  },
  name: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 10,
  },
  bio: {
    fontSize: 16,
    color: '#666',
  },
});

export default SwipeableCard;
```

---

## Exercice : Création d'une carte swipeable (suite)

Maintenant, intégrez ce composant dans votre écran principal :

```jsx
import React, { useState, useEffect } from 'react';
import { View, StyleSheet } from 'react-native';
import SwipeableCard from './SwipeableCard';
import { fetchProfiles } from '../api';

const MainScreen = () => {
  const [profiles, setProfiles] = useState([]);
  const [currentIndex, setCurrentIndex] = useState(0);

  useEffect(() => {
    loadProfiles();
  }, []);

  const loadProfiles = async () => {
    const fetchedProfiles = await fetchProfiles(10);
    setProfiles(fetchedProfiles);
  };

  const handleSwipeLeft = () => {
    setCurrentIndex(currentIndex + 1);
  };

  const handleSwipeRight = () => {
    // Ici, vous pourriez implémenter une logique de "match"
    setCurrentIndex(currentIndex + 1);
  };

  return (
    <View style={styles.container}>
      {profiles.length > currentIndex && (
        <SwipeableCard
          profile={profiles[currentIndex]}
          onSwipeLeft={handleSwipeLeft}
          onSwipeRight={handleSwipeRight}
        />
      )}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default MainScreen;
```

Cet exercice vous permet de pratiquer la création d'animations complexes et la gestion des gestes dans React Native. Vous avez créé une interface de swipe similaire à celle de Tinder, ce qui rend votre application TinderLikeApp plus interactive et engageante.
