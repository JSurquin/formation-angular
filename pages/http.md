---
layout: new-section
routeAlias: 'http-client'
---

# HTTP dans Angular

---

## Commandes CLI pour HTTP

```bash
# Générer un service HTTP
ng generate service core/services/api

# Générer un intercepteur HTTP
ng generate interceptor core/interceptors/auth

# Générer un service avec mock
ng generate service core/services/api --mock

# Générer un intercepteur avec transformation
ng generate interceptor core/interceptors/transform --transform

# Générer un service HTTP avec cache
ng generate service core/services/cached-api --cache

# Générer un intercepteur de gestion d'erreurs
ng generate interceptor core/interceptors/error-handler

# Générer un service avec retry policy
ng generate service core/services/resilient-api --retry

# Générer un service avec pagination
ng generate service features/products/product-api --paginated
```

---

# HTTP et Communication

---

## Configuration moderne du HttpClient

```typescript {1-3|4-6|7-9}
// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(
      withInterceptors([authInterceptor]), // Intercepteurs pour les requêtes (jwt/token)
      withFetch(), // Nouvelle API Fetch
      withJsonpSupport() // Support JSONP
    )
  ]
};
```

---

## Service HTTP avec Signals

```typescript {1-3|4-6|7-9|10-12|13-15|16-20}
@Injectable({
  providedIn: 'root'
})
export class ApiService {
  private http = inject(HttpClient);
  private baseUrl = 'https://api.example.com';
  
  // États avec Signals
  private loading = signal(false);
  private error = signal<string | null>(null);
  
  // Getters publics en lecture seule
  readonly isLoading = this.loading.asReadonly();
  readonly currentError = this.error.asReadonly();
  
  getData<T>(endpoint: string) {
    this.loading.set(true);
    this.error.set(null);
    
    return this.http.get<T>(`${this.baseUrl}/${endpoint}`).pipe(
      catchError(err => {
        this.error.set(err.message);
        return EMPTY;
      }),
      finalize(() => this.loading.set(false))
    );
  }
}
```

---

## Intercepteurs modernes

```typescript {1-3|4-6|7-9|10-12|13-15|16-18}
// auth.interceptor.ts
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authToken = localStorage.getItem('token');
  
  if (authToken) {
    const authReq = req.clone({
      headers: req.headers.set('Authorization', `Bearer ${authToken}`)
    });
    
    return next(authReq).pipe(
      catchError(error => {
        if (error.status === 401) {
          // Redirection vers login
          inject(Router).navigate(['/login']);
        }
        return throwError(() => error);
      })
    );
  }
  
  return next(req);
};
```

---

## Gestion des erreurs HTTP

```typescript {1-3|4-6|7-9|10-12|13-15|16-18|19-21}
@Injectable({
  providedIn: 'root'
})
export class ErrorHandlingService {
  handleHttpError(error: HttpErrorResponse): Observable<never> {
    let errorMessage = 'Une erreur est survenue';
    
    if (error.error instanceof ErrorEvent) {
      // Erreur côté client
      errorMessage = error.error.message;
    } else {
      // Erreur côté serveur
      switch (error.status) {
        case 404:
          errorMessage = 'Ressource non trouvée';
          break;
        case 403:
          errorMessage = 'Accès non autorisé';
          break;
        case 500:
          errorMessage = 'Erreur serveur';
          break;
      }
    }
    
    // Notification à l'utilisateur
    inject(NotificationService).show(errorMessage, 'error');
    
    return throwError(() => new Error(errorMessage));
  }
}
```

---

## Requêtes parallèles

```typescript {1-3|4-6|7-9|10-12|13-15|16-18|19-21}
@Component({
  template: `
    @if (data(); as result) {
      <div>
        <h2>Utilisateur: {{ result.user.name }}</h2>
        <p>Posts: {{ result.posts.length }}</p>
        <p>Commentaires: {{ result.comments.length }}</p>
      </div>
    }
  `
})
export class UserDataComponent {
  private http = inject(HttpClient);
  
  data = toSignal(
    forkJoin({
      user: this.http.get<User>('/api/user'),
      posts: this.http.get<Post[]>('/api/posts'),
      comments: this.http.get<Comment[]>('/api/comments')
    }).pipe(
      catchError(error => {
        console.error('Erreur de chargement:', error);
        return of(null);
      })
    ),
    { initialValue: null }
  );
}
```

---

## Upload de fichiers

