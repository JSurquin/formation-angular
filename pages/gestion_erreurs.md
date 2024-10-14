---
routeAlias: 'gestion-erreurs'
---

# Gestion des erreurs et exceptions en JavaScript

- **Types d'erreurs en JavaScript**
  - SyntaxError, TypeError, ReferenceError, etc.

- **Gestion des erreurs**
  - Bloc `try-catch`
  - Clause `finally`

- **Lancer des erreurs**
  - Utilisation de `throw`

- **Création d'erreurs personnalisées**
  - Étendre la classe `Error`

---
routeAlias: 'exercice-gestion-erreurs-exceptions'
---

## Exercice : Gestion d'erreurs et d'exceptions

1. Créez une fonction qui lance une exception si un paramètre invalide est passé.
2. Utilisez un bloc try-catch pour gérer cette exception.
3. Créez une classe d'erreur personnalisée pour votre application.
4. Utilisez cette erreur personnalisée dans une fonction et gérez-la.

---
routeAlias: 'correction-exercice-gestion-erreurs-exceptions'
---

## Correction de l'exercice

```javascript
class AgeInvalideError extends Error {
  constructor(message) {
    super(message)
    this.name = 'AgeInvalideError'
  }
}

function verifierAge(age) {
  if (age < 0 || age > 120) {
    throw new AgeInvalideError('L\'âge doit être compris entre 0 et 120 ans.')
  }
  return true
}

try {
  verifierAge(150)
} 
catch (error) {
  if (error instanceof AgeInvalideError) {
    console.log('Erreur d\'âge :', error.message)
  } 
  else {
    console.log('Une erreur inattendue s\'est produite :', error.message)
  }
}
```
