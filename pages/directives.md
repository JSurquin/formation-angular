---
layout: new-section
routeAlias: 'directives-pipes'
---

# Directives

---

## Types de directives

```typescript
// Nouvelle syntaxe de control flow (Angular 18+)
@Component({
  template: `
    @if (isLoggedIn()) {
      <nav>Menu utilisateur</nav>
    } @else {
      <auth-form />
    }

    @for (item of items(); track item.id) {
      <item-card [data]="item" />
    }

    @switch (status()) {
      @case ('loading') {
        <spinner />
      }
      @case ('error') {
        <error-message />
      }
      @default {
        <content />
      }
    }
  `
})
```

---

## Directives d'attributs personnalisées

```typescript
// highlight.directive.ts
@Directive({
  selector: '[appHighlight]',
  standalone: true
})
export class HighlightDirective {
  @Input('appHighlight') highlightColor = '';
  
  constructor(private el: ElementRef) {}
  
  @HostListener('mouseenter')
  onMouseEnter() {
    this.highlight(this.highlightColor || 'yellow');
  }
  
  @HostListener('mouseleave')
  onMouseLeave() {
    this.highlight('');
  }
  
  private highlight(color: string) {
    this.el.nativeElement.style.backgroundColor = color;
  }
}
```

---

## Utilisation des directives

```typescript
@Component({
  selector: 'app-root',
  template: `
    <div appHighlight="lightblue">
      Survolez-moi !
    </div>
    
    <p [appHighlight]="color">
      Couleur dynamique
    </p>
  `,
  imports: [HighlightDirective]
})
export class AppComponent {
  color = 'lightgreen';
}
```

---

## Pipes personnalisés

```typescript
// time-ago.pipe.ts
@Pipe({
  name: 'timeAgo',
  standalone: true
})
export class TimeAgoPipe implements PipeTransform {
  transform(value: Date | string): string {
    const date = new Date(value);
    const now = new Date();
    const seconds = Math.floor((now.getTime() - date.getTime()) / 1000);
    
    if (seconds < 60) {
      return 'Il y a quelques secondes';
    }
    if (seconds < 3600) {
      const minutes = Math.floor(seconds / 60);
      return `Il y a ${minutes} minute${minutes > 1 ? 's' : ''}`;
    }
    // ... autres cas
  }
}
```

---

## Utilisation des pipes

```typescript
@Component({
  selector: 'app-post',
  template: `
    <article>
      <h2>{{ title | uppercase }}</h2>
      <p>Publié {{ date | timeAgo }}</p>
      <p>Prix: {{ price | currency:'EUR' }}</p>
      <p>{{ content | slice:0:100 }}...</p>
    </article>
  `,
  imports: [TimeAgoPipe]
})
export class PostComponent {
  title = 'Mon article';
  date = new Date();
  price = 19.99;
  content = 'Lorem ipsum...';
}
```

---

## Pipes avec Signals

```typescript
@Pipe({
  name: 'filter',
  standalone: true
})
export class FilterPipe implements PipeTransform {
  transform(items: Signal<any[]>, searchTerm: Signal<string>): Signal<any[]> {
    return computed(() => {
      const term = searchTerm().toLowerCase();
      return items().filter(item => 
        item.name.toLowerCase().includes(term)
      );
    });
  }
}

@Component({
  template: `
    <input [ngModel]="searchTerm()" 
           (ngModelChange)="searchTerm.set($event)" />
           
    @for (item of items | filter:searchTerm; track item.id) {
      <item-card [data]="item" />
    }
  `,
  imports: [FilterPipe]
})
export class ListComponent {
  items = signal([/* ... */]);
  searchTerm = signal('');
}
```

---

## Directive avec Injection de dépendances

```typescript
@Directive({
  selector: '[appTooltip]',
  standalone: true
})
export class TooltipDirective implements OnDestroy {
  @Input('appTooltip') text = '';
  
  private tooltip: HTMLElement | null = null;
  
  constructor(
    private el: ElementRef,
    private renderer: Renderer2,
    private zone: NgZone
  ) {}
  
  @HostListener('mouseenter')
  onMouseEnter() {
    this.zone.runOutsideAngular(() => {
      this.show();
    });
  }
  
  @HostListener('mouseleave')
  onMouseLeave() {
    this.hide();
  }
  
  private show() {
    this.tooltip = this.renderer.createElement('div');
    this.renderer.addClass(this.tooltip, 'tooltip');
    this.renderer.setProperty(this.tooltip, 'textContent', this.text);
    this.renderer.appendChild(document.body, this.tooltip);
    
    // Positionnement du tooltip
    const hostPos = this.el.nativeElement.getBoundingClientRect();
    this.renderer.setStyle(this.tooltip, 'top', `${hostPos.bottom + 10}px`);
    this.renderer.setStyle(this.tooltip, 'left', `${hostPos.left}px`);
  }
  
  private hide() {
    if (this.tooltip) {
      this.renderer.removeChild(document.body, this.tooltip);
      this.tooltip = null;
    }
  }
  
  ngOnDestroy() {
    this.hide();
  }
}
```

---

# Exercice : Création d'une directive de validation

Créez une directive de validation personnalisée pour les formulaires :

```typescript
@Directive({
  selector: '[appPasswordStrength]',
  standalone: true,
  providers: [{
    provide: NG_VALIDATORS,
    useExisting: PasswordStrengthDirective,
    multi: true
  }]
})
export class PasswordStrengthDirective implements Validator {
  validate(control: AbstractControl): ValidationErrors | null {
    const value = control.value;
    
    if (!value) {
      return null;
    }
    
    const hasNumber = /\d/.test(value);
    const hasUpper = /[A-Z]/.test(value);
    const hasLower = /[a-z]/.test(value);
    const hasSpecial = /[!@#$%^&*]/.test(value);
    
    const valid = hasNumber && hasUpper && hasLower && hasSpecial;
    
    return valid ? null : {
      passwordStrength: {
        hasNumber,
        hasUpper,
        hasLower,
        hasSpecial
      }
    };
  }
}

// Utilisation
@Component({
  template: `
    <form>
      <input type="password" 
             [(ngModel)]="password" 
             appPasswordStrength
             #pwd="ngModel">
             
      @if (!pwd.valid && pwd.errors?.['passwordStrength'] as e) {
        <ul class="errors">
          @if (!e.hasNumber) {
            <li>Doit contenir un chiffre</li>
          }
          @if (!e.hasUpper) {
            <li>Doit contenir une majuscule</li>
          }
          // ... autres validations
        </ul>
      }
    </form>
  `
})
```

Cet exercice vous à permis de comprendre comment créer des directives de validation personnalisées et les intégrer avec les formulaires Angular. 

# Directives et Control Flow

## Syntaxe Traditionnelle
```typescript
@Component({
  template: `
    <div *ngIf="isLoading; else content">
      Loading...
    </div>
    <ng-template #content>
      <div *ngFor="let item of items">
        {{ item }}
      </div>
    </ng-template>
  `
})
```

---

## Nouveau Control Flow (Angular 18/19)
```typescript
@Component({
  template: `
    @if (isLoading()) {
      Loading...
    } @else {
      @for (item of items(); track item.id) {
        {{ item }}
      }
    }
  `
})
``` 