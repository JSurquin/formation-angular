---
routeAlias: 'configuration-environnement'
---

# Configuration de l'environnement (2024/2025)

## Prérequis

```bash
# Installation de Node.js via nvm (recommandé)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install --lts

# Installation du CLI Angular
npm install -g @angular/cli@latest
```

---

## Création d'un projet Angular moderne

```bash
# Création d'un nouveau projet
ng new my-app --standalone

# Options recommandées
✔ Would you like to add routing? Yes
✔ Which stylesheet format would you like to use? SCSS
✔ Would you like to enable Server-Side Rendering (SSR)? No
```

> **standalone : pour utiliser les composants sans NgModules par défaut donc pas besoin de préciser**

---

## Structure du projet moderne

```
my-app/
├── src/
│   ├── app/
│   │   ├── components/
│   │   ├── services/
│   │   ├── app.config.ts
│   │   ├── app.routes.ts
│   │   └── app.component.ts
│   ├── assets/
│   └── main.ts
├── package.json
└── angular.json
```

---

## Configuration TypeScript

```json
// tsconfig.json
{
  "compileOnSave": false,
  "compilerOptions": {
    "strict": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "sourceMap": true,
    "declaration": false,
    "experimentalDecorators": true,
    "moduleResolution": "node",
    "importHelpers": true,
    "target": "ES2022",
    "module": "ES2022",
    "useDefineForClassFields": false,
    "lib": ["ES2022", "dom"]
  }
}
```

---

## Configuration des environnements

```typescript
// src/environments/environment.ts
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000/api'
};

// src/environments/environment.prod.ts
export const environment = {
  production: true,
  apiUrl: 'https://api.monapp.com'
};
```

---

## Scripts NPM utiles

```json
// package.json
{
  "scripts": {
    "start": "ng serve",
    "build": "ng build",
    "build:ssr": "ng build && ng run my-app:server",
    "dev:ssr": "ng run my-app:serve-ssr",
    "lint": "ng lint",
    "test": "ng test",
    "e2e": "ng e2e"
  }
}
```

---

# Exercice : Configuration initiale

1. Créez un nouveau projet Angular :

```bash
ng new exercice-app
cd exercice-app
```

2. Ajoutez ESLint et Prettier :

```bash
ng add @angular-eslint/schematics
npm install prettier prettier-eslint --save-dev
```

---

3. Configurez les environnements :

```typescript
// src/app/environments/environment.ts
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000',
  features: {
    darkMode: true,
    analytics: false
  }
};
```

4. Testez votre configuration :

```bash
ng serve
ng lint
ng test
```

Cette configuration vous donnera une base solide pour développer des applications Angular modernes.

---

# Configuration avec Vite (Angular 18/19)

## Nouvelle configuration de build

```typescript
// vite.config.ts
import { defineConfig } from '@angular-devkit/build-angular/vite';

export default defineConfig({
  build: {
    target: 'es2022'
  },
  server: {
    port: 4200
  }
});
```

---

## Avantages de Vite & ESBuild

- Build plus rapide
- Hot Module Replacement amélioré
- Meilleure gestion des dépendances
- Consommation mémoire réduite
