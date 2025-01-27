---
routeAlias: 'gestion-etat-navigation'
---

# Gestion de l'état local

## useState

```jsx
// État simple
const [count, setCount] = useState(0);

// État objet
const [user, setUser] = useState({ 
  name: '', 
  age: 0 
});
```

---

## useReducer

```jsx
// Définition du reducer
const [state, dispatch] = useReducer(reducer, { count: 0 });

function reducer(state, action) {
  switch(action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      return state;
  }
}
```

---

# Gestion de l'état global

## Context API - Création

```jsx
// Création du context
const UserContext = createContext();

// Provider au niveau supérieur
function App() {
  return (
    <UserContext.Provider value={user}>
      <MainApp />
    </UserContext.Provider>
  );
}
```

---

## Context API - Utilisation

```jsx
// Utilisation dans un composant
function Profile() {
  const user = useContext(UserContext);
  return <Text>{user.name}</Text>;
}
```

---

## Redux - Configuration

```jsx
// Store Redux
const store = {
  user: { 
    name: 'John', 
    age: 25 
  },
  theme: 'dark',
  notifications: []
}
```

---

## Redux - Actions

```jsx
// Action
const updateUser = (userData) => ({
  type: 'UPDATE_USER',
  payload: userData
});
```

---

# Navigation avec React Navigation

## Stack Navigator - Structure

```jsx
// Configuration de base
const Stack = createStackNavigator();
```

---

## Stack Navigator - Composant

```jsx
function App() {
  return (
    <Stack.Navigator>
      <Stack.Screen 
        name="Home" 
        component={HomeScreen} 
      />
      <Stack.Screen 
        name="Profile" 
        component={ProfileScreen} 
      />
    </Stack.Navigator>
  );
}
```

---

## Tab Navigator - Structure

```jsx
// Configuration de base
const Tab = createBottomTabNavigator();
```

---

## Tab Navigator - Composant

```jsx
function App() {
  return (
    <Tab.Navigator>
      <Tab.Screen 
        name="Feed" 
        component={FeedScreen} 
        options={{
          tabBarIcon: ({ color }) => (
            <Icon name="home" color={color} />
          )
        }}
      />
      <Tab.Screen 
        name="Messages" 
        component={MessagesScreen} 
      />
    </Tab.Navigator>
  );
}
```

---

## Drawer Navigator - Configuration

```jsx
const Drawer = createDrawerNavigator();

function App() {
  return (
    <Drawer.Navigator>
      <Drawer.Screen 
        name="Home" 
        component={HomeScreen} 
      />
      <Drawer.Screen 
        name="Settings" 
        component={SettingsScreen} 
      />
    </Drawer.Navigator>
  );
}
```

---

# Navigation avec paramètres

## Navigation vers un écran

```jsx
// Navigation avec paramètres
navigation.navigate('Profile', { 
  userId: 123,
  userName: 'John'
});

// Récupération dans l'écran de destination
function ProfileScreen({ route }) {
  const { userId, userName } = route.params;
  return <Text>Profil de {userName}</Text>;
}
```

---

## Mise à jour du titre

```jsx
// Mise à jour dynamique du titre
React.useLayoutEffect(() => {
  navigation.setOptions({
    title: `Profil de ${route.params.userName}`
  });
}, [navigation, route.params.userName]);
```

---
routeAlias: 'exercice-navigation-profils'
---

# Exercice : Navigation entre profils

## Configuration initiale

1. Installation des dépendances :
```bash
npm install @react-navigation/native @react-navigation/stack
```

2. Création des composants nécessaires :
- ProfileList
- UserProfile

---

## Structure ProfileList - Imports et données

```jsx
// ProfileList.js - Partie 1
import React from 'react';
import { View, FlatList, TouchableOpacity, Text, StyleSheet } from 'react-native';

const profiles = [
  { 
    id: '1', 
    name: 'John Doe', 
    bio: 'Passionné de voyages', 
    imageUrl: 'https://randomuser.me/api/portraits/men/1.jpg' 
  },
  // ... autres profils
];
```

---

## Structure ProfileList - Composant

```jsx
// ProfileList.js - Partie 2
const ProfileList = ({ navigation }) => {
  const renderItem = ({ item }) => (
    <TouchableOpacity
      style={styles.item}
      onPress={() => navigation.navigate('UserProfile', item)}
    >
      <Text style={styles.name}>{item.name}</Text>
    </TouchableOpacity>
  );

  return (
    <View style={styles.container}>
      <FlatList
        data={profiles}
        renderItem={renderItem}
        keyExtractor={item => item.id}
      />
    </View>
  );
};
```

---

## Styles ProfileList

```jsx
// ProfileList.js - Partie 3
const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: 22,
  },
  item: {
    padding: 10,
    fontSize: 18,
    height: 44,
  },
  name: {
    fontSize: 18,
  },
});

export default ProfileList;
```

---

## Configuration de la navigation - Structure

```jsx
// App.js - Partie 1
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import ProfileList from './components/ProfileList';
import UserProfile from './components/UserProfile';

const Stack = createStackNavigator();
```

---

## Configuration de la navigation - Composant

```jsx
// App.js - Partie 2
export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="ProfileList">
        <Stack.Screen 
          name="ProfileList" 
          component={ProfileList} 
          options={{ title: 'Profils' }}
        />
        <Stack.Screen 
          name="UserProfile" 
          component={UserProfile} 
          options={({ route }) => ({ 
            title: route.params.name 
          })}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

Cet exercice vous permet de pratiquer la mise en place d'une navigation de base entre écrans, le passage de paramètres, et la mise à jour dynamique des titres d'écran.
