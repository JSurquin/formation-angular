---
layout: new-section
routeAlias: 'rxjs-observables'
---

# RxJS et Observables

---

## Concepts de base

Bon déjà c'est quoi un Observable ?

Un Observable est un objet qui représente une séquence de valeurs, émises à des moments différents.

```typescript
const apiCall$ = new Observable(observer => {
  fetch('https://api.example.com/data')
    .then(response => response.json())
    .then(data => observer.next(data))
    .catch(error => observer.error(error))
    .finally(() => observer.complete())
})
```

---

## Observer - Partie 1

Un observer est un objet qui écoute les événements d'un Observable.

```typescript
// Observable simple
const numbers$ = of(1, 2, 3, 4, 5)
```

---

## Observer - Partie 2

```typescript
// Observer
numbers$.subscribe({
  next: value => console.log(value),
  error: err => console.error(err),
  complete: () => console.log('Terminé')
})
```

---

## Types de Subjects - Partie 1

```typescript
// Subject basique
const subject = new Subject<string>()
subject.subscribe(value => console.log('A:', value))
subject.subscribe(value => console.log('B:', value))
subject.next('Hello') // A: Hello, B: Hello
```

---

## Types de Subjects - Partie 2

```typescript
// BehaviorSubject - Garde la dernière valeur
const behavior = new BehaviorSubject<number>(0)
behavior.subscribe(value => console.log('Valeur actuelle:', value))
console.log('Valeur stockée:', behavior.value)
```

---

## Types de Subjects - Partie 3

```typescript
// ReplaySubject - Rejoue X valeurs
const replay = new ReplaySubject<string>(2)
replay.next('Un')
replay.next('Deux')
replay.next('Trois')
replay.subscribe(value => console.log(value)) // Deux, Trois
```

---

## Opérateurs essentiels - Filtrage - Partie 1

```typescript
const source$ = interval(1000)

// filter - Garde uniquement les valeurs qui passent le prédicat
source$.pipe(
  filter(n => n % 2 === 0)
) // 0, 2, 4, 6...
```

---

## Opérateurs essentiels - Filtrage - Partie 2

```typescript
// take - Prend X valeurs puis complète
source$.pipe(
  take(3)
) // 0, 1, 2, complete

// takeUntil - Émet jusqu'à ce qu'un autre Observable émette
const stop$ = timer(5000)
source$.pipe(
  takeUntil(stop$)
) // Émet pendant 5 secondes
```

---

## Opérateurs essentiels - Filtrage - Partie 3

```typescript
// distinctUntilChanged - Ignore les doublons consécutifs
of(1, 1, 2, 2, 3, 3).pipe(
  distinctUntilChanged()
) // 1, 2, 3
```

---

## Opérateurs essentiels - Transformation - Partie 1

```typescript
// map - Transforme chaque valeur
source$.pipe(
  map(n => n * 2)
) // 0, 2, 4, 6...
```

---

## Opérateurs essentiels - Transformation - Partie 2

```typescript
// switchMap - Annule l'Observable précédent
searchTerm$.pipe(
  switchMap(term => 
    this.http.get(`/api/search?q=${term}`)
  )
)
```

---

## Opérateurs essentiels - Transformation - Partie 3

```typescript
// mergeMap - Fusionne tous les Observables
userIds$.pipe(
  mergeMap(id => 
    this.http.get(`/api/user/${id}`)
  )
)
```

---

## Opérateurs essentiels - Transformation - Partie 4

```typescript
// concatMap - Attend que chaque Observable soit complété
uploads$.pipe(
  concatMap(file => 
    this.uploadFile(file)
  )
)
```

---

## Opérateurs essentiels - Combinaison - Partie 1

```typescript
// combineLatest - Combine les dernières valeurs
combineLatest({
  user: userProfile$,
  preferences: userPrefs$,
  theme: themeSettings$
}).subscribe(({ user, preferences, theme }) => {
  console.log('État complet:', { user, preferences, theme })
})
```

---

## Opérateurs essentiels - Combinaison - Partie 2

```typescript
// merge - Fusionne plusieurs Observables
merge(
  clicks$,
  keypresses$,
  touches$
).subscribe(event => {
  console.log('Interaction utilisateur:', event)
})
```

---

## Opérateurs essentiels - Combinaison - Partie 3

```typescript
// forkJoin - Attend que tous les Observables soient complétés
forkJoin({
  posts: this.http.get('/api/posts'),
  comments: this.http.get('/api/comments'),
  users: this.http.get('/api/users')
}).subscribe(data => {
  console.log('Toutes les données chargées:', data)
})
```

---

## Cas d'utilisation concrets - Partie 1

```typescript
@Component({
  template: `
    <input [ngModel]="searchTerm()" 
           (ngModelChange)="searchTerm$.next($event)">
    <div *ngFor="let result of results()">
      {{ result.name }}
    </div>
  `
})
```

---

## Cas d'utilisation concrets - Partie 2

```typescript
class SearchComponent {
  private searchTerm$ = new BehaviorSubject<string>('')
  
  results = toSignal(
    this.searchTerm$.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      filter(term => term.length >= 2),
      switchMap(term => this.searchService.search(term)),
      catchError(err => {
        console.error('Erreur de recherche:', err)
        return of([])
      })
    ),
    { initialValue: [] }
  )
}
```

---

## Gestion des websockets - Partie 1

```typescript
class WebSocketService {
  private wsSubject = new BehaviorSubject<WebSocket | null>(null)
  private messagesSubject = new Subject<any>()
  private reconnectAttempts = 0
  
  connect() {
    const ws = new WebSocket('ws://api.example.com')
    
    ws.addEventListener('message', event => {
      this.messagesSubject.next(JSON.parse(event.data))
    })
    
    ws.addEventListener('close', () => {
      this.reconnect()
    })
    
    this.wsSubject.next(ws)
  }
}
```

---

## Gestion des websockets - Partie 2

```typescript
class WebSocketService {
  messages$ = this.messagesSubject.asObservable().pipe(
    retry(3),
    share()
  )
  
  private reconnect() {
    if (this.reconnectAttempts < 3) {
      this.reconnectAttempts++
      timer(1000 * this.reconnectAttempts).pipe(
        take(1)
      ).subscribe(() => this.connect())
    }
  }
}
```

---

## Intégration avec Signals - Partie 1

```typescript
@Component({
  template: `
    <div>Données: {{ data() }}</div>
    <div>Statut: {{ status() }}</div>
  `
})
```

---

## Intégration avec Signals - Partie 2

```typescript
export class DataComponent {
  private dataService = inject(DataService)
  
  // Conversion d'Observable en Signal
  data = toSignal(
    this.dataService.getData().pipe(
      catchError(error => {
        console.error('Erreur:', error)
        return of(null)
      })
    ),
    { initialValue: null }
  )
  
  // Signal computed basé sur Observable
  status = computed(() => 
    this.data() ? 'Chargé' : 'Chargement...'
  )
}
```

---

## Opérateurs RxJS modernes - Partie 1

```typescript
@Injectable({
  providedIn: 'root'
})
export class SearchService {
  private http = inject(HttpClient)
  
  search(term: string): Observable<Result[]> {
    return of(term).pipe(
      // Attendre que l'utilisateur arrête de taper
      debounceTime(300),
      
      // Ignorer si le terme est le même
      distinctUntilChanged()
    )
  }
}
```

---

## Opérateurs RxJS modernes - Partie 2

```typescript
export class SearchService {
  search(term: string): Observable<Result[]> {
    return of(term).pipe(
      // Annuler la requête précédente
      switchMap(term => 
        this.http.get<Result[]>(`/api/search?q=${term}`).pipe(
          // Gérer les erreurs par requête
          catchError(error => {
            console.error('Erreur de recherche:', error)
            return of([])
          })
        )
      )
    )
  }
}
```

---

## Gestion des souscriptions - Partie 1

```typescript
@Component({
  template: `
    <input [ngModel]="searchTerm()"
           (ngModelChange)="searchTerm.set($event)">
           
    @if (results(); as items) {
      @for (item of items; track item.id) {
        <div>{{ item.name }}</div>
      }
    }
  `
})
```

---

## Gestion des souscriptions - Partie 2

```typescript
export class SearchComponent implements OnDestroy {
  private searchService = inject(SearchService)
  private destroy$ = new Subject<void>()
  
  searchTerm = signal('')
  
  // Conversion du signal en Observable
  private searchTerm$ = toObservable(this.searchTerm)
  
  results = toSignal(
    this.searchTerm$.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      switchMap(term => this.searchService.search(term)),
      takeUntil(this.destroy$)
    ),
    { initialValue: [] }
  )
  
  ngOnDestroy() {
    this.destroy$.next()
    this.destroy$.complete()
  }
}
```

---

## Combinaison d'Observables - Partie 1

```typescript
@Component({
  template: `
    <div>
      Utilisateur: {{ userData()?.name }}
      Préférences: {{ userData()?.preferences }}
      Notifications: {{ userData()?.notifications }}
    </div>
  `
})
```

---

## Combinaison d'Observables - Partie 2

```typescript
export class UserDashboardComponent {
  private userService = inject(UserService)
  private prefService = inject(PreferencesService)
  private notifService = inject(NotificationService)
  
  userData = toSignal(
    combineLatest({
      user: this.userService.getCurrentUser(),
      preferences: this.prefService.getPreferences(),
      notifications: this.notifService.getNotifications()
    }).pipe(
      map(({ user, preferences, notifications }) => ({
        name: user.name,
        preferences,
        notifications: notifications.length
      }))
    ),
    { initialValue: null }
  )
}
```

---

## Gestion des erreurs avancée - Partie 1

```typescript
@Injectable({
  providedIn: 'root'
})
export class ErrorHandlingService {
  private errorSubject = new Subject<Error>()
  
  error$ = this.errorSubject.asObservable().pipe(
    // Grouper les erreurs similaires
    groupBy(error => error.message),
    // Limiter les notifications
    mergeMap(group => group.pipe(
      debounceTime(1000),
      take(3)
    ))
  )
  
  handleError(error: Error) {
    this.errorSubject.next(error)
  }
}
```

---

## Gestion des erreurs avancée - Partie 2

```typescript
@Component({
  template: `
    @if (error()) {
      <div class="error-banner">
        {{ error() }}
      </div>
    }
  `
})
export class ErrorComponent {
  private errorService = inject(ErrorHandlingService)
  
  error = toSignal(
    this.errorService.error$.pipe(
      map(error => error.message)
    ),
    { initialValue: null }
  )
}
```

---

## WebSocket avec RxJS - Partie 1

```typescript
@Injectable({
  providedIn: 'root'
})
export class WebSocketService {
  private socket$ = new WebSocket('ws://example.com')
  private messagesSubject = new Subject<any>()
  
  messages$ = this.messagesSubject.asObservable().pipe(
    // Reconnexion automatique
    retryWhen(errors => errors.pipe(
      tap(error => console.error('WebSocket error:', error)),
      delay(1000)
    ))
  )
}
```

---

## WebSocket avec RxJS - Partie 2

```typescript
export class WebSocketService {
  messages$ = this.messagesSubject.asObservable().pipe(
    // Filtrage et transformation des messages
    filter(msg => msg.type === 'data'),
    map(msg => msg.payload),
    share()
  )
  
  constructor() {
    this.socket$.addEventListener('message', (event) => {
      this.messagesSubject.next(JSON.parse(event.data))
    })
  }
  
  send(message: any) {
    this.socket$.send(JSON.stringify(message))
  }
}
```

---
layout: exercices
routeAlias: 'exercice-search-realtime'
---

## Exercice : Recherche en temps réel pour le Mini-Blog

---

## Composant de recherche - Partie 1

```typescript
@Component({
  selector: 'app-post-search',
  template: `
    <div class="search-container">
      <input
        type="text"
        [ngModel]="searchTerm()"
        (ngModelChange)="searchTerm$.next($event)"
        placeholder="Rechercher des articles..."
        class="search-input"
      >

      @if (loading()) {
        <div class="loading-spinner">Chargement...</div>
      }

      @if (error()) {
        <div class="error-message">
          {{ error() }}
        </div>
      }
    </div>
  `
})
```

---

## Composant de recherche - Partie 2

```typescript
@Component({
  template: `
    <div class="search-results">
      @for (post of searchResults(); track post.id) {
        <div class="post-card">
          <h3>{{ post.title }}</h3>
          <p>{{ post.excerpt }}</p>
          <a [routerLink]="['/posts', post.id]">Lire la suite</a>
        </div>
      }
    </div>
  `
})
```

---

## Composant de recherche - Partie 3

```typescript
export class PostSearchComponent {
  private searchTerm$ = new BehaviorSubject<string>('')
  private loading = signal(false)
  private error = signal<string | null>(null)

  // État de la recherche avec Signals
  searchTerm = toSignal(this.searchTerm$)
  
  // Résultats de recherche
  searchResults = toSignal(
    this.searchTerm$.pipe(
      // Attendre que l'utilisateur arrête de taper
      debounceTime(300),
      // Éviter les recherches identiques
      distinctUntilChanged(),
      // Ignorer les termes trop courts
      filter(term => term.length >= 2),
      // Indiquer le chargement
      tap(() => this.loading.set(true)),
      // Annuler la recherche précédente si une nouvelle arrive
      switchMap(term => this.postService.searchPosts(term).pipe(
        // Gérer les erreurs pour chaque recherche
        catchError(err => {
          this.error.set('Erreur lors de la recherche')
          return of([])
        }),
        // Fin du chargement
        finalize(() => this.loading.set(false))
      ))
    ),
    { initialValue: [] }
  )

  constructor(private postService: PostService) {}
}
```

---

## Service de recherche - Partie 1

```typescript
@Injectable({ providedIn: 'root' })
export class PostService {
  searchPosts(term: string): Observable<Post[]> {
    return this.http.get<Post[]>('/api/posts/search', {
      params: { q: term }
    }).pipe(
      // Transformer les résultats
      map(posts => posts.map(post => ({
        ...post,
        excerpt: this.createExcerpt(post.content)
      })))
    )
  }
}
```

---

## Service de recherche - Partie 2

```typescript
export class PostService {
  private createExcerpt(content: string): string {
    return content.substring(0, 150) + '...'
  }

  private sortByRelevance(posts: Post[], term: string): Post[] {
    return [...posts].sort((a, b) => {
      const aScore = this.calculateRelevance(a, term)
      const bScore = this.calculateRelevance(b, term)
      return bScore - aScore
    })
  }

  private calculateRelevance(post: Post, term: string): number {
    const titleMatch = post.title.toLowerCase().includes(term.toLowerCase())
    const contentMatch = post.content.toLowerCase().includes(term.toLowerCase())
    return (titleMatch ? 2 : 0) + (contentMatch ? 1 : 0)
  }
}
```

---

## Filtres combinés - Partie 1

```typescript
@Component({
  selector: 'app-post-filters',
  template: `
    <div class="filters">
      <select [ngModel]="category()" (ngModelChange)="category$.next($event)">
        <option value="">Toutes les catégories</option>
        @for (cat of categories(); track cat) {
          <option [value]="cat">{{ cat }}</option>
        }
      </select>

      <select [ngModel]="sortBy()" (ngModelChange)="sortBy$.next($event)">
        <option value="date">Plus récents</option>
        <option value="title">Alphabétique</option>
        <option value="popularity">Popularité</option>
      </select>
    </div>
  `
})
```

---

## Filtres combinés - Partie 2

```typescript
@Component({
  template: `
    <select [ngModel]="author()" (ngModelChange)="author$.next($event)">
      <option value="">Tous les auteurs</option>
      @for (auth of authors(); track auth.id) {
        <option [value]="auth.id">{{ auth.name }}</option>
      }
    </select>
  `
})
export class PostFiltersComponent {
  // Sources des filtres
  private category$ = new BehaviorSubject<string>('')
  private sortBy$ = new BehaviorSubject<string>('date')
  private author$ = new BehaviorSubject<string>('')

  // Conversion en Signals
  category = toSignal(this.category$)
  sortBy = toSignal(this.sortBy$)
  author = toSignal(this.author$)
}
```

---

## Filtres combinés - Partie 3

```typescript
export class PostFiltersComponent {
  // Données des filtres
  categories = computed(() => 
    [...new Set(this.postStore.posts().map(p => p.category))]
  )
  
  authors = computed(() => 
    [...new Set(this.postStore.posts().map(p => p.author))]
  )

  // Combinaison des filtres
  filteredPosts = toSignal(
    combineLatest({
      posts: this.postStore.posts$,
      category: this.category$,
      sortBy: this.sortBy$,
      author: this.author$
    }).pipe(
      map(({ posts, category, sortBy, author }) => {
        let filtered = posts

        // Filtre par catégorie
        if (category) {
          filtered = filtered.filter(p => p.category === category)
        }

        // Filtre par auteur
        if (author) {
          filtered = filtered.filter(p => p.author.id === author)
        }

        return filtered
      })
    ),
    { initialValue: [] }
  )

  constructor(private postStore: PostStore) {}
}
```

---

## Filtres combinés - Partie 4

```typescript
export class PostFiltersComponent {
  filteredPosts = toSignal(
    combineLatest({
      posts: this.postStore.posts$,
      sortBy: this.sortBy$
    }).pipe(
      map(({ posts, sortBy }) => {
        // Tri
        return [...posts].sort((a, b) => {
          switch (sortBy) {
            case 'date':
              return new Date(b.date).getTime() - new Date(a.date).getTime()
            case 'title':
              return a.title.localeCompare(b.title)
            case 'popularity':
              return b.views - a.views
            default:
              return 0
          }
        })
      })
    ),
    { initialValue: [] }
  )

  constructor(private postStore: PostStore) {}
}