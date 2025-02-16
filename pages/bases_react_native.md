---
routeAlias: 'bases-react-native'
---

# Bases de React Native

- **Composants React Native**
  - View : conteneur générique (équivalent à une `div` en HTML)
  - Text : conteneur pour le texte (équivalent à `p`, `h1`, `span` en HTML)
  - Image : affichage d'images (équivalent à `img` en HTML)
  - TextInput : champ de saisie (équivalent à `input` en HTML)
  - TouchableOpacity : zone cliquable (équivalent à `button` en HTML)

---

# Bases de React Native (suite)

- **Composants React Native (suite)**
  - ScrollView : conteneur avec défilement (équivalent à une `<div>` avec overflow-y: scroll en CSS)
  - FlatList : liste optimisée pour de grandes quantités de données (équivalent à une boucle .map() sur un tableau en React Web, mais avec virtualisation)

- **JSX et styles**
  - Utilisation de JSX ou TSX pour décrire l'interface utilisateur
  - Styles inspirés de CSS, mais avec des différences clés, alors ça je vous le résume à l'oral
  - StyleSheet.create pour définir des styles ou d'autres packages à importer sois même

---

# Bases de React Native (suite)

# Props et State

Les props sont comme les paramètres d'une fonction - ils permettent de passer des données d'un composant parent à un composant enfant.

Prenons un exemple simple avec une fonction qui s'apelle un composant :

(Car un composant est une fonction qui retourne du html tout simplement !)


```jsx
// Composant parent
<UserProfile name="John" age={25} />
```

---

## Props - Utilisation

```jsx
// Composant enfant UserProfile
const UserProfile = (props) => {
  return (
    <Text>
      Nom: {props.name}, Age: {props.age}
      {/* affiche "John Doe" et 25 */}
    </Text>
  )
}
```

---

## State - Structure

```jsx
// Gestion de l'état avec useState
const Counter = () => {
  const [count, setCount] = useState(0)
  
  return (
    <TouchableOpacity 
      onPress={() => setCount(count + 1)}
      {/* quand j'appuie, l'état d'avant de la variable count se met à jour donc 0 + 1 , au prochain appuie 1 + 1 , etc */}
    >
      <Text>
        Compteur: 
        {count} 
        {/* count se met à jour automatiquement dans la vue */}
      </Text>
    </TouchableOpacity>
  )
}
```

---

Si vous n'avez aucune expérience avec ce concept :

Ce sont des variables en fait, juste React relis plusieurs fois la page à chaque changement, donc ça voudrais dire que si il relis il reverrais :

let count = 0

donc il ferais toujours 0 + 1, puis au prochain tour 0 + 1 ce qui n'as aucun sens quand on veux incrémenter un bouton

---

# Exercice : Création d'un profil utilisateur

## Structure du composant - Partie 1

```jsx
// UserProfile.js - Structure
import React from 'react';
import { View, Text, Image, StyleSheet } from 'react-native';

const UserProfile = ({ name, bio, imageUrl }) => {
  return (
    <View style={styles.container}>
      <Image source={{ uri: imageUrl }} style={styles.image} />
      <Text style={styles.name}>{name}</Text>
      <Text style={styles.bio}>{bio}</Text>
    </View>
  );
};
```

---

## Structure du composant - Partie 2

```jsx
// UserProfile.js - Styles
const styles = StyleSheet.create({
  container: {
    alignItems: 'center',
    padding: 20,
  },
  image: {
    width: 150,
    height: 150,
    borderRadius: 75,
    marginBottom: 20,
  },
  name: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 10,
  },
  bio: {
    fontSize: 16,
    textAlign: 'center',
  },
});

export default UserProfile;
```

---

## Utilisation du composant

```jsx
// App.js
import React from 'react';
import { View, StyleSheet } from 'react-native';
import UserProfile from './components/UserProfile';

export default function App() {
  return (
    <View style={styles.container}>
      <UserProfile
        name="John Doe"
        bio="Passionné de voyages et de photographie."
        imageUrl="https://randomuser.me/api/portraits/men/1.jpg"
      />
    </View>
  );
}
```

---

## Styles de l'application

```jsx
// App.js - Styles
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
});
```

---

# Résultat de l'exercice

Cet exercice vous permet de pratiquer la création de composants, l'utilisation de props, et le styling en React Native.

---

# Composants de base - Visualisation

<div class="grid grid-cols-2 gap-4">
<div>
<img src="/view.png" class="h-60 rounded-lg" />
<p class="text-sm text-center mt-2">Structure de base avec View</p>
</div>
<div>
<img src="/text.png" class="h-40 rounded-lg" />
<p class="text-sm text-center mt-2">Utilisation du composant Text</p>
</div>
</div>

---

<div class="-mt-4">

### Exemple de Flex Direction

Ici on peut voir comment on peut aligner les éléments en utilisant flexbox dans react native

<iframe src="https://snack.expo.dev/@jsrq/flex-direction" class="h-[50vh] w-full rounded-lg" />

</div>
---

<div class="-mt-4">

### Layout et Flexbox

Et ici le justifyContent qui permet de centrer les éléments horizontalement

<iframe src="https://snack.expo.dev/@jsrq/justify-content" class="h-[50vh] w-full rounded-lg" />

</div>

---

# Composants d'interaction

<div class="grid grid-cols-2 gap-4">
<div class="col-span-1">

Boutons : comme un vrai bouton en HTML

<img src="/button.png" class="h-60 rounded-lg" />
</div>
<div class="col-span-1">
<p> TouchableOpacity : comme un bouton en HTML mais avec une animation</p>
<img src="/touchable.png" class="h-60 rounded-lg" />
</div>
</div>

---

# TextInput pour la saisie

<img src="/textinput.png" class="h-60 rounded-lg" />

---

<div class="-mt-4">

### ScrollView

Voici un exemple de ScrollView qui permet de faire défiler du contenu verticalement

<iframe src="https://snack.expo.dev/@jsrq/scrollview-example" class="h-[50vh] w-full rounded-lg" />

</div>

---

<div class="-mt-4">

### FlatList 

Et ici un exemple de FlatList qui permet d'afficher une grande liste de données de manière optimisée

<iframe src="https://snack.expo.dev/@jsrq/flatlist-basics" class="h-[50vh] w-full rounded-lg" />

</div>
