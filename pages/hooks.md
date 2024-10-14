---
routeAlias: 'hooks-react'
---

# React Hooks

- **useState**
  - Gestion de l'état local dans les composants fonctionnels

- **useEffect**
  - Gestion des effets de bord (ex: appels API, abonnements)

- **useContext**
  - Accès au contexte React dans les composants fonctionnels

- **useReducer**
  - Gestion d'états complexes avec un pattern Redux-like

- **Hooks personnalisés**
  - Création de hooks réutilisables pour la logique partagée

---
routeAlias: 'exercice-utilisation-hooks'
---

## Exercice : Utilisation des Hooks React

1. Créez un hook personnalisé `useLocalStorage` pour gérer le stockage local.
2. Utilisez `useEffect` pour charger des données depuis une API au montage du composant.
3. Implémentez un dark mode avec `useContext` et `useState`.
4. Créez un formulaire complexe en utilisant `useReducer` pour gérer son état.

---
routeAlias: 'correction-exercice-utilisation-hooks'
---

## Correction de l'exercice

```jsx
import React, { useContext, useEffect, useReducer, useState } from 'react'

// Hook personnalisé useLocalStorage
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key)
      return item ? JSON.parse(item) : initialValue
    } catch (error) {
      console.log(error)
      return initialValue
    }
  })

  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value
      setStoredValue(valueToStore)
      window.localStorage.setItem(key, JSON.stringify(valueToStore))
    } catch (error) {
      console.log(error)
    }
  }

  return [storedValue, setValue]
}

// Contexte pour le thème
const ThemeContext = React.createContext()

// Composant principal
function App() {
  const [darkMode, setDarkMode] = useLocalStorage('darkMode', false)

  return (
    <ThemeContext.Provider value={{ darkMode, setDarkMode }}>
      <div className={darkMode ? 'dark-mode' : 'light-mode'}>
        <h1>Application React avec Hooks</h1>
        <DataFetcher />
        <ThemeToggle />
        <ComplexForm />
      </div>
    </ThemeContext.Provider>
  )
}

// Composant pour charger des données
function DataFetcher() {
  const [data, setData] = useState(null)

  useEffect(() => {
    fetch('https://api.example.com/data')
      .then(response => response.json())
      .then(data => setData(data))
  }, [])

  return (
    <div>
      <h2>Données chargées :</h2>
      {data ? <pre>{JSON.stringify(data, null, 2)}</pre> : 'Chargement...'}
    </div>
  )
}

// Composant pour basculer le thème
function ThemeToggle() {
  const { darkMode, setDarkMode } = useContext(ThemeContext)

  return (
    <button onClick={() => setDarkMode(!darkMode)}>
      Basculer en mode 
      {darkMode ? 'clair' : 'sombre'}
    </button>
  )
}

// Réducteur pour le formulaire complexe
function formReducer(state, action) {
  switch (action.type) {
    case 'update_field':
      return { ...state, [action.field]: action.value }
    case 'reset':
      return { name: '', email: '', message: '' }
    default:
      return state
  }
}

// Composant de formulaire complexe
function ComplexForm() {
  const [formState, dispatch] = useReducer(formReducer, {
    name: '',
    email: '',
    message: ''
  })

  const handleSubmit = (e) => {
    e.preventDefault()
    console.log('Form submitted:', formState)
    dispatch({ type: 'reset' })
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={formState.name}
        onChange={e => dispatch({
          type: 'update_field',
          field: 'name',
          value: e.target.value
        })}
        placeholder="Nom"
      />
      <input
        value={formState.email}
        onChange={e => dispatch({
          type: 'update_field',
          field: 'email',
          value: e.target.value
        })}
        placeholder="Email"
      />
      <textarea
        value={formState.message}
        onChange={e => dispatch({
          type: 'update_field',
          field: 'message',
          value: e.target.value
        })}
        placeholder="Message"
      />
      <button type="submit">Envoyer</button>
    </form>
  )
}

export default App
```

Cet exemple montre l'utilisation de plusieurs hooks React, y compris un hook personnalisé, dans une application simple.
