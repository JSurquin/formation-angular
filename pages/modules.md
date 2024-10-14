---
routeAlias: 'modules-javascript'
---

# Modules en JavaScript

- **Définition et utilisation des modules**
  - Mot-clé `export`
  - Mot-clé `import`

- **Types d'exports**
  - Export nommé
  - Export par défaut

- **Avantages des modules**
  - Organisation du code
  - Encapsulation
  - Réutilisabilité

- **Utilisation avec Node.js et dans le navigateur**
  - CommonJS vs ES modules
  - Utilisation de bundlers (webpack, Rollup)

---
routeAlias: 'exercice-organisation-code-modules'
---

## Exercice : Organisation du code avec modules

1. Créez un module `mathUtils.js` avec des fonctions mathématiques de base.
2. Créez un module `stringUtils.js` avec des fonctions de manipulation de chaînes.
3. Importez et utilisez ces modules dans un fichier `main.js`.
4. Utilisez un bundler comme webpack pour regrouper ces modules pour le navigateur.

---
routeAlias: 'correction-exercice-organisation-code-modules'
---

## Correction de l'exercice

```javascript
// mathUtils.js
export const addition = (a, b) => a + b
export const subtract = (a, b) => a - b

// stringUtils.js
export const capitalize = (str) => str.charAt(0).toUpperCase() + str.slice(1)
export const reverse = (str) => str.split('').reverse().join('')

// main.js
import { addition, subtract } from './mathUtils.js'
import * as stringUtils from './stringUtils.js'

console.log(addition(5, 3))
console.log(subtract(10, 4))
console.log(stringUtils.capitalize('hello'))
console.log(stringUtils.reverse('world'))
```

Pour utiliser avec webpack, vous devrez configurer un fichier `webpack.config.js` et installer les dépendances nécessaires.
