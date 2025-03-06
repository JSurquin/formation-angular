---
layout: new-section
routeAlias: 'template-syntax'
---

# Syntaxe des Templates

---

## Interpolation et expressions

Nous avons déjà vu rapidement il y a quelques instants l'interpolation avec la syntaxe `{{ expression }}` mais nous allons revoir des cas concrets différents.

---

```typescript
@Component({
  template: `
    <!-- Interpolation basique -->
    <h1>{{ title }}</h1>
    
    <!-- Expressions -->
    <p>Total: {{ price * quantity }}</p>
    
    <!-- Méthodes -->
    <div>{{ getMessage() }}</div>
    
    <!-- Chaînage de propriétés -->
    <span>{{ user?.address?.city }}</span>
  `
})
export class DemoComponent {
  title = 'Mon App';
  price = 10;
  quantity = 2;
  user = {
    address: { city: 'Paris' }
  };
  
  getMessage() {
    return 'Hello!';
  }
}
```

---

## Bindings de propriétés et d'événements

```typescript
@Component({
  template: `
    <!-- Property binding -->
    <img [src]="imageUrl" [alt]="imageAlt">
    <button [disabled]="isDisabled">Click me</button>
    
    <!-- Event binding -->
    <button (click)="handleClick($event)">
      Click count: {{ clickCount }}
    </button>
    
    <!-- Two-way binding -->
    <input [(ngModel)]="userName">
    <p>Hello, {{ userName }}!</p>
  `
})
export class BindingsComponent {
  imageUrl = 'path/to/image.jpg';
  imageAlt = 'Une image';
  isDisabled = false;
  clickCount = 0;
  userName = '';
  
  handleClick(event: MouseEvent) {
    this.clickCount++;
    console.log('Click event:', event);
  }
}
```

---

## Directives structurelles modernes

```typescript
@Component({
  template: `
    <!-- If moderne -->
    @if (isLoggedIn()) {
      <nav>Menu utilisateur</nav>
    } @else {
      <auth-form />
    }

    <!-- For moderne -->
    @for (item of items(); track item.id) {
      <item-card [data]="item" />
    }

    <!-- Switch moderne -->
    @switch (status()) {
      @case ('loading') {
        <spinner />
      }
      @case ('error') {
        <error-message />
      }
      @default {
        <content />
      }
    }
  `
})
export class ModernDirectivesComponent {
  items = signal<Item[]>([]);
  status = signal<'loading' | 'error' | 'success'>('loading');
  
  isLoggedIn() {
    return true;
  }
}
```

---

## Pipes et formatage

```typescript
@Component({
  template: `
    <!-- Pipes de base -->
    <p>{{ date | date:'shortDate' }}</p>
    <p>{{ price | currency:'EUR' }}</p>
    <p>{{ text | uppercase }}</p>
    
    <!-- Chaînage de pipes -->
    <p>{{ data | json | async }}</p>
    
    <!-- Pipes avec paramètres -->
    <p>{{ number | number:'1.0-2' }}</p>
    
    <!-- Pipe personnalisé -->
    <p>{{ text | highlight:searchTerm }}</p>
  `
})
export class PipesComponent {
  date = new Date();
  price = 42.99;
  text = 'Hello World';
  number = 3.14159;
  searchTerm = 'World';
  data = of({ name: 'John' });
}
```

---

## Références de template et variables

```typescript
@Component({
  template: `
    <!-- Référence locale -->
    <input #nameInput type="text">
    <button (click)="greet(nameInput.value)">
      Saluer
    </button>
    
    <!-- ViewChild -->
    <div #content>
      Contenu dynamique
    </div>
    
    <!-- Variables de template -->
    @for (item of items; track item.id; let i = $index) {
      <div>{{ i + 1 }}. {{ item.name }}</div>
    }
  `
})
export class ReferencesComponent {
  @ViewChild('content') contentDiv!: ElementRef;
  
  greet(name: string) {
    alert(`Bonjour ${name}!`);
  }
  
  ngAfterViewInit() {
    console.log(this.contentDiv.nativeElement);
  }
}
```

---
layout: exercices
routeAlias: 'exercice-template-post'
---

## Exercice : Template du Post

Vous allez devoir modifier le template de votre composant article pour qu'il affiche les informations du post.

Faire un bouton like qui incrémente le nombre de likes.

Créer un composant like qui affiche un bouton like et un compteur de likes. (donc il reçoit le nombre de likes en entrée et affiche un bouton like et un compteur de likes)

---

## Communication entre composants

```mermaid
graph TD
    BL[BlogListComponent]
    P[PostComponent]
    L[LikeComponent]
    
    BL -->|@Input post| P
    P -->|@Input likes| L
    
    L -->|@Output onLike| P
    P -->|@Output onLike| BL
    
    style BL fill:#f9f,stroke:#333
    style P fill:#bbf,stroke:#333
    style L fill:#bfb,stroke:#333
```

---

### Flux des données
- ⬇️ **@Input** : Les données descendent du parent vers l'enfant
  - `BlogList` → `Post` : passe l'objet post complet
  - `Post` → `Like` : passe le nombre de likes

- ⬆️ **@Output** : Les événements remontent de l'enfant vers le parent
  - `Like` → `Post` : émet quand le bouton est cliqué
  - `Post` → `BlogList` : relaie l'événement avec l'ID du post

