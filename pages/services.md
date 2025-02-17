---
routeAlias: 'services-dependency-injection'
---

# Services et Injection de Dépendances (2024/2025)

## Service moderne avec Signals

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {
  private users = signal<User[]>([]);
  private loading = signal(false);
  private error = signal<Error | null>(null);
  
  // Computed values
  readonly sortedUsers = computed(() => 
    [...this.users()].sort((a, b) => a.name.localeCompare(b.name))
  );
  
  constructor(private http: HttpClient) {}
  
  async fetchUsers() {
    this.loading.set(true);
    this.error.set(null);
    
    try {
      const data = await this.http.get<User[]>('/api/users').toPromise();
      this.users.set(data);
    } catch (err) {
      this.error.set(err as Error);
    } finally {
      this.loading.set(false);
    }
  }
}
```

---

## Injection de dépendances moderne

```typescript
// Service avec configuration
@Injectable({
  providedIn: 'root',
  useFactory: (config: AppConfig) => {
    return new ApiService(config.apiUrl);
  },
  deps: [APP_CONFIG]
})
export class ApiService {
  constructor(private baseUrl: string) {}
}

// Token d'injection
const APP_CONFIG = new InjectionToken<AppConfig>('app.config');

// Provider au niveau de l'application
bootstrapApplication(AppComponent, {
  providers: [
    {
      provide: APP_CONFIG,
      useValue: {
        apiUrl: 'https://api.example.com',
        version: '1.0.0'
      }
    }
  ]
});
```

---

## Service avec état partagé

```typescript
@Injectable({
  providedIn: 'root'
})
export class ThemeService {
  private darkMode = signal(false);
  
  // API publique en lecture seule
  readonly isDarkMode = this.darkMode.asReadonly();
  
  // Computed values
  readonly theme = computed(() => 
    this.darkMode() ? 'dark' : 'light'
  );
  
  toggleTheme() {
    this.darkMode.update(dark => !dark);
    document.body.classList.toggle('dark-theme');
  }
}
```

---

## Utilisation dans les composants

```typescript
@Component({
  selector: 'app-theme-switcher',
  template: `
    <button (click)="toggleTheme()">
      Mode {{ themeService.isDarkMode() ? 'Clair' : 'Sombre' }}
    </button>
  `
})
export class ThemeSwitcherComponent {
  constructor(public themeService: ThemeService) {}
  
  toggleTheme() {
    this.themeService.toggleTheme();
  }
}
```

---

## Service avec gestion d'état avancée

```typescript
interface AppState {
  user: User | null;
  preferences: UserPreferences;
  notifications: Notification[];
}

@Injectable({
  providedIn: 'root'
})
export class StateService {
  private state = signal<AppState>({
    user: null,
    preferences: defaultPreferences,
    notifications: []
  });
  
  // Selectors
  readonly user = computed(() => this.state().user);
  readonly preferences = computed(() => this.state().preferences);
  readonly notifications = computed(() => this.state().notifications);
  
  // Actions
  updateUser(user: User) {
    this.state.update(state => ({
      ...state,
      user
    }));
  }
  
  addNotification(notification: Notification) {
    this.state.update(state => ({
      ...state,
      notifications: [...state.notifications, notification]
    }));
  }
}
```

---

## Service avec effets

```typescript
@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private user = signal<User | null>(null);
  
  constructor() {
    // Effet pour synchroniser avec le localStorage
    effect(() => {
      const currentUser = this.user();
      if (currentUser) {
        localStorage.setItem('user', JSON.stringify(currentUser));
      } else {
        localStorage.removeItem('user');
      }
    });
  }
  
  async login(credentials: Credentials) {
    // Logique de login
    const user = await this.apiLogin(credentials);
    this.user.set(user);
  }
  
  logout() {
    this.user.set(null);
  }
}
```

---

## Service avec injection hiérarchique

```typescript
// Service au niveau du module/composant
@Injectable()
export class FeatureService {
  private data = signal<any[]>([]);
  
  // Méthodes spécifiques à la fonctionnalité
}

@Component({
  selector: 'app-feature',
  providers: [FeatureService], // Injection locale
  template: `
    <div>
      Feature Component
      <child-component></child-component>
    </div>
  `
})
export class FeatureComponent {
  constructor(private featureService: FeatureService) {}
}
```

---

# Exercice : Service de panier d'achat

Créez un service de panier d'achat avec Signals :

```typescript
interface CartItem {
  id: number;
  name: string;
  price: number;
  quantity: number;
}

@Injectable({
  providedIn: 'root'
})
export class CartService {
  private items = signal<CartItem[]>([]);
  
  // Computed values
  readonly totalItems = computed(() => 
    this.items().reduce((sum, item) => sum + item.quantity, 0)
  );
  
  readonly totalPrice = computed(() => 
    this.items().reduce((sum, item) => sum + item.price * item.quantity, 0)
  );
  
  addItem(product: Product, quantity: number = 1) {
    this.items.update(items => {
      const existingItem = items.find(item => item.id === product.id);
      
      if (existingItem) {
        return items.map(item => 
          item.id === product.id
            ? { ...item, quantity: item.quantity + quantity }
            : item
        );
      }
      
      return [...items, { ...product, quantity }];
    });
  }
  
  removeItem(productId: number) {
    this.items.update(items => 
      items.filter(item => item.id !== productId)
    );
  }
  
  updateQuantity(productId: number, quantity: number) {
    if (quantity <= 0) {
      this.removeItem(productId);
      return;
    }
    
    this.items.update(items =>
      items.map(item =>
        item.id === productId
          ? { ...item, quantity }
          : item
      )
    );
  }
  
  clearCart() {
    this.items.set([]);
  }
}
```

Utilisez ce service dans vos composants pour gérer un panier d'achat avec état persistant et réactif. 