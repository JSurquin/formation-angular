---
routeAlias: 'gestion-etat-navigation'
---

# Gestion de l'état et Navigation (2024/2025)

## État Local avec Hooks

```tsx
// Exemple basique de useState
const [count, setCount] = useState(0);

// État avec objet
const [user, setUser] = useState<User>({ 
  name: '', 
  age: 0 
});
```

---

## Gestion d'état complexe

```tsx
// useReducer pour état complexe
const [state, dispatch] = useReducer(reducer, { 
  matches: [],
  likes: 0,
  messages: [] 
});

function reducer(state, action) {
  switch(action.type) {
    case 'ADD_MATCH':
      return { ...state, matches: [...state.matches, action.payload] };
    case 'INCREMENT_LIKES':
      return { ...state, likes: state.likes + 1 };
    default:
      return state;
  }
}
```

---

# Navigation Moderne avec Expo Router

## Structure de base (2024/2025)

```
app/
├── _layout.tsx
├── index.tsx
├── (tabs)/
│   ├── _layout.tsx
│   ├── home.tsx
│   ├── matches.tsx
│   └── profile.tsx
└── (auth)/
    ├── login.tsx
    └── register.tsx
```

---

## Layout Principal

```tsx
// app/_layout.tsx
import { Stack } from 'expo-router';

export default function RootLayout() {
  return (
    <Stack>
      <Stack.Screen 
        name="(tabs)" 
        options={{ headerShown: false }} 
      />
      <Stack.Screen 
        name="(auth)" 
        options={{ headerShown: false }} 
      />
    </Stack>
  );
}
```

---

## Navigation par Tabs

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Home, Heart, User } from 'lucide-react-native';

export default function TabsLayout() {
  return (
    <Tabs screenOptions={{
      tabBarActiveTintColor: '#FF6B6B',
    }}>
      <Tabs.Screen
        name="home"
        options={{
          title: 'Découvrir',
          tabBarIcon: ({ color }) => (
            <Home size={24} color={color} />
          ),
        }}
      />
      <Tabs.Screen
        name="matches"
        options={{
          title: 'Matches',
          tabBarIcon: ({ color }) => (
            <Heart size={24} color={color} />
          ),
        }}
      />
    </Tabs>
  );
}
```

---

## Navigation vers un profil

```tsx
// app/(tabs)/home.tsx
import { Link } from 'expo-router';

export default function Home() {
  return (
    <View className="flex-1 p-4">
      <Link href="/profile/123" asChild>
        <TouchableOpacity className="bg-primary p-4 rounded-lg">
          <Text className="text-white">Voir Profil</Text>
        </TouchableOpacity>
      </Link>
    </View>
  );
}
```

---

## Pages dynamiques

```tsx
// app/profile/[id].tsx
import { useLocalSearchParams } from 'expo-router';

export default function Profile() {
  const { id } = useLocalSearchParams();
  
  return (
    <View className="flex-1 p-4">
      <Text className="text-xl">Profil {id}</Text>
    </View>
  );
}
```

---

# Exercice : Application de rencontre

Créez une application de rencontre avec :

1. Navigation par tabs (Découvrir, Matches, Profil)
2. Système de likes avec useReducer
3. Pages de profil dynamiques
4. Authentification protégée

---

## Structure de l'exercice

```tsx
// app/(tabs)/home.tsx
export default function Home() {
  const [profiles, dispatch] = useReducer(profilesReducer, []);
  
  const handleLike = (profile) => {
    dispatch({ type: 'LIKE_PROFILE', payload: profile });
  };

  return (
    <View className="flex-1">
      <Swiper
        cards={profiles}
        onSwipedRight={(cardIndex) => {
          handleLike(profiles[cardIndex]);
        }}
        renderCard={(card) => (
          <ProfileCard {...card} />
        )}
      />
    </View>
  );
}
```

---

## Authentification sécurisée

```tsx
// app/_layout.tsx
import { useAuth } from '../hooks/useAuth';

export default function RootLayout() {
  const { isAuthenticated } = useAuth();

  return (
    <Stack>
      {isAuthenticated ? (
        <Stack.Screen 
          name="(tabs)" 
          options={{ headerShown: false }} 
        />
      ) : (
        <Stack.Screen 
          name="(auth)" 
          options={{ headerShown: false }} 
        />
      )}
    </Stack>
  );
}
```

Cette version modernisée reflète les pratiques actuelles de 2024/2025 avec Expo Router, tout en gardant les concepts fondamentaux de la gestion d'état.

---

# Implémentation du Swiper

## Composant HomeScreen complet

```tsx
// app/(tabs)/home.tsx
import React from "react";
import { View, SafeAreaView } from "react-native";
import Swiper from "react-native-deck-swiper";
import ProfileCard from "../components/ProfileCard";
import { User, users } from "../data/users";
import { X, Heart } from "lucide-react-native";
import { Button } from "~/components/ui/button";

export default function HomeScreen() {
  const swiperRef = React.useRef<Swiper<User>>(null);
  
  const handleLike = (cardIndex: number) => {
    console.log(`Liked ${users[cardIndex].name}`);
  };

  const handleDislike = (cardIndex: number) => {
    console.log(`Disliked ${users[cardIndex].name}`);
  };

  return (
    <SafeAreaView className="bg-background flex-1">
      <View className="flex-1 px-4">
        <View className="flex-1 pt-4">
          <Swiper
            cards={users}
            renderCard={(card: any) =>
              card ? <ProfileCard user={card} /> : null
            }
            onSwipedLeft={handleDislike}
            onSwipedRight={handleLike}
            cardIndex={0}
            backgroundColor="transparent"
            stackSize={10}
            stackScale={10}
            stackSeparation={14}
            animateOverlayLabelsOpacity
            animateCardOpacity
            disableTopSwipe
            disableBottomSwipe
            overlayLabels={{
              left: {
                title: "NON",
                style: {
                  label: {
                    backgroundColor: "red",
                    color: "white",
                    fontSize: 24,
                  },
                  wrapper: {
                    flexDirection: "column",
                    alignItems: "flex-end",
                    justifyContent: "flex-start",
                    marginTop: 30,
                    marginLeft: -30,
                  },
                },
              },
              right: {
                title: "OUI",
                style: {
                  label: {
                    backgroundColor: "#4DED30",
                    color: "white",
                    fontSize: 24,
                  },
                  wrapper: {
                    flexDirection: "column",
                    alignItems: "flex-start",
                    justifyContent: "flex-start",
                    marginTop: 30,
                    marginLeft: 30,
                  },
                },
              },
            }}
          />
        </View>

        <View className="flex-row items-center justify-center gap-4 pb-24">
          <Button
            variant="outline"
            size="icon"
            className="h-16 w-16 rounded-full border-2 border-red-500 bg-white"
            onPress={() => {
              if (swiperRef.current) {
                swiperRef.current.swipeLeft();
              }
            }}
          >
            <X className="h-8 w-8 text-red-500" />
          </Button>

          <Button
            size="icon"
            className="bg-primary h-16 w-16 rounded-full"
            onPress={() => {
              if (swiperRef.current) {
                swiperRef.current.swipeRight();
              }
            }}
          >
            <Heart className="h-8 w-8 text-white" />
          </Button>
        </View>
      </View>
    </SafeAreaView>
  );
}
```

---

## Explications des fonctionnalités clés

### 1. Configuration du Swiper

```tsx
// Props importantes du Swiper
<Swiper
  stackSize={10}        // Nombre de cartes visibles dans la pile
  stackScale={10}       // Échelle de réduction pour les cartes empilées
  stackSeparation={14}  // Espacement vertical entre les cartes
  animateOverlayLabelsOpacity  // Animation des labels OUI/NON
  animateCardOpacity          // Fondu des cartes
  disableTopSwipe            // Désactive le swipe vers le haut
  disableBottomSwipe         // Désactive le swipe vers le bas
/>
```

---

### 2. Gestion des labels de swipe

```tsx
// Configuration des labels OUI/NON
overlayLabels={{
  left: {
    title: "NON",
    style: {
      label: {
        backgroundColor: "red",
        // ... styles du label
      },
      wrapper: {
        // ... styles du wrapper
      },
    },
  },
  right: {
    // ... configuration similaire pour "OUI"
  },
}}
```

---

### 3. Boutons physiques avec refs

```tsx
// Utilisation de la ref pour contrôler le Swiper
const swiperRef = React.useRef<Swiper<User>>(null);

// Déclenchement manuel des swipes
<Button
  onPress={() => {
    if (swiperRef.current) {
      swiperRef.current.swipeLeft();
    }
  }}
>
  <X />
</Button>
```

---

### 4. Gestion des événements de swipe

```tsx
// Handlers pour les swipes
const handleLike = (cardIndex: number) => {
  console.log(`Liked ${users[cardIndex].name}`);
  // Ici vous pouvez ajouter votre logique de match
  // Par exemple : appel API, mise à jour du state, etc.
};

const handleDislike = (cardIndex: number) => {
  console.log(`Disliked ${users[cardIndex].name}`);
  // Logique de rejet
};
```

Cette implémentation moderne combine :
- Styling avec NativeWind
- Gestion d'état avec TypeScript
- Animations fluides
- Interface utilisateur intuitive
- Gestion des gestes tactiles

Le tout dans un composant réutilisable et maintenable.
