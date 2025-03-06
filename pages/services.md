---
layout: new-section
routeAlias: 'services-dependency-injection'
---

# Services Angular

---

## Qu'est-ce qu'un service ?

Un service est une classe qui encapsule la logique métier et les données partagées :
- Réutilisable dans toute l'application
- Suit le principe de responsabilité unique
- Facilite la maintenance et les tests
- Permet la séparation des préoccupations

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

## Service classique avec Observables

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {
  private userSubject = new BehaviorSubject<User | null>(null);
  // userSubject est un Observable (new BehaviorSubject donc il peut être observé) de type User | null par défaut à null
  // user$ est un Observable de type User | null
  user$ = this.userSubject.asObservable();

  constructor(private http: HttpClient) {}
  // on injecte le http client

  // on récupère un user par son id dans la fonction getUser
  getUser(id: number): Observable<User> {
    // appel à l'api pour récupérer un user par son id
    return this.http.get<User>(`/api/users/${id}`).pipe(
      // on met à jour l'utilisateur dans le subject
      tap(user => this.userSubject.next(user))
    );
  }
  
  // on met à jour le user dans le subject dans la fonction updateUser
  updateUser(user: User): Observable<User> {
    // appel à l'api pour mettre à jour le user
    return this.http.put<User>(`/api/users/${user.id}`, user).pipe(
      // on met à jour l'utilisateur dans le subject
      tap(updatedUser => this.userSubject.next(updatedUser))
    );
  }
}
```

---

## Utilisation d'un service classique

```typescript
@Component({
  selector: 'app-user-profile',
  template: `
    <div *ngIf="user$ | async as user">
      <h2>{{ user.name }}</h2>
      <button (click)="updateName()">Modifier le nom</button>
    </div>
  `
})
export class UserProfileComponent {
  user$ = this.userService.user$;

  constructor(private userService: UserService) {
    this.userService.getUser(1).subscribe();
  }

  updateName() {
    this.user$.pipe(
      take(1),
      switchMap(user => this.userService.updateUser({
        ...user!,
        name: 'Nouveau nom'
      }))
    ).subscribe();
  }
}
```

---
layout: new-section
---

# Services avec Signals

---

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
layout: exercices
routeAlias: 'exercice-service-blog'
---

# Exercice : Service de Blog

---

1. Créez l'interface Post :
```typescript
// features/posts/post.model.ts
export interface Post {
  id: number;
  title: string;
  content: string;
  excerpt: string;
  author: string;
  date: Date;
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
  private posts = signal<Post[]>([]);
  readonly allPosts = this.posts.asReadonly();
  
  constructor(private http: HttpClient) {}

  loadPosts() {
    return this.http.get<Post[]>('/api/posts').pipe(
      tap(posts => this.posts.set(posts))
    );
  }

  getPost(id: number) {
    return this.http.get<Post>(`/api/posts/${id}`);
  }

  createPost(post: Omit<Post, 'id'>) {
    return this.http.post<Post>('/api/posts', post).pipe(
      tap(newPost => {
        this.posts.update(posts => [...posts, newPost]);
      })
    );
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
