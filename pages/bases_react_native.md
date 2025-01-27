---
routeAlias: 'bases-react-native'
---

# Bases de React Native

- **Composants React Native**
  - View : conteneur générique (équivalent à une `div` en HTML)
  - Text : conteneur pour le texte (équivalent à `p`, `h1`, `span` en HTML)
  - Image : affichage d'images (équivalent à `img` en HTML)
  - TextInput : champ de saisie (équivalent à `input` en HTML)
  - TouchableOpacity : zone cliquable (équivalent à `button` en HTML)

---

# Bases de React Native (suite)

- **Composants React Native (suite)**
  - ScrollView : conteneur avec défilement (équivalent à une `<div>` avec overflow-y: scroll en CSS)
  - FlatList : liste optimisée pour de grandes quantités de données (équivalent à une boucle .map() sur un tableau en React Web, mais avec virtualisation)

- **JSX et styles**
  - Utilisation de JSX ou TSX pour décrire l'interface utilisateur
  - Styles inspirés de CSS, mais avec des différences clés, alors ça je vous le résume à l'oral
  - StyleSheet.create pour définir des styles ou d'autres packages à importer sois même

---

# Bases de React Native (suite)

# Props et State

## Props : Passage de données parent-enfant

```jsx
// Composant parent
<UserProfile name="John" age={25} />
```

```jsx
// Composant enfant UserProfile
const UserProfile = (props) => {
  return (
    <Text>
      Nom: 
      {props.name}
      , Age: 
      {props.age}
    </Text>
  )
}
```

---

## State : Gestion de l'état interne

```jsx
const Counter = () => {
  const [count, setCount] = useState(0)
  
  return (
    <TouchableOpacity 
      onPress={() => setCount(count + 1)}
    >
      <Text>
        Compteur: 
        {count}
      </Text>
    </TouchableOpacity>
  )
}
```

- **Gestion des événements**
  - Utilisation de props comme onPress pour gérer les interactions

**Car oui sur mobile ce n'est pas un onClick mais un onPress**

---
routeAlias: 'exercice-profil-utilisateur'
---

## Exercice : Création d'un profil utilisateur simple

Créons un composant de profil utilisateur pour notre application **TinderLikeApp**.

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
