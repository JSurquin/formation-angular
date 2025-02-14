---
routeAlias: 'composants-natifs-styling'
---

# Composants Natifs et Styling (2024/2025)

## Styling avec NativeWind

```tsx
// components/Card.tsx
export function Card({ children }) {
  return (
    <View className="bg-white rounded-2xl shadow-lg p-4 m-2">
      {children}
    </View>
  );
}
```

---

## Composants de base stylisés

```tsx
// components/StyledComponents.tsx
export function Button({ onPress, children }) {
  return (
    <TouchableOpacity 
      onPress={onPress}
      className="bg-primary-500 px-4 py-2 rounded-lg active:bg-primary-600"
    >
      <Text className="text-white font-medium text-center">
        {children}
      </Text>
    </TouchableOpacity>
  );
}

export function Input({ placeholder, ...props }) {
  return (
    <TextInput 
      placeholder={placeholder}
      className="w-full px-4 py-2 bg-gray-100 rounded-lg"
      placeholderTextColor="#9CA3AF"
      {...props}
    />
  );
}
```

---

## Composants Safe Area et Platform

```tsx
// app/_layout.tsx
import { Stack } from 'expo-router';
import { SafeAreaProvider } from 'react-native-safe-area-context';

export default function Layout() {
  return (
    <SafeAreaProvider>
      <Stack 
        screenOptions={{
          headerStyle: {
            backgroundColor: '#fff',
          },
          headerShadowVisible: false,
          headerBlurEffect: 'regular',
        }}
      />
    </SafeAreaProvider>
  );
}
```

---

## Gestion des images avec Expo Image

```tsx
// components/ProfileImage.tsx
import { Image } from 'expo-image';

export function ProfileImage({ uri }) {
  return (
    <Image
      source={uri}
      className="w-32 h-32 rounded-full"
      transition={1000}
      contentFit="cover"
      placeholder={blurhash}
      cachePolicy="memory-disk"
    />
  );
}
```

---

## Animations avec Reanimated

```tsx
// components/AnimatedCard.tsx
import Animated, { 
  useAnimatedStyle, 
  withSpring 
} from 'react-native-reanimated';

export function AnimatedCard({ isVisible }) {
  const animatedStyles = useAnimatedStyle(() => {
    return {
      transform: [
        { 
          scale: withSpring(isVisible ? 1 : 0.8),
        },
      ],
      opacity: withSpring(isVisible ? 1 : 0),
    };
  });

  return (
    <Animated.View 
      className="bg-white rounded-2xl p-4"
      style={animatedStyles}
    >
      {/* Contenu de la carte */}
    </Animated.View>
  );
}
```

---

## Gestes avec Reanimated

```tsx
// components/SwipeableCard.tsx
import { Gesture, GestureDetector } from 'react-native-gesture-handler';

export function SwipeableCard() {
  const gesture = Gesture.Pan()
    .onUpdate((event) => {
      // Logique de mise à jour
    })
    .onEnd((event) => {
      // Logique de fin de geste
    });

  return (
    <GestureDetector gesture={gesture}>
      <Animated.View className="w-full aspect-[3/4] bg-white rounded-2xl">
        {/* Contenu de la carte */}
      </Animated.View>
    </GestureDetector>
  );
}
```

---

## Composants spécifiques à la plateforme

```tsx
// components/PlatformSpecific.tsx
import { Platform } from 'react-native';

export function StatusBarHeight() {
  return (
    <View 
      className={Platform.select({
        ios: 'h-11',
        android: 'h-7',
        default: 'h-0'
      })}
    />
  );
}

export function PlatformShadow() {
  return (
    <View
      className={Platform.select({
        ios: 'shadow-lg',
        android: 'elevation-5',
        default: 'shadow-sm'
      })}
    />
  );
}
```

---

# Exercice : Création d'une carte de profil

Créez une carte de profil pour l'application de rencontre avec :

1. Styling avec NativeWind
2. Animations fluides avec Reanimated
3. Gestion optimisée des images
4. Gestes de swipe

---

## Solution de l'exercice

```tsx
// components/ProfileCard.tsx
import { Image } from 'expo-image';
import Animated, { 
  useAnimatedStyle, 
  withSpring 
} from 'react-native-reanimated';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';

export function ProfileCard({ profile, onSwipe }) {
  const gesture = Gesture.Pan()
    .onUpdate((event) => {
      // Logique de swipe
    })
    .onEnd((event) => {
      if (Math.abs(event.velocityX) > 500) {
        onSwipe(event.velocityX > 0 ? 'right' : 'left');
      }
    });

  return (
    <GestureDetector gesture={gesture}>
      <Animated.View className="w-full aspect-[3/4] bg-white rounded-3xl overflow-hidden shadow-xl">
        <Image
          source={profile.photos[0]}
          className="w-full h-3/4"
          contentFit="cover"
          transition={500}
        />
        <View className="p-4">
          <Text className="text-2xl font-bold">
            {profile.name}, {profile.age}
          </Text>
          <Text className="text-gray-600 mt-1">
            {profile.bio}
          </Text>
        </View>
      </Animated.View>
    </GestureDetector>
  );
}
```