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

## Virtual Scrolling

```typescript
@Component({
  template: `
    <cdk-virtual-scroll-viewport
      itemSize="50"
      class="viewport"
    >
      @for (item of items(); track item.id; let i = $index) {
        <div class="item">
          {{ i }} - {{ item.name }}
        </div>
      }
    </cdk-virtual-scroll-viewport>
  `,
  styles: [`
    .viewport {
      height: 500px;
      width: 100%;
    }
    .item {
      height: 50px;
    }
  `]
})
export class VirtualScrollComponent {
  items = signal<Item[]>([]);
  
  constructor() {
    // Simulation de 10000 items
    this.items.set(Array.from({ length: 10000 }).map((_, i) => ({
      id: i,
      name: `Item ${i}`
    })));
  }
}
```

---

## Web Workers

```typescript
// worker.ts
/// <reference lib="webworker" />

addEventListener('message', ({ data }) => {
  const result = expensiveCalculation(data);
  postMessage(result);
});

// component.ts
@Component({
  template: `
    <button (click)="startCalculation()">
      Calculer
    </button>
    @if (result()) {
      <div>Résultat: {{ result() }}</div>
    }
  `
})
export class WorkerComponent {
  private worker = new Worker(new URL('./worker.ts', import.meta.url));
  result = signal<number | null>(null);
  
  constructor() {
    this.worker.onmessage = ({ data }) => {
      this.result.set(data);
    };
  }
  
  startCalculation() {
    this.worker.postMessage(/* données */);
  }
}
```

---

# Exercice : Optimisation d'une liste de produits

Créez une liste de produits optimisée avec :
- Virtual scrolling
- Lazy loading des images
- Mise en cache des données
- Filtrage optimisé

---

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
  image: string;
  description: string;
}

@Component({
  template: `
    <div class="filters">
      <input
        [ngModel]="searchTerm()"
        (ngModelChange)="searchTerm.set($event)"
        placeholder="Rechercher..."
      />
      <select
        [ngModel]="priceFilter()"
        (ngModelChange)="priceFilter.set($event)"
      >
        <option value="all">Tous les prix</option>
        <option value="low">< 50€</option>
        <option value="high">>= 50€</option>
      </select>
    </div>

    <cdk-virtual-scroll-viewport
      itemSize="100"
      class="product-list"
    >
      @for (product of filteredProducts(); track product.id) {
        <div class="product-card">
          @defer (on viewport) {
            <img
              ngSrc="{{ product.image }}"
              width="100"
              height="100"
              loading="lazy"
            />
          }
          <div class="product-info">
            <h3>{{ product.name }}</h3>
            <p>{{ product.price }}€</p>
          </div>
        </div>
      }
    </cdk-virtual-scroll-viewport>
  `
})
export class ProductListComponent {
  private productService = inject(ProductService);
  
  // État
  products = signal<Product[]>([]);
  searchTerm = signal('');
  priceFilter = signal<'all' | 'low' | 'high'>('all');
  
  // Computed
  filteredProducts = computed(() => {
    let result = this.products();
    const search = this.searchTerm().toLowerCase();
    
    if (search) {
      result = result.filter(p => 
        p.name.toLowerCase().includes(search)
      );
    }
    
    switch (this.priceFilter()) {
      case 'low':
        result = result.filter(p => p.price < 50);
        break;
      case 'high':
        result = result.filter(p => p.price >= 50);
        break;
    }
    
    return result;
  });
  
  constructor() {
    this.loadProducts();
  }
  
  private async loadProducts() {
    const products = await this.productService.getProducts();
    this.products.set(products);
  }
}
```

Cet exercice vous permettra de pratiquer :
- L'utilisation du virtual scrolling
- L'optimisation des images
- La gestion efficace des filtres avec Signals
- Le lazy loading des composants
- La mise en cache des données

---

## Hydration (Nouveauté Angular 18/19)

```typescript
// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideClientHydration() // Active l'hydration
  ]
};
```

---

## Comment fonctionne l'Hydration ?

- Réutilise le HTML du SSR
- Restaure l'état de l'application
- Évite le re-rendu complet
- Améliore le First Contentful Paint (FCP)

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
