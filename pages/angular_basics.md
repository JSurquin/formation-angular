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
ng new mon-projet   --routing --style=scss

# Lancer le serveur de développement
ng serve

# Générer un composant
ng generate component mon-composant
# ou version courte
ng g c mon-composant

# Générer un service
ng generate service mon-service
# ou version courte
ng g s mon-service

# Générer une directive
ng generate directive ma-directive
# ou version courte
ng g d ma-directive

# Générer un pipe
ng generate pipe mon-pipe
# ou version courte
ng g p mon-pipe

# Générer une interface
ng generate interface mon-interface
# ou version courte
ng g i mon-interface

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

---

- **Property binding (Liaison de propriété)**
  - Syntaxe: `[property]="value"`
  - Permet de lier une propriété d'un élément HTML à une valeur du composant
  - Exemple: `[disabled]="isLoading"` désactive un bouton selon l'état
  - Très utilisé pour les attributs HTML: `[src]`, `[href]`, `[class]`, etc.

---

## Exemple de Property Binding

```typescript {1-3|4-6|7-9|10-12|13-15|16-18}
@Component({
  template: `
    <!-- Binding d'attributs HTML -->
    <img [src]="imageUrl" [alt]="imageAlt">
    
    <!-- Binding de propriétés -->
    <button [disabled]="isButtonDisabled">
      {{ buttonText }}
    </button>
    
    <!-- Binding de classes -->
    <div [class.active]="isActive"
         [class.error]="hasError">
      Contenu stylé
    </div>
    
    <!-- Binding de styles -->
    <p [style.color]="textColor"
       [style.font-size.px]="fontSize">
      Texte stylé
    </p>
  `
})
export class PropertyBindingComponent {
  imageUrl = 'assets/image.jpg';
  imageAlt = 'Une belle image';
  isButtonDisabled = false;
  buttonText = 'Cliquez-moi';
  isActive = true;
  hasError = false;
  textColor = 'blue';
  fontSize = 16;
}
```

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

## Exemple d'Event Binding

```typescript {1-3|4-6|7-9|10-12|13-15|16-18}
@Component({
  template: `
    <!-- Click events -->
    <button (click)="handleClick($event)">
      Compteur: {{ clickCount }}
    </button>
    
    <!-- Keyboard events -->
    <input (keyup.enter)="handleEnter($event)"
           (keydown)="handleKeyDown($event)">
           
    <!-- Mouse events -->
    <div (mouseenter)="handleMouseEnter()"
         (mouseleave)="handleMouseLeave()">
      Zone interactive
    </div>
    
    <!-- Form events -->
    <form (submit)="handleSubmit($event)">
      <button type="submit">Envoyer</button>
    </form>
  `
})
export class EventBindingComponent {
  clickCount = 0;
  
  handleClick(event: MouseEvent) {
    this.clickCount++;
    console.log('Position du clic:', event.clientX, event.clientY);
  }
  
  handleEnter(event: KeyboardEvent) {
    console.log('Touche entrée pressée:', event.target['value']);
  }
  
  handleKeyDown(event: KeyboardEvent) {
    console.log('Touche pressée:', event.key);
  }
  
  handleMouseEnter() {
    console.log('Souris entrée dans la zone');
  }
  
  handleMouseLeave() {
    console.log('Souris sortie de la zone');
  }
  
  handleSubmit(event: Event) {
    event.preventDefault();
    console.log('Formulaire soumis');
  }
}
```

---

- **Two-way binding (Liaison bidirectionnelle)**
  - Syntaxe banana in a box: `[(ngModel)]="property"`
  - Combine property binding et event binding
  - Met à jour automatiquement la vue ET le composant (synchro)
  - Parfait pour les formulaires
  - Exemple: `[(ngModel)]="user.name"` synchronise un champ input avec une propriété
  - Nécessite d'importer FormsModule ou le NgModel dans le composant standalone

---

## Exemple de Two-way Binding

```typescript {1-3|4-6|7-9|10-12|13-15|16-18}
@Component({
  standalone: true,
  imports: [FormsModule],
  template: `
    <!-- Input text basique -->
    <input [(ngModel)]="userName">
    <p>Bonjour {{ userName }}!</p>
    
    <!-- Textarea -->
    <textarea [(ngModel)]="description"
              rows="4">
    </textarea>
    <p>Description: {{ description }}</p>
    
    <!-- Select -->
    <select [(ngModel)]="selectedColor">
      <option value="red">Rouge</option>
      <option value="blue">Bleu</option>
      <option value="green">Vert</option>
    </select>
    <p>Couleur sélectionnée: {{ selectedColor }}</p>
    
    <!-- Checkbox -->
    <label>
      <input type="checkbox" [(ngModel)]="isSubscribed">
      S'abonner à la newsletter
    </label>
    <p>Statut abonnement: {{ isSubscribed }}</p>
  `
})
export class TwoWayBindingComponent {
  userName = '';
  description = '';
  selectedColor = 'red';
  isSubscribed = false;
}
```

---
layout: exercices
routeAlias: 'exercice-mini-blog'
---

# Exercice : Création du projet Mini-Blog

---

1. Créez un nouveau projet Angular :
```bash
ng new mini-blog   --routing --style=scss
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