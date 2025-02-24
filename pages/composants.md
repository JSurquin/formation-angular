---
layout: new-section
routeAlias: 'composants-angular'
---

# Les Composants dans Angular

---

## Qu'est-ce qu'un composant ?

Un composant est comme une brique LEGO® de votre application :
- Une partie de l'interface utilisateur (UI)
- Autonome et réutilisable
- Avec sa propre logique et son propre template

---

## Structure d'un composant

```mermaid
graph TD
    A[Composant Angular] --> B[Template HTML]
    A --> C[Logique TypeScript]
    A --> D[Styles CSS]
    C --> E[Propriétés]
    C --> F[Méthodes]
    C --> G[Cycle de vie]
```

---

## Composants Standalone (Angular 18/19)

```typescript
@Component({
  selector: 'app-user-card',
  standalone: true, // Plus besoin de NgModule !
  imports: [CommonModule],
  template: `...`
})
export class UserCardComponent {}
```

---

## Le décorateur @Component - Partie 1

```typescript
@Component({
  // Nom de la balise HTML pour utiliser ce composant
  selector: 'app-user-profile',
  
  // Template HTML du composant
  template: `
    <div class="user-profile">
      <h2>{{ userName }}</h2>
    </div>
  `
})
```

---

## Le décorateur @Component - Partie 2

```typescript
@Component({
  // Styles spécifiques au composant
  styles: [`
    .user-profile {
      padding: 20px;
      border: 1px solid #ccc;
    }
  `]
})
```

---

## Communication Parent → Enfant

### Entrées (@Input)

```typescript
@Component({
  selector: 'app-user-card',
  template: `
    <div class="card">
      <h3>{{ userName }}</h3>
      <p>{{ userRole }}</p>
    </div>
  `
})
export class UserCardComponent {
  @Input() userName: string;
  @Input() userRole: string;
}
```

---

## Utilisation des @Input

```html
<app-user-card
  userName="John Doe"
  userRole="Admin"
/>
```

---

## Communication Enfant → Parent

### Sorties (@Output)

```typescript
@Component({
  selector: 'app-counter',
  template: `
    <div>
      <h2>{{ count() }}</h2>
      <button (click)="increment()">+</button>
    </div>
  `
})
export class CounterComponent {
  count = signal(0);
  @Output() countChange = new EventEmitter<number>();
}
```

---

## Gestion des événements @Output

```typescript
export class CounterComponent {
  increment() {
    this.count.update(n => n + 1);
    this.countChange.emit(this.count());
  }
}
```

Utilisation :
```html
<app-counter
  (countChange)="handleCountChange($event)"
/>
```

---

## Styles des composants - Partie 1

```typescript
@Component({
  selector: 'app-styled-button',
  template: `
    <button class="custom-btn">
      <ng-content></ng-content>
    </button>
  `
})
```

---

## Styles des composants - Partie 2

```typescript
@Component({
  styles: [`
    .custom-btn {
      padding: 10px 20px;
      border-radius: 4px;
      border: none;
      background: #007bff;
      color: white;
      cursor: pointer;
    }
  `],
  encapsulation: ViewEncapsulation.ShadowDom
})
```

---

## Projection de contenu - Structure

```typescript
@Component({
  selector: 'app-card',
  template: `
    <div class="card">
      <div class="header">
        <ng-content select="[header]"></ng-content>
      </div>
      <div class="content">
        <ng-content></ng-content>
      </div>
    </div>
  `
})
```

---

## Projection de contenu - Utilisation

```html
<app-card>
  <h2 header>Mon titre</h2>
  <p>Contenu principal</p>
  <button footer>Action</button>
</app-card>
```

---

## Bonnes pratiques - À faire ✅

- Un composant = une responsabilité unique
- Garder les composants petits et focalisés
- Utiliser des interfaces pour typer les inputs
- Documenter les inputs/outputs importants

---

## Bonnes pratiques - À éviter ❌

- Trop de logique dans les templates
- Composants trop complexes
- Duplication de code entre composants
- Couplage fort entre composants

---

## Exercice - Interface

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  role: 'admin' | 'user';
}
```

---

## Exercice - Composant

```typescript
@Component({
  selector: 'app-user-list',
  template: `
    <div class="user-list">
      <h2>Utilisateurs</h2>
      @for (user of users(); track user.id) {
        <app-user-card
          [user]="user"
          (userClick)="onUserSelect($event)"
        />
      }
    </div>
  `
})
```

---

## Exercice - Logique

```typescript
export class UserListComponent {
  users = signal<User[]>([]);
  @Output() userSelect = new EventEmitter<User>();

  onUserSelect(user: User) {
    this.userSelect.emit(user);
  }
}
```

---
layout: exercices
routeAlias: 'exercice-post-list'
---

## Exercice : Composants du Blog

---

1. Créez le composant PostList :
```typescript
// features/posts/post-list.component.ts
@Component({
  selector: 'app-post-list',
  standalone: true,
  template: `
    <div class="posts">
      @for (post of posts; track post.id) {
        <article class="post-card">
          <h2>{{ post.title }}</h2>
          <p>{{ post.excerpt }}</p>
        </article>
      }
    </div>
  `,
  styles: [`
    .posts {
      display: grid;
      gap: 1rem;
    }
    .post-card {
      padding: 1rem;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
  `]
})
export class PostListComponent {
  posts = [
    { id: 1, title: 'Premier article', excerpt: 'Un extrait du premier article...' },
    { id: 2, title: 'Deuxième article', excerpt: 'Un extrait du deuxième article...' }
  ];
}
``` 