---
layout: new-section
routeAlias: 'configuration-environnement'
---

# Configuration de l'environnement

---

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

## Analyse des scripts NPM utiles

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
  apiUrl: 'http://localhost:3000', // URL de l'API
  features: {
    darkMode: true, // Mode sombre
    analytics: false // Analyse
  }
};
```

---

4. Testez votre configuration :

```bash
ng serve
ng lint
ng test
```

Cette configuration vous donnera une base solide pour développer des applications Angular modernes.
