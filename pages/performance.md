---
layout: new-section
routeAlias: 'performance'
---

# Performance et Optimisation

---

## Stratégies d'optimisation

```typescript
// Avant (avec Zone.js)
@Component({
  template: `
    <div>{{ value }}</div>
    <button (click)="increment()">+</button>
  `
})
class OldComponent {
  value = 0;
  
  increment() {
    this.value++;  // Déclenche la détection des changements globale
  }
}

// Après (avec Signals)
@Component({
  template: `
    <div>{{ value() }}</div>
    <button (click)="increment()">+</button>
  `
})
class NewComponent {
  value = signal(0);
  
  increment() {
    this.value.update(v => v + 1);  // Mise à jour granulaire
  }
}
```

---

## Optimisation des templates

```typescript
@Component({
  template: `
    <!-- Utilisation de @if au lieu de *ngIf -->
    @if (showContent()) {
      <heavy-component />
    }
    
    <!-- Utilisation de @for au lieu de *ngFor -->
    @for (item of items(); track item.id) {
      <item-component [data]="item" />
    }
    
    <!-- Defer loading -->
    @defer {
      <expensive-component />
    } @loading {
      <spinner />
    }
  `
})
export class OptimizedComponent {
  showContent = signal(false);
  items = signal<Item[]>([]);
}
```

---

## Lazy Loading moderne

```typescript
// app.routes.ts
export const routes: Routes = [{
  path: 'admin',
  loadChildren: () => import('./admin/routes'),
  canActivate: [authGuard],
  providers: [
    importProvidersFrom(AdminModule)
  ]
}];

// Composant lazy-loadé
@Component({
  standalone: true,
  imports: [CommonModule],
  template: `
    @defer (on viewport) {
      <heavy-chart [data]="chartData()" />
    }
  `
})
export class AdminDashboardComponent {
  chartData = signal<ChartData[]>([]);
}
```

---

## Optimisation des images

```typescript
@Component({
  template: `
    <img
      ngSrc="{{ imageUrl }}"
      width="300"
      height="200"
      priority
      loading="eager"
    />
    
    @for (image of images(); track image) {
      <img
        ngSrc="{{ image.url }}"
        [width]="image.width"
        [height]="image.height"
        loading="lazy"
      />
    }
  `
})
export class ImageOptimizedComponent {
  images = signal<ImageData[]>([]);
}
```

---

## Memoization avec Signals

```typescript
@Component({
  template: `
    <div>Résultat filtré: {{ filteredData() }}</div>
  `
})
export class MemoizedComponent {
  data = signal<number[]>([]);
  threshold = signal(10);
  
  // Computed value avec memoization intégrée
  filteredData = computed(() => {
    console.log('Calcul coûteux effectué');
    return this.data().filter(n => n > this.threshold());
  });
  
  // Alternative avec fonction pure
  expensiveCalculation = memoize((data: number[]) => {
    console.log('Calcul très coûteux effectué');
    return data.reduce((acc, curr) => acc + curr, 0);
  });
}
```

---

## Optimisation des requêtes HTTP

```typescript
@Injectable({
  providedIn: 'root'
})
export class OptimizedApiService {
  private cache = new Map<string, any>();
  private http = inject(HttpClient);
  
  getData<T>(url: string, options: { ttl?: number } = {}) {
    const cached = this.cache.get(url);
    if (cached && (!options.ttl || Date.now() - cached.timestamp < options.ttl)) {
      return of(cached.data);
    }
    
    return this.http.get<T>(url).pipe(
      tap(data => {
        this.cache.set(url, {
          data,
          timestamp: Date.now()
        });
      }),
      shareReplay(1)
    );
  }
}
```

---

## Defer Loading (Nouveauté Angular 18/19)

```typescript
@Component({
  template: `
    @defer {
      <heavy-component />
    } @loading {
      <spinner />
    } @error {
      <error-message />
    } @placeholder {
      <div>Chargement...</div>
    }
  `
})
```
