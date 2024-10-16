---
routeAlias: 'gestion-etat-navigation'
---

# Gestion de l'état et navigation

- **Gestion de l'état local**
  - useState pour l'état local des composants
  - useReducer pour des états plus complexes

- **Gestion de l'état global**
  - Context API pour partager l'état entre composants
  - Redux ou MobX pour des applications plus complexes

---

# Gestion de l'état et navigation (suite)

- **Navigation avec React Navigation**
  - Stack Navigator pour la navigation entre écrans
  - Tab Navigator pour les onglets
  - Drawer Navigator pour les menus latéraux

- **Passage de paramètres entre écrans**
  - Utilisation de route.params
  - Mise à jour du titre de l'écran dynamiquement

---
routeAlias: 'exercice-navigation-profils'
---

## Exercice : Ajout de navigation entre profils

Créons une navigation simple entre plusieurs profils d'utilisateurs pour notre application TinderLikeApp.

1. Installez React Navigation : `npm install @react-navigation/native @react-navigation/stack`
2. Créez un nouveau composant `ProfileList` qui affiche une liste de profils
3. Implémentez une navigation stack entre `ProfileList` et `UserProfile`

---

## Exercice : Ajout de navigation entre profils (suite)

Voici le code pour `ProfileList.js` :

```
import React from 'react';
import { View, FlatList, TouchableOpacity, Text, StyleSheet } from 'react-native';

const profiles = [
  { id: '1', name: 'John Doe', bio: 'Passionné de voyages', imageUrl: 'https://randomuser.me/api/portraits/men/1.jpg' },
  { id: '2', name: 'Jane Smith', bio: 'Amoureuse de la nature', imageUrl: 'https://randomuser.me/api/portraits/women/1.jpg' },
  { id: '3', name: 'Mike Johnson', bio: 'Sportif et aventurier', imageUrl: 'https://randomuser.me/api/portraits/men/2.jpg' },
];

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

## Exercice : Ajout de navigation entre profils (suite)

Maintenant, mettez à jour `App.js` pour implémenter la navigation :

```jsx
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import ProfileList from './components/ProfileList';
import UserProfile from './components/UserProfile';

const Stack = createStackNavigator();

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
          options={({ route }) => ({ title: route.params.name })}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

Cet exercice vous permet de pratiquer la mise en place d'une navigation de base entre écrans, le passage de paramètres entre les écrans, et la mise à jour dynamique du titre de l'écran.
