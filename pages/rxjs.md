---
layout: new-section
routeAlias: 'rxjs-observables'
---

# RxJS et Observables

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

# Exercice : Système de recherche en temps réel

Créez un système de recherche avec auto-complétion :

```typescript
interface SearchResult {
  id: number;
  title: string;
  description: string;
}

@Component({
  template: `
    <div class="search-container">
      <input
        [ngModel]="searchTerm()"
        (ngModelChange)="searchTerm.set($event)"
        placeholder="Rechercher..."
      >
      
      @if (loading()) {
        <div class="spinner">Chargement...</div>
      }
      
      @if (error()) {
        <div class="error">{{ error() }}</div>
      }
      
      @if (results(); as items) {
        <div class="results">
          @for (item of items; track item.id) {
            <div class="result-item" 
                 (click)="selectItem(item)">
              <h3>{{ item.title }}</h3>
              <p>{{ item.description }}</p>
            </div>
          }
        </div>
      }
    </div>
  `
})
export class SearchComponent {
  private searchService = inject(SearchService);
  
  searchTerm = signal('');
  loading = signal(false);
  error = signal<string | null>(null);
  
  private searchTerm$ = toObservable(this.searchTerm);
  
  results = toSignal(
    this.searchTerm$.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      tap(() => {
        this.loading.set(true);
        this.error.set(null);
      }),
      switchMap(term => 
        this.searchService.search(term).pipe(
          catchError(error => {
            this.error.set('Erreur de recherche');
            return of([]);
          }),
          finalize(() => this.loading.set(false))
        )
      )
    ),
    { initialValue: [] }
  );
  
  selectItem(item: SearchResult) {
    // Logique de sélection
  }
}
```

Cet exercice vous permettra de pratiquer :
- La gestion des états de chargement
- La gestion des erreurs
- Les opérateurs RxJS
- L'intégration avec les Signals
- La création d'une interface utilisateur réactive 