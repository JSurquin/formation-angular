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