```typescript {1-3|4-6|7-9|10-12|13-15|16-18|19-21|22-24}
@Injectable({
  providedIn: 'root'
})
export class FileUploadService {
  private http = inject(HttpClient);
  
  uploadFile(file: File) {
    const formData = new FormData();
    formData.append('file', file);
    
    return this.http.post('/api/upload', formData, {
      reportProgress: true,
      observe: 'events'
    }).pipe(
      map(event => {
        switch (event.type) {
          case HttpEventType.UploadProgress:
            const progress = event.total
              ? Math.round(100 * event.loaded / event.total)
              : 0;
            return { type: 'progress', progress };
          case HttpEventType.Response:
            return { type: 'complete', data: event.body };
          default:
            return { type: 'other' };
        }
      })
    );
  }
}

@Component({
  template: `
    <input type="file" (change)="onFileSelected($event)">
    @if (uploadProgress() > 0) {
      <progress [value]="uploadProgress()" max="100">
        {{ uploadProgress() }}%
      </progress>
    }
  `
})
export class UploadComponent {
  private uploadService = inject(FileUploadService);
  uploadProgress = signal(0);
  
  onFileSelected(event: Event) {
    const file = (event.target as HTMLInputElement).files?.[0];
    if (file) {
      this.uploadService.uploadFile(file).subscribe(result => {
        if (result.type === 'progress') {
          this.uploadProgress.set(result.progress);
        }
      });
    }
  }
}
```

---

## Cache HTTP avec Signals

```typescript {1-3|4-6|7-9|10-12|13-15|16-18|19-21|22-24}
@Injectable({
  providedIn: 'root'
})
export class CacheService {
  private cache = signal<Map<string, any>>(new Map());
  private http = inject(HttpClient);
  
  getData<T>(url: string, options: { ttl?: number } = {}) {
    const cached = this.cache().get(url);
    
    if (cached && (!options.ttl || Date.now() - cached.timestamp < options.ttl)) {
      return of(cached.data);
    }
    
    return this.http.get<T>(url).pipe(
      tap(data => {
        this.cache.update(cache => {
          cache.set(url, {
            data,
            timestamp: Date.now()
          });
          return cache;
        });
      })
    );
  }
  
  clearCache() {
    this.cache.set(new Map());
  }
}
```

---
layout: exercices
routeAlias: 'exercice-http-blog'
---

## Exercice : Implémentation HTTP pour le Mini-Blog

### Objectif
Mettre en place les appels HTTP pour notre mini-blog en utilisant les concepts vus précédemment.

---

### 1. Configuration du service API

```typescript
@Injectable({ providedIn: 'root' })
export class BlogApiService {
  constructor(private http: HttpClient) {}

  // Récupération paginée des articles
  getPosts(page = 1, limit = 10): Observable<PaginatedResponse<Post>> {
    return this.http.get<PaginatedResponse<Post>>('/api/posts', {
      params: { page: page.toString(), limit: limit.toString() }
    });
  }

  // Création d'un article avec upload d'image
  createPost(post: CreatePostDto, image?: File): Observable<Post> {
    const formData = new FormData();
    formData.append('title', post.title);
    formData.append('content', post.content);
    if (image) {
      formData.append('image', image);
    }

    return this.http.post<Post>('/api/posts', formData);
  }
}
```

---

### 2. Gestion des erreurs

```typescript
@Injectable()
export class BlogErrorInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    return next.handle(req).pipe(
      catchError((error: HttpErrorResponse) => {
        let errorMessage = 'Une erreur est survenue';
        
        if (error.error instanceof ErrorEvent) {
          // Erreur côté client
          errorMessage = error.error.message;
        } else {
          // Erreur côté serveur
          switch (error.status) {
            case 404:
              errorMessage = 'Article non trouvé';
              break;
            case 403:
              errorMessage = 'Accès non autorisé';
              break;
            // ... autres cas
          }
        }
        
        return throwError(() => new Error(errorMessage));
      })
    );
  }
}
```

---

### 3. Cache des articles

```typescript
@Injectable({ providedIn: 'root' })
export class PostCacheService {
  private cache = new Map<string, Post>();
  private cacheTime = 5 * 60 * 1000; // 5 minutes

  getPost(id: number): Observable<Post> {
    const cached = this.cache.get(id.toString());
    if (cached) {
      return of(cached);
    }

    return this.http.get<Post>(`/api/posts/${id}`).pipe(
      tap(post => {
        this.cache.set(id.toString(), post);
        setTimeout(() => {
          this.cache.delete(id.toString());
        }, this.cacheTime);
      })
    );
  }
}
```

Cette section d'exercice permet de mettre en pratique :
- Les appels HTTP avec gestion de paramètres
- L'upload de fichiers
- La gestion d'erreurs personnalisée
- La mise en cache des données
- L'utilisation des intercepteurs
``` 