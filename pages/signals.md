---
routeAlias: 'signals'
---

# Signals dans Angular 18/19

## Introduction aux Signals

```typescript
// Création de signals basiques
const count = signal(0);
const name = signal('John');
const items = signal<string[]>([]);

// Lecture des valeurs
console.log(count()); // 0
console.log(name()); // 'John'

// Mise à jour des valeurs
count.set(1);
name.set('Jane');
items.update(current => [...current, 'Nouveau']);
```

---

## Computed Signals

```typescript
@Component({
  template: `
    <div>
      <p>Prix: {{ price() }}€</p>
      <p>TVA: {{ vat() }}€</p>
      <p>Total: {{ total() }}€</p>
    </div>
  `
})
export class PriceComponent {
  price = signal(100);
  vatRate = signal(0.20);
  
  // Valeur calculée automatiquement
  vat = computed(() => this.price() * this.vatRate());
  total = computed(() => this.price() + this.vat());
}
```

---

## Effects

```typescript
@Component({
  template: `
    <button (click)="increment()">
      Compteur: {{ count() }}
    </button>
  `
})
export class CounterComponent {
  count = signal(0);
  
  constructor() {
    // L'effet se déclenche à chaque changement de count
    effect(() => {
      console.log(`Nouvelle valeur: ${this.count()}`);
      localStorage.setItem('count', this.count().toString());
    });
  }
  
  increment() {
    this.count.update(n => n + 1);
  }
}
```

---

## Signals avec objets et tableaux

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

@Component({
  template: `
    <div>
      <h2>{{ user().name }}</h2>
      @for (friend of friends(); track friend.id) {
        <div>{{ friend.name }}</div>
      }
    </div>
  `
})
export class UserComponent {
  user = signal<User>({
    id: 1,
    name: 'John',
    email: 'john@example.com'
  });
  
  friends = signal<User[]>([]);
  
  updateUser(newData: Partial<User>) {
    this.user.update(user => ({
      ...user,
      ...newData
    }));
  }
  
  addFriend(friend: User) {
    this.friends.update(friends => [...friends, friend]);
  }
}
```

---

## Signals avec async data

```typescript
@Component({
  template: `
    @if (loading()) {
      <spinner />
    } @else if (error()) {
      <error-message [message]="error()" />
    } @else if (data(); as users) {
      @for (user of users; track user.id) {
        <user-card [user]="user" />
      }
    }
  `
})
export class UsersComponent {
  private userService = inject(UserService);
  
  data = signal<User[] | null>(null);
  loading = signal(true);
  error = signal<string | null>(null);
  
  constructor() {
    this.loadUsers();
  }
  
  async loadUsers() {
    try {
      this.loading.set(true);
      this.error.set(null);
      const users = await this.userService.getUsers();
      this.data.set(users);
    } catch (err) {
      this.error.set(err.message);
    } finally {
      this.loading.set(false);
    }
  }
}
```

---

## Signal Inputs

```typescript
@Component({
  selector: 'app-user-profile',
  template: `
    <div>
      <h2>{{ name() }}</h2>
      <p>Age: {{ age() }}</p>
      <p>Score: {{ score() }}</p>
    </div>
  `
})
export class UserProfileComponent {
  name = input.required<string>();
  age = input<number>(18); // Valeur par défaut
  score = input<number>(); // Optionnel
}

