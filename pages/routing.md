---
layout: new-section
routeAlias: 'routing-navigation'
---

# Routing et Navigation dans Angular

---

## Commandes CLI pour le Routing

```bash
# Générer un nouveau module avec routing
ng generate module features/admin --routing

# Générer un guard de route
ng generate guard core/guards/auth --implements CanActivate

# Générer un resolver
ng generate resolver features/product/product-data

# Générer une route lazy-loadée
ng generate module features/dashboard --route dashboard --module app.routes.ts

# Générer un composant avec route
ng generate component features/profile --route profile --type page

# Générer un service de navigation
ng generate service core/services/navigation

# Générer un guard avec plusieurs interfaces
ng generate guard core/guards/admin --implements CanActivate,CanDeactivate

# Générer un module feature complet
ng generate module features/shop --routing --route shop --module app.routes.ts
```

---

## Configuration des routes modernes

```typescript
// app.routes.ts
export const routes: Routes = [
  {
    path: '',
    component: HomeComponent
  },
  {
    path: 'products',
    loadChildren: () => import('./products/routes')
  },
  {
    path: 'profile',
    canActivate: [authGuard],
    loadComponent: () => import('./profile.component')
  }
];

// Configuration de l'application dans le main.ts ou app.config.ts
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes, 
      withComponentInputBinding(),
      withViewTransitions()
    )
  ]
});
```

---

## Routes avec Signals

```typescript
// products/routes.ts
export default [{
  path: '',
  component: ProductListComponent,
  resolve: {
    products: () => inject(ProductService).getProducts()
  }
}, {
  path: ':id',
  component: ProductDetailComponent,
  resolve: {
    product: (route: ActivatedRoute) => {
      const id = route.paramMap.pipe(map(params => params.get('id')));
      return inject(ProductService).getProduct(id);
    }
  }
}];
```

---

## Navigation programmatique

```typescript
@Component({
  template: `
    <nav>
      <a routerLink="/products">Produits</a>
      <a routerLink="/profile">Profil</a>
    </nav>
    
    <button (click)="goToProduct(123)">
      Voir Produit
    </button>
  `
})
export class NavComponent {
  constructor(private router: Router) {}
  
  goToProduct(id: number) {
    this.router.navigate(['/products', id], {
      queryParams: { source: 'nav' }
    });
  }
}
```

---

## Guards modernes

Un guard est un service qui permet de contrôler l'accès à une route.
Parfait pour les routes sensibles ou les routes qui nécessitent une authentification.

```typescript
// auth.guard.ts
export const authGuard: CanActivateFn = (
  route: ActivatedRouteSnapshot,
  state: RouterStateSnapshot
) => {
  const authService = inject(AuthService);
  const router = inject(Router);
  
  if (authService.isAuthenticated()) {
    return true;
  }
  
  return router.createUrlTree(['/login'], {
    queryParams: { returnUrl: state.url }
  });
};

// Utilisation
{
  path: 'admin',
  loadChildren: () => import('./admin/routes'),
  canActivate: [authGuard]
}
```

---

## View Transitions API

```typescript
@Component({
  template: `
    <div @routeAnimations>
      <router-outlet></router-outlet>
    </div>
  `,
  animations: [
    trigger('routeAnimations', [
      transition('* <=> *', [
        style({ position: 'relative' }),
        query(':enter, :leave', [
          style({
            position: 'absolute',
            top: 0,
            left: 0,
            width: '100%'
          })
        ]),
        query(':enter', [style({ left: '100%' })]),
        query(':leave', animateChild()),
        group([
          query(':leave', [
            animate('300ms ease-out', style({ left: '-100%' }))
          ]),
          query(':enter', [
            animate('300ms ease-out', style({ left: '0%' }))
          ])
        ])
      ])
    ])
  ]
})
export class AppComponent {}
```

---

## Lazy Loading avec Preloading

```typescript
// Configuration du preloading
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes,
      withPreloading(PreloadAllModules),
      withViewTransitions()
    )
  ]
});

// Route avec lazy loading
{
  path: 'dashboard',
  loadComponent: () => import('./dashboard/dashboard.component')
    .then(m => m.DashboardComponent),
  data: { preload: true }
}
```

---

## Paramètres de route avec Signals

```typescript
@Component({
  template: `
    <h1>Produit {{ productId() }}</h1>
    @if (product()) {
      <div>
        <h2>{{ product().name }}</h2>
        <p>{{ product().description }}</p>
      </div>
    }
  `
})
export class ProductDetailComponent {
  private route = inject(ActivatedRoute);
  private productService = inject(ProductService);
  
  productId = computed(() => 
    this.route.paramMap.pipe(
      map(params => params.get('id'))
    )
  );
  
  product = computed(async () => {
    const id = this.productId();
    if (!id) return null;
    return await this.productService.getProduct(id);
  });
}
```

---

## Nested Routes avec Outlets multiples

```typescript
// Configuration des routes
export const routes: Routes = [{
  path: 'dashboard',
  component: DashboardComponent,
  children: [{
    path: '',
    component: DashboardOverviewComponent
  }, {
    path: 'stats',
    component: StatsComponent,
    outlet: 'sidebar'
  }]
}];

// Template avec multiple outlets
@Component({
  template: `
    <div class="dashboard-layout">
      <main>
        <router-outlet></router-outlet>
      </main>
      <aside>
        <router-outlet name="sidebar"></router-outlet>
      </aside>
    </div>
  `
})
export class DashboardComponent {}
```

---
layout: exercices
routeAlias: 'exercice-routing-blog'
---

## Exercice : Routes du Blog

---

1. Configurez les routes :
```typescript
// app.routes.ts
export const routes: Routes = [
  {
    path: '',
    component: HomeComponent
  },
  {
    path: 'posts',
    children: [
      {
        path: '',
        component: PostListComponent,
        resolve: {
          posts: () => inject(PostService).loadPosts()
        }
      },
      {
        path: 'new',
        component: PostFormComponent,
        canActivate: [authGuard]
      },
      {
        path: ':id',
        component: PostDetailComponent,
        resolve: {
          post: (route: ActivatedRoute) => {
            const id = route.paramMap.pipe(
              map(params => Number(params.get('id')))
            )
            return inject(PostService).getPost(id)
          }
        }
      }
    ]
  }
]
```

---

2. Créez le guard d'authentification :
```typescript
// core/guards/auth.guard.ts
export const authGuard: CanActivateFn = () => {
  const authService = inject(AuthService)
  const router = inject(Router)
  
  if (authService.isAuthenticated()) {
    return true
  }
  
  return router.createUrlTree(['/login'], {
    queryParams: { returnUrl: router.url }
  })
}
``` 