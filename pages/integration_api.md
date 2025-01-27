---
routeAlias: 'integration-api-donnees'
---

# Appels API

## Fetch

```js
// Exemple avec Fetch
fetch('https://api.monsite.com/users')
  .then(response => response.json())
  .then(data => console.log(data))
  // Équivalent à $.ajax en jQuery
```

## Axios

```js
// Exemple avec Axios 
axios.get('https://api.monsite.com/users')
  .then(response => console.log(response.data))
  // Plus simple que Fetch car pas besoin de .json()
```

---

# Gestion des états de chargement

## Loading Spinner

```jsx
// Comme un loader en CSS mais en natif
const [isLoading, setIsLoading] = useState(true);

return (
  <View>
    {isLoading && <ActivityIndicator size="large" />}
    {data && <DataComponent data={data} />}
  </View>
);
```

## Gestion des erreurs

```jsx
// Comme une div .error en CSS
const [error, setError] = useState(null);

return (
  <View>
    {error && <Text style={{color: 'red'}}>{error}</Text>}
  </View>
);
```

---

# Stockage local

## AsyncStorage - Sauvegarde

```js
// Comme localStorage en web
const saveUserData = async (userData) => {
  try {
    await AsyncStorage.setItem(
      'user', 
      JSON.stringify(userData)
    );
  } catch (error) {
    console.error('Erreur de sauvegarde:', error);
  }
};
```

---

## AsyncStorage - Récupération

```js
// Pour garder des données même après fermeture de l'app
const loadUserData = async () => {
  try {
    const userData = await AsyncStorage.getItem('user');
    return userData != null ? JSON.parse(userData) : null;
  } catch (error) {
    console.error('Erreur de chargement:', error);
    return null;
  }
};
```

---

# Gestion de l'état global

## Context API

```jsx
// Comme une variable globale accessible partout
const ThemeContext = React.createContext('light');

// Dans un composant parent
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <MainApp />
    </ThemeContext.Provider>
  );
}

// Dans n'importe quel composant enfant
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <Button theme={theme} />;
}
```

---

## État global complexe

```js
// Comme un grand objet qui contient tout l'état
const initialState = {
  user: null,
  theme: 'light',
  notifications: [],
  settings: {
    language: 'fr',
    notifications: true
  }
};

// Gestion avec useReducer ou Redux
const [state, dispatch] = useReducer(reducer, initialState);
```

---
routeAlias: 'exercice-integration-api'
---

# Exercice : Intégration API de profils

## Objectifs
1. Utiliser l'API Random User Generator
2. Gérer le chargement et les erreurs
3. Stocker les profils localement
4. Implémenter un pull-to-refresh

---

## Service API

```js
// services/api.js
const fetchProfiles = async (count = 10) => {
  try {
    const response = await fetch(
      `https://randomuser.me/api/?results=${count}`
    );
    const data = await response.json();
    return data.results.map(user => ({
      id: user.login.uuid,
      name: `${user.name.first} ${user.name.last}`,
      bio: `${user.location.city}, ${user.location.country}`,
      imageUrl: user.picture.large
    }));
  } catch (error) {
    throw new Error('Erreur lors du chargement des profils');
  }
};
```

---

## Composant ProfileList avec API

```jsx
const ProfileList = () => {
  const [profiles, setProfiles] = useState([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  const loadProfiles = async () => {
    try {
      setIsLoading(true);
      setError(null);
      const data = await fetchProfiles(10);
      setProfiles(data);
    } catch (error) {
      setError(error.message);
    } finally {
      setIsLoading(false);
    }
  };

  useEffect(() => {
    loadProfiles();
  }, []);

  if (isLoading) {
    return <ActivityIndicator size="large" />;
  }

  if (error) {
    return <Text style={styles.error}>{error}</Text>;
  }

  return (
    <FlatList
      data={profiles}
      renderItem={renderProfile}
      keyExtractor={item => item.id}
      refreshing={isLoading}
      onRefresh={loadProfiles}
    />
  );
};
```

---

## Stockage local des profils

```jsx
const saveProfiles = async (profiles) => {
  try {
    await AsyncStorage.setItem(
      'cached_profiles',
      JSON.stringify(profiles)
    );
  } catch (error) {
    console.error('Erreur de cache:', error);
  }
};

const loadCachedProfiles = async () => {
  try {
    const cached = await AsyncStorage.getItem('cached_profiles');
    return cached ? JSON.parse(cached) : [];
  } catch (error) {
    console.error('Erreur de lecture du cache:', error);
    return [];
  }
};
```

Cet exercice vous permet de pratiquer l'intégration d'API, la gestion des états de chargement et d'erreur, ainsi que le stockage local des données dans votre application React Native.
</rewritten_file>