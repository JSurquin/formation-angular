---
routeAlias: 'navigation-comparaison'
---

<a name="navigation-comparaison"></a>

# Navigation dans React Native (2024/2025)

## Expo Router vs React Navigation

- **Expo Router**
  - Basé sur les fichiers (comme Next.js)
  - Navigation déclarative
  - URLs web natives
  - Meilleure performance sur web

- **React Navigation**
  - Plus flexible
  - Plus mature
  - Meilleur contrôle
  - Plus de composants personnalisables

---

## Quand utiliser Expo Router ?

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';

export default function TabsLayout() {
  return (
    <Tabs>
      <Tabs.Screen
        name="index"
        options={{
          title: 'Home',
        }}
      />
      <Tabs.Screen
        name="profile"
        options={{
          title: 'Profile',
        }}
      />
    </Tabs>
  );
}
```

- Applications avec besoin de web
- Projets qui suivent le file-system routing
- Besoin de deep linking simplifié

---

## Quand utiliser React Navigation ?

```tsx
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen 
          name="Home" 
          component={HomeScreen}
          options={{
            headerShown: false,
          }}
        />
        <Stack.Screen 
          name="Profile" 
          component={ProfileScreen}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

- Projets nécessitant une navigation complexe
- Besoin de contrôle total sur les transitions
- Applications natives uniquement

---

## Navigation modale avec Expo Router

```tsx
// app/modal/login.tsx
import { View, Text } from 'react-native';

export default function LoginModal() {
  return (
    <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
      <Text>Login Modal</Text>
    </View>
  );
}

// Pour ouvrir : router.push('/modal/login');
```

---

## Navigation modale avec React Navigation

```tsx
const Stack = createStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator mode="modal">
        <Stack.Screen name="Main" component={MainStack} />
        <Stack.Screen 
          name="Login" 
          component={LoginModal}
          options={{
            presentation: 'modal'
          }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

---

## Deep Linking

### Expo Router (automatique)

```tsx
// app.json
{
  "expo": {
    "scheme": "myapp"
  }
}

// Liens supportés automatiquement :
// myapp:// 
// myapp://profile/123
```

### React Navigation (configuration manuelle)

```tsx
const linking = {
  prefixes: ['myapp://'],
  config: {
    screens: {
      Home: '',
      Profile: 'profile/:id',
      Settings: 'settings',
    },
  },
};

function App() {
  return (
    <NavigationContainer linking={linking}>
      {/* ... */}
    </NavigationContainer>
  );
}
```

---

## Navigation par gestes

### Expo Router

```tsx
// app/_layout.tsx
import { Stack } from 'expo-router';

export default function Layout() {
  return (
    <Stack
      screenOptions={{
        gestureEnabled: true,
        gestureDirection: 'horizontal',
      }}
    />
  );
}
```

### React Navigation

```tsx
<Stack.Navigator
  screenOptions={{
    gestureEnabled: true,
    gestureDirection: 'horizontal',
    cardStyleInterpolator: CardStyleInterpolators.forHorizontalIOS,
  }}
>
  {/* ... */}
</Stack.Navigator>
```

---

## Bonnes pratiques

1. **Choix de la solution**
   - Expo Router pour projets web + mobile
   - React Navigation pour applications natives pures

2. **Performance**
   - Lazy loading des écrans
   - Mise en cache des routes
   - Préchargement des écrans fréquents

3. **Organisation**
   - Structure claire des dossiers/fichiers
   - Séparation des layouts
   - Typage des paramètres de navigation

4. **Sécurité**
   - Protection des routes authentifiées
   - Validation des paramètres
   - Gestion des deep links malveillants 