---
layout: new-section
routeAlias: 'dependency-injection'
---

# Injection de Dépendances

---

## Principes fondamentaux

- **Qu'est-ce que l'injection de dépendances ?**
  - Pattern de conception logicielle
  - Gestion des dépendances entre composants
  - Facilite les tests et la maintenance

- **Avantages**
  - Découplage du code
  - Réutilisabilité
  - Testabilité améliorée

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

## Création d'un service injectable

```typescript
// user.service.ts
@Injectable({
  providedIn: 'root' // Service singleton au niveau application
})
export class UserService {
  private users: User[] = [];

  constructor(private http: HttpClient) {}

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users');
  }

  addUser(user: User): Observable<User> {
    return this.http.post<User>('/api/users', user);
  }
}
```

---

## Hiérarchie d'injection

```typescript
// Component-level provider
@Component({
  selector: 'app-feature',
  providers: [FeatureService] // Scope limité à ce composant
})
export class FeatureComponent {
  constructor(private featureService: FeatureService) {}
}

// Module-level provider
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

## Tokens d'injection et providers

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
    },
    {
      provide: LoggerService,
      useFactory: (config: AppConfig) => {
        return new LoggerService(config.apiUrl);
      },
      deps: [APP_CONFIG]
    }
  ]
};
```

---

## Utilisation avancée

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

  get(key: string): any {
    return this.cache.get(key);
  }
}
```

---

## Injection moderne avec inject()

```typescript
// Avant (constructor injection)
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

// Après (fonction inject)
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

- Plus concis
- Utilisable en dehors du constructor
- Parfait pour les composants standalone
- Meilleure inférence de type

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
  // Injection et Signal en une ligne
  private auth = inject(AuthService)
  user = this.auth.currentUser

  // Injection dans une méthode
  logout() {
    inject(Router).navigate(['/login'])
  }
}
```

---

## Injection conditionnelle avec inject()

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

## Injection dans les services

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

