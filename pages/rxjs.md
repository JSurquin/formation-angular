---
layout: new-section
routeAlias: 'rxjs-observables'
---

# RxJS et Observables

---

## Concepts de base

Bon déjà c'est quoi un Observable ?

Un Observable est un objet qui représente une séquence de valeurs, émises à des moments différents.

Imaginons je fais un appel api , regardons du marble "testing" pour mieux comprendre :

```typescript
const apiCall$ = new Observable(observer => {
  fetch('https://api.example.com/data')
    .then(response => response.json())
    .then(data => observer.next(data))
    .catch(error => observer.error(error))
    .finally(() => observer.complete())
})
```

<!-- retoucher -->

donc ce flux : --1--2--3--4--5--

ce qui veut dire :

- 1 premiere étape : j'aurais donc une réponse de l'api
- 2 deuxième étape : j'aurais donc une autre réponse de l'api
- 3 troisième étape : j'aurais donc une autre réponse de l'api
- 4 quatrième étape : j'aurais donc une autre réponse de l'api
- 5 cinquième étape : j'aurais donc une autre réponse de l'api

---

## Observer

Un observer est un objet qui écoute les événements d'un Observable.

```typescript
// Observable simple
const numbers$ = of(1, 2, 3, 4, 5)

// Observer
numbers$.subscribe({
  next: value => console.log(value),
  error: err => console.error(err),
  complete: () => console.log('Terminé')
})
```

---

## Types de Subjects

```typescript
// Subject basique
const subject = new Subject<string>()
subject.subscribe(value => console.log('A:', value))
subject.subscribe(value => console.log('B:', value))
subject.next('Hello') // A: Hello, B: Hello

// BehaviorSubject - Garde la dernière valeur
const behavior = new BehaviorSubject<number>(0)
behavior.subscribe(value => console.log('Valeur actuelle:', value))
console.log('Valeur stockée:', behavior.value)

// ReplaySubject - Rejoue X valeurs
const replay = new ReplaySubject<string>(2)
replay.next('Un')
replay.next('Deux')
replay.next('Trois')
replay.subscribe(value => console.log(value)) // Deux, Trois
```

---

## Opérateurs essentiels - Filtrage

```typescript
const source$ = interval(1000)

// filter - Garde uniquement les valeurs qui passent le prédicat
source$.pipe(
  filter(n => n % 2 === 0)
) // 0, 2, 4, 6...

// take - Prend X valeurs puis complète
source$.pipe(
  take(3)
) // 0, 1, 2, complete

// takeUntil - Émet jusqu'à ce qu'un autre Observable émette
const stop$ = timer(5000)
source$.pipe(
  takeUntil(stop$)
) // Émet pendant 5 secondes

// distinctUntilChanged - Ignore les doublons consécutifs
of(1, 1, 2, 2, 3, 3).pipe(
  distinctUntilChanged()
) // 1, 2, 3
```

---

## Opérateurs essentiels - Transformation

```typescript
// map - Transforme chaque valeur
source$.pipe(
  map(n => n * 2)
) // 0, 2, 4, 6...

// switchMap - Annule l'Observable précédent
searchTerm$.pipe(
  switchMap(term => 
    this.http.get(`/api/search?q=${term}`)
  )
)

// mergeMap - Fusionne tous les Observables
userIds$.pipe(
  mergeMap(id => 
    this.http.get(`/api/user/${id}`)
  )
)

// concatMap - Attend que chaque Observable soit complété
uploads$.pipe(
  concatMap(file => 
    this.uploadFile(file)
  )
)
```

---

## Opérateurs essentiels - Combinaison

```typescript
// combineLatest - Combine les dernières valeurs
combineLatest({
  user: userProfile$,
  preferences: userPrefs$,
  theme: themeSettings$
}).subscribe(({ user, preferences, theme }) => {
  console.log('État complet:', { user, preferences, theme })
})

// merge - Fusionne plusieurs Observables
merge(
  clicks$,
  keypresses$,
  touches$
).subscribe(event => {
  console.log('Interaction utilisateur:', event)
})

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

## Cas d'utilisation concrets

### 1. Recherche en temps réel
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

### 2. Gestion des websockets avec reconnexion
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

## Introduction à RxJS

## Intégration avec Signals

```typescript
@Component({
  template: `
    <div>Données: {{ data() }}</div>
    <div>Statut: {{ status() }}</div>
  `
})
export class DataComponent {
  private dataService = inject(DataService);
  
  // Conversion d'Observable en Signal
  data = toSignal(
    this.dataService.getData().pipe(
      catchError(error => {
        console.error('Erreur:', error);
        return of(null);
      })
    ),
    { initialValue: null }
  );
  
