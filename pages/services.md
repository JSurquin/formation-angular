---
layout: new-section
routeAlias: 'services-dependency-injection'
---

# Services Angular

---

## Introduction aux services

<br>

### Voyons un service moderne avec Signals

<br>

> Avant tout, je vous rapelle que vous n'êtes pas obligé d'utiliser Signals pour faire des services.
Vous pouvez utiliser rxjs et les Observables comme Subject, BehaviorSubject, ReplaySubject, etc.

---

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
layout: exercices
routeAlias: 'exercice-service-blog'
---

# Exercice : Service de Blog

---

1. Créez l'interface Post :
```typescript
// features/posts/post.model.ts
export interface Post {
  id: number
  title: string
  content: string
  excerpt: string
  author: string
  date: Date
}
```

---

2. Créez le service PostService :
```typescript
// features/posts/post.service.ts
@Injectable({
  providedIn: 'root'
})
export class PostService {
  private posts = signal<Post[]>([])
  readonly allPosts = this.posts.asReadonly()
  
  constructor(private http: HttpClient) {}

  loadPosts() {
    return this.http.get<Post[]>('/api/posts').pipe(
      tap(posts => this.posts.set(posts))
    )
  }

  getPost(id: number) {
    return this.http.get<Post>(`/api/posts/${id}`)
  }

  createPost(post: Omit<Post, 'id'>) {
    return this.http.post<Post>('/api/posts', post).pipe(
      tap(newPost => {
        this.posts.update(posts => [...posts, newPost])
      })
    )
  }
}
```
