---
routeAlias: 'structures-controle'
---

# Structures de contrôle en JavaScript

- **Conditions**
  - `if`, `else`, `else if`
  - `switch`
  - Opérateur ternaire

- **Boucles**
  - `for`
  - `while`
  - `do-while`
  - `for...of` (pour les itérables)
  - `for...in` (pour les propriétés d'objets)

- **Contrôle de flux**
  - `break`
  - `continue`

---
routeAlias: 'exercice-utilisation-structures-controle'
---

## Exercice : Utilisation des structures de contrôle

Créez un script JavaScript qui :
1. Utilise une boucle `for` pour afficher les nombres de 1 à 10.
2. Utilise une structure `if-else` pour déterminer si chaque nombre est pair ou impair.
3. Utilise `for...of` pour parcourir un tableau de fruits et afficher chaque fruit.

---
routeAlias: 'correction-exercice-utilisation-structures-controle'
---

## Correction de l'exercice

```javascript
// Boucle for et structure if-else
for (let i = 1; i <= 10; i++) {
  if (i % 2 === 0) {
    console.log(`${i} est pair.`)
  }
  else {
    console.log(`${i} est impair.`)
  }
}

// Utilisation de for...of
const fruits = ['pomme', 'banane', 'cerise', 'fraise']
for (const fruit of fruits) {
  console.log(`J'aime les ${fruit}s.`)
}
```
