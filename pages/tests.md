---
routeAlias: 'tests-react-native'
---

<a name="tests-react-native"></a>

# Tests dans React Native (2024/2025)

## Introduction aux tests

Les tests sont essentiels dans le développement React Native pour :
- Assurer la qualité du code
- Prévenir les régressions
- Faciliter la maintenance
- Documenter le comportement attendu

---

## Types de tests

### Tests Unitaires
- Tests de composants isolés
- Tests de fonctions pures
- Tests de hooks personnalisés
- Utilisation de Jest et React Native Testing Library

### Tests d'Intégration
- Tests de flux complets
- Tests de navigation
- Interactions entre composants
- Communication avec les APIs

### Tests End-to-End (E2E)
- Tests sur device réel/émulateur
- Scénarios utilisateur complets
- Utilisation de Detox
- Tests de performance

---

## Tests unitaires avec Jest

```tsx
// components/__tests__/Button.test.tsx
import { render, fireEvent } from '@testing-library/react-native'
import { Button } from '../Button'

describe('Button Component', () => {
  it('appelle onPress quand cliqué', () => {
    const onPress = jest.fn()
    const { getByText } = render(
      <Button onPress={onPress}>
        Cliquez-moi
      </Button>
    )

    fireEvent.press(getByText('Cliquez-moi'))
    expect(onPress).toHaveBeenCalled()
  })

  it('affiche le texte correctement', () => {
    const { getByText } = render(
      <Button onPress={() => {}}>
        Test
      </Button>
    )

    expect(getByText('Test')).toBeTruthy()
  })
})
```

---

## Mocking des modules natifs

```tsx
// __mocks__/react-native-camera.ts
export const RNCamera = {
  Constants: {
    Type: {
      back: 'back',
      front: 'front'
    },
    FlashMode: {
      on: 'on',
      off: 'off'
    }
  }
};

// __tests__/CameraScreen.test.tsx
jest.mock('react-native-camera', () => ({
  RNCamera: {
    Constants: {
      Type: {
        back: 'back',
        front: 'front'
      }
    }
  }
}));
```

---

## Tests de performance

```tsx
// __tests__/performance.test.tsx
import { measurePerformance } from 'react-native-performance';

describe('Performance Tests', () => {
  it('charge la liste en moins de 500ms', async () => {
    const startTime = performance.now();
    
    await renderListScreen();
    const endTime = performance.now();
    
    expect(endTime - startTime).toBeLessThan(500);
  });

  it('maintient un FPS stable pendant l\'animation', () => {
    const fpsMonitor = new FPSMonitor();
    
    // Démarrer l'animation
    startAnimation();
    
    // Vérifier que le FPS reste au-dessus de 30
    expect(fpsMonitor.getAverageFPS()).toBeGreaterThan(30);
  });
});
```

---

## Tests de snapshot

```tsx
// __tests__/ProfileCard.test.tsx
import renderer from 'react-test-renderer';
import { ProfileCard } from '../components/ProfileCard';

describe('ProfileCard', () => {
  it('correspond au snapshot', () => {
    const tree = renderer.create(
      <ProfileCard
        name="John Doe"
        age={25}
        bio="Developer"
        image="https://example.com/photo.jpg"
      />
    ).toJSON();
    
    expect(tree).toMatchSnapshot();
  });

  it('gère correctement les longues descriptions', () => {
    const tree = renderer.create(
      <ProfileCard
        name="John Doe"
        age={25}
        bio="Une très longue description qui devrait être tronquée..."
        image="https://example.com/photo.jpg"
      />
    ).toJSON();
    
    expect(tree).toMatchSnapshot();
  });
});
```

---

## Tests d'accessibilité

```tsx
// __tests__/accessibility.test.tsx
import { render } from '@testing-library/react-native';
import { AccessibleButton } from '../components/AccessibleButton';

describe('Tests d\'accessibilité', () => {
  it('a les bonnes propriétés d\'accessibilité', () => {
    const { getByRole } = render(
      <AccessibleButton
        label="Valider"
        onPress={() => {}}
      />
    );

    const button = getByRole('button');
    expect(button.props.accessibilityLabel).toBe('Valider');
    expect(button.props.accessibilityRole).toBe('button');
  });

  it('supporte VoiceOver/TalkBack', () => {
    const { getByA11yLabel } = render(
      <AccessibleButton
        label="Valider"
        onPress={() => {}}
      />
    );

    expect(getByA11yLabel('Valider')).toBeTruthy();
  });
});
```

---

## Configuration CI/CD pour les tests

```yaml
# .github/workflows/tests.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Setup Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '18'
        
    - name: Install dependencies
      run: npm install
      
    - name: Run unit tests
      run: npm test
      
    - name: Run E2E tests
      uses: wix/detox-action@v1
      with:
        configuration: 'android.emu.debug'
        
    - name: Upload test coverage
      uses: codecov/codecov-action@v2
```