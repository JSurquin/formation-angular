---
layout: new-section
routeAlias: 'angular-basics'
---

# Les Bases d'Angular

---

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

## Commandes CLI Angular essentielles

```bash
# Création d'un nouveau projet
ng new mon-projet

# Lancer le serveur de développement
ng serve

# Générer un composant
ng generate component mon-composant
# ou version courte
ng g c mon-composant

# Générer un service
ng generate service mon-service

# Générer une directive
ng generate directive ma-directive

# Générer un pipe
ng generate pipe mon-pipe

# Générer une interface
ng generate interface mon-interface

# Builder l'application pour la production
ng build --prod
```

---
layout: new-section
routeAlias: 'architecture-fondamentale'
---

<img class="w-1/3 mx-auto" src="/architecture.png" alt="Architecture d'Angular" />

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

## Initialisation de l'application

C'est ici que l'on initialise l'application Angular.
Si nous voulons par exemple utiliser un router ou utiliser provideHttpClient, nous pouvons le faire ici.

<small>

> **Je vous ai mis en évidence les lignes 3 et 4 qui sont les plus importantes.**

</small>

```typescript {3,4}
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes),
    provideHttpClient()
  ]
}).catch(err => console.error(err));
```

---

## Structure d'un composant de base

Nous allons voir un exemple de composant de base.

Ne vous inquiétez pas , nous allons revenir dessus dans les slides suivantes.

---

```typescript {2-4|5-6|7-13|all}
// hello.component.ts
@Component({
  selector: 'app-hello',
  // le selector est le nom de la balise html qui va être utilisée pour utiliser le composant
  standalone: true,
  // standalone: true pour utiliser le composant sans NgModule, le composant vis en autonomie
  template: `
    <h1>Hello {{ name }}</h1>
    <button (click)="sayHello()">
      Click me
    </button>
  `
  // template: ` pour le template du composant , en clair la vue :)
})
export class HelloComponent {
  name = 'Angular';
  // name est une propriété du composant, je viens de la créer moi même
  
  // sayHello est une méthode du composant, je viens de la créer moi même
  sayHello() {
    // cette méthode va être appelée lorsque l'on va cliquer sur le bouton dans la vue
    // <button (click)="sayHello()">Click me</button>
    console.log('Hello from Angular!');
  }
}
```

---

## Data Binding fondamental

- **One-way binding (Liaison à sens unique)**

Interpolation: `{{ expression }}`

  - Affiche des données du composant dans le template
  - Exemple: `{{ user.name }}` affiche le nom de l'utilisateur
  - Supporte les expressions simples: `{{ 1 + 1 }}`, `{{ user.firstName + ' ' + user.lastName }}`

---

## Exemple d'interpolation

```typescript {1-3|4-6|7-9|10-12|13-15}
@Component({
  template: `
    <!-- Interpolation simple -->
    <h1>Bienvenue {{ userName }}</h1>
    
    <!-- Expressions mathématiques -->
    <p>Total: {{ price * quantity + tax }}</p>
    
    <!-- Expressions conditionnelles -->
    <span>Status: {{ isActive ? 'Actif' : 'Inactif' }}</span>
    
    <!-- Méthodes -->
    <div>Message: {{ getMessage() }}</div>
  `
})
export class InterpolationComponent {
  userName = 'John';
  price = 100;
  quantity = 2;
  tax = 20;
  isActive = true;
  
  getMessage() {
    return 'Hello ' + this.userName;
  }
}
```