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

## Création d'un service avec le CLI

```bash
# Générer un service global (root)
ng generate service services/user
# ou version courte
ng g s services/user

# Générer un service dans un feature module
ng g s features/auth/services/auth

# Générer un service avec des tests
ng g s services/user --spec

# Générer un service sans créer un dossier
ng g s services/user --flat

# Générer un service avec une interface
ng g s services/user --spec --type interface
```

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

---

## Exercice : Services du Mini-Blog

---

### Service d'authentification

```typescript
@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private user = signal<User | null>(null);
  private token = signal<string | null>(null);

  constructor(private http: HttpClient) {
    // Restauration de la session
    const savedToken = localStorage.getItem('token');
    if (savedToken) {
      this.token.set(savedToken);
      this.loadUser();
    }
  }

  async login(credentials: { email: string; password: string }) {
    const response = await this.http.post<AuthResponse>('/api/login', credentials).toPromise();
    this.token.set(response.token);
    this.user.set(response.user);
    localStorage.setItem('token', response.token);
  }

  logout() {
    this.token.set(null);
    this.user.set(null);
    localStorage.removeItem('token');
  }

  private async loadUser() {
    if (this.token()) {
      const user = await this.http.get<User>('/api/me').toPromise();
      this.user.set(user);
    }
  }
}
```

---

### Service des articles

```typescript
@Injectable({
  providedIn: 'root'
})
export class PostService {
  private posts = signal<Post[]>([]);
  private loading = signal(false);
  private error = signal<Error | null>(null);

  // Computed values
  readonly sortedPosts = computed(() => 
    [...this.posts()].sort((a, b) => 
      new Date(b.date).getTime() - new Date(a.date).getTime()
    )
  );

  constructor(private http: HttpClient) {}

  async fetchPosts() {
    this.loading.set(true);
    try {
      const posts = await this.http.get<Post[]>('/api/posts').toPromise();
      this.posts.set(posts);
    } catch (err) {
      this.error.set(err as Error);
    } finally {
      this.loading.set(false);
    }
  }

  async createPost(post: Omit<Post, 'id'>) {
    const newPost = await this.http.post<Post>('/api/posts', post).toPromise();
    this.posts.update(posts => [...posts, newPost]);
    return newPost;
  }

  async deletePost(id: number) {
    await this.http.delete(`/api/posts/${id}`).toPromise();
    this.posts.update(posts => posts.filter(p => p.id !== id));
  }
}
```

---

### Service des commentaires

```typescript
@Injectable({
  providedIn: 'root'
})
export class CommentService {
  private comments = signal<Comment[]>([]);

  constructor(private http: HttpClient) {}

  async getCommentsByPost(postId: number) {
    const comments = await this.http.get<Comment[]>(`/api/posts/${postId}/comments`).toPromise();
    this.comments.set(comments);
    return comments;
  }

  async addComment(postId: number, content: string) {
    const comment = await this.http.post<Comment>(`/api/posts/${postId}/comments`, { content }).toPromise();
    this.comments.update(comments => [...comments, comment]);
    return comment;
  }
}
```
