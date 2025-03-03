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

- **Property binding (Liaison de propriété)**
  - Syntaxe: `[property]="value"`
  - Permet de lier une propriété d'un élément HTML à une valeur du composant
  - Exemple: `[disabled]="isLoading"` désactive un bouton selon l'état
  - Très utilisé pour les attributs HTML: `[src]`, `[href]`, `[class]`, etc.

---

- **Event binding (Liaison d'événement)**
  - Syntaxe: `(event)="handler()"`
  - Réagit aux événements de l'utilisateur
  - Exemples courants:
    - `(click)="onClick()"`
    - `(submit)="onSubmit()"`
    - `(input)="onInput($event)"`
  - `$event` donne accès aux données de l'événement

---

- **Two-way binding (Liaison bidirectionnelle)**
  - Syntaxe banana in a box: `[(ngModel)]="property"`
  - Combine property binding et event binding
  - Met à jour automatiquement la vue ET le composant (synchro)
  - Parfait pour les formulaires
  - Exemple: `[(ngModel)]="user.name"` synchronise un champ input avec une propriété
  - Nécessite d'importer FormsModule ou le NgModel dans le composant standalone

---
layout: exercices
routeAlias: 'exercice-mini-blog'
---

# Exercice : Création du projet Mini-Blog

---

1. Créez un nouveau projet Angular :
```bash
ng new mini-blog --standalone --routing --style=scss
cd mini-blog
```

2. Configurez la structure initiale :
```bash
mkdir src/app/features
mkdir src/app/shared
mkdir src/app/core
```

---

3. Créez le composant principal :
```typescript
// app.component.ts
@Component({
  selector: 'app-root',
  template: `
    <header>
      <h1>{{ title }}</h1>
    </header>
    <main>
      <router-outlet />
    </main>
  `
})
export class AppComponent {
  title = 'Mini Blog';
}
```

4. Testez l'application :
```bash
ng serve
``` 