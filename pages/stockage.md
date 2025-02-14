---
routeAlias: 'stockage-local'
---

<a name="stockage-local"></a>

# Stockage Local dans React Native (2024/2025)

- **AsyncStorage**
  - Simple key-value store
  - Stockage non sécurisé
  - Idéal pour les données non sensibles

- **Expo SecureStore**
  - Stockage chiffré
  - Limité à 2KB par clé
  - iOS Keychain & Android Keystore

- **React Native Keychain**
  - Stockage sécurisé natif
  - Idéal pour les credentials
  - Support de la biométrie

---

## AsyncStorage - Données basiques

```tsx
import AsyncStorage from '@react-native-async-storage/async-storage';

// Stockage
const storeData = async (key: string, value: any) => {
  try {
    await AsyncStorage.setItem(key, JSON.stringify(value));
  } catch (error) {
    console.error('Erreur de stockage:', error);
  }
};

// Récupération
const getData = async (key: string) => {
  try {
    const value = await AsyncStorage.getItem(key);
    return value != null ? JSON.parse(value) : null;
  } catch (error) {
    console.error('Erreur de lecture:', error);
    return null;
  }
};
```

---

## Expo SecureStore - Données sensibles

```tsx
import * as SecureStore from 'expo-secure-store';

// Stockage sécurisé
const saveSecureItem = async (key: string, value: string) => {
  try {
    await SecureStore.setItemAsync(key, value, {
      keychainAccessible: SecureStore.WHEN_UNLOCKED
    });
  } catch (error) {
    console.error('Erreur de stockage sécurisé:', error);
  }
};

// Récupération sécurisée
const getSecureItem = async (key: string) => {
  try {
    return await SecureStore.getItemAsync(key);
  } catch (error) {
    console.error('Erreur de lecture sécurisée:', error);
    return null;
  }
};
```

---

## React Native Keychain - Authentification

```tsx
import * as Keychain from 'react-native-keychain';

// Stockage des credentials
const saveCredentials = async (username: string, password: string) => {
  try {
    await Keychain.setGenericPassword(username, password, {
      accessControl: Keychain.ACCESS_CONTROL.BIOMETRY_ANY,
      accessible: Keychain.ACCESSIBLE.WHEN_UNLOCKED
    });
  } catch (error) {
    console.error('Erreur de stockage keychain:', error);
  }
};

// Récupération avec biométrie
const getCredentials = async () => {
  try {
    const credentials = await Keychain.getGenericPassword();
    return credentials;
  } catch (error) {
    console.error('Erreur de lecture keychain:', error);
    return null;
  }
};
```

---

## MMKV - Performance optimale

```tsx
import { MMKV } from 'react-native-mmkv';

const storage = new MMKV();

// Stockage rapide
const storeMMKV = (key: string, value: any) => {
  try {
    storage.set(key, JSON.stringify(value));
  } catch (error) {
    console.error('Erreur MMKV:', error);
  }
};

// Lecture rapide
const getMMKV = (key: string) => {
  try {
    const value = storage.getString(key);
    return value ? JSON.parse(value) : null;
  } catch (error) {
    console.error('Erreur MMKV:', error);
    return null;
  }
};
```

---

## Bonnes pratiques

1. **Choix de la solution**
   - AsyncStorage pour données non sensibles
   - SecureStore/Keychain pour données sensibles
   - MMKV pour performance

2. **Gestion des erreurs**
   - Toujours utiliser try/catch
   - Fallback values
   - Logging approprié

3. **Organisation**
   - Clés constantes
   - Services centralisés
   - Types forts

4. **Sécurité**
   - Ne jamais stocker de tokens JWT en clair
   - Chiffrement si nécessaire
   - Nettoyage au logout 