---
routeAlias: 'tests-unitaires'
---

# Tests unitaires en JavaScript

- **Jest**
  - Framework de test pour JavaScript
  - Assertions, mocks, et couverture de code

- **React Testing Library**
  - Tests de composants React
  - Focus sur le comportement plutôt que l'implémentation

- **Mocha et Chai**
  - Mocha comme runner de tests
  - Chai pour les assertions

- **Sinon**
  - Création de stubs, mocks et spies

---
routeAlias: 'exercice-tests-unitaires-fonction-utilitaire'
---

## Exercice : Tests unitaires pour une fonction utilitaire

1. Créez une fonction utilitaire (par exemple, pour formater des dates).
2. Écrivez des tests unitaires pour cette fonction en utilisant Jest.
3. Utilisez des cas de test pour couvrir différents scénarios.
4. Ajoutez des tests pour les cas d'erreur.
5. Générez un rapport de couverture de code.

---
routeAlias: 'correction-exercice-tests-unitaires-fonction-utilitaire'
---

## Correction de l'exercice

```javascript
// dateUtils.js
export function formatDate(date) {
  if (!(date instanceof Date)) {
    throw new Error('Invalid date')
  }
  return date.toISOString().split('T')[0]
}

// dateUtils.test.js
import { formatDate } from './dateUtils'

describe('formatDate', () => {
  it('should format a valid date correctly', () => {
    const date = new Date('2023-05-15')
    expect(formatDate(date)).toBe('2023-05-15')
  })

  it('should throw an error for invalid input', () => {
    expect(() => formatDate('not a date')).toThrow('Invalid date')
  })

  it('should handle edge cases', () => {
    const date = new Date('1900-01-01')
    expect(formatDate(date)).toBe('1900-01-01')
  })
})
```

Pour générer un rapport de couverture, ajoutez `"test:coverage": "jest --coverage"` à vos scripts npm.