---
layout: new-section
routeAlias: 'exercice-template-post-solution'
---

## Solution étape par étape

---

### 2. Composant Like (enfant)

```typescript
// components/like.component.ts
@Component({
  selector: 'app-like',
  standalone: true,
  template: `
    <div class="flex items-center gap-2">
      <button 
        (click)="handleLike()"
        class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600"
      >
        Like
      </button>
      <span>{{ likes }} likes</span>
    </div>
  `
})
export class LikeComponent {
  @Input() likes = 0;
  @Output() onLike = new EventEmitter<void>();

  handleLike() {
    this.onLike.emit();
  }
}
```

---

### 3. Composant Post (parent du Like)

```typescript
// components/post.component.ts
@Component({
  selector: 'app-post',
  standalone: true,
  imports: [LikeComponent],
  template: `
    <article class="p-4 border rounded-lg shadow-sm">
      <h2 class="text-2xl font-bold">{{ post.title }}</h2>
      <p class="mt-2">{{ post.content }}</p>
      <p class="text-sm text-gray-500">
        Publié le {{ post.publishedAt | date:'dd/MM/yyyy' }}
      </p>
      <app-like 
        [likes]="post.likes" 
        (onLike)="handleLike()"
      />
    </article>
  `
})
export class PostComponent {
  @Input({ required: true }) post!: Post;
  @Output() onLike = new EventEmitter<number>();

  handleLike() {
    this.onLike.emit(this.post.id);
  }
}
```

---

### 4. Composant Blog List (parent du Post)

```typescript
// components/blog-list.component.ts
@Component({
  selector: 'app-blog-list',
  standalone: true,
  imports: [PostComponent],
  template: `
    <div class="grid gap-4">
      @for (post of posts(); track post.id) {
        <app-post 
          [post]="post"
          (onLike)="handlePostLike($event)"
        />
      }
    </div>
  `
})
export class BlogListComponent {
  posts = signal<Post[]>([
    {
      id: 1,
      title: 'Mon premier article',
      content: 'Contenu de mon article',
      publishedAt: new Date(),
      likes: 0
    }
  ]);

  handlePostLike(postId: number) {
    this.posts.update(posts => 
      posts.map(post => 
        post.id === postId 
          ? { ...post, likes: post.likes + 1 }
          : post
      )
    );
  }
}
```

---

## Flux de communication entre composants

<br>

<span class="text-red-500">

### 1. LikeComponent (enfant le plus bas)

</span>

- **Entrée** : Reçoit `@Input() likes` 
- **Sortie** : Émet `@Output() onLike` vers PostComponent

<br>

<span class="text-orange-500">

### 2. PostComponent (composant intermédiaire)

</span>

- **Entrée** : Reçoit `@Input() post`
- **Sortie** : Émet `@Output() onLike` vers BlogListComponent
- **Rôle** : Reçoit l'événement de LikeComponent et le relaie au parent

<br>

<span class="text-green-500">

### 3. BlogListComponent (parent)

</span>

- **État** : Contient le state (signal `posts`)
- **Gestion** : Reçoit l'événement du PostComponent et met à jour le state

---

Cette solution montre de manière progressive :
1. La définition des types
2. La création du composant le plus petit (Like)
3. Le composant intermédiaire (Post)
4. Le composant parent qui gère l'état (BlogList)

Points clés :
- Communication parent-enfant avec `@Input()` et `@Output()`
- Gestion d'état avec Signals
- Composants standalone
- Typage fort
- Utilisation des pipes
- Styling avec Tailwind

# Control Flow Moderne (Angular 18/19)

## Nouvelle Syntaxe @if

```typescript
@Component({
  template: `
    @if (user()) {
      <h1>Bienvenue {{ user().name }}</h1>
    } @else if (loading()) {
      <spinner-component />
    } @else {
      <login-form />
    }
  `
})
export class WelcomeComponent {
  user = signal<User | null>(null);
  loading = signal(true);
}
```

## Boucle @for Optimisée

```typescript
@Component({
  template: `
    @for (post of posts(); track post.id) {
      <article class="post">
        <h2>{{ post.title }}</h2>
        <p>{{ post.content }}</p>
        @if (post.isPublished) {
          <span class="badge">Publié</span>
        }
      </article>
    }
  `
})
export class BlogListComponent {
  posts = signal<Post[]>([]);
}
```

## Switch Case Moderne

```typescript
@Component({
  template: `
    @switch (post.status) {
      @case ('draft') {
        <draft-preview [post]="post" />
      }
      @case ('published') {
        <published-post [post]="post" />
      }
      @case ('archived') {
        <archived-post [post]="post" />
      }
      @default {
        <p>Status inconnu</p>
      }
    }
  `
})
```

## Note d'Expert 💡

Le nouveau control flow d'Angular 18/19 apporte plusieurs avantages :
- Meilleure performance (plus besoin de NgModules)
- Syntaxe plus claire et intuitive
- Meilleure intégration avec TypeScript
- Support natif du tracking pour les boucles

Pour le mini-blog, utilisez ces nouvelles syntaxes pour :
1. Gérer l'affichage conditionnel des boutons d'édition (@if)
2. Afficher la liste des articles (@for avec tracking)
3. Gérer les différents états de chargement (@switch)
