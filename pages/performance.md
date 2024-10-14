---
routeAlias: 'performance-javascript'
---

# Performance en JavaScript

- **Optimisation du code**
  - Profilage avec les outils de développement du navigateur
  - Identification des goulots d'étranglement

- **Lazy loading**
  - Chargement à la demande des modules et composants

- **Memoization**
  - Mise en cache des résultats de fonctions coûteuses

- **Optimisation des rendus React**
  - `useMemo` et `useCallback` hooks
  - `React.memo` pour les composants

---
routeAlias: 'exercice-optimisation-application-existante'
---

## Exercice : Optimisation d'une application existante

1. Utilisez les outils de profilage pour identifier les parties lentes d'une application React.
2. Implémentez le lazy loading pour les routes ou composants lourds.
3. Optimisez les rendus en utilisant `useMemo`, `useCallback`, et `React.memo`.
4. Mesurez les améliorations de performance avant et après optimisation.

---
routeAlias: 'correction-exercice-optimisation-application-existante'
---

## Correction de l'exercice

```jsx
import React, { lazy, Suspense, useCallback, useMemo } from 'react'
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom'

// Lazy loading des composants
const HeavyComponent = lazy(() => import('./HeavyComponent'))

function App() {
  return (
    <Router>
      <Suspense fallback={<div>Chargement...</div>}>
        <Switch>
          <Route path="/heavy" component={HeavyComponent} />
          {/* Autres routes */}
        </Switch>
      </Suspense>
    </Router>
  )
}

// Optimisation avec useMemo et useCallback
function OptimizedList({ items }) {
  const sortedItems = useMemo(() => {
    console.log('Tri des éléments')
    return [...items].sort((a, b) => a.localeCompare(b))
  }, [items])

  const handleItemClick = useCallback((item) => {
    console.log(`Élément cliqué : ${item}`)
  }, [])

  return (
    <ul>
      {sortedItems.map(item => (
        <li key={item} onClick={() => handleItemClick(item)}>
          {item}
        </li>
      ))}
    </ul>
  )
}

// Utilisation de React.memo pour éviter les rendus inutiles
const MemoizedComponent = React.memo(({ value }) => {
  console.log('Rendu de MemoizedComponent')
  return <div>{value}</div>
})

export { App, OptimizedList, MemoizedComponent }
```

Ces optimisations peuvent significativement améliorer les performances d'une application React, particulièrement pour les composants qui gèrent de grandes quantités de données ou des calculs complexes.
