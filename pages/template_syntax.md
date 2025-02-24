---
layout: new-section
routeAlias: 'template-syntax'
---

# Syntaxe des Templates

---

## Interpolation et expressions

Nous avons déjà vu rapidement il y a quelques instants l'interpolation avec la syntaxe `{{ expression }}` mais nous allons revoir des cas concrets différents.

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

Cet exercice vous permettra de maîtriser :
- La syntaxe d'interpolation
- Les différents types de binding
- Les directives structurelles modernes
- L'utilisation des pipes
- Les références de template 

---
layout: exercices
routeAlias: 'exercice-template-post'
---

## Exercice : Template du Post

---

1. Créez le composant PostCard :
```typescript
// features/posts/post-card.component.ts
@Component({
  selector: 'app-post-card',
  standalone: true,
  template: `
    <article class="post-card">
      <h2>{{ post.title }}</h2>
      <p class="meta">
        Par {{ post.author }} 
        le {{ post.date | date:'shortDate' }}
      </p>
      <p [innerHTML]="post.excerpt"></p>
      <div class="actions">
        <button (click)="onRead()">Lire</button>
        @if (isAuthor) {
          <button (click)="onEdit()">Éditer</button>
        }
      </div>
    </article>
  `,
  styles: [`
    .post-card {
      padding: 1rem;
      border: 1px solid #ddd;
      border-radius: 4px
    }
    .meta {
      color: #666;
      font-size: 0.9rem
    }
  `]
})
export class PostCardComponent {
  @Input() post: Post
  @Input() isAuthor = false
  @Output() read = new EventEmitter<void>()
  @Output() edit = new EventEmitter<void>()

  onRead() {
    this.read.emit()
  }

  onEdit() {
    this.edit.emit()
  }
} 