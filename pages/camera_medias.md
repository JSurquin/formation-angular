---
routeAlias: 'camera-medias'
---

# Utilisation de la caméra et des médias

- **Accès à la caméra avec Expo Camera**
  - Configuration des permissions
  - Capture de photos et de vidéos

- **Gestion de la galerie avec Expo ImagePicker**
  - Sélection d'images depuis la galerie
  - Téléchargement d'images

---

# Utilisation de la caméra et des médias (suite)

- **Manipulation d'images avec Expo ImageManipulator**
  - Redimensionnement et rotation d'images
  - Application de filtres simples

- **Stockage des médias**
  - Sauvegarde locale avec Expo FileSystem
  - Téléchargement vers un serveur distant

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

Cet exercice vous permet de pratiquer l'utilisation de la caméra et de la galerie d'images dans une application React Native.
