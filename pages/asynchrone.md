---
routeAlias: 'asynchrone-javascript'
---

# Programmation asynchrone en JavaScript

- **Callbacks**
  - Fonctions passées en argument et exécutées plus tard

- **Promesses**
  - Objet représentant la complétion ou l'échec d'une opération asynchrone
  - Méthodes `then()`, `catch()`, `finally()`

- **Async/Await**
  - Syntaxe plus lisible pour travailler avec les promesses
  - Mot-clé `async` pour les fonctions
  - Mot-clé `await` pour attendre la résolution d'une promesse

- **Event Loop**
  - Comprendre comment JavaScript gère les opérations asynchrones

---
routeAlias: 'exercice-programmation-asynchrone'
---

## Exercice : Programmation asynchrone

1. Créez une fonction qui simule une opération asynchrone avec `setTimeout`.
2. Utilisez cette fonction avec des callbacks, puis refactorisez-la pour utiliser des promesses.
3. Créez une fonction asynchrone qui utilise `await` pour attendre le résultat de plusieurs opérations asynchrones.

---
routeAlias: 'correction-exercice-programmation-asynchrone'
---

## Correction de l'exercice

```javascript
// Fonction avec callback
function operationAsynchrone(callback) {
  setTimeout(() => {
    callback('Opération terminée')
  }, 1000)
}

// Fonction avec promesse
function operationAsynchronePromise() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve('Opération terminée')
    }, 1000)
  })
}

// Utilisation avec async/await
async function executerOperations() {
  try {
    const resultat1 = await operationAsynchronePromise()
    console.log(resultat1)
    const resultat2 = await operationAsynchronePromise()
    console.log(resultat2)
  }
  catch (error) {
    console.error('Une erreur s\'est produite:', error)
  }
}

// Exécution
operationAsynchrone(resultat => console.log(resultat))
operationAsynchronePromise().then(console.log)
executerOperations()
```
