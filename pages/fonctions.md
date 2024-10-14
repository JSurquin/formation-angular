---
routeAlias: 'fonctions-javascript'
layout: two-cols
---

# Fonctions en JavaScript

- **Définition de fonctions**
  - Déclaration de fonction
  - Expression de fonction
  - Fonctions fléchées

- **Portée des variables**
  - Portée globale et locale
  - Closure

::right::

- **Fonctions de première classe**
  - Passage de fonctions comme arguments
  - Retour de fonctions

- **Méthodes natives importantes**
  - Manipulation de chaînes : `split()`, `replace()`
  - Manipulation de tableaux : `push()`, `map()`, `filter()`

---
routeAlias: 'exercice-creation-utilisation-fonctions'
---

## Exercice : Création et utilisation de fonctions

1. Créez une fonction qui calcule le factoriel d'un nombre.
2. Créez une fonction fléchée qui trie un tableau de nombres.
3. Utilisez `map()` avec une fonction fléchée pour doubler tous les éléments d'un tableau.

---
routeAlias: 'correction-exercice-creation-utilisation-fonctions'
---

## Correction de l'exercice

```javascript
// Fonction factorielle
function factorielle(n) {
  if (n === 0 || n === 1) {
    return 1
  }
  return n * factorielle(n - 1)
}

// Fonction fléchée pour trier un tableau
const trierTableau = (arr) => arr.sort((a, b) => a - b)

// Utilisation de map() avec une fonction fléchée
const tableau = [1, 2, 3, 4, 5]
const doubler = (x) => x * 2
const resultats = tableau.map(doubler)

console.log(factorielle(5))
console.log(trierTableau([3, 1, 4, 1, 5]))
console.log(resultats)
```