// Utilisation
@Component({
  template: `
    <app-user-profile
      [name]="user.name"
      [age]="user.age"
      [score]="user.score"
    />
  `
})
```

---

## Signals avec formulaires

```typescript
@Component({
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      <input formControlName="name">
      <input formControlName="email">
      
      <div>Form Value: {{ formValue() | json }}</div>
      <div>Valid: {{ isValid() }}</div>
      
      <button type="submit" [disabled]="!isValid()">
        Envoyer
      </button>
    </form>
  `
})
export class SignupFormComponent {
  form = new FormGroup({
    name: new FormControl('', Validators.required),
    email: new FormControl('', [Validators.required, Validators.email])
  });
  
  // Conversion des valeurs du formulaire en signals
  formValue = toSignal(this.form.valueChanges, {
    initialValue: this.form.value
  });
  
  isValid = computed(() => this.form.valid);
  
  onSubmit() {
    if (this.isValid()) {
      console.log('Envoi:', this.formValue());
    }
  }
}
```

---

# Exercice : Gestionnaire de tâches avec Signals

Créez un gestionnaire de tâches utilisant les Signals :

```typescript
interface Task {
  id: number;
  title: string;
  completed: boolean;
  priority: 'low' | 'medium' | 'high';
}

@Component({
  template: `
    <div class="task-manager">
      <!-- Formulaire d'ajout -->
      <form (submit)="addTask($event)">
        <input [(ngModel)]="newTaskTitle" placeholder="Nouvelle tâche">
        <select [(ngModel)]="newTaskPriority">
          <option value="low">Basse</option>
          <option value="medium">Moyenne</option>
          <option value="high">Haute</option>
        </select>
        <button type="submit">Ajouter</button>
      </form>
      
      <!-- Statistiques -->
      <div class="stats">
        <p>Total: {{ totalTasks() }}</p>
        <p>Complétées: {{ completedTasks() }}</p>
        <p>En cours: {{ pendingTasks() }}</p>
      </div>
      
      <!-- Liste des tâches -->
      @for (task of filteredTasks(); track task.id) {
        <div class="task" [class.completed]="task.completed">
          <input type="checkbox"
                 [checked]="task.completed"
                 (change)="toggleTask(task.id)">
          <span [class]="task.priority">{{ task.title }}</span>
          <button (click)="deleteTask(task.id)">Supprimer</button>
        </div>
      }
    </div>
  `
})
export class TaskManagerComponent {
  // État
  tasks = signal<Task[]>([]);
  filter = signal<'all' | 'active' | 'completed'>('all');
  
  // UI state
  newTaskTitle = signal('');
  newTaskPriority = signal<Task['priority']>('medium');
  
  // Computed values
  totalTasks = computed(() => this.tasks().length);
  completedTasks = computed(() => 
    this.tasks().filter(t => t.completed).length
  );
  pendingTasks = computed(() => 
    this.tasks().filter(t => !t.completed).length
  );
  
  filteredTasks = computed(() => {
    const filter = this.filter();
    const tasks = this.tasks();
    
    switch (filter) {
      case 'active':
        return tasks.filter(t => !t.completed);
      case 'completed':
        return tasks.filter(t => t.completed);
      default:
        return tasks;
    }
  });
  
  // Actions
  addTask(event: Event) {
    event.preventDefault();
    const title = this.newTaskTitle();
    if (!title.trim()) return;
    
    this.tasks.update(tasks => [
      ...tasks,
      {
        id: Date.now(),
        title,
        completed: false,
        priority: this.newTaskPriority()
      }
    ]);
    
    this.newTaskTitle.set('');
  }
  
  toggleTask(id: number) {
    this.tasks.update(tasks =>
      tasks.map(task =>
        task.id === id
          ? { ...task, completed: !task.completed }
          : task
      )
    );
  }
  
  deleteTask(id: number) {
    this.tasks.update(tasks =>
      tasks.filter(task => task.id !== id)
    );
  }
}
```

Cet exercice vous permettra de pratiquer :
- La gestion d'état avec Signals
- Les computed values
- La mise à jour immutable des données
- L'intégration avec les templates
- La gestion des événements utilisateur 

# Gestion de l'État avec Signals

## Avant les Signals
```typescript
@Component({
  template: `
    <h1>{{ count }}</h1>
    <button (click)="increment()">+1</button>
  `
})
class CounterComponent {
  count = 0
  
  increment() {
    this.count++ // Déclenche détection globale
  }
}
```

---

## Avec les Signals
```typescript
@Component({
  template: `
    <h1>{{ count() }}</h1>
    <button (click)="increment()">+1</button>
  `
})
class CounterComponent {
  count = signal(0)
  
  increment() {
    this.count.update(n => n + 1) // Mise à jour granulaire
  }
}
``` 