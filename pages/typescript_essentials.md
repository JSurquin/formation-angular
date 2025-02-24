---
routeAlias: 'typescript-essentials'
---

# TypeScript pour Angular

## Types de base

```typescript
// Types primitifs
let name: string = 'John';
let age: number = 25;
let isActive: boolean = true;

// Arrays
let numbers: number[] = [1, 2, 3];
let names: Array<string> = ['John', 'Jane'];

// Tuple
let tuple: [string, number] = ['John', 25];
```

---

## Interfaces et Types

```typescript
// Interface
interface User {
  id: number;
  name: string;
  email?: string; // Propriété optionnelle
}

// Type
type UserRole = 'admin' | 'user' | 'guest';

// Utilisation
const user: User = {
  id: 1,
  name: 'John',
  email: 'john@example.com'
};

const role: UserRole = 'admin';
```

---

## Décorateurs TypeScript

```typescript
// Décorateur de classe
function Logger(target: any) {
  console.log('Class decorated:', target);
}

// Décorateur de propriété
function Required(target: any, propertyKey: string) {
  console.log('Property decorated:', propertyKey);
}

@Logger
class Example {
  @Required
  name: string;

  constructor(name: string) {
    this.name = name;
  }
}
```

---

## Generics

```typescript
// Fonction générique
function getFirst<T>(array: T[]): T {
  return array[0];
}

// Classe générique
class DataContainer<T> {
  private data: T;

  constructor(data: T) {
    this.data = data;
  }

  getData(): T {
    return this.data;
  }
}

// Utilisation
const numbers = getFirst([1, 2, 3]); // Type inféré: number
const container = new DataContainer<string>('Hello');
```

---

## Utility Types

```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

// Partial - Toutes les propriétés deviennent optionnelles
type PartialTodo = Partial<Todo>;

// Pick - Sélectionne certaines propriétés
type TodoPreview = Pick<Todo, 'title' | 'completed'>;

// Omit - Omet certaines propriétés
type TodoWithoutDescription = Omit<Todo, 'description'>;

// Readonly - Rend toutes les propriétés en lecture seule
type ReadonlyTodo = Readonly<Todo>;
``` 