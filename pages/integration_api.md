---
routeAlias: 'integration-api-donnees'
---

# Intégration d'API et gestion des données

- **Appels API avec Fetch ou Axios**
  - Utilisation de Fetch (intégré à React Native)
  - Axios comme alternative populaire

- **Gestion des états de chargement et d'erreur**
  - Affichage de spinners pendant le chargement
  - Gestion des erreurs et affichage de messages d'erreur

---

# Intégration d'API et gestion des données (suite)

- **Stockage local avec AsyncStorage**
  - Sauvegarde de données sur l'appareil
  - Persistance de l'état de l'application

- **Gestion de l'état global avec Context API ou Redux**
  - Partage de données entre composants
  - Gestion d'un état complexe de l'application

---
routeAlias: 'exercice-integration-api'
---

## Exercice : Intégration d'une API de profils

Améliorons notre application TinderLikeApp en intégrant une API réelle pour récupérer des profils d'utilisateurs.

1. Utilisez l'API gratuite [Random User Generator](https://randomuser.me/) pour obtenir des profils aléatoires
2. Créez une fonction pour récupérer plusieurs profils
3. Implémentez un état de chargement et de gestion d'erreur
4. Stockez les profils récupérés dans le state de l'application

---

## Exercice : Intégration d'une API de profils (suite)

Voici le code pour un nouveau fichier `api.js` :

```jsx
import axios from 'axios';

export const fetchProfiles = async (count = 10) => {
  try {
    const response = await axios.get(`https://randomuser.me/api/?results=${count}`);
    return response.data.results.map(user => ({
      id: user.login.uuid,
      name: `${user.name.first} ${user.name.last}`,
      bio: `Je suis ${user.dob.age} ans et je viens de ${user.location.city}, ${user.location.country}.`,
      imageUrl: user.picture.large
    }));
  } catch (error) {
    console.error('Erreur lors de la récupération des profils:', error);
    throw error;
  }
};
```

---

## Exercice : Intégration d'une API de profils (suite)

Maintenant, mettons à jour `ProfileList.js` pour utiliser cette API :

```jsx
import React, { useState, useEffect } from 'react';
import { View, FlatList, TouchableOpacity, Text, StyleSheet, ActivityIndicator } from 'react-native';
import { fetchProfiles } from '../api';

const ProfileList = ({ navigation }) => {
  const [profiles, setProfiles] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    loadProfiles();
  }, []);

  const loadProfiles = async () => {
    try {
      setLoading(true);
      const fetchedProfiles = await fetchProfiles(20);
      setProfiles(fetchedProfiles);
      setError(null);
    } catch (err) {
      setError('Impossible de charger les profils. Veuillez réessayer.');
    } finally {
      setLoading(false);
    }
  };

  const renderItem = ({ item }) => (
    <TouchableOpacity
      style={styles.item}
      onPress={() => navigation.navigate('UserProfile', item)}
    >
      <Text style={styles.name}>{item.name}</Text>
    </TouchableOpacity>
  );

  if (loading) {
    return (
      <View style={styles.centered}>
        <ActivityIndicator size="large" color="#0000ff" />
      </View>
    );
  }

  if (error) {
    return (
      <View style={styles.centered}>
        <Text style={styles.error}>{error}</Text>
        <TouchableOpacity style={styles.button} onPress={loadProfiles}>
          <Text style={styles.buttonText}>Réessayer</Text>
        </TouchableOpacity>
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <FlatList
        data={profiles}
        renderItem={renderItem}
        keyExtractor={item => item.id}
        refreshing={loading}
        onRefresh={loadProfiles}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: 22
  },
  item: {
    padding: 10,
    fontSize: 18,
    height: 44,
  },
  name: {
    fontSize: 18,
  },
  centered: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  error: {
    color: 'red',
    fontSize: 18,
    marginBottom: 20,
  },
  button: {
    backgroundColor: '#007AFF',
    padding: 10,
    borderRadius: 5,
  },
  buttonText: {
    color: 'white',
    fontSize: 16,
  },
});

export default ProfileList;
