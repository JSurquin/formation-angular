---
routeAlias: 'introduction-angular'
---

# Introduction à Angular

## Qu'est-ce qu'Angular ?

- **Framework web complet**
  - Développé et maintenu par Google
  - Écrit en TypeScript
  - Architecture robuste et scalable

- **Écosystème riche**
  - CLI puissant
  - Outils de développement
  - Grande communauté
  - Documentation complète

---

## Évolution d'Angular

### Angular.js (1.x) vs Angular Moderne (2+)
- Réécriture complète
- Adoption de TypeScript
- Architecture par composants
- Performance améliorée

### Principales évolutions jusqu'à Angular 18/19
- Standalone Components (v14+)
- Signals (v16+)
- Control Flow (v17+)
- Hydration (v16+)
- Defer Loading (v17+)

---

## Objectifs des évolutions

- **Performance**
  - Détection des changements plus précise
  - Meilleure gestion de la mémoire
  - Rendu plus rapide

- **Developer Experience**
  - Syntaxe plus intuitive
  - Moins de boilerplate
  - Meilleur support TypeScript

---

## Bénéfices pour les développeurs

- **Maintenabilité**
  - Code plus prévisible
  - Débogage facilité
  - Tests plus simples

- **Productivité**
  - Moins de configuration
  - API plus cohérentes
  - Meilleurs messages d'erreur

---

# Nouveautés Angular 18/19

## Signals

```typescript
// Création d'un signal
const count = signal(0);

// Lecture de la valeur
console.log(count()); // 0

// Mise à jour de la valeur
count.set(1);
count.update(value => value + 1);
```

---

## Defer Loading

```typescript
@Component({
  template: `
    @defer {
      <heavy-component />
    } @loading {
      <spinner />
    }
  `
})
export class AppComponent {}
```

---

# Architecture Angular

## Structure d'une application

```
mon-app/
├── src/
│   ├── app/
│   │   ├── components/
│   │   ├── services/
│   │   ├── models/
│   │   └── app.component.ts
│   ├── assets/
│   └── main.ts
└── package.json
```

---

# Composants de base

## Exemple de composant

```typescript
@Component({
  selector: 'app-hello',
  standalone: true,
  template: `
    <h1>Hello {{ name }}</h1>
    <button (click)="updateName()">
      Update Name
    </button>
  `
})
export class HelloComponent {
  name = signal('Angular 18');
  
  updateName() {
    this.name.update(n => n + '!');
  }
}
```

---

# Concepts clés

- **Composants Standalone**
  - Plus besoin de NgModule
  - Configuration simplifiée
  - Meilleure portabilité

- **Hydration**
  - Rendu côté serveur amélioré
  - Meilleure performance initiale
  - SEO optimisé

- **Control Flow**
  - Nouvelle syntaxe pour les conditions
  - Boucles optimisées
  - Meilleure lisibilité

---

# Exemple de Control Flow

```typescript
@Component({
  template: `
    @if (user()) {
      <h1>Welcome {{ user().name }}</h1>
    } @else {
      <login-form />
    }

    @for (item of items(); track item.id) {
      <item-card [data]="item" />
    }
  `
})
export class AppComponent {
  user = signal<User | null>(null);
  items = signal<Item[]>([]);
}
```

---

# Performance et Optimisation

- **Signals vs Zone.js**
  - Détection des changements plus précise
  - Meilleures performances
  - Moins de re-rendus inutiles

- **Vite & ESBuild**
  - Compilation plus rapide
  - Développement plus fluide
  - Build optimisé

---

# Exercice : Premier composant Angular

1. Créez un nouveau projet Angular :
```bash
npm create @angular@latest my-app
cd my-app
```

2. Créez votre premier composant :
```typescript
@Component({
  selector: 'app-counter',
  standalone: true,
  template: `
    <div>
      <h2>Counter: {{ count() }}</h2>
      <button (click)="increment()">+</button>
      <button (click)="decrement()">-</button>
    </div>
  `
})
export class CounterComponent {
  count = signal(0);
  
  increment() {
    this.count.update(n => n + 1);
  }
  
  decrement() {
    this.count.update(n => n - 1);
  }
}
```

Cet exercice vous permettra de comprendre les bases des composants Angular et l'utilisation des signals.
