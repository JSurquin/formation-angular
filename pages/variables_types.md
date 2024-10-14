---
routeAlias: 'variables-types'
layout: two-cols
---

# Variables et types de données en JavaScript

- **Déclaration de variables**
  - `let` pour les variables qui peuvent changer
  - `const` pour les constantes
  - `var` (déconseillé dans le code moderne)

- **Types de données de base**
  - Number, String, Boolean
  - undefined, null

::right::

- **Types composés**
  - Object
  - Array

- **Typage dynamique**
  - Les variables peuvent changer de type
  - Utilisation de TypeScript pour un typage statique

---
routeAlias: 'exercice-manipulation-types-donnees'
---

## Exercice : Manipulation des types de données

Créez un script JavaScript qui :
1. Déclare des variables de différents types (number, string, boolean, array, object).
2. Effectue des opérations de base sur ces variables (addition, concaténation, etc.).
3. Utilise `console.log()` et `typeof` pour afficher le type et la valeur de chaque variable.

---
routeAlias: 'correction-exercice-manipulation-types-donnees'
---

## Correction de l'exercice : Manipulation des types de données

```javascript
// Déclaration des variables
const nombre = 42
let chaine = 'Hello, JavaScript!'
const booleen = true
const tableau = [1, 2, 3, 4, 5]
const objet = { nom: 'John', age: 30 }

// Opérations de base
const somme = nombre + 10
chaine = `${chaine} J'aime les nombres comme ${nombre}`

// Affichage des types et valeurs
console.log(nombre, typeof nombre)
console.log(chaine, typeof chaine)
console.log(booleen, typeof booleen)
console.log(tableau, typeof tableau)
console.log(objet, typeof objet)
console.log(somme, typeof somme)
```
