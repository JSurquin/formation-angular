---
routeAlias: 'angular-basics'
---

# Les bases d'Angular

## Architecture fondamentale

- **Structure d'un projet Angular**
  - Le fichier angular.json
  - Les dossiers src/, app/, assets/
  - Les fichiers de configuration

- **Concepts clés**
  - Modules (NgModule)
  - Composants
  - Services
  - Directives
  - Pipes

---

## Comprendre le bootstrapping

```typescript
// main.ts
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent, {
  providers: [
    // Configuration globale
  ]
}).catch(err => console.error(err));
```

---

## Structure d'un composant de base

```typescript
// hello.component.ts
@Component({
  selector: 'app-hello',
  standalone: true,
  template: `
    <h1>Hello {{ name }}</h1>
    <button (click)="sayHello()">
      Click me
    </button>
  `
})
export class HelloComponent {
  name = 'Angular';

  sayHello() {
    console.log('Hello from Angular!');
  }
}
```

---

## Data Binding fondamental

- **One-way binding**
  - Interpolation: `{{ expression }}`
  - Property binding: `[property]="value"`
  - Event binding: `(event)="handler()"`

- **Two-way binding**
  - Syntaxe banana in a box: `[(ngModel)]="property"`

---

## Exercice pratique : Premier composant

1. Créez un nouveau projet Angular
2. Créez un composant de compteur
3. Implémentez les différents types de binding

```typescript
@Component({
  selector: 'app-counter',
  standalone: true,
  template: `
    <div>
      <h2>Counter: {{ count }}</h2>
      <button (click)="increment()">+</button>
      <button (click)="decrement()">-</button>
    </div>
  `
})
export class CounterComponent {
  count = 0;

  increment() {
    this.count++;
  }

  decrement() {
    this.count--;
  }
}
``` 