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
ng new my-app --standalone --ssr

# Options recommandées
✔ Would you like to add routing? Yes
✔ Which stylesheet format would you like to use? SCSS
✔ Would you like to enable Server-Side Rendering (SSR)? Yes
```

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

## Configuration de l'environnement de développement

### VS Code Extensions recommandées

```json
{
  "recommendations": [
    "Angular.ng-template",
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "johnpapa.Angular2"
  ]
}
```

---

## Configuration ESLint moderne

```javascript
// .eslintrc.json
{
  "root": true,
  "ignorePatterns": ["projects/**/*"],
  "overrides": [
    {
      "files": ["*.ts"],
      "extends": [
        "eslint:recommended",
        "plugin:@typescript-eslint/recommended",
        "plugin:@angular-eslint/recommended"
      ],
      "rules": {
        "@angular-eslint/directive-selector": [
          "error",
          {
            "type": "attribute",
            "prefix": "app",
            "style": "camelCase"
          }
        ],
        "@angular-eslint/component-selector": [
          "error",
          {
            "type": "element",
            "prefix": "app",
            "style": "kebab-case"
          }
        ]
      }
    }
  ]
}
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

## Configuration du SSR (Server-Side Rendering)

```typescript
// app.config.server.ts
import { mergeApplicationConfig, ApplicationConfig } from '@angular/core';
import { provideServerRendering } from '@angular/platform-server';
import { appConfig } from './app.config';

const serverConfig: ApplicationConfig = {
  providers: [
    provideServerRendering()
  ]
};

export const config = mergeApplicationConfig(appConfig, serverConfig);
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
ng new exercice-app --standalone --ssr
cd exercice-app
```

2. Ajoutez ESLint et Prettier :

```bash
ng add @angular-eslint/schematics
npm install prettier prettier-eslint --save-dev
```

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
