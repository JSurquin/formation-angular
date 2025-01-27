---
routeAlias: 'composants-natifs-styling'
---

# Composants natifs et styling

- **Composants spécifiques à la plateforme**
  - SafeAreaView : équivalent à padding-top pour éviter la notch sur iOS (pas d'équivalent web)
  - StatusBar : équivalent à la balise meta theme-color en web
  - Platform : équivalent à @media queries en CSS pour détecter le device

---

## Platform API

```jsx
import { Platform } from 'react-native';

const styles = {
  padding: Platform.OS === 'ios' ? 20 : 16,
  ...Platform.select({
    ios: {
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
    },
    android: {
      elevation: 4,
    },
  }),
};
```

---

# Styling en React Native

## Flexbox Layout

```jsx
const styles = {
  container: {
    flex: 1,
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center',
  },
  item: {
    flex: 1,
    margin: 5,
  }
};
```

---

## StyleSheet et Dimensions

```jsx
const styles = StyleSheet.create({
  container: {
    width: Dimensions.get('window').width,
    backgroundColor: '#fff'
  }
});
```

---

# Composants d'interaction

## TouchableOpacity Example

```jsx
<TouchableOpacity onPress={() => console.log('Pressed!')}>
  <Text>Appuyez ici</Text>
</TouchableOpacity>
```

## TouchableHighlight Example

```jsx
<TouchableHighlight underlayColor="#DDDDDD">
  <Text>Avec effet de surbrillance</Text>
</TouchableHighlight>
```

---

# Composants de saisie

## TextInput Example

```jsx
<TextInput
  placeholder="Entrez votre texte"
  onChangeText={text => setTexte(text)}
/>
```

## Switch Example

```jsx
<Switch
  value={isEnabled}
  onValueChange={toggleSwitch}
/>
```

---
routeAlias: 'exercice-amelioration-profil'
---

## Exercice : Amélioration du profil utilisateur

Améliorons notre composant UserProfile en ajoutant plus d'interactivité et en utilisant des composants natifs avancés.

1. Modifiez `components/UserProfile.js` pour inclure un bouton "Like" et un champ de saisie pour un message
2. Utilisez SafeAreaView pour gérer les zones sûres sur iOS
3. Ajoutez un style conditionnel basé sur la plateforme

---

## Imports et structure de base

```jsx
// UserProfile.js - Partie 1
import React, { useState } from 'react';
import { 
  View, 
  Text, 
  Image, 
  StyleSheet, 
  TouchableOpacity, 
  TextInput, 
  SafeAreaView, 
  Platform 
} from 'react-native';

const UserProfile = ({ name, bio, imageUrl }) => {
  const [liked, setLiked] = useState(false);
  const [message, setMessage] = useState('');
```

---

## Rendu du composant

```jsx
// UserProfile.js - Partie 2
  return (
    <SafeAreaView style={styles.container}>
      <Image source={{ uri: imageUrl }} style={styles.image} />
      <Text style={styles.name}>{name}</Text>
      <Text style={styles.bio}>{bio}</Text>
      <TouchableOpacity 
        style={[styles.button, liked && styles.buttonLiked]} 
        onPress={() => setLiked(!liked)}
      >
        <Text style={styles.buttonText}>
          {liked ? 'Liked!' : 'Like'}
        </Text>
      </TouchableOpacity>
      <TextInput
        style={styles.input}
        placeholder="Envoyer un message..."
        value={message}
        onChangeText={setMessage}
      />
    </SafeAreaView>
  );
};
```

---

## Styles - Première partie

```jsx
// UserProfile.js - Partie 3
const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    padding: 20,
    ...Platform.select({
      ios: {
        backgroundColor: '#F0F0F0',
      },
      android: {
        backgroundColor: '#E0E0E0',
      },
    }),
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
```

---

## Styles - Deuxième partie

```jsx
// UserProfile.js - Partie 4
  bio: {
    fontSize: 16,
    textAlign: 'center',
    marginBottom: 20,
  },
  button: {
    backgroundColor: '#FF6B6B',
    padding: 10,
    borderRadius: 5,
    marginBottom: 20,
  },
  buttonLiked: {
    backgroundColor: '#4ECDC4',
  },
  buttonText: {
    color: 'white',
    fontSize: 16,
    fontWeight: 'bold',
  },
  input: {
    width: '100%',
    borderWidth: 1,
    borderColor: '#ddd',
    padding: 10,
    borderRadius: 5,
  },
});

export default UserProfile;
```

---

## Utilisation dans App.js

```jsx
// App.js - Partie 1
import React from 'react';
import { View, StyleSheet } from 'react-native';
import UserProfile from './components/UserProfile';

export default function App() {
  return (
    <View style={styles.container}>
      <UserProfile
        name="John Doe"
        bio="Passionné de voyages et de photographie."
        imageUrl="https://randomuser.me/api/portraits/men/1.jpg"
      />
    </View>
  );
}
```

---

## Styles App.js

```jsx
// App.js - Partie 2
const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
});
```

Cet exercice vous permet de pratiquer l'utilisation de composants natifs plus avancés, la gestion d'état local avec useState, et l'application de styles conditionnels basés sur la plateforme.
