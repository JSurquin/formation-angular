---
routeAlias: 'composants-natifs-styling'
---

# Composants natifs et styling

- **Composants spécifiques à la plateforme**
  - SafeAreaView : gestion des zones sûres sur iOS
  - StatusBar : personnalisation de la barre de statut
  - Platform : détection et conditionnement selon la plateforme

---

# Composants natifs et styling (suite)

- **Styling avancé**
  - Flexbox pour la mise en page
  - Dimensions pour obtenir la taille de l'écran
  - Utilisation de StyleSheet pour optimiser les performances

---

# Composants natifs et styling (suite)

- **Composants d'interaction**
  - TouchableOpacity : zone tactile avec effet d'opacité
  - TouchableHighlight : zone tactile avec effet de surbrillance
  - Button : bouton standard de la plateforme

- **Composants de saisie**
  - TextInput : champ de saisie de texte
  - Switch : interrupteur on/off

---
routeAlias: 'exercice-amelioration-profil'
---

## Exercice : Amélioration du profil utilisateur

Améliorons notre composant UserProfile en ajoutant plus d'interactivité et en utilisant des composants natifs avancés.

1. Modifiez `components/UserProfile.js` pour inclure un bouton "Like" et un champ de saisie pour un message
2. Utilisez SafeAreaView pour gérer les zones sûres sur iOS
3. Ajoutez un style conditionnel basé sur la plateforme

---

## Exercice : Amélioration du profil utilisateur (suite)

Voici le début du code mis à jour pour `UserProfile.js` :

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

const UserProfile = ({ name, bio, imageUrl }) => {
  const [liked, setLiked] = useState(false);
  const [message, setMessage] = useState('');

  // ... (suite du code dans la prochaine slide)
```

---

## Exercice : Amélioration du profil utilisateur (suite)

Suite du code pour `UserProfile.js` :

```jsx
  return (
    <SafeAreaView style={styles.container}>
      <Image source={{ uri: imageUrl }} style={styles.image} />
      <Text style={styles.name}>{name}</Text>
      <Text style={styles.bio}>{bio}</Text>
      <TouchableOpacity 
        style={[styles.button, liked && styles.buttonLiked]} 
        onPress={() => setLiked(!liked)}
      >
        <Text style={styles.buttonText}>{liked ? 'Liked!' : 'Like'}</Text>
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

// ... (styles dans la prochaine slide)
```

---

## Exercice : Amélioration du profil utilisateur (suite)

Styles pour `UserProfile.js` :

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

## Exercice : Amélioration du profil utilisateur (suite)

Mettez à jour `App.js` pour utiliser ce composant amélioré :

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
  },
});
```

Cet exercice vous permet de pratiquer l'utilisation de composants natifs plus avancés, la gestion d'état local avec useState, et l'application de styles conditionnels basés sur la plateforme.
