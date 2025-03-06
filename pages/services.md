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
routeAlias: 'exercice-blog-auth'
---

## Mini-Blog : Service d'Authentification

---

### Service de Base

```typescript
// services/auth.service.ts
@Injectable({ providedIn: 'root' })
export class AuthService {
  private readonly storageKey = 'blog_auth';
  private isAuthenticatedSignal = signal(false);
  private userSignal = signal<User | null>(null);
  
  // Getters publics en lecture seule
  readonly isAuthenticated = this.isAuthenticatedSignal.asReadonly();
  readonly currentUser = this.userSignal.asReadonly();
  
  constructor() {
    // Restaurer l'état d'authentification au démarrage
    this.checkAuthState();
  }
  
  private checkAuthState() {
    const stored = localStorage.getItem(this.storageKey);
    if (stored) {
      const auth = JSON.parse(stored);
      this.isAuthenticatedSignal.set(true);
      this.userSignal.set(auth.user);
    }
  }
  
  login(email: string, password: string): boolean {
    // Simulation d'authentification
    if (email === 'admin@blog.com' && password === 'admin') {
      const user: User = {
        id: 1,
        email,
        role: 'admin',
        name: 'Admin'
      };
      
      // Sauvegarder dans le localStorage
      localStorage.setItem(this.storageKey, JSON.stringify({ user }));
      
      // Mettre à jour les signals
      this.isAuthenticatedSignal.set(true);
      this.userSignal.set(user);
      
      return true;
    }
    
    return false;
  }
  
  logout() {
    localStorage.removeItem(this.storageKey);
    this.isAuthenticatedSignal.set(false);
    this.userSignal.set(null);
  }
  
  isAdmin(): boolean {
    return this.userSignal()?.role === 'admin';
  }
}

// types/user.ts
interface User {
  id: number;
  email: string;
  role: 'user' | 'admin';
  name: string;
}
```

---

### Utilisation dans un Composant

```typescript
// components/header.component.ts
@Component({
  selector: 'app-header',
  standalone: true,
  template: `
    <header class="bg-white shadow">
      <nav class="container mx-auto px-4 py-3">
        <div class="flex justify-between items-center">
          <a routerLink="/" class="text-xl font-bold">Mini Blog</a>
          
          <div class="flex items-center gap-4">
            @if (isAuthenticated()) {
              <span>{{ currentUser()?.name }}</span>
              <button 
                (click)="logout()"
                class="text-red-600 hover:text-red-800"
              >
                Déconnexion
              </button>
            } @else {
              <a 
                routerLink="/login"
                class="text-blue-600 hover:text-blue-800"
              >
                Connexion
              </a>
            }
          </div>
        </div>
      </nav>
    </header>
  `
})
export class HeaderComponent {
  private authService = inject(AuthService);
  private router = inject(Router);
  
  readonly isAuthenticated = this.authService.isAuthenticated;
  readonly currentUser = this.authService.currentUser;
  
  logout() {
    this.authService.logout();
    this.router.navigate(['/']);
  }
}
```

---

Cette implémentation :
- Utilise les Signals pour l'état d'authentification
- Persiste l'état dans le localStorage
- Gère les rôles utilisateur (admin/user)
- Fournit des getters en lecture seule
- S'intègre avec le système de routing et les guards
