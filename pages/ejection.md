---
routeAlias: 'ejection-react-native'
---

<a name="ejection-react-native"></a>

# Éjection de React Native (2024/2025)

## Qu'est-ce que l'éjection ?

- Processus de conversion d'un projet Expo en projet React Native pur
- Donne accès à la configuration native complète
- Irréversible : il est recommandé d'utiliser `prebuild` plutôt que `eject`

---

## Quand utiliser prebuild/éjection ?

- **Besoins spécifiques natifs**
  - Modules natifs non supportés par Expo
  - Personnalisation profonde d'iOS/Android
  - Intégration de SDK natifs spécifiques

- **Performance critique**
  - Optimisations natives poussées
  - Réduction de la taille de l'app
  - Contrôle total du build

---

## Processus moderne (2024/2025)

```bash
# 1. Sauvegardez votre projet
git commit -am "Backup avant prebuild"

# 2. Utilisez prebuild plutôt que eject
npx expo prebuild

# 3. Installez les dépendances natives
cd ios && pod install && cd ..
```

---

## Configuration post-prebuild

```bash
# Installation des dépendances natives
npx expo install react-native-reanimated react-native-gesture-handler

# Configuration de CocoaPods (iOS)
cd ios
pod install
cd ..

# Configuration d'Android
# Modifier android/app/build.java si nécessaire
```

---

## Structure après prebuild

```
MonProjet/
├── android/          # Configuration Android native
│   ├── app/
│   └── build.f
├── ios/             # Configuration iOS native
│   ├── Podfile
│   └── MonProjet.xcworkspace
├── app/             # Votre code React Native
└── package.json
```

---

## Avantages de prebuild vs eject

- Maintient l'accès aux outils Expo
  - EAS Build toujours utilisable
  - Mises à jour OTA possibles
  - Development builds disponibles
- Accès aux configurations natives
- Possibilité d'ajouter des modules natifs

---

## Configuration moderne des plugins

```js
// app.config.js
export default {
  plugins: [
    'expo-camera',
    ['expo-build-properties', {
      ios: {
        deploymentTarget: '13.0',
        useFrameworks: 'static'
      },
      android: {
        compileSdkVersion: 33,
        targetSdkVersion: 33,
        buildToolsVersion: "33.0.0"
      },
    }],
  ],
};
```

---

# Guide détaillé du prebuild

## Préparation du projet

```bash
# 1. Vérification des dépendances
npm outdated
npm update

# 2. Sauvegarde de l'état
git add .
git commit -m "Pre-prebuild backup"
git checkout -b native-config
```

---

## Processus détaillé

```bash
# 1. Lancement du prebuild
npx expo prebuild

# 2. Configuration des identifiants
# - Bundle ID iOS: com.votreapp
# - Package Android: com.votreapp

# 3. Vérification post-prebuild
ls ios/ # Vérifiez la présence des fichiers iOS
ls android/ # Vérifiez la présence des fichiers Android
```

---

# Problèmes courants et solutions

## 1. Erreurs iOS courantes

```bash
# Erreur: Multiple commands produce
cd ios
xcodebuild clean
pod deintegrate
pod install

# Erreur: No bundle URL present
# Modifiez AppDelegate.mm :
- (NSURL *)sourceURLForBridge:(RCTBridge *)bridge {
#if DEBUG
  return [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index"];
#else
  return [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
#endif
}
```

---

## 2. Erreurs Android courantes

```java
// Erreur: Duplicate class
android {
    packagingOptions {
        pickFirst '**/libc++_shared.so'
        pickFirst '**/libfbjni.so'
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/LICENSE.txt'
    }
}

// Erreur: SDK location not found
// Créez android/local.properties :
sdk.dir=/Users/USERNAME/Library/Android/sdk
```

---

# Exemple moderne : Expo Camera

## Installation et configuration

```bash
# 1. Installation
npx expo install expo-camera

# 2. Ajout des permissions
# iOS: Info.plist est géré automatiquement
# Android: AndroidManifest.xml est géré automatiquement

# 3. Utilisation des hooks de permission
const [permission, requestPermission] = Camera.useCameraPermissions();
```

---

## Implémentation moderne de la caméra

```tsx
import React from 'react';
import { View, TouchableOpacity, StyleSheet, Text, Button } from 'react-native';
import { Camera, CameraType } from 'expo-camera';

export function CameraView() {
  const [permission, requestPermission] = Camera.useCameraPermissions();
  
  if (!permission?.granted) {
    return (
      <View className="flex-1 items-center justify-center">
        <Text className="text-lg mb-4">
          Nous avons besoin de votre permission pour utiliser la caméra
        </Text>
        <Button 
          onPress={requestPermission} 
          title="Autoriser la caméra" 
        />
      </View>
    );
  }

  const takePicture = async () => {
    if (cameraRef.current) {
      const photo = await cameraRef.current.takePictureAsync();
      console.log(photo.uri);
    }
  };

  const cameraRef = React.useRef(null);

  return (
    <View className="flex-1">
      <Camera
        ref={cameraRef}
        className="flex-1"
        type={CameraType.back}
      >
        <View className="flex-1 justify-end items-center pb-10">
          <TouchableOpacity 
            onPress={takePicture}
            className="w-16 h-16 rounded-full bg-white"
          />
        </View>
      </Camera>
    </View>
  );
}
```