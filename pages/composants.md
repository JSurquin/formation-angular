---
routeAlias: 'composants-angular'
---

# Composants Angular (2024/2025)

## Composants Standalone

```typescript
// user-card.component.ts
@Component({
  selector: 'app-user-card',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div class="card">
      <h2>{{ user().name }}</h2>
      <p>{{ user().email }}</p>
      <button (click)="handleClick()">
        Contact
      </button>
    </div>
  `,
  styles: [`
    .card {
      padding: 1rem;
      border-radius: 8px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
  `]
})
export class UserCardComponent {
  user = signal<User>({
    name: 'John Doe',
    email: 'john@example.com'
  });

  handleClick() {
    // Logique de gestion du clic
  }
}
```

---

## Control Flow (Nouveau en Angular 18)

```typescript
@Component({
  selector: 'app-user-list',
  template: `
    @if (users().length) {
      @for (user of users(); track user.id) {
        <app-user-card [user]="user" />
      } 
    } @else {
      <p>Aucun utilisateur trouvé</p>
    }
  `
})
export class UserListComponent {
  users = signal<User[]>([]);
}
```

---

## Inputs et Outputs

```typescript
@Component({
  selector: 'app-counter',
  template: `
    <div>
      <h3>{{ title }}</h3>
      <p>Count: {{ count() }}</p>
      <button (click)="increment()">+</button>
      <button (click)="decrement()">-</button>
    </div>
  `
})
export class CounterComponent {
  @Input() title: string = 'Counter';
  @Output() countChange = new EventEmitter<number>();
  
  count = signal(0);
  
  increment() {
    this.count.update(n => n + 1);
    this.countChange.emit(this.count());
  }
  
  decrement() {
    this.count.update(n => n - 1);
    this.countChange.emit(this.count());
  }
}
```

---

## Cycle de vie des composants

```typescript
@Component({
  selector: 'app-lifecycle',
  template: `<div>{{ data() }}</div>`
})
export class LifecycleComponent implements OnInit, OnDestroy {
  data = signal<string>('');
  private subscription: Subscription;
  
  constructor(private dataService: DataService) {}
  
  ngOnInit() {
    this.subscription = this.dataService.getData()
      .subscribe(data => {
        this.data.set(data);
      });
  }
  
  ngOnDestroy() {
    this.subscription.unsubscribe();
  }
}
```

---

## Injection de dépendances

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {
  private apiUrl = 'https://api.example.com/users';
  
  constructor(private http: HttpClient) {}
  
  getUsers() {
    return this.http.get<User[]>(this.apiUrl);
  }
}

@Component({
  selector: 'app-users',
  template: `
    @if (users()) {
      @for (user of users(); track user.id) {
        <app-user-card [user]="user" />
      }
    }
  `
})
export class UsersComponent {
  users = signal<User[]>([]);
  
  constructor(private userService: UserService) {
    this.userService.getUsers()
      .subscribe(users => this.users.set(users));
  }
}
```

---

## View Encapsulation

```typescript
@Component({
  selector: 'app-styled',
  template: `
    <div class="container">
      <h1>Styled Component</h1>
    </div>
  `,
  styles: [`
    .container {
      background: #f0f0f0;
      padding: 20px;
    }
  `],
  encapsulation: ViewEncapsulation.ShadowDom
})
export class StyledComponent {}
```

---

## Content Projection

```typescript
@Component({
  selector: 'app-card',
  template: `
    <div class="card">
      <div class="header">
        <ng-content select="[header]"></ng-content>
      </div>
      <div class="body">
        <ng-content></ng-content>
      </div>
      <div class="footer">
        <ng-content select="[footer]"></ng-content>
      </div>
    </div>
  `
})
export class CardComponent {}

// Utilisation
@Component({
  template: `
    <app-card>
      <h2 header>Titre de la carte</h2>
      <p>Contenu principal</p>
      <button footer>Action</button>
    </app-card>
  `
})
```

---

# Exercice : Création d'un composant réutilisable

Créez un composant de bouton réutilisable avec différentes variantes :

```typescript
// components/button.component.ts
@Component({
  selector: 'app-button',
  standalone: true,
  template: `
    <button
      [class]="buttonClass()"
      [disabled]="loading()"
      (click)="handleClick()"
    >
      @if (loading()) {
        <span class="spinner"></span>
      }
      <ng-content></ng-content>
    </button>
  `,
  styles: [`
    button {
      padding: 0.5rem 1rem;
      border-radius: 4px;
      border: none;
      cursor: pointer;
      transition: all 0.2s;
    }
    
    .primary {
      background: #007bff;
      color: white;
    }
    
    .secondary {
      background: #6c757d;
      color: white;
    }
    
    .spinner {
      display: inline-block;
      width: 1rem;
      height: 1rem;
      border: 2px solid #fff;
      border-radius: 50%;
      border-top-color: transparent;
      animation: spin 1s linear infinite;
    }
    
    @keyframes spin {
      to { transform: rotate(360deg); }
    }
  `]
})
export class ButtonComponent {
  @Input() variant: 'primary' | 'secondary' = 'primary';
  @Input() loading = signal(false);
  @Output() click = new EventEmitter<void>();
  
  buttonClass = computed(() => this.variant);
  
  handleClick() {
    if (!this.loading()) {
      this.click.emit();
    }
  }
}
```

Utilisez ce composant dans votre application pour comprendre les concepts de base des composants Angular. 