---
routeAlias: 'state-management'
---

# Gestion d'état avec Redux

- **Principes de Redux**
  - Store unique
  - Actions pour décrire les changements d'état
  - Reducers pour spécifier comment l'état change

- **Actions**
  - Objets simples décrivant ce qui s'est passé

- **Reducers**
  - Fonctions pures qui prennent l'état précédent et une action, et retournent le nouvel état

- **Store**
  - Objet qui contient l'état de l'application
  - Permet l'accès à l'état via `getState()`
  - Permet la mise à jour de l'état via `dispatch(action)`

---
routeAlias: 'exercice-implementation-redux'
---

## Exercice : Implémentation de Redux dans une application React

1. Ajoutez Redux à votre application React.
2. Créez des actions et des reducers pour gérer l'état de votre application.
3. Utilisez `useSelector` et `useDispatch` pour connecter vos composants au store Redux.
4. Implémentez une fonctionnalité asynchrone avec Redux Thunk ou Redux Saga.

---
routeAlias: 'correction-exercice-implementation-redux'
---

## Correction de l'exercice

```jsx
import { applyMiddleware, createStore } from 'redux'
import thunk from 'redux-thunk'
import { Provider, useDispatch, useSelector } from 'react-redux'

// Actions
const INCREMENT = 'INCREMENT'
const DECREMENT = 'DECREMENT'
const SET_COUNT = 'SET_COUNT'

const increment = () => ({ type: INCREMENT })
const decrement = () => ({ type: DECREMENT })
const setCount = count => ({ type: SET_COUNT, payload: count })

// Reducer
const initialState = { count: 0 }

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { ...state, count: state.count + 1 }
    case DECREMENT:
      return { ...state, count: state.count - 1 }
    case SET_COUNT:
      return { ...state, count: action.payload }
    default:
      return state
  }
}

// Store
const store = createStore(counterReducer, applyMiddleware(thunk))

// Composant React utilisant Redux
function Counter() {
  const count = useSelector(state => state.count)
  const dispatch = useDispatch()

  const handleIncrement = () => dispatch(increment())
  const handleDecrement = () => dispatch(decrement())
  const handleSetCount = () => {
    const newCount = Math.floor(Math.random() * 100)
    dispatch(setCount(newCount))
  }

  return (
    <div>
      <h2>
        Compteur: 
        {count}
      </h2>
      <button onClick={handleIncrement}>Incrémenter</button>
      <button onClick={handleDecrement}>Décrémenter</button>
      <button onClick={handleSetCount}>Définir un nombre aléatoire</button>
    </div>
  )
}

// Application principale
function App() {
  return (
    <Provider store={store}>
      <div>
        <h1>Application Redux</h1>
        <Counter />
      </div>
    </Provider>
  )
}

export default App
