---
layout: new-section
routeAlias: 'routing-navigation'
---

# Routing et Navigation dans Angular

---

## Qu'est-ce que le Routing ?

Le routing dans Angular permet de :
- Gérer la navigation entre les pages
- Créer des applications Single Page (SPA)
- Protéger les routes avec des guards
- Charger les modules de manière paresseuse (lazy loading)

---

## Configuration de base

```typescript
// app.routes.ts
export const routes: Routes = [
  {
    path: '',
    component: HomeComponent
  },
  {
    path: 'products',
    component: ProductListComponent
  },
  {
    path: 'products/:id',
    component: ProductDetailComponent
  },
  {
    path: '**',  // Wildcard route pour 404
    component: NotFoundComponent
  }
];

// Configuration dans main.ts
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes)
  ]
});
```

---

## Navigation dans les templates

```typescript
@Component({
  template: `
    <!-- Navigation déclarative -->
    <nav>
      <a routerLink="/">Accueil</a>
      <a routerLink="/products">Produits</a>
      <a [routerLink]="['/products', product.id]">
        Détail Produit
      </a>
    </nav>

    <!-- Outlet pour afficher les composants -->
    <router-outlet></router-outlet>
  `
})
export class AppComponent {}
```

---

## Navigation programmatique

```typescript
@Component({
  template: `
    <button (click)="goToProduct(123)">
      Voir Produit
    </button>
  `
})
export class NavComponent {
  private router = inject(Router);
  
  goToProduct(id: number) {
    this.router.navigate(['/products', id], {
      queryParams: { source: 'nav' }
    });
  }
}
```

---

## Lazy Loading

```typescript
// Configuration des routes
export const routes: Routes = [
  {
    path: 'admin',
    loadChildren: () => import('./admin/routes')
  },
  {
    path: 'products',
    loadComponent: () => import('./products/product-list.component')
  }
];

// Configuration avec preloading
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes,
      withPreloading(PreloadAllModules),
      withViewTransitions()
    )
  ]
});
```

---

## Protection des routes avec Guards

```typescript
// auth.guard.ts
export const authGuard: CanActivateFn = (route, state) => {
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

## Routes avec Resolvers

```typescript
// routes.ts
export default [{
  path: 'products',
  component: ProductListComponent,
  resolve: {
    products: () => inject(ProductService).getProducts()
  }
}, {
  path: 'products/:id',
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

## Nested Routes avec Outlets multiples

```typescript
// Configuration
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

// Template
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

## Commandes CLI utiles

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
```

---
layout: exercices
routeAlias: 'exercice-blog-routing'
---

## Mini-Blog : Configuration du Routing

### Configuration des routes

```typescript
// app.routes.ts
export const routes: Routes = [
  {
    path: '',
    component: HomeComponent,
    title: 'Accueil'
  },
  {
    path: 'blog',
    children: [
      {
        path: '',
        component: PostListComponent,
        title: 'Blog'
      },
      {
        path: 'new',
        component: PostFormComponent,
        title: 'Nouvel article',
        canActivate: [authGuard]
      },
      {
        path: ':id',
        component: PostDetailComponent,
        title: 'Article'
      }
    ]
  },
  {
    path: 'login',
    component: LoginComponent,
    title: 'Connexion'
  },
  {
    path: '**',
    component: NotFoundComponent,
    title: '404'
  }
]
```

### Guard d'authentification

```typescript
// guards/auth.guard.ts
export const authGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService)
  
  if (authService.isAuthenticated()) {
    return true
  }
  
  return inject(Router).createUrlTree(['/login'], {
    queryParams: { returnUrl: state.url }
  })
}
```

### Navigation dans les composants

```typescript
// post-card.component.ts
@Component({
  template: `
    <article class="border rounded-lg p-4">
      <h2>{{ post.title }}</h2>
      <button (click)="goToDetail()">Lire la suite</button>
    </article>
  `
})
export class PostCardComponent {
  @Input() post!: Post
  private router = inject(Router)
  
  goToDetail() {
    this.router.navigate(['/blog', this.post.id])
  }
}
```

### Menu de navigation

```typescript
// header.component.ts
@Component({
  template: `
    <nav class="bg-gray-800 text-white p-4">
      <div class="container mx-auto flex justify-between items-center">
        <a routerLink="/" class="text-xl font-bold">Mini-Blog</a>
        
        <div class="flex gap-4">
          <a routerLink="/blog" 
             routerLinkActive="text-blue-400"
             class="hover:text-blue-300">
            Articles
          </a>
          
          @if (isAuthenticated()) {
            <a routerLink="/blog/new"
               routerLinkActive="text-blue-400"
               class="hover:text-blue-300">
              Nouvel Article
            </a>
            <button (click)="logout()" 
                    class="hover:text-red-300">
              Déconnexion
            </button>
          } @else {
            <a routerLink="/login"
               routerLinkActive="text-blue-400"
               class="hover:text-blue-300">
              Connexion
            </a>
          }
        </div>
      </div>
    </nav>
  `
})
export class HeaderComponent {
  private authService = inject(AuthService)
  isAuthenticated = this.authService.isAuthenticated
  
  logout() {
    this.authService.logout()
  }
} 