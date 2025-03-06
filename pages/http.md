---
layout: new-section
routeAlias: 'http-client'
---

# HTTP dans Angular

---

## Commandes CLI pour HTTP - Partie 1

```bash
# Générer un service HTTP
ng generate service core/services/api

# Générer un intercepteur HTTP
ng generate interceptor core/interceptors/auth

# Générer un service avec mock
ng generate service core/services/api --mock
```

---

## Commandes CLI pour HTTP - Partie 2

```bash
# Générer un intercepteur avec transformation
ng generate interceptor core/interceptors/transform --transform

# Générer un service HTTP avec cache
ng generate service core/services/cached-api --cache
```

---

## Commandes CLI pour HTTP - Partie 3

```bash
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

```typescript
// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(
      withInterceptors([authInterceptor]), // Intercepteurs pour les requêtes (jwt/token)
      withFetch(), // Nouvelle API Fetch
      withJsonpSupport() // Support JSONP
    )
  ]
}
```

---

## Service HTTP avec Signals - Partie 1

```typescript
@Injectable({
  providedIn: 'root'
})
export class ApiService {
  private http = inject(HttpClient)
  private baseUrl = 'https://api.example.com'
  
  // États avec Signals
  private loading = signal(false)
  private error = signal<string | null>(null)
  
  // Getters publics en lecture seule
  readonly isLoading = this.loading.asReadonly()
  readonly currentError = this.error.asReadonly()
}
```

---

## Service HTTP avec Signals - Partie 2

```typescript
export class ApiService {
  getData<T>(endpoint: string) {
    this.loading.set(true)
    this.error.set(null)
    
    return this.http.get<T>(`${this.baseUrl}/${endpoint}`).pipe(
      catchError(err => {
        this.error.set(err.message)
        return EMPTY
      }),
      finalize(() => this.loading.set(false))
    )
  }
}
```

---

## Intercepteurs modernes - Partie 1

```typescript
// auth.interceptor.ts
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authToken = localStorage.getItem('token')
  
  if (authToken) {
    const authReq = req.clone({
      headers: req.headers.set('Authorization', `Bearer ${authToken}`)
    })
    
    return next(authReq)
  }
  
  return next(req)
}
```

---

## Intercepteurs modernes - Partie 2

```typescript
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  return next(req).pipe(
    catchError(error => {
      if (error.status === 401) {
        // Redirection vers login
        inject(Router).navigate(['/login'])
      }
      return throwError(() => error)
    })
  )
}
```

---

## Gestion des erreurs HTTP - Partie 1

```typescript
@Injectable({
  providedIn: 'root'
})
export class ErrorHandlingService {
  handleHttpError(error: HttpErrorResponse): Observable<never> {
    let errorMessage = 'Une erreur est survenue'
    
    if (error.error instanceof ErrorEvent) {
      // Erreur côté client
      errorMessage = error.error.message
    }
    
    return throwError(() => new Error(errorMessage))
  }
}
```

---

## Gestion des erreurs HTTP - Partie 2

```typescript
export class ErrorHandlingService {
  handleHttpError(error: HttpErrorResponse): Observable<never> {
    let errorMessage = 'Une erreur est survenue'
    
    // Erreur côté serveur
    switch (error.status) {
      case 404:
        errorMessage = 'Ressource non trouvée'
        break
      case 403:
        errorMessage = 'Accès non autorisé'
        break
      case 500:
        errorMessage = 'Erreur serveur'
        break
    }
    
    // Notification à l'utilisateur
    inject(NotificationService).show(errorMessage, 'error')
    
    return throwError(() => new Error(errorMessage))
  }
}
```

---

## Requêtes parallèles - Partie 1

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
```

---

## Requêtes parallèles - Partie 2

```typescript
export class UserDataComponent {
  private http = inject(HttpClient)
  
  data = toSignal(
    forkJoin({
      user: this.http.get<User>('/api/user'),
      posts: this.http.get<Post[]>('/api/posts'),
      comments: this.http.get<Comment[]>('/api/comments')
    }).pipe(
      catchError(error => {
        console.error('Erreur de chargement:', error)
        return of(null)
      })
    ),
    { initialValue: null }
  )
}
```

---

## Upload de fichiers - Partie 1

```typescript
@Injectable({
  providedIn: 'root'
})
export class FileUploadService {
  private http = inject(HttpClient)
  
  uploadFile(file: File) {
    const formData = new FormData()
    formData.append('file', file)
    
    return this.http.post('/api/upload', formData, {
      reportProgress: true,
      observe: 'events'
    })
  }
}
```

---

## Upload de fichiers - Partie 2

```typescript
export class FileUploadService {
  uploadFile(file: File) {
    return this.http.post('/api/upload', formData, {
      reportProgress: true,
      observe: 'events'
    }).pipe(
      map(event => {
        switch (event.type) {
          case HttpEventType.UploadProgress:
            const progress = event.total
              ? Math.round(100 * event.loaded / event.total)
              : 0
            return { type: 'progress', progress }
          case HttpEventType.Response:
            return { type: 'complete', data: event.body }
          default:
            return { type: 'other' }
        }
      })
    )
  }
}
```

---

## Upload de fichiers - Partie 3

```typescript
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
  private uploadService = inject(FileUploadService)
  uploadProgress = signal(0)
  
  onFileSelected(event: Event) {
    const file = (event.target as HTMLInputElement).files?.[0]
    if (file) {
      this.uploadService.uploadFile(file).subscribe(result => {
        if (result.type === 'progress') {
          this.uploadProgress.set(result.progress)
        }
      })
    }
  }
}
```

---

## Cache HTTP avec Signals - Partie 1

```typescript
@Injectable({
  providedIn: 'root'
})
export class CacheService {
  private cache = signal<Map<string, any>>(new Map())
  private http = inject(HttpClient)
  
  getData<T>(url: string, options: { ttl?: number } = {}) {
    const cached = this.cache().get(url)
    
    if (cached && (!options.ttl || Date.now() - cached.timestamp < options.ttl)) {
      return of(cached.data)
    }
    
    return this.http.get<T>(url)
  }
}
```

---

## Cache HTTP avec Signals - Partie 2

```typescript
export class CacheService {
  getData<T>(url: string, options: { ttl?: number } = {}) {
    return this.http.get<T>(url).pipe(
      tap(data => {
        this.cache.update(cache => {
          cache.set(url, {
            data,
            timestamp: Date.now()
          })
          return cache
        })
      })
    )
  }
  
  clearCache() {
    this.cache.set(new Map())
  }
}
```

---
layout: exercices
routeAlias: 'exercice-blog-http'
---

## Mini-Blog : Simulation d'API REST

---

### Service API

```typescript
// services/api.service.ts
@Injectable({ providedIn: 'root' })
export class ApiService {
  private http = inject(HttpClient)
  private baseUrl = 'https://api.example.com' // À remplacer par votre API
  
  // Simulation de délai réseau
  private delay(ms: number) {
    return new Promise(resolve => setTimeout(resolve, ms))
  }
  
  // GET /api/posts
  getPosts(): Observable<Post[]> {
    return of(MOCK_POSTS).pipe(
      delay(500) // Simulation de latence réseau
    )
  }
  
  // GET /api/posts/:id
  getPost(id: number): Observable<Post> {
    return of(MOCK_POSTS.find(p => p.id === id)).pipe(
      delay(300),
      map(post => {
        if (!post) throw new Error('Post not found')
        return post
      })
    )
  }
  
  // POST /api/posts
  createPost(post: Omit<Post, 'id' | 'createdAt'>): Observable<Post> {
    const newPost: Post = {
      ...post,
      id: Math.max(...MOCK_POSTS.map(p => p.id)) + 1,
      createdAt: new Date()
    }
    
    MOCK_POSTS.push(newPost)
    
    return of(newPost).pipe(delay(500))
  }
  
  // PUT /api/posts/:id
  updatePost(id: number, updates: Partial<Post>): Observable<Post> {
    return of(MOCK_POSTS.find(p => p.id === id)).pipe(
      delay(300),
      map(post => {
        if (!post) throw new Error('Post not found')
        Object.assign(post, updates)
        return post
      })
    )
  }
  
  // DELETE /api/posts/:id
  deletePost(id: number): Observable<void> {
    const index = MOCK_POSTS.findIndex(p => p.id === id)
    if (index > -1) {
      MOCK_POSTS.splice(index, 1)
    }
    
    return of(void 0).pipe(delay(300))
  }
  
  // POST /api/login
  login(credentials: { email: string, password: string }): Observable<{ token: string }> {
    return of(credentials).pipe(
      delay(500),
      map(({ email, password }) => {
        if (email === 'admin@blog.com' && password === 'admin123') {
          return { token: 'fake-jwt-token' }
        }
        throw new Error('Invalid credentials')
      })
    )
  }
}
```

---

### Intercepteur d'authentification

```typescript
// interceptors/auth.interceptor.ts
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = localStorage.getItem('token')
  
  if (token) {
    req = req.clone({
      setHeaders: {
        Authorization: `Bearer ${token}`
      }
    })
  }
  
  return next(req).pipe(
    catchError(error => {
      if (error.status === 401) {
        localStorage.removeItem('token')
        inject(Router).navigate(['/login'])
      }
      return throwError(() => error)
    })
  )
}
```

---

### Service d'authentification avec HTTP

```typescript
// services/auth.service.ts
@Injectable({ providedIn: 'root' })
export class AuthService {
  private api = inject(ApiService)
  private isAuthenticatedSubject = signal(false)
  
  readonly isAuthenticated = this.isAuthenticatedSubject.asReadonly()
  
  constructor() {
    // Vérifier le token au démarrage
    this.isAuthenticatedSubject.set(!!localStorage.getItem('token'))
  }
  
  login(email: string, password: string): Observable<boolean> {
    return this.api.login({ email, password }).pipe(
      map(response => {
        localStorage.setItem('token', response.token)
        this.isAuthenticatedSubject.set(true)
        return true
      }),
      catchError(error => {
        console.error('Erreur de connexion:', error)
        return of(false)
      })
    )
  }
  
  logout() {
    localStorage.removeItem('token')
    this.isAuthenticatedSubject.set(false)
  }
}
```

---

### Store avec appels HTTP

```typescript
// stores/post.store.ts
@Injectable({ providedIn: 'root' })
export class PostStore {
  private api = inject(ApiService)
  
  // État
  private _posts = signal<Post[]>([])
  private _loading = signal(false)
  private _error = signal<string | null>(null)
  
  // Sélecteurs
  readonly posts = this._posts.asReadonly()
  readonly loading = this._loading.asReadonly()
  readonly error = this._error.asReadonly()
  
  // Actions
  loadPosts() {
    this._loading.set(true)
    this._error.set(null)
    
    this.api.getPosts().pipe(
      finalize(() => this._loading.set(false))
    ).subscribe({
      next: (posts) => this._posts.set(posts),
      error: (err) => this._error.set('Erreur lors du chargement des articles')
    })
  }
  
  createPost(post: Omit<Post, 'id' | 'createdAt'>) {
    this._loading.set(true)
    this._error.set(null)
    
    this.api.createPost(post).pipe(
      finalize(() => this._loading.set(false))
    ).subscribe({
      next: (newPost) => this._posts.update(posts => [...posts, newPost]),
      error: (err) => this._error.set('Erreur lors de la création de l\'article')
    })
  }
  
  updatePost(id: number, updates: Partial<Post>) {
    this._loading.set(true)
    this._error.set(null)
    
    this.api.updatePost(id, updates).pipe(
      finalize(() => this._loading.set(false))
    ).subscribe({
      next: (updatedPost) => {
        this._posts.update(posts =>
          posts.map(post =>
            post.id === id ? updatedPost : post
          )
        )
      },
      error: (err) => this._error.set('Erreur lors de la mise à jour de l\'article')
    })
  }
  
  deletePost(id: number) {
    this._loading.set(true)
    this._error.set(null)
    
    this.api.deletePost(id).pipe(
      finalize(() => this._loading.set(false))
    ).subscribe({
      next: () => {
        this._posts.update(posts =>
          posts.filter(post => post.id !== id)
        )
      },
      error: (err) => this._error.set('Erreur lors de la suppression de l\'article')
    })
  }
}
```

---

Cette implémentation simule une API REST complète pour notre mini-blog, avec :

- Gestion des requêtes HTTP
- Simulation de latence réseau
- Gestion des erreurs
- Intercepteur d'authentification
- Store centralisé avec état de chargement et erreurs
