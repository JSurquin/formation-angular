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
