---
routeAlias: 'bases-react-native'
---

# Bases de React Native

- **Composants React Native**
  - View : conteneur générique (équivalent à une div)
  - Text : affichage de texte
  - Image : affichage d'images

---

# Bases de React Native (suite)

- **Composants React Native (suite)**
  - ScrollView : conteneur avec défilement
  - FlatList : liste optimisée pour de grandes quantités de données

- **JSX et styles**
  - Utilisation de JSX pour décrire l'interface utilisateur
  - Styles inspirés de CSS, mais avec des différences clés
  - StyleSheet.create pour définir des styles

---

# Bases de React Native (suite)

- **Props et State**
  - Props : données passées d'un composant parent à un enfant
  - State : données gérées à l'intérieur d'un composant

- **Gestion des événements**
  - Utilisation de props comme onPress pour gérer les interactions

---
routeAlias: 'exercice-profil-utilisateur'
---

## Exercice : Création d'un profil utilisateur simple

Créons un composant de profil utilisateur pour notre application TinderLikeApp.

1. Créez un nouveau fichier `components/UserProfile.js`
2. Implémentez un composant fonctionnel avec une image, un nom et une courte bio
3. Utilisez ce composant dans `App.js`

---

## Exercice : Création d'un profil utilisateur simple (suite)

Voici le code pour `UserProfile.js` :

```jsx
import React from 'react';
import { View, Text, Image, StyleSheet } from 'react-native';

const UserProfile = ({ name, bio, imageUrl }) => {
  return (
    <View style={styles.container}>
      <Image source={{ uri: imageUrl }} style={styles.image} />
      <Text style={styles.name}>{name}</Text>
      <Text style={styles.bio}>{bio}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    alignItems: 'center',
    padding: 20,
  },
  image: {
    width: 150,
    height: 150,
    borderRadius: 75,
    marginBottom: 20,
  },
  name: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 10,
  },
  bio: {
    fontSize: 16,
    textAlign: 'center',
  },
});

export default UserProfile;
```

---

## Exercice : Création d'un profil utilisateur simple (suite)

Maintenant, modifiez `App.js` pour utiliser ce composant :

```jsx
import React from 'react';
import { View, StyleSheet } from 'react-native';
import UserProfile from './components/UserProfile';

export default function App() {
  return (
    <View style={styles.container}>
      <UserProfile
        name="John Doe"
        bio="Passionné de voyages et de photographie. Toujours à la recherche de nouvelles aventures !"
        imageUrl="https://randomuser.me/api/portraits/men/1.jpg"
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
});
```

Ce exercice vous permet de pratiquer la création de composants, l'utilisation de props, et le styling en React Native. Dans les prochaines sections, nous allons enrichir ce profil et ajouter plus de fonctionnalités à notre application TinderLikeApp.
