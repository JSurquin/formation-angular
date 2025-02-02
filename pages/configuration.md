---
routeAlias: 'configuration-environnement'
---

# Configuration de l'environnement (2024/2025)

## Installation de Node.js et des outils de base

```bash
# Installation de Node.js via nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install node

# Installation des outils globaux
npm install -g typescript
```

---

## Création d'un projet Expo moderne

```bash
# Création du projet avec le template TypeScript
npx create-expo-app@latest -t expo-template-typescript

# OU avec le template Tabs (recommandé pour une app type Tinder)
npx create-expo-app@latest -t tabs
```

---

## Installation des dépendances essentielles

```bash
npx expo install nativewind@3.0.0
npm install tailwindcss@3.3.2 --save-dev

# Animations et gestes
npx expo install react-native-reanimated
npx expo install react-native-gesture-handler

# UI et effets
npx expo install expo-linear-gradient
npm install react-native-deck-swiper
```

---

## Configuration de NativeWind

```js
// tailwind.config.js
module.exports = {
  content: [
    "./app/**/*.{js,jsx,ts,tsx}",
    "./components/**/*.{js,jsx,ts,tsx}"
  ],
  presets: [require("nativewind/preset")],
  theme: {
    extend: {
      colors: {
        primary: {
          500: '#FF6B6B',
          600: '#FF5252',
        }
      }
    },
  },
}
```

---

## Configuration de Babel pour Reanimated

```js
// babel.config.js
module.exports = function(api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: [
      'nativewind/babel',
      'react-native-reanimated/plugin',
    ],
  };
};
```

---

## Configuration de Metro

```js
// metro.config.js
const { getDefaultConfig } = require('expo/metro-config');
const { withNativeWind } = require('nativewind/metro');

const config = getDefaultConfig(__dirname);

module.exports = withNativeWind(config, {
  input: './global.css',
});
```

---

## Création des fichiers de style

```css
/* global.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

## Configuration TypeScript

```json
// tsconfig.json
{
  "extends": "expo/tsconfig.base",
  "compilerOptions": {
    "strict": true,
    "paths": {
      "@/*": ["./*"]
    }
  },
  "include": [
    "**/*.ts",
    "**/*.tsx",
    ".expo/types/**/*.ts",
    "expo-env.d.ts"
  ]
}
```

---

## Structure du projet recommandée (2024/2025)

```
MonProjet/
├── app/                    # Routing avec Expo Router
├── components/            # Composants réutilisables
├── hooks/                # Custom hooks
├── services/            # Services (API, etc.)
├── types/               # Types TypeScript
├── utils/               # Utilitaires
├── assets/             # Images, fonts
├── global.css          # Styles Tailwind
├── tailwind.config.js  # Config Tailwind
├── babel.config.js     # Config Babel
├── metro.config.js     # Config Metro
└── tsconfig.json       # Config TypeScript
```

Cette configuration moderne permet de développer des applications React Native performantes avec un excellent DX (Developer Experience).

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

## Résultat

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
