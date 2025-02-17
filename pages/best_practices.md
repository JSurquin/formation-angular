---
routeAlias: 'best-practices'
---

# Bonnes Pratiques Angular 18/19

## Architecture des composants

```typescript
// ❌ À éviter
@Component({
  template: `
    <div>
      <h1>{{ title }}</h1>
      <div *ngFor="let item of items">
        <!-- Logique complexe -->
      </div>
    </div>
  `
})
class BigComponent {
  // Trop de responsabilités
}

// ✅ Recommandé
@Component({
  template: `
    <app-header [title]="title" />
    <app-item-list [items]="items" />
  `
})
class WellStructuredComponent {
  // Responsabilité unique
}
```

---

## Gestion de l'état

```typescript
// ❌ État global mutable
class StateService {
  public data = [];
  
  updateData(newData) {
    this.data = newData;  // Mutation directe
  }
}

// ✅ État immutable avec Signals
@Injectable({ providedIn: 'root' })
class StateService {
  private _data = signal<Data[]>([]);
  readonly data = this._data.asReadonly();
  
  updateData(newData: Data[]) {
    this._data.set([...newData]);
  }
  
  addItem(item: Data) {
    this._data.update(data => [...data, item]);
  }
}
```

---

## Performance

```typescript
// ❌ Calculs inutiles
@Component({
  template: `
    <div>{{ heavyComputation() }}</div>
  `
})
class SlowComponent {
  heavyComputation() {
    // Recalculé à chaque cycle
    return this.data.reduce((acc, val) => acc + val, 0);
  }
}

// ✅ Optimisé avec computed
@Component({
  template: `
    <div>{{ total() }}</div>
  `
})
class FastComponent {
  total = computed(() => {
    console.log('Calcul effectué uniquement si nécessaire');
    return this.data().reduce((acc, val) => acc + val, 0);
  });
}
```

---

## Gestion des formulaires

```typescript
// ❌ Validation non structurée
@Component({
  template: `
    <form (ngSubmit)="onSubmit()">
      <input [(ngModel)]="email">
      <span *ngIf="!isValidEmail">Email invalide</span>
    </form>
  `
})
class FormComponent {
  isValidEmail = true;
  
  validateEmail() {
    this.isValidEmail = /^[^@]+@[^@]+\.[^@]+$/.test(this.email);
  }
}

// ✅ Validation structurée avec FormBuilder
@Component({
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      <input formControlName="email">
      @if (emailControl.invalid && emailControl.touched) {
        <span class="error">
          {{ getEmailError() }}
        </span>
      }
    </form>
  `
})
class FormComponent {
  form = inject(FormBuilder).group({
    email: ['', [
      Validators.required,
      Validators.email
    ]]
  });
  
  get emailControl() {
    return this.form.get('email');
  }
  
  getEmailError() {
    const control = this.emailControl;
    if (control?.errors?.['required']) return 'Email requis';
    if (control?.errors?.['email']) return 'Email invalide';
    return '';
  }
}
```

---

## Gestion des erreurs

```typescript
// ❌ Gestion d'erreur basique
@Injectable()
class ApiService {
  getData() {
    return this.http.get('/api/data').pipe(
      catchError(error => {
        console.error(error);
        return of(null);
      })
    );
  }
}

// ✅ Gestion d'erreur robuste
@Injectable()
class ApiService {
  getData() {
    return this.http.get<Data[]>('/api/data').pipe(
      retry(3),
      timeout(5000),
      catchError(error => {
        if (error instanceof TimeoutError) {
          return throwError(() => new Error('Requête expirée'));
        }
        if (error.status === 404) {
          return throwError(() => new Error('Ressource non trouvée'));
        }
        return throwError(() => new Error('Erreur serveur'));
      }),
      finalize(() => {
        // Nettoyage
      })
    );
  }
}
```

---

## Organisation du code

```typescript
// ❌ Fichier surchargé
// huge-file.ts
@Component({
  // 500+ lignes de template
})
class HugeComponent {
  // 1000+ lignes de logique
}

// ✅ Organisation modulaire
// feature/
// ├── components/
// │   ├── feature-list.component.ts
// │   ├── feature-item.component.ts
// │   └── feature-filter.component.ts
// ├── services/
// │   ├── feature.service.ts
// │   └── feature-state.service.ts
// ├── models/
// │   └── feature.model.ts
// └── feature.routes.ts
```

---

# Exercice : Refactoring

Refactorisez ce composant selon les bonnes pratiques :

```typescript
// ❌ Avant
@Component({
  template: `
    <div>
      <h1>{{ title }}</h1>
      <div *ngFor="let user of users">
        <div (click)="selectUser(user)">
          {{ user.name }}
          <button (click)="deleteUser(user.id)">X</button>
        </div>
      </div>
      <form (submit)="addUser($event)">
        <input [(ngModel)]="newUser.name">
        <input [(ngModel)]="newUser.email">
        <button>Ajouter</button>
      </form>
    </div>
  `
})
class UserComponent {
  users = [];
  newUser = { name: '', email: '' };
  
  selectUser(user) {
    // ...
  }
  
  deleteUser(id) {
    // ...
  }
  
  addUser(event) {
    event.preventDefault();
    // ...
  }
}

// ✅ Après
interface User {
  id: number;
  name: string;
  email: string;
}

@Component({
  template: `
    <app-header [title]="title()" />
    
    <app-user-list
      [users]="users()"
      (userSelected)="selectUser($event)"
      (userDeleted)="deleteUser($event)"
    />
    
    <app-user-form
      (userSubmitted)="addUser($event)"
    />
  `
})
class UserComponent {
  private userService = inject(UserService);
  
  title = signal('Gestion des utilisateurs');
  users = signal<User[]>([]);
  selectedUser = signal<User | null>(null);
  
  selectUser(user: User) {
    this.selectedUser.set(user);
  }
  
  async deleteUser(id: number) {
    try {
      await this.userService.deleteUser(id);
      this.users.update(users => 
        users.filter(u => u.id !== id)
      );
    } catch (error) {
      // Gestion de l'erreur
    }
  }
  
  async addUser(userData: Omit<User, 'id'>) {
    try {
      const newUser = await this.userService.createUser(userData);
      this.users.update(users => [...users, newUser]);
    } catch (error) {
      // Gestion de l'erreur
    }
  }
}
```

Cet exercice vous permettra de pratiquer :
- La séparation des responsabilités
- L'utilisation des Signals
- La gestion des types TypeScript
- La gestion des erreurs
- L'organisation du code 