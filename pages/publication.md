---
routeAlias: 'publication-application'
---

# Publication de l'application

- **Préparation de l'application pour la production**
  - Configuration des icônes et des écrans de démarrage
  - Optimisation des performances

- **Publication sur l'App Store (iOS)**
  - Création d'un compte développeur Apple
  - Utilisation d'Xcode pour générer l'archive
  - Soumission via App Store Connect

---

# Publication de l'application (suite)

- **Publication sur le Google Play Store (Android)**
  - Création d'un compte développeur Google Play
  - Génération du bundle Android signé
  - Soumission via la Google Play Console

- **Utilisation d'Expo EAS (Expo Application Services)**
  - Configuration d'EAS Build
  - Création de builds pour iOS et Android
  - Soumission automatisée aux stores

---
routeAlias: 'exercice-preparation-publication'
---

## Exercice : Préparation de l'application pour la publication

Préparons notre application TinderLikeApp pour la publication sur les stores.

1. Configurez les icônes et l'écran de démarrage :
   - Remplacez les icônes par défaut dans le dossier `assets`
   - Modifiez `app.json` pour inclure les configurations d'icônes et d'écran de démarrage

2. Optimisez les performances :
   - Utilisez des images optimisées
   - Implémentez la pagination pour le chargement des profils

3. Configurez EAS Build :
   - Installez EAS CLI : `npm install -g eas-cli`
   - Initialisez EAS : `eas init`
   - Configurez les profils de build dans `eas.json`

---

## Exercice : Préparation de l'application pour la publication (suite)

Voici un exemple de configuration `app.json` :

```json
{
  "expo": {
    "name": "TinderLikeApp",
    "slug": "tinder-like-app",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/icon.png",
    "splash": {
      "image": "./assets/splash.png",
      "resizeMode": "contain",
      "backgroundColor": "#ffffff"
    },
    "updates": {
      "fallbackToCacheTimeout": 0
    },
    "assetBundlePatterns": [
      "**/*"
    ],
    "ios": {
      "supportsTablet": true,
      "bundleIdentifier": "com.yourcompany.tinderlikeapp"
    },
    "android": {
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png",
        "backgroundColor": "#FFFFFF"
      },
      "package": "com.yourcompany.tinderlikeapp"
    },
    "web": {
      "favicon": "./assets/favicon.png"
    }
  }
}
```

---

## Exercice : Préparation de l'application pour la publication (suite)

Et un exemple de configuration `eas.json` :

```json
{
  "build": {
    "preview": {
      "android": {
        "buildType": "apk"
      }
    },
    "preview2": {
      "android": {
        "gradleCommand": ":app:assembleRelease"
      }
    },
    "preview3": {
      "developmentClient": true
    },
    "production": {}
  }
}
```

Pour créer un build de production :

```bash
eas build --platform android
eas build --platform ios
```

Ces commandes généreront des builds pour Android et iOS respectivement, que vous pourrez ensuite soumettre aux stores.

N'oubliez pas de tester minutieusement votre application avant de la soumettre, et assurez-vous de respecter les directives de chaque store pour maximiser vos chances d'approbation.
