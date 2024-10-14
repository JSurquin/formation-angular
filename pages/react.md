---
routeAlias: 'introduction-react'
---

# Introduction à React

- **Qu'est-ce que React ?**
  - Bibliothèque JavaScript pour construire des interfaces utilisateur
  - Basé sur le concept de composants réutilisables

- **JSX**
  - Syntaxe permettant d'écrire du HTML dans du JavaScript
  - Transpilé en JavaScript pur

- **Composants**
  - Fonctionnels vs Classe
  - Props et State

- **Virtual DOM**
  - Optimisation des performances de rendu

---
routeAlias: 'exercice-creation-composant-react'
---

## Exercice : Création d'un composant React simple

1. Utilisez Create React App pour initialiser un nouveau projet React.
2. Créez un composant fonctionnel qui affiche une liste d'éléments.
3. Utilisez les props pour passer des données au composant.
4. Ajoutez un état local au composant pour gérer une interaction utilisateur simple.

---
routeAlias: 'correction-exercice-creation-composant-react'
---

## Correction de l'exercice

```jsx
import React, { useState } from 'react'

function ListeElements({ elements }) {
  const [elementSelectionne, setElementSelectionne] = useState(null)

  const handleClick = (element) => {
    setElementSelectionne(element)
  }

  return (
    <div>
      <h2>Liste d'éléments</h2>
      <ul>
        {elements.map((element, index) => (
          <li key={index} onClick={() => handleClick(element)}>
            {element}
          </li>
        ))}
      </ul>
      {elementSelectionne && (
        <p>
          Élément sélectionné : 
          {elementSelectionne}
        </p>
      )}
    </div>
  )
}

// Utilisation du composant
function App() {
  const elements = ['Pomme', 'Banane', 'Cerise', 'Date']

  return (
    <div className="App">
      <ListeElements elements={elements} />
    </div>
  )
}

export default App
```

Pour utiliser ce composant, assurez-vous d'avoir initialisé un projet React avec Create React App et remplacez le contenu du fichier `src/App.js` par ce code.
