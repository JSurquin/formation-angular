---
layout: new-section
routeAlias: 'http-client'
---

# HTTP et Communication

---

## Le HttpClient

# HTTP Client dans Angular 18/19

## Configuration moderne du HttpClient

```typescript
// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(
      withInterceptors([authInterceptor]),
      withFetch(), // Nouvelle API Fetch
      withJsonpSupport()
    )
  ]
};
```

---

## Service HTTP avec Signals

```typescript
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

```typescript
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

```typescript
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

```typescript
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

```typescript
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

```typescript
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

# Exercice : API REST complète

Créez un service complet pour gérer une API REST :

```typescript
interface CrudOperations<T> {
  getAll(): Observable<T[]>;
  getById(id: string): Observable<T>;
  create(item: Omit<T, 'id'>): Observable<T>;
  update(id: string, item: Partial<T>): Observable<T>;
  delete(id: string): Observable<void>;
}

@Injectable({
  providedIn: 'root'
})
export class ApiService<T extends { id: string }> implements CrudOperations<T> {
  constructor(
    private http = inject(HttpClient),
    private baseUrl: string
  ) {}
  
  getAll(): Observable<T[]> {
    return this.http.get<T[]>(this.baseUrl).pipe(
      catchError(this.handleError)
    );
  }
  
  getById(id: string): Observable<T> {
    return this.http.get<T>(`${this.baseUrl}/${id}`).pipe(
      catchError(this.handleError)
    );
  }
  
  create(item: Omit<T, 'id'>): Observable<T> {
    return this.http.post<T>(this.baseUrl, item).pipe(
      catchError(this.handleError)
    );
  }
  
  update(id: string, item: Partial<T>): Observable<T> {
    return this.http.patch<T>(`${this.baseUrl}/${id}`, item).pipe(
      catchError(this.handleError)
    );
  }
  
  delete(id: string): Observable<void> {
    return this.http.delete<void>(`${this.baseUrl}/${id}`).pipe(
      catchError(this.handleError)
    );
  }
  
  private handleError(error: HttpErrorResponse) {
    console.error('Une erreur est survenue:', error);
    return throwError(() => 
      new Error('Erreur lors de la communication avec le serveur')
    );
  }
}
```

Cet exercice vous permettra de pratiquer :
- La création d'un service générique
- La gestion des erreurs HTTP
- L'utilisation des types TypeScript
- L'implémentation des opérations CRUD
- L'injection de dépendances 