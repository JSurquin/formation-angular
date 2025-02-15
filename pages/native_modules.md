---
routeAlias: 'native-modules'
---

<a name="native-modules"></a>

# Création de Modules Natifs (2024/2025)

## Introduction aux modules natifs

- Pourquoi créer un module natif ?
  - Accès à des fonctionnalités natives spécifiques
  - Optimisation des performances
  - Réutilisation de code natif existant
  - Intégration de SDKs tiers

---

## Structure d'un module natif

### Android (Kotlin)

```kotlin
// android/app/src/main/java/com/myapp/CustomModule.kt
package com.myapp

import com.facebook.react.bridge.ReactContextBaseJavaModule
import com.facebook.react.bridge.ReactMethod
import com.facebook.react.bridge.Promise

class CustomModule(reactContext: ReactContext) : ReactContextBaseJavaModule(reactContext) {
    override fun getName() = "CustomModule"
    
    @ReactMethod
    fun doSomething(message: String, promise: Promise) {
        try {
            // Logique native ici
            val result = "Résultat: $message"
            promise.resolve(result)
        } catch (e: Exception) {
            promise.reject("ERROR", e.message)
        }
    }
}
```

---

### iOS (Swift)

```swift
// ios/CustomModule.swift
import Foundation

@objc(CustomModule)
class CustomModule: NSObject {
    @objc
    func doSomething(_ message: String, 
                     resolver resolve: @escaping RCTPromiseResolveBlock,
                     rejecter reject: @escaping RCTPromiseRejectBlock) {
        // Logique native ici
        let result = "Résultat: \(message)"
        resolve(result)
    }
    
    @objc
    static func requiresMainQueueSetup() -> Bool {
        return false
    }
}
```

---

## Bridge JavaScript

```tsx
// src/NativeModules/CustomModule.ts
import { NativeModules } from 'react-native';

const { CustomModule } = NativeModules;

interface CustomModuleInterface {
  doSomething(message: string): Promise<string>;
}

export default CustomModule as CustomModuleInterface;
```

---

## Utilisation dans React Native

```tsx
import CustomModule from './NativeModules/CustomModule';

function MyComponent() {
  const handleNativeCall = async () => {
    try {
      const result = await CustomModule.doSomething('Test');
      console.log(result);
    } catch (error) {
      console.error(error);
    }
  };

  return (
    <Button 
      title="Appeler module natif" 
      onPress={handleNativeCall} 
    />
  );
}
```

---

## Exemple concret : Scanner de QR Code natif

### Android (Kotlin)

```kotlin
class QRScannerModule(reactContext: ReactContext) : ReactContextBaseJavaModule(reactContext) {
    override fun getName() = "QRScanner"
    
    @ReactMethod
    fun startScan(promise: Promise) {
        try {
            // Initialisation de la caméra
            val scanner = MLKit.getScanner()
            scanner.setCallback { qrCode ->
                promise.resolve(qrCode)
            }
        } catch (e: Exception) {
            promise.reject("SCAN_ERROR", e.message)
        }
    }
}
```

---

### iOS (Swift)

```swift
@objc(QRScanner)
class QRScanner: NSObject {
    private var captureSession: AVCaptureSession?
    
    @objc
    func startScan(_ resolve: @escaping RCTPromiseResolveBlock,
                   rejecter reject: @escaping RCTPromiseRejectBlock) {
        DispatchQueue.main.async {
            self.setupCaptureSession()
            // Configuration de la détection de QR code
            let detector = CIDetector(...)
            // Callback quand un QR code est détecté
            resolve(qrCode)
        }
    }
}
```

---

## Bonnes pratiques

1. **Performance**
   - Éviter les appels fréquents entre JS et natif
   - Batch les opérations quand possible
   - Utiliser les événements pour les updates continus

2. **Gestion des erreurs**
   - Toujours utiliser les Promises
   - Messages d'erreur détaillés
   - Codes d'erreur constants

3. **Mémoire**
   - Nettoyer les ressources
   - Éviter les fuites mémoire
   - Gérer le cycle de vie

---

## Exercice : Module de Biométrie

Créons un module natif pour gérer l'authentification biométrique :

```kotlin
// Android
class BiometricModule(reactContext: ReactContext) : ReactContextBaseJavaModule(reactContext) {
    @ReactMethod
    fun authenticate(promise: Promise) {
        val biometricPrompt = BiometricPrompt(
            currentActivity!!,
            object : BiometricPrompt.AuthenticationCallback() {
                override fun onAuthenticationSucceeded(result: BiometricPrompt.AuthenticationResult) {
                    promise.resolve(true)
                }
                
                override fun onAuthenticationError(errorCode: Int, errString: CharSequence) {
                    promise.reject("AUTH_ERROR", errString.toString())
                }
            }
        )
        
        biometricPrompt.authenticate(promptInfo)
    }
}
```

---

## Exercice : Module de Biométrie (suite)

```swift
// iOS
@objc(BiometricModule)
class BiometricModule: NSObject {
    @objc
    func authenticate(_ resolve: @escaping RCTPromiseResolveBlock,
                     rejecter reject: @escaping RCTPromiseRejectBlock) {
        let context = LAContext()
        var error: NSError?
        
        if context.canEvaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, error: &error) {
            context.evaluatePolicy(.deviceOwnerAuthenticationWithBiometrics,
                                 localizedReason: "Authentification requise") { success, error in
                if success {
                    resolve(true)
                } else {
                    reject("AUTH_ERROR", "Échec de l'authentification", error)
                }
            }
        } else {
            reject("NOT_AVAILABLE", "Biométrie non disponible", error)
        }
    }
}
``` 