---
layout: new-section
routeAlias: 'component-lifecycle'
---

# Cycle de Vie des Composants

---

## Hooks essentiels

- **ngOnInit**: Après la première initialisation des propriétés
- **ngOnDestroy**: Juste avant que le composant soit détruit
- **ngOnChanges**: Quand une propriété liée aux données change
- **ngAfterViewInit**: Après l'initialisation de la vue

---

## Exemple d'implémentation

```typescript {1-3|4-6|7-9|10-12|13-15}
@Component({
  selector: 'app-lifecycle',
  template: `
    <h1>{{ title }}</h1>
    <p>{{ message }}</p>
  `
})
export class LifecycleComponent implements OnInit, OnDestroy {
  @Input() title: string;
  message: string;

  ngOnInit() {
    console.log('Component initialized');
    this.message = 'Component is ready!';
  }

  ngOnDestroy() {
    console.log('Component will be destroyed');
  }
}
```

---

## Détection des changements d'Input

```typescript {1-3|4-6|7-9|10-12|13-15}
@Component({
  selector: 'app-child',
  template: `
    <div>Data: {{ data }}</div>
  `
})
export class ChildComponent implements OnChanges {
  @Input() data: any;
  
  ngOnChanges(changes: SimpleChanges) {
    if (changes['data']) {
      console.log(
        'Previous:', changes['data'].previousValue,
        'Current:', changes['data'].currentValue
      );
    }
  }
}
```

---

## AfterView et AfterContent

```typescript {1-3|4-6|7-9|10-12|13-15}
@Component({
  selector: 'app-view-child',
  template: `
    <div #contentDiv>
      <ng-content></ng-content>
    </div>
  `
})
export class ViewChildComponent implements AfterViewInit {
  @ViewChild('contentDiv') contentDiv: ElementRef;
  
  ngAfterViewInit() {
    console.log('View initialized:', this.contentDiv.nativeElement);
  }
}
```

---

## Bonnes pratiques

1. **Initialisation**
   - Utiliser `ngOnInit` pour l'initialisation des données
   - Éviter les opérations lourdes dans le constructeur

2. **Nettoyage**
   - Toujours implémenter `ngOnDestroy` pour nettoyer les souscriptions
   - Libérer les ressources (timers, listeners, etc.)

3. **Performance**
   - Éviter les calculs lourds dans `ngOnChanges`
   - Utiliser `ChangeDetectionStrategy.OnPush` quand possible

