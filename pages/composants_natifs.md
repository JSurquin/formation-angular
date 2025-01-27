---
routeAlias: 'composants-natifs-styling'
---

# Composants natifs et styling

- **Composants spécifiques à la plateforme**
  - SafeAreaView : équivalent à padding-top pour éviter la notch sur iOS (pas d'équivalent web)
  - StatusBar : équivalent à la balise meta theme-color en web
  - Platform : équivalent à @media queries en CSS pour détecter le device

---

## Platform API - Configuration

```jsx
import { Platform } from 'react-native';

const styles = {
  padding: Platform.OS === 'ios' ? 20 : 16,
};
```

---

## Platform API - Sélection

```jsx
const styles = {
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

---

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

---

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

# Exercice : Amélioration du profil utilisateur

Améliorons notre composant UserProfile en ajoutant plus d'interactivité et en utilisant des composants natifs avancés.

1. Modifiez `components/UserProfile.js` pour inclure un bouton "Like" et un champ de saisie pour un message
2. Utilisez SafeAreaView pour gérer les zones sûres sur iOS
3. Ajoutez un style conditionnel basé sur la plateforme

---

## Structure du composant - Partie 1

```jsx
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
```

---

## Structure du composant - Partie 2

```jsx
const UserProfile = ({ name, bio, imageUrl }) => {
  const [liked, setLiked] = useState(false);

  return (
    <View style={styles.container}>
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
    </View>
  );
};
```

---

## Styles - Partie 1

```jsx
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
});
```

---

## Styles - Partie 2

```jsx
const styles = StyleSheet.create({
  name: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 10,
  },
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
});

export default UserProfile;
```

---

# Résultat de l'exercice

Cet exercice vous permet de pratiquer l'utilisation de composants natifs plus avancés, la gestion d'état local avec useState, et l'application de styles conditionnels basés sur la plateforme.
