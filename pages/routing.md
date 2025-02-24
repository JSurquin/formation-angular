---
layout: new-section
routeAlias: 'routing'
---

# Routing et Navigation

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

// Configuration de l'application
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

# Exercice : Navigation complexe

Créez une structure de navigation pour une application e-commerce :

```typescript
// app.routes.ts
export const routes: Routes = [{
  path: '',
  component: HomeComponent
}, {
  path: 'products',
  children: [{
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
        const id = route.paramMap.pipe(
          map(params => params.get('id'))
        );
        return inject(ProductService).getProduct(id);
      }
    }
  }]
}, {
  path: 'cart',
  component: CartComponent,
  canActivate: [authGuard]
}, {
  path: 'checkout',
  loadChildren: () => import('./checkout/routes'),
  canActivate: [authGuard]
}];

// checkout/routes.ts
export default [{
  path: '',
  component: CheckoutComponent,
  children: [{
    path: 'shipping',
    component: ShippingComponent
  }, {
    path: 'payment',
    component: PaymentComponent,
    canActivate: [
      (route: ActivatedRouteSnapshot) => {
        const cartService = inject(CartService);
        return cartService.hasItems();
      }
    ]
  }, {
    path: 'confirmation',
    component: ConfirmationComponent
  }]
}];
```

Cet exercice vous permettra de pratiquer :
- La navigation imbriquée
- Les guards conditionnels
- La résolution de données
- Le lazy loading
- Les transitions de vue 