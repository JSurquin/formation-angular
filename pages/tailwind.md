---
layout: new-section
routeAlias: 'tailwind-setup'
---

# Tailwind v4 dans Angular

---

## Installation de Tailwind v4

```bash
# Installation des dépendances
npm install -D tailwindcss@latest postcss@latest autoprefixer@latest

# Génération du fichier de configuration Tailwind
npx tailwindcss init
```

---

## Configuration de Tailwind

```javascript
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{html,ts}",
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {
      // Vos personnalisations ici
    },
  },
  plugins: [],
}
```

---

## Configuration de PostCSS

```javascript
// postcss.config.js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  }
}
```

---

## Intégration avec Angular

```css
/* src/styles.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

## Configuration de l'application Angular

```json
// angular.json
{
  "projects": {
    "your-app": {
      "architect": {
        "build": {
          "options": {
            "styles": [
              "src/styles.css"
            ],
            "postcssConfig": "postcss.config.js"
          }
        }
      }
    }
  }
}
```

---

## Utilisation dans les composants

```typescript
@Component({
  selector: 'app-header',
  template: `
    <header class="bg-gray-800 text-white p-4">
      <nav class="container mx-auto flex items-center justify-between">
        <h1 class="text-2xl font-bold">Mon App</h1>
        <ul class="flex space-x-4">
          <li><a href="#" class="hover:text-blue-400">Accueil</a></li>
          <li><a href="#" class="hover:text-blue-400">À propos</a></li>
          <li><a href="#" class="hover:text-blue-400">Contact</a></li>
        </ul>
      </nav>
    </header>
  `
})
export class HeaderComponent {}
```

---

## Fonctionnalités avancées de Tailwind v4

- Support natif des variables CSS
- Nouveau système de couleurs
- Améliorations des performances
- Meilleure compatibilité avec Angular

---

## Bonnes pratiques avec Tailwind

- Utiliser les composants pour réutiliser les styles
- Créer des classes utilitaires personnalisées
- Optimiser la taille du bundle final
- Suivre les conventions de nommage

---

## Configuration pour la production

```javascript
// tailwind.config.js
module.exports = {
  content: [
    "./src/**/*.{html,ts}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
  // Optimisations pour la production
  purge: {
    enabled: process.env.NODE_ENV === 'production',
    content: [
      './src/**/*.{html,ts}',
    ]
  }
} 