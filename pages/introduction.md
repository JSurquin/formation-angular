---
routeAlias: 'introduction-react-native'
---

# Introduction à React Native

- **Qu'est-ce que React Native ?**
  - Framework pour développer des applications mobiles natives sur ios et android
  - Utilise React et JavaScript pour créer des interfaces utilisateur

- **Avantages de React Native**
  - Développement multiplateforme (iOS et Android)
  - Performance proche du natif (avec quelques ajustements nécessaires pour certains modules)
  - Large communauté et écosystème riche
  - Basé sur React, courbe d'apprentissage très facile au début.

---

# Introduction à Expo

- **Qu'est-ce qu'Expo ?**
  - Ensemble d'outils et de services pour React Native
  - Simplifie le développement et le déploiement énormement

- **Pourquoi utiliser Expo ?**
  - Configuration rapide et facile
  - Accès aux API natives sans configuration complexe
  - Mise à jour Over-The-Air (OTA)

---

# Disclaimer concernant React Native

- **React Native n'est pas toujours nécessaire**
  - Pour des applications simples, des solutions web peuvent suffire
  - PWA (Progressive Web Apps) comme alternative viable
  - Solutions natives pures parfois plus adaptées selon les besoins

---

# Disclaimer concernant React Native (suite)

- **React Native n'est pas toujours suffisant**
  - Certaines fonctionnalités nécessitent du code natif (Kotlin/Swift)
  - Besoin de compétences natives pour des fonctionnalités complexes
  - Exemples :
    - Modules natifs personnalisés
    - Optimisations de performance spécifiques
    - Intégrations matérielles avancées

---

# Disclaimer concernant React Native (suite)

- **Compétences complémentaires requises**
  - Connaissance basique d'Android (Kotlin/Java) peut être nécessaire
  - Notions d'iOS (Swift/Objective-C) parfois indispensables
  - Compréhension de l'architecture native mobile

---

# Disclaimer concernant Expo

- **Limitations de personnalisation**
  - Accès limité aux modules natifs personnalisés
  - Impossibilité d'utiliser certaines bibliothèques natives non supportées
  - Configuration native restreinte

---

# Disclaimer concernant Expo (suite)

- **Taille des applications**
  - Applications plus volumineuses car inclusion de nombreuses dépendances
  - Impact sur le temps de téléchargement initial

- **Dépendance à Expo**
  - Mises à jour forcées de l'écosystème Expo
  - Difficultés potentielles pour "éjecter" le projet plus tard
  - Dépendance aux serveurs Expo pour certaines fonctionnalités

---

# Disclaimer concernant Expo (suite)

- **Performance**
  - Légère baisse de performance par rapport à React Native pur
  - Temps de compilation plus longs
  - Consommation mémoire plus importante

---

# Donc comment faire ?

### Donc dans ce cas , il faudra ejecter expo avec dans votre terminal

```bash
expo eject
```

---

# Metro : Le bundler de React Native

- **Qu'est-ce que Metro ?**
  - Bundler JavaScript par défaut pour React Native
  - Compile et empaquette le code source
  - Gère les assets (images, polices, etc.)

---

# Metro : Fonctionnalités

- **Fonctionnalités principales**
  - Hot Reloading (HMR)
  - Source maps pour le debugging
  - Optimisation du bundle
  - Gestion des dépendances

---

# Metro : Architecture

- **Architecture**
  - Serveur de développement intégré
  - Cache intelligent pour des builds rapides
  - Support des transformations personnalisées
  - Compatible avec les plugins Babel

---

# Metro : Avantages

- **Avantages**
  - Rapide et optimisé pour mobile
  - Configuration simple
  - Intégration native avec React Native
  - Support du Fast Refresh

---

# A quoi ressemble Metro ?

- **Interface de Metro**
  - Console avec informations de build
  - Affichage des erreurs et warnings
  - QR code pour connexion des appareils
  - Menu avec options de développement

- **Fonctionnalités visibles**
  - Logs en temps réel
  - État du bundle et du HMR
  - Liste des appareils connectés
  - Statistiques de performance

---

# Des concurrents à Metro ?

- **Webpack**
  - Plus généraliste
  - Écosystème plus large
  - Configuration plus complexe
  - Moins optimisé pour mobile

- **Vite**
  - Plus récent et moderne
  - Très rapide en développement
  - Support expérimental de React Native
  - Communauté grandissante

- **esbuild**
  - Ultra rapide
  - Écrit en Go
  - Support limité pour React Native
  - Utilisé comme dépendance par d'autres bundlers

---
routeAlias: 'exercice-hello-world'
---

## Installation d'Expo CLI

```bash
# Installation globale d'Expo CLI
npm install -g expo-cli
```

---

## Création du projet

```bash
# Création d'un nouveau projet
expo init TinderLikeApp
```

---

## Navigation et lancement

```bash
# Navigation dans le dossier
cd TinderLikeApp

# Lancement de l'application
expo start
```

---

# Test de l'application

Vous pouvez tester rapidement le développement moderne :

- Installer expo sur android/ios (play store ou app store)
- Scanner le QRcode dans votre terminal (Metro)
- Ça va automatiquement ouvrir expo sur votre mobile et pré compiler

---

# Hot Module Reload

Vous pouvez voir votre application en temps réel, et à chaque changement le HMR (Hot module reload) se lance, vous êtes donc toujours à jour !

---

## Structure du composant Hello World

```jsx
// Structure du composant
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.text}>Hello World</Text>
    </View>
  );
}
```

---

## Styles de l'application

```jsx
// Styles du composant
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

---

# Félicitations !

Vous avez créé votre première application React Native avec Expo.
