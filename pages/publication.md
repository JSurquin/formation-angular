---
routeAlias: 'publication-application'
---

# Préparation pour la production

## Configuration des icônes et splash screen

```json
// app.json - Partie 1
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
    }
  }
}
```

---

## Configuration des plateformes

```json
// app.json - Partie 2
{
  "expo": {
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
    }
  }
}
```

---

## Optimisation des performances

```js
// Optimisation des images
const optimizedImage = await Image.prefetch(imageUrl);

// Activation du mode production
enableProduction();

// Configuration du cache
const cacheConfig = {
  maxAge: 7 * 24 * 60 * 60 * 1000, // 7 jours
  maxEntries: 150
};
```

---

# Publication iOS

## Compte développeur Apple

```bash
# Création du certificat
xcodebuild archive -scheme TinderLikeApp -configuration Release

# Génération de l'archive
xcodebuild -exportArchive -archivePath TinderLikeApp.xcarchive \
           -exportPath ./build -exportOptionsPlist ExportOptions.plist
```

---

## Soumission App Store

```bash
# Validation du build
xcrun altool --validate-app -f build/TinderLikeApp.ipa \
             -t ios -u user@email.com -p pass

# Upload sur App Store Connect
xcrun altool --upload-app -f build/TinderLikeApp.ipa \
             -t ios -u user@email.com -p pass
```

---

# Publication Android

## Génération du bundle

```bash
# Génération de la keystore
keytool -genkey -v -keystore tinder-like-app.keystore \
        -alias tinder-like-app -keyalg RSA -validity 10000

# Build du bundle
./gradlew bundleRelease
```

---

## Soumission Play Store

```bash
# Génération des APKs
bundletool build-apks --bundle=./app/build/outputs/bundle/release/app.aab \
                      --output=./app/build/outputs/apks/release/app.apks \
                      --ks=tinder-like-app.keystore \
                      --ks-key-alias=tinder-like-app
```

---

# Expo EAS

## Configuration initiale

```bash
# Installation d'EAS CLI
npm install -g eas-cli

# Login et configuration
eas login
eas build:configure
```

---

## Configuration EAS

```json
// eas.json
{
  "build": {
    "preview": {
      "android": {
        "buildType": "apk"
      }
    },
    "production": {
      "android": {
        "buildType": "app-bundle"
      },
      "ios": {
        "distribution": "store"
      }
    }
  }
}
```

---

## Commandes de build

```bash
# Build iOS
eas build --platform ios

# Build Android
eas build --platform android

# Soumission
eas submit -p ios
eas submit -p android
```

---

# Bonnes pratiques de publication

## Checklist avant soumission

1. Tests approfondis sur différents appareils
2. Vérification des performances
3. Validation des assets (icônes, splash screen)
4. Préparation des captures d'écran
5. Rédaction de la description
6. Configuration de la confidentialité

N'oubliez pas de tester minutieusement votre application avant la soumission, et assurez-vous de respecter les directives de chaque store pour maximiser vos chances d'approbation.

---
routeAlias: 'exercice-preparation-publication'
---

# Exercice : Préparation publication

## Étapes initiales

1. Configuration des icônes et splash screen
   - Remplacer les icônes dans `assets`
   - Modifier `app.json`

2. Optimisation des performances
   - Images optimisées
   - Pagination des profils

3. Configuration EAS
   - Installation : `npm install -g eas-cli`
   - Initialisation : `eas init`

---

## Configuration app.json

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

## Configuration eas.json

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

---

## Commandes de build

Pour créer un build de production :

```bash
# Build iOS
eas build --platform ios

# Build Android
eas build --platform android
```

Ces commandes généreront des builds que vous pourrez soumettre aux stores.

N'oubliez pas de tester minutieusement votre application avant la soumission, et assurez-vous de respecter les directives de chaque store pour maximiser vos chances d'approbation.
