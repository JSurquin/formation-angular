---
layout: new-section
routeAlias: 'angular-basics'
---

# Angular Basics

---

## Fundamental Architecture

- **Structure of an Angular Project**
  - The angular.json file
  - The src/, app/, assets/ folders
  - Configuration files

- **Key Concepts**
  - Modules (NgModule)
  - Components
  - Services
  - Directives
  - Pipes

---
layout: new-section
routeAlias: 'fundamental-architecture'
---

<img class="w-1/3 mx-auto" src="/architecture.png" alt="Angular Architecture" />

---

## Understanding Bootstrapping

```typescript
// main.ts
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent, {
  providers: [
    // Global configuration
  ]
}).catch(err => console.error(err));
```

---

## Initializing the Application

This is where we initialize the Angular application.
If we want to use a router or use provideHttpClient, we can do it here.

<small>

> **I highlighted lines 3 and 4 which are the most important.**

</small>

```typescript {3,4}
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes),
    provideHttpClient()
  ]
}).catch(err => console.error(err));
```

---

## Structure of a Basic Component

```typescript {2-4|5-6|7-13|all}
// hello.component.ts
@Component({
  selector: 'app-hello',
  // The selector is the name of the HTML tag that will be used to use the component
  standalone: true,
  // standalone: true to use the component without NgModule, the component is autonomous
  template: `
    <h1>Hello {{ name }}</h1>
    <button (click)="sayHello()">
      Click me
    </button>
  `
  // template: ` for the component template, in other words the view :)
})
export class HelloComponent {
  name = 'Angular';
  // name is a property of the component, I created it myself
  
  // sayHello is a method of the component, I created it myself
  sayHello() {
    // this method will be called when we click on the button in the view
    // <button (click)="sayHello()">Click me</button>
    console.log('Hello from Angular!');
  }
}
```

---

## Fundamental Data Binding

- **One-way binding (One-way Data Binding)**

Interpolation: `{{ expression }}`

  - Displays component data in the template
  - Example: `{{ user.name }}` displays the user's name
  - Supports simple expressions: `{{ 1 + 1 }}`, `{{ user.firstName + ' ' + user.lastName }}`

---

- **Property binding (Property Binding)**
  - Syntax: `[property]="value"`
  - Binds an HTML element property to a component value
  - Example: `[disabled]="isLoading"` disables a button based on the state
  - Widely used for HTML attributes: `[src]`, `[href]`, `[class]`, etc.

---

- **Event binding (Event Binding)**
  - Syntax: `(event)="handler()"`
  - Reacts to user events
  - Common examples:
    - `(click)="onClick()"`
    - `(submit)="onSubmit()"`
    - `(input)="onInput($event)"`
  - `$event` provides access to event data

---

- **Two-way binding (Two-way Data Binding)**
  - Syntax banana in a box: `[(ngModel)]="property"`
  - Combines property binding and event binding
  - Automatically updates the view AND the component (sync)
  - Perfect for forms
  - Example: `[(ngModel)]="user.name"` synchronizes an input field with a property
  - Requires importing FormsModule or NgModel in the standalone component

---
layout: exercices
routeAlias: 'mini-blog-exercise'
---

# Exercise: Creating the Mini-Blog Project

---

1. Create a new Angular project:
```bash
ng new mini-blog --routing --style=scss
cd mini-blog
```

2. Configure the initial structure:
```bash
mkdir src/app/features
mkdir src/app/shared
mkdir src/app/core
```

---

3. Create the main component:
```typescript
// app.component.ts
@Component({
  selector: 'app-root',
  template: `
    <header>
      <h1>{{ title }}</h1>
    </header>
    <main>
      <router-outlet />
    </main>
  `
})
export class AppComponent {
  title = 'Mini Blog';
}
```

4. Test the application:
```bash
ng serve
``` 