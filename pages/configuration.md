---
routeAlias: 'configuration-environnement'
---

# Configuration de l'environnement

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
```

---

## Configuration de l'éditeur

```json
// settings.json - Partie 1
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

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

## Configuration ESLint

```json
// .eslintrc.js - Partie 1
module.exports = {
  root: true,
  extends: '@react-native-community',
  rules: {
    'prettier/prettier': 'error',
    'react-native/no-inline-styles': 'error'
  }
}
```

---

## Configuration ESLint (suite)

```json
// .eslintrc.js - Partie 2
{
  "plugins": [
    "react",
    "react-native"
  ],
  "settings": {
    "react": {
      "version": "detect"
    }
  }
}
```

---

# Installation des dépendances

## Dépendances de base

```bash
npm install @react-navigation/native @react-navigation/stack
```

---

## Dépendances supplémentaires

```bash
expo install react-native-gesture-handler react-native-reanimated
```

---

# Configuration de l'émulateur

## Android Studio

```bash
# Configuration des variables d'environnement
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools
```

---

## Xcode (macOS uniquement)

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
2. Installez l'extension "React Native Tools" dans VS Code
3. Créez un fichier `.gitignore` à la racine du projet avec le contenu suivant :

```
node_modules/
.expo/
dist/
npm-debug.*
*.jks
*.p8
*.p12
*.key
*.mobileprovision
*.orig.*
web-build/
.DS_Store
```

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

## Structure du projet Expo

Voici la structure de base de votre projet TinderLikeApp :

```
TinderLikeApp/
├── App.js
├── app.json
├── assets/
│   ├── adaptive-icon.png
│   ├── favicon.png
│   ├── icon.png
│   └── splash.png
├── babel.config.js
├── package.json
└── node_modules/
```

---

- `App.js` : Point d'entrée de votre application
- `app.json` : Configuration de l'application Expo
- `assets/` : Images et autres ressources statiques
- `babel.config.js` : Configuration de Babel pour la transpilation
- `package.json` : Dépendances et scripts npm

Dans les prochaines sections, nous allons enrichir cette structure avec de nouveaux composants et fonctionnalités pour notre application TinderLikeApp.
