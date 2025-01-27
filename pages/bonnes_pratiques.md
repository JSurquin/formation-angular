---
routeAlias: 'bonnes-pratiques'
---

# Bonnes pratiques de développement JavaScript

## Configuration ESLint

```js
// Configuration de base ESLint
{
  "rules": {
    "no-console": "error",
    "indent": ["error", 2]
  }
}
```

---

## Design Pattern - Module

```js
// Pattern Module
const monModule = (function() {
  let compteur = 0;
  
  return {
    increment() {
      compteur++;
      return compteur;
    }
  };
})();
```

---

## Clean Code - Nommage

```js
// Mauvais
const x = users.filter(u => u.a > 5);

// Bon
const usersActifs = utilisateurs.filter(user => user.age > 5);
```

---

## Clean Code - Fonctions

```js
// Mauvais
function gererUtilisateur(user) {
  // 50 lignes qui font plein de choses
}

// Bon 
function validerUtilisateur(user) {
  return user.age >= 18;
}

function sauvegarderUtilisateur(user) {
  // Sauvegarde uniquement
}
```

---

## Principes SOLID - Single Responsibility

```js
// Single Responsibility
class UtilisateurService {
  creerUtilisateur() {}
  supprimerUtilisateur() {}
}

class EmailService {
  envoyerEmail() {}
}
```

---

# Exercice de Refactoring

## Code Initial

```javascript
// Avant refactoring
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  save() {
    // Logique de sauvegarde
  }

  sendEmail(subject, body) {
    // Logique d'envoi d'email
  }
}
```

---

## Code Refactorisé - Partie 1

```javascript
// Classes séparées
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }
}

class UserRepository {
  save(user) {
    // Logique de sauvegarde
  }
}
```

---

## Code Refactorisé - Partie 2

```javascript
// Services séparés
class EmailService {
  sendEmail(to, subject, body) {
    // Logique d'envoi d'email
  }
}

class UserFactory {
  static createUser(name, email) {
    return new User(name, email);
  }
}
```

---

# Design Patterns (suite)

```js
// Pattern Module (comme en web)
const monModule = (function() {
  // Variables privées
  let compteur = 0;
  
  // Méthodes publiques
  return {
    increment() {
      compteur++;
      return compteur;
    }
  };
})();
```

---

# Clean Code

- **Nommage significatif**
```js
// Mauvais
const x = users.filter(u => u.a > 5);

// Bon
const usersActifs = utilisateurs.filter(user => user.age > 5);
```

---

# Clean Code (suite)

- **Fonctions courtes et focalisées**
```js
// Mauvais
function gererUtilisateur(user) {
  // 50 lignes qui font plein de choses
}

// Bon 
function validerUtilisateur(user) {
  return user.age >= 18;
}

function sauvegarderUtilisateur(user) {
  // Sauvegarde uniquement
}
```

---

# Principes SOLID

- **Adaptés au contexte JavaScript/TypeScript**
```js
// Single Responsibility
class UtilisateurService {
  // Une seule responsabilité : gestion utilisateur
  creerUtilisateur() {}
  supprimerUtilisateur() {}
}

class EmailService {
  // Une seule responsabilité : envoi d'emails
  envoyerEmail() {}
}
```

---
routeAlias: 'principes-solid-javascript'
---

# Parlons des principes SOLID en JavaScript

- **Single Responsibility Principle (SRP)**
  - Une fonction ou classe ne doit avoir qu'une seule raison de changer

- **Open/Closed Principle (OCP)**
  - Les entités logicielles doivent être ouvertes à l'extension, mais fermées à la modification

---

# Principes SOLID (suite)

- **Liskov Substitution Principle (LSP)**
  - Les objets d'une superclasse doivent pouvoir être remplacés par des objets de ses sous-classes sans altérer le fonctionnement du programme

- **Interface Segregation Principle (ISP)**
  - Préférer plusieurs interfaces spécifiques plutôt qu'une interface générale

- **Dependency Inversion Principle (DIP)**
  - Dépendre des abstractions, pas des implémentations concrètes

---
routeAlias: 'exercice-refactoring-code-existant'
---

## Exercice : Refactoring d'un code existant

1. Prenez un morceau de code JavaScript existant (peut être fourni ou de votre propre projet).
2. Identifiez les violations des principes SOLID et des bonnes pratiques.
3. Refactorisez le code pour le rendre plus propre et maintenable.
4. Appliquez un ou deux design patterns appropriés.

---
routeAlias: 'correction-exercice-refactoring-code-existant'
---

## Correction de l'exercice

Voici un exemple de refactoring appliquant le principe de responsabilité unique (SRP) et le pattern Factory :

```javascript
// Avant
class User {
  constructor(name, email) {
    this.name = name
    this.email = email
  }

  save() {
    // Logique pour sauvegarder l'utilisateur dans la base de données
  }

  sendEmail(subject, body) {
    // Logique pour envoyer un email
  }
}
```

---

## Correction de l'exercice (suite)

```javascript
// Après
class User {
  constructor(name, email) {
    this.name = name
    this.email = email
  }
}

class UserRepository {
  save(user) {
    // Logique pour sauvegarder l'utilisateur dans la base de données
  }
}

class EmailService {
  sendEmail(to, subject, body) {
    // Logique pour envoyer un email
  }
}

class UserFactory {
  static createUser(name, email) {
    return new User(name, email)
  }
}

// Utilisation
const user = UserFactory.createUser('John Doe', 'john@example.com')
const userRepo = new UserRepository()
userRepo.save(user)

const emailService = new EmailService()
emailService.sendEmail(user.email, 'Bienvenue', 'Bienvenue sur notre plateforme !')
```

