---
routeAlias: 'configuration-environnement'
---

# Configuration de l'environnement

Reparlons rapidement de tout ce que nous avons du installer et ce que nous allons configurer.

## Installation de Node.js

```bash
# Téléchargement et installation de Node.js
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install node
```

---

## Installation d'Expo CLI

```bash
# Installation globale d'Expo CLI
npm install -g expo-cli
# deprecated !
```

utilisez plutot :

```bash
npx expo `laCommande'
```

pour créer une app :

```bash
npx create-expo-app@latest
```

---

## Configuration de l'éditeur

Je vous conseille sur vs code ou cursor ou autre d'avoir , déjà installer les plugins pour prettier (et ses nouveaux concurrents), eslint etc

---

## Configuration de l'éditeur (suite)

```json
// settings.json - Partie 2
{
  "prettier.semi": true,
  "prettier.singleQuote": true,
  "prettier.printWidth": 80,
  "prettier.tabWidth": 2
}
```

---

# Installation des dépendances

## Dépendances de base

Elles sont déjà installer avec expo , seulement si vous faite un projet sans expo.

```bash
npm install @react-navigation/native @react-navigation/stack
```

---

## Dépendances supplémentaires

```bash
npx expo install react-native-gesture-handler react-native-reanimated
```

---

# Configuration de l'émulateur

## Android Studio

```bash
# Configuration des variables d'environnement
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools
```

Pour android studio, nous n'allons pas nous attarder dessus dans cette formation.
Cependant si nous avons le temps nous allons essayer de l'utiliser pour une build.

---

## Xcode (macOS uniquement)

Ne pas oubliez d'installer [brew.sh](https://brew.sh/)

```bash
# Installation de CocoaPods
sudo gem install cocoapods
```

---

# Résultat

Votre environnement de développement est maintenant configuré pour React Native et Expo.

---

# Configuration de l'environnement de développement (suite)

- **Éditeur de code**
  - Recommandation : Visual Studio Code avec les extensions React Native
  - Autres options : WebStorm, Atom, Sublime Text

- **Simulateurs et émulateurs**
  - iOS : Xcode (Mac uniquement)
  - Android : Android Studio avec AVD Manager

- **Application Expo Go**
  - Téléchargez sur votre appareil mobile depuis l'App Store ou Google Play
  - Permet de tester l'application sur un appareil physique

---
routeAlias: 'exercice-configuration-projet'
---

## Exercice : Configuration de votre projet TinderLikeApp

1. Ouvrez votre projet TinderLikeApp dans Visual Studio Code

---

## Exercice : Configuration de votre projet TinderLikeApp (suite)

4. Initialisez un dépôt Git :
   ```
   git init
   git add .
   git commit -m "Initial commit"
   ```

5. Lancez l'application avec `expo start` et testez-la sur :
   - Un simulateur iOS (Mac uniquement)
   - Un émulateur Android
   - Votre appareil mobile avec Expo Go

---
routeAlias: 'structure-projet-expo'
---

## Structure du projet Expo moderne (2024/2025)

Voici la structure actuelle d'un projet créé avec `create-expo-app` :

---

```
MonProjet/
├── app/                    # Dossier principal avec le nouveau système de routing
│   ├── _layout.tsx        # Layout principal de l'application
│   ├── index.tsx          # Page d'accueil
│   ├── (tabs)/            # Groupe de routes pour les tabs
│   │   ├── home.tsx       # Tab Home
│   │   ├── profile.tsx    # Tab Profile
│   │   └── _layout.tsx    # Layout spécifique aux tabs
│   └── (auth)/            # Groupe de routes pour l'authentification
│       ├── login.tsx      # Page de login
│       └── register.tsx   # Page d'inscription
├── assets/                # Images, fonts et autres ressources
├── components/            # Composants réutilisables
├── constants/            # Constants (colors, dimensions, etc.)
├── hooks/                # Custom hooks
├── services/            # Services (API, auth, etc.)
├── types/               # Types TypeScript
├── utils/               # Fonctions utilitaires
├── app.json             # Configuration Expo
├── babel.config.js      # Configuration Babel
├── package.json         # Dépendances et scripts
└── tsconfig.json        # Configuration TypeScript
```

---

## Points clés de la nouvelle structure

- **Routing basé sur les fichiers** : 
  - Plus besoin de React Navigation explicite
  - Les fichiers dans `app/` définissent automatiquement les routes
  - Système similaire à Next.js

---

## Organisation des routes

- **Groupes de routes** :
  - `(tabs)` : Routes avec navigation par tabs
  - `(auth)` : Routes pour l'authentification
  - Les parenthèses indiquent des groupes logiques

- **Fichiers spéciaux** :
  - `_layout.tsx` : Définit le layout d'un groupe de routes
  - `index.tsx` : Page par défaut d'un dossier

---

## Avantages de la nouvelle structure

- Organisation plus intuitive
- Routing automatique
- Support TypeScript par défaut
- Meilleure séparation des préoccupations
- Plus facile à maintenir et à faire évoluer

Cette nouvelle structure est inspirée des frameworks web modernes comme Next.js et offre une meilleure expérience de développement.
