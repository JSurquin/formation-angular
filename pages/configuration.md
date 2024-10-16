---
routeAlias: 'configuration-environnement'
---

# Configuration de l'environnement de développement

- **Installation de Node.js et npm**
  - Téléchargez et installez Node.js depuis [nodejs.org](https://nodejs.org/)
  - npm (Node Package Manager) est inclus avec Node.js

- **Installation d'Expo CLI**
  - Ouvrez un terminal et exécutez : `npm install -g expo-cli`

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

- `App.js` : Point d'entrée de votre application
- `app.json` : Configuration de l'application Expo
- `assets/` : Images et autres ressources statiques
- `babel.config.js` : Configuration de Babel pour la transpilation
- `package.json` : Dépendances et scripts npm

Dans les prochaines sections, nous allons enrichir cette structure avec de nouveaux composants et fonctionnalités pour notre application TinderLikeApp.