---
routeAlias: 'bonnes-pratiques-optimisation'
---

# Bonnes pratiques React Native

## Architecture des composants - Partie 1

```jsx
// Mauvaise pratique
const MauvaisComposant = () => {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    fetch('https://api.example.com/data')
      .then(response => response.json())
      .then(data => setData(data))
      .catch(error => setError(error))
      .finally(() => setLoading(false));
  }, []);
};
```

---

## Architecture des composants - Partie 2

```jsx
// Bonne pratique - Hook personnalisé
const useData = () => {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      setLoading(true);
      try {
        const response = await fetch('https://api.example.com/data');
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };
    fetchData();
  }, []);

  return { data, loading, error };
};
```

---

## Architecture des composants - Partie 3

```jsx
// Utilisation du hook personnalisé
const BonComposant = () => {
  const { data, loading, error } = useData();

  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;

  return (
    <View>
      {data.map(item => (
        <ItemComponent key={item.id} item={item} />
      ))}
    </View>
  );
};
```

---

## Optimisation des performances - Partie 1

```jsx
// Utilisation de useMemo pour les calculs coûteux
const MemoizedComponent = () => {
  const expensiveValue = useMemo(() => {
    return someExpensiveCalculation(props);
  }, [props]);

  return <Text>{expensiveValue}</Text>;
};
```

---

## Optimisation des performances - Partie 2

```jsx
// Utilisation de useCallback pour les fonctions
const OptimizedComponent = () => {
  const handlePress = useCallback(() => {
    // Logique de gestion du clic
  }, []);

  return <TouchableOpacity onPress={handlePress} />;
};
```

---

# Performance et État

- **Performance**
  - Utilisation de `React.memo` pour éviter les re-rendus inutiles
  - Optimisation des listes avec `FlatList` et `VirtualizedList`
  - Lazy loading des composants et des images

- **Gestion de l'état**
  - Utilisation appropriée des hooks (useState, useEffect, useCallback, useMemo)
  - Mise en place d'un état global avec Context API ou Redux

---

# Debugging et Tests

- **Debugging**
  - Utilisation de React Native Debugger
  - Mise en place de logs appropriés

- **Tests**
  - Mise en place de tests unitaires avec Jest
  - Tests d'intégration avec React Native Testing Library

---
routeAlias: 'exercice-optimisation-application'
---

## Exercice : Optimisation de l'application TinderLikeApp

Optimisons notre application TinderLikeApp en appliquant certaines des meilleures pratiques.

---

1. Optimisez le rendu de la liste des profils :

```jsx
import React, { memo } from 'react';
import { FlatList } from 'react-native';

const ProfileItem = memo(({ profile, onPress }) => {
  // Composant d'item de profil optimisé
});

const ProfileList = ({ profiles, onProfilePress }) => {
  const renderItem = ({ item }) => (
    <ProfileItem profile={item} onPress={() => onProfilePress(item)} />
  );

  return (
    <FlatList
      data={profiles}
      renderItem={renderItem}
      keyExtractor={(item) => item.id}
      initialNumToRender={10}
      maxToRenderPerBatch={20}
      windowSize={21}
    />
  );
};

export default ProfileList;
```

---

## Exercice : Optimisation (suite)

2. Implémentez le lazy loading des images :

```jsx
import React, { useState } from 'react';
import { Image, View } from 'react-native';

const LazyImage = ({ source, style }) => {
  const [loaded, setLoaded] = useState(false);

  return (
    <View>
      {!loaded && <View style={[style, { backgroundColor: '#ccc' }]} />}
      <Image
        source={source}
        style={[style, { display: loaded ? 'flex' : 'none' }]}
        onLoad={() => setLoaded(true)}
      />
    </View>
  );
};

export default LazyImage;
```

---

## Exercice : Optimisation (suite)

3. Mettez en place un système de logging :

```jsx
// utils/logger.js
const logger = {
  info: (message) => {
    if (__DEV__) {
      console.log(`[INFO] ${message}`);
    }
  },
  error: (message, error) => {
    if (__DEV__) {
      console.error(`[ERROR] ${message}`, error);
    }
    // Ici, vous pourriez également envoyer les erreurs à un service de suivi des erreurs
  }
};

export default logger;
```

---

## Exercice : Optimisation (fin)

4. Ajoutez un test unitaire simple :

```jsx
// __tests__/ProfileItem.test.js
import React from 'react';
import { render, fireEvent } from '@testing-library/react-native';
import ProfileItem from '../components/ProfileItem';

describe('ProfileItem', () => {
  it('renders correctly', () => {
    const profile = { id: '1', name: 'John Doe', bio: 'Test bio' };
    const { getByText } = render(<ProfileItem profile={profile} />);
    
    expect(getByText('John Doe')).toBeTruthy();
    expect(getByText('Test bio')).toBeTruthy();
  });

  it('calls onPress when pressed', () => {
    const profile = { id: '1', name: 'John Doe', bio: 'Test bio' };
    const onPressMock = jest.fn();
    const { getByTestId } = render(<ProfileItem profile={profile} onPress={onPressMock} />);
    
    fireEvent.press(getByTestId('profile-item'));
    expect(onPressMock).toHaveBeenCalledTimes(1);
  });
});
```

Ces optimisations et bonnes pratiques amélioreront les performances et la maintenabilité de votre application TinderLikeApp. N'oubliez pas de les appliquer tout au long du développement de votre application.

Félicitations ! Vous avez maintenant terminé cette formation sur React Native et Expo. Vous avez appris à créer une application mobile complète, de la configuration initiale à l'optimisation et au déploiement. Continuez à pratiquer et à explorer les nombreuses possibilités offertes par React Native pour créer des applications mobiles performantes et attrayantes.
