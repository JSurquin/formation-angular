---
layout: new-section
routeAlias: 'dependency-injection'
---

# Injection de Dépendances

---

## Qu'est-ce que l'injection de dépendances ?

- Un pattern de conception logicielle fondamental
- Permet de gérer les dépendances entre composants
- Rend le code plus modulaire et testable

### Avantages clés
- Découplage du code
- Réutilisabilité accrue
- Facilite les tests unitaires
- Maintenance simplifiée

---

## Injection de base

```typescript
// Service injectable basique
@Injectable({
  providedIn: 'root'
})
export class UserService {
  constructor(private http: HttpClient) {}

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users');
  }
}

// Utilisation dans un composant
@Component({
  selector: 'app-users'
})
export class UsersComponent {
  constructor(private userService: UserService) {}
}
```

---

## Commandes CLI pour l'Injection de Dépendances

```bash
# Générer un service injectable global
ng generate service core/services/config --providedIn root

# Générer un service avec un scope spécifique
ng generate service features/admin/services/admin --flat

# Générer une interface pour un token
ng generate interface core/interfaces/config-token

# Générer un guard (protection de route)
ng generate guard core/guards/auth

# Générer un resolver
ng generate resolver features/product/product-data

# Générer un intercepteur HTTP
ng generate interceptor core/interceptors/auth

# Générer un service avec des tests
ng generate service shared/services/cache --spec

# Générer un service avec une interface
ng generate service core/services/api --type interface
```

---

## Hiérarchie d'injection

```typescript
// Niveau composant
@Component({
  selector: 'app-feature',
  providers: [FeatureService] // Scope limité à ce composant
})
export class FeatureComponent {
  constructor(private featureService: FeatureService) {}
}

// Niveau module
@NgModule({
  providers: [
    GlobalService,
    {
      provide: API_URL,
      useValue: 'https://api.example.com'
    }
  ]
})
export class AppModule {}
```

---

## Configuration des providers

```typescript
// Token d'injection personnalisé
export interface AppConfig {
  apiUrl: string;
  theme: 'light' | 'dark';
}

export const APP_CONFIG = new InjectionToken<AppConfig>('app.config');

// Configuration des providers
export const appConfig: ApplicationConfig = {
  providers: [
    {
      provide: APP_CONFIG,
      useValue: {
        apiUrl: 'https://api.example.com',
        theme: 'light'
      }
    },
    {
      provide: ErrorHandler,
      useClass: CustomErrorHandler
    }
  ]
};
```

---

## Services avec dépendances avancées

```typescript
@Injectable({
  providedIn: 'root'
})
export class CacheService {
  private cache = new Map<string, any>();

  constructor(
    @Optional() @SkipSelf() parentCache: CacheService,
    @Inject(CACHE_SIZE) private maxSize: number
  ) {
    if (parentCache) {
      this.cache = new Map(parentCache.cache);
    }
  }

  set(key: string, value: any): void {
    if (this.cache.size >= this.maxSize) {
      const firstKey = this.cache.keys().next().value;
      this.cache.delete(firstKey);
    }
    this.cache.set(key, value);
  }
}
```

---
layout: new-section
---

# Injection Moderne avec inject()

---

## Introduction à inject()

```typescript
// Approche classique
@Component({
  template: `...`
})
class OldComponent {
  constructor(
    private userService: UserService,
    private router: Router,
    @Inject(APP_CONFIG) private config: AppConfig
  ) {}
}

// Approche moderne avec inject()
@Component({
  template: `...`
})
class ModernComponent {
  private userService = inject(UserService)
  private router = inject(Router)
  private config = inject(APP_CONFIG)
}
```

---

## Avantages de inject()

- Plus concis et lisible
- Utilisable en dehors du constructor
- Parfait pour les composants standalone
- Meilleure inférence de type
- Facilite l'utilisation avec les Signals

```typescript
@Component({
  standalone: true,
  template: `
    @if (user()) {
      <h1>Bienvenue {{ user().name }}</h1>
    }
  `
})
class WelcomeComponent {
  private auth = inject(AuthService)
  user = this.auth.currentUser

  logout() {
    inject(Router).navigate(['/login'])
  }
}
```

---

## Options avancées avec inject()

```typescript
@Component({
  template: `...`
})
class FeatureComponent {
  // Injection optionnelle
  private logger = inject(LoggerService, { optional: true })

  // Injection avec fallback
  private analytics = inject(AnalyticsService, {
    optional: true,
    self: true
  }) ?? new NoopAnalyticsService()

  // Injection au niveau parent
  private parentCache = inject(CacheService, {
    skipSelf: true,
    host: true
  })
}
```

---

## Injection dans les services modernes

```typescript
@Injectable({ providedIn: 'root' })
class ModernService {
  // Injection directe sans constructor
  private http = inject(HttpClient)
  private config = inject(APP_CONFIG)
  
  // Computed basé sur injection
  private apiUrl = computed(() => 
    `${this.config.baseUrl}/api`
  )

  getData() {
    return this.http.get(`${this.apiUrl()}/data`)
  }
}
```

---
layout: exercices
routeAlias: 'exercice-di-blog'
---

# Exercice : Injection de Dépendances dans le Blog

---

## Configuration de l'API

```typescript
// Configuration de l'API
export interface ApiConfig {
  baseUrl: string;
  version: string;
  timeout: number;
}

export const API_CONFIG = new InjectionToken<ApiConfig>('api.config');

// Provider global
export const appConfig: ApplicationConfig = {
  providers: [
    {
      provide: API_CONFIG,
      useValue: {
        baseUrl: 'https://api.blog.com',
        version: 'v1',
        timeout: 5000
      }
    }
  ]
};
```

---

## Service avec injection

```typescript
@Injectable({ providedIn: 'root' })
export class BlogApiService {
  private http = inject(HttpClient)
  private config = inject(API_CONFIG)
  private auth = inject(AuthService)
  
  private apiUrl = computed(() => 
    `${this.config.baseUrl}/${this.config.version}`
  )

  getPosts() {
    return this.http.get(`${this.apiUrl()}/posts`, {
      headers: {
        Authorization: `Bearer ${this.auth.getToken()}`
      }
    })
  }
}
```