  // Signal computed basé sur Observable
  status = computed(() => 
    this.data() ? 'Chargé' : 'Chargement...'
  );
}
```

---

## Opérateurs RxJS modernes

```typescript
@Injectable({
  providedIn: 'root'
})
export class SearchService {
  private http = inject(HttpClient);
  
  search(term: string): Observable<Result[]> {
    return of(term).pipe(
      // Attendre que l'utilisateur arrête de taper
      debounceTime(300),
      
      // Ignorer si le terme est le même
      distinctUntilChanged(),
      
      // Annuler la requête précédente
      switchMap(term => 
        this.http.get<Result[]>(`/api/search?q=${term}`).pipe(
          // Gérer les erreurs par requête
          catchError(error => {
            console.error('Erreur de recherche:', error);
            return of([]);
          })
        )
      )
    );
  }
}
```

---

## Gestion des souscriptions

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
export class SearchComponent implements OnDestroy {
  private searchService = inject(SearchService);
  private destroy$ = new Subject<void>();
  
  searchTerm = signal('');
  
  // Conversion du signal en Observable
  private searchTerm$ = toObservable(this.searchTerm);
  
  results = toSignal(
    this.searchTerm$.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      switchMap(term => this.searchService.search(term)),
      takeUntil(this.destroy$)
    ),
    { initialValue: [] }
  );
  
  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.complete();
  }
}
```

---

## Combinaison d'Observables

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
export class UserDashboardComponent {
  private userService = inject(UserService);
  private prefService = inject(PreferencesService);
  private notifService = inject(NotificationService);
  
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
  );
}
```

---

## Gestion des erreurs avancée

```typescript
@Injectable({
  providedIn: 'root'
})
export class ErrorHandlingService {
  private errorSubject = new Subject<Error>();
  
  error$ = this.errorSubject.asObservable().pipe(
    // Grouper les erreurs similaires
    groupBy(error => error.message),
    // Limiter les notifications
    mergeMap(group => group.pipe(
      debounceTime(1000),
      take(3)
    ))
  );
  
  handleError(error: Error) {
    this.errorSubject.next(error);
  }
}

// Utilisation dans un composant
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
  private errorService = inject(ErrorHandlingService);
  
  error = toSignal(
    this.errorService.error$.pipe(
      map(error => error.message)
    ),
    { initialValue: null }
  );
}
```

---

## WebSocket avec RxJS

```typescript
@Injectable({
  providedIn: 'root'
})
export class WebSocketService {
  private socket$ = new WebSocket('ws://example.com');
  private messagesSubject = new Subject<any>();
  
  messages$ = this.messagesSubject.asObservable().pipe(
    // Reconnexion automatique
    retryWhen(errors => errors.pipe(
      tap(error => console.error('WebSocket error:', error)),
      delay(1000)
    )),
    // Filtrage et transformation des messages
    filter(msg => msg.type === 'data'),
    map(msg => msg.payload),
    share()
  );
  
  constructor() {
    this.socket$.addEventListener('message', (event) => {
      this.messagesSubject.next(JSON.parse(event.data));
    });
  }
  
  send(message: any) {
    this.socket$.send(JSON.stringify(message));
  }
}
```

---
layout: exercices
routeAlias: 'exercice-search-realtime'
---

## Exercice : Recherche de Posts

---

1. Créez le composant de recherche :
```typescript
// features/posts/post-search.component.ts
@Component({
  selector: 'app-post-search',
  standalone: true,
  template: `
    <div class="search">
      <input 
        type="text" 
        [ngModel]="searchTerm()"
        (ngModelChange)="searchTerm.set($event)"
        placeholder="Rechercher..."
      >
      
      @if (loading()) {
        <spinner />
      } @else {
        <div class="results">
          @for (post of results(); track post.id) {
            <app-post-card 
              [post]="post"
              (click)="selectPost(post)"
            />
          }
        </div>
      }
    </div>
  `
})
export class PostSearchComponent {
  private postService = inject(PostService)
  
  searchTerm = signal('')
  loading = signal(false)
  
  // Convertir le signal en Observable
  private searchTerm$ = toObservable(this.searchTerm)
  
  results = toSignal(
    this.searchTerm$.pipe(
      // Attendre que l'utilisateur arrête de taper
      debounceTime(300),
      // Ignorer si le terme est le même
      distinctUntilChanged(),
      tap(() => this.loading.set(true)),
      // Annuler la requête précédente
      switchMap(term => 
        term.length > 2
          ? this.postService.searchPosts(term)
          : of([])
      ),
      tap(() => this.loading.set(false))
    ),
    { initialValue: [] }
  )

  selectPost(post: Post) {
    // Navigation vers le post
  }
} 