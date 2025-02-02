---
routeAlias: 'camera-medias'
---

# Utilisation de la caméra et des médias

## Configuration des permissions

```js
// Configuration des permissions caméra
const { status } = await Camera.requestPermissionsAsync();
if (status === 'granted') {
  // On peut utiliser la caméra
}
```

---

## Capture de photos

```jsx
// Composant de capture photo
<Camera ref={cameraRef}>
  <Button title="Photo" onPress={async () => {
    const photo = await cameraRef.current.takePictureAsync();
    console.log(photo.uri);
  }} />
</Camera>
```

---

## Sélection d'images

```js
// Sélection depuis la galerie
const result = await ImagePicker.launchImageLibraryAsync({
  mediaTypes: 'Images',
  allowsEditing: true,
});
if (!result.cancelled) {
  console.log(result.uri);
}
```

---

## Téléchargement d'images

```js
// Configuration du FormData
const formData = new FormData();
formData.append('photo', {
  uri: imageUri,
  type: 'image/jpeg',
  name: 'photo.jpg',
});

// Envoi au serveur
await fetch('https://monapi.com/upload', {
  method: 'POST',
  body: formData
});
```

---

# Utilisation de la caméra et des médias (suite)

- **Manipulation d'images avec Expo ImageManipulator**
  - Redimensionnement et rotation d'images
    ```js
    // Comme canvas.getContext('2d') en web
    const manipResult = await ImageManipulator.manipulateAsync(
      imageUri,
      [
        { resize: { width: 300 } },
        { rotate: 90 }
      ],
      { compress: 0.8 }
    );
    ```
  - Application de filtres simples
    ```js
    // Comme les filtres CSS mais en natif
    const filteredImage = await ImageManipulator.manipulateAsync(
      imageUri,
      [{ flip: { horizontal: true } }]
    );
    ```

---

- **Stockage des médias**
  - Sauvegarde locale avec Expo FileSystem
    ```js
    // Comme localStorage mais pour fichiers
    const fileName = `${FileSystem.documentDirectory}photo.jpg`;
    await FileSystem.copyAsync({
      from: photoUri,
      to: fileName
    });
    ```
  - Téléchargement vers un serveur distant
    ```js
    // Comme un upload de fichier classique
    const uploadPhoto = async (uri) => {
      const response = await fetch('https://monapi.com/photos', {
        method: 'POST',
        body: JSON.stringify({ photo: uri })
      });
      return response.json();
    };
    ```

---
routeAlias: 'exercice-ajout-photo-profil'
---

## Exercice : Ajout de photo de profil

Améliorons notre application TinderLikeApp en permettant aux utilisateurs d'ajouter une photo de profil en utilisant la caméra ou en sélectionnant une image de la galerie.

1. Installez les dépendances nécessaires :
   ```
   expo install expo-camera expo-image-picker expo-permissions
   ```

2. Créez un nouveau composant `ProfileImagePicker.js`

---

## Exercice : Ajout de photo de profil (suite)

Voici le début du code pour `ProfileImagePicker.js` :

```jsx
import React, { useState, useEffect } from 'react';
import { View, Image, Button, StyleSheet } from 'react-native';
import * as ImagePicker from 'expo-image-picker';
import { Camera } from 'expo-camera';

const ProfileImagePicker = ({ onImageSelected }) => {
  const [hasPermission, setHasPermission] = useState(null);
  const [image, setImage] = useState(null);

  useEffect(() => {
    (async () => {
      const { status } = await Camera.requestPermissionsAsync();
      setHasPermission(status === 'granted');
    })();
  }, []);

  // ... (suite dans la prochaine slide)
```

---

## Exercice : Ajout de photo de profil (suite)

Suite du code pour `ProfileImagePicker.js` :

```jsx
  const takePhoto = async () => {
    const result = await ImagePicker.launchCameraAsync({
      allowsEditing: true,
      aspect: [1, 1],
      quality: 1,
    });

    if (!result.cancelled) {
      setImage(result.uri);
      onImageSelected(result.uri);
    }
  };

  const pickImage = async () => {
    const result = await ImagePicker.launchImageLibraryAsync({
      mediaTypes: ImagePicker.MediaTypeOptions.Images,
      allowsEditing: true,
      aspect: [1, 1],
      quality: 1,
    });

    if (!result.cancelled) {
      setImage(result.uri);
      onImageSelected(result.uri);
    }
  };

  // ... (suite dans la prochaine slide)
```

---

## Exercice : Ajout de photo de profil (suite)

Fin du code pour `ProfileImagePicker.js` :

```jsx
  if (hasPermission === null) {
    return <View />;
  }
  if (hasPermission === false) {
    return <Text>Pas d'accès à la caméra</Text>;
  }

  return (
    <View style={styles.container}>
      {image && <Image source={{ uri: image }} style={styles.image} />}
      <View style={styles.buttonContainer}>
        <Button title="Prendre une photo" onPress={takePhoto} />
        <Button title="Choisir une image" onPress={pickImage} />
      </View>
    </View>
  );
};
```

---

# Style

```jsx
const styles = StyleSheet.create({
  container: {
    alignItems: 'center',
  },
  image: {
    width: 200,
    height: 200,
    borderRadius: 100,
    marginBottom: 20,
  },
  buttonContainer: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    width: '100%',
  },
});

export default ProfileImagePicker;
```

---

## Exercice : Ajout de photo de profil (suite)

Intégrez ce composant dans `UserProfile.js` :

```jsx
import React, { useState } from 'react';
import { View, Text, StyleSheet, SafeAreaView } from 'react-native';
import ProfileImagePicker from './ProfileImagePicker';

const UserProfile = ({ name, bio }) => {
  const [profileImage, setProfileImage] = useState(null);

  const handleImageSelected = (imageUri) => {
    setProfileImage(imageUri);
  };

  return (
    <SafeAreaView style={styles.container}>
      <ProfileImagePicker onImageSelected={handleImageSelected} />
      <Text style={styles.name}>{name}</Text>
      <Text style={styles.bio}>{bio}</Text>
    </SafeAreaView>
  );
};

// ... (styles dans la prochaine slide)
```

---

## Exercice : Ajout de photo de profil (suite)

Styles pour `UserProfile.js` :

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    padding: 20,
  },
  name: {
    fontSize: 24,
    fontWeight: 'bold',
    marginTop: 20,
  },
  bio: {
    fontSize: 16,
    textAlign: 'center',
    marginTop: 10,
  },
});

export default UserProfile;
```

---

Cet exercice vous permet de pratiquer l'utilisation de la caméra et de la galerie d'images dans une application React Native.

## Manipulation d'images - Redimensionnement

```js
// Redimensionnement et rotation
const manipResult = await ImageManipulator.manipulateAsync(
  imageUri,
  [
    { resize: { width: 300 } },
    { rotate: 90 }
  ],
  { compress: 0.8 }
);
```

---

## Manipulation d'images - Filtres

```js
// Application de filtres
const filteredImage = await ImageManipulator.manipulateAsync(
  imageUri,
  [{ flip: { horizontal: true } }]
);
```

---

## Stockage local

```js
// Sauvegarde dans le système de fichiers
const fileName = `${FileSystem.documentDirectory}photo.jpg`;
await FileSystem.copyAsync({
  from: photoUri,
  to: fileName
});
```

---

## Upload vers serveur

```js
// Fonction d'upload
const uploadPhoto = async (uri) => {
  const response = await fetch('https://monapi.com/photos', {
    method: 'POST',
    body: JSON.stringify({ photo: uri })
  });
  return response.json();
};
```

---

# Configuration de l'exercice

Améliorons notre application TinderLikeApp en permettant aux utilisateurs d'ajouter une photo de profil en utilisant la caméra ou en sélectionnant une image de la galerie.

## Installation des dépendances

```bash
expo install expo-camera expo-image-picker expo-permissions
```

---

## Structure du composant - Partie 1

```jsx
// ProfileImagePicker.js - Imports et état
import React, { useState, useEffect } from 'react';
import { View, Image, Button, StyleSheet } from 'react-native';
import * as ImagePicker from 'expo-image-picker';
import { Camera } from 'expo-camera';

const ProfileImagePicker = ({ onImageSelected }) => {
  const [hasPermission, setHasPermission] = useState(null);
  const [image, setImage] = useState(null);

  useEffect(() => {
    (async () => {
      const { status } = await Camera.requestPermissionsAsync();
      setHasPermission(status === 'granted');
    })();
  }, []);
```

---

## Structure du composant - Partie 2

```jsx
// ProfileImagePicker.js - Méthodes de capture
const takePhoto = async () => {
  const result = await ImagePicker.launchCameraAsync({
    allowsEditing: true,
    aspect: [1, 1],
    quality: 1,
  });

  if (!result.cancelled) {
    setImage(result.uri);
    onImageSelected(result.uri);
  }
};
```

---

## Structure du composant - Partie 3

```jsx
// ProfileImagePicker.js - Méthode de sélection
const pickImage = async () => {
  const result = await ImagePicker.launchImageLibraryAsync({
    mediaTypes: ImagePicker.MediaTypeOptions.Images,
    allowsEditing: true,
    aspect: [1, 1],
    quality: 1,
  });

  if (!result.cancelled) {
    setImage(result.uri);
    onImageSelected(result.uri);
  }
};
```

---

## Structure du composant - Partie 4

```jsx
// ProfileImagePicker.js - Rendu conditionnel
if (hasPermission === null) {
  return <View />;
}
if (hasPermission === false) {
  return <Text>Pas d'accès à la caméra</Text>;
}
```

---

## Structure du composant - Partie 5

```jsx
// ProfileImagePicker.js - Rendu principal
return (
  <View style={styles.container}>
    {image && <Image source={{ uri: image }} style={styles.image} />}
    <View style={styles.buttonContainer}>
      <Button title="Prendre une photo" onPress={takePhoto} />
      <Button title="Choisir une image" onPress={pickImage} />
    </View>
  </View>
);
```

---

## Styles du composant

```jsx
// ProfileImagePicker.js - Styles
const styles = StyleSheet.create({
  container: {
    alignItems: 'center',
  },
  image: {
    width: 200,
    height: 200,
    borderRadius: 100,
    marginBottom: 20,
  },
  buttonContainer: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    width: '100%',
  },
});

export default ProfileImagePicker;
```

---

# Intégration du composant

## UserProfile - Structure

```jsx
// UserProfile.js - Structure
import React, { useState } from 'react';
import { View, Text, StyleSheet, SafeAreaView } from 'react-native';
import ProfileImagePicker from './ProfileImagePicker';

const UserProfile = ({ name, bio }) => {
  const [profileImage, setProfileImage] = useState(null);

  const handleImageSelected = (imageUri) => {
    setProfileImage(imageUri);
  };
```

---

## UserProfile - Rendu

```jsx
// UserProfile.js - Rendu
return (
  <SafeAreaView style={styles.container}>
    <ProfileImagePicker onImageSelected={handleImageSelected} />
    <Text style={styles.name}>{name}</Text>
    <Text style={styles.bio}>{bio}</Text>
  </SafeAreaView>
);
```

---

## UserProfile - Styles

```jsx
// UserProfile.js - Styles
const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    padding: 20,
  },
  name: {
    fontSize: 24,
    fontWeight: 'bold',
    marginTop: 20,
  },
  bio: {
    fontSize: 16,
    textAlign: 'center',
    marginTop: 10,
  },
});

export default UserProfile;
```

---

# Résultat de l'exercice

Cet exercice vous permet de pratiquer l'utilisation de la caméra et de la galerie d'images dans une application React Native.
