---
routeAlias: 'introduction-react-native'
---

# Introduction à React Native et Expo

- **Qu'est-ce que React Native ?**
  - Framework pour développer des applications mobiles natives
  - Utilise React et JavaScript pour créer des interfaces utilisateur

- **Avantages de React Native**
  - Développement multiplateforme (iOS et Android)
  - Performance proche du natif
  - Large communauté et écosystème riche

---

# Introduction à React Native et Expo (suite)

- **Qu'est-ce qu'Expo ?**
  - Ensemble d'outils et de services pour React Native
  - Simplifie le développement et le déploiement

- **Pourquoi utiliser Expo ?**
  - Configuration rapide et facile
  - Accès aux API natives sans configuration complexe
  - Mise à jour Over-The-Air (OTA)

---
routeAlias: 'exercice-hello-world'
---

## Exercice : Créer votre première application "Hello World"

1. Installez Expo CLI globalement : 
   ```
   npm install -g expo-cli
   ```
2. Créez un nouveau projet : 
   ```
   expo init TinderLikeApp
   ```
3. Choisissez le template "blank"
4. Naviguez dans le dossier du projet : 
   ```
   cd TinderLikeApp
   ```
5. Lancez l'application : 
   ```
   expo start
   ```

---

## Exercice : Créer votre première application "Hello World" (suite)

Modifiez le fichier `App.js` pour afficher "Hello World" :

```jsx
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.text}>Hello World</Text>
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
  text: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
});
```

Félicitations ! Vous avez créé votre première application React Native avec Expo.
