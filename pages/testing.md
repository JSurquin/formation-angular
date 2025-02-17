---
routeAlias: 'testing'
---

# Tests dans Angular 18/19

## Tests unitaires avec Jasmine et Karma

```typescript
// user.service.spec.ts
describe('UserService', () => {
  let service: UserService;
  let httpMock: HttpTestingController;
  
  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [UserService]
    });
    
    service = TestBed.inject(UserService);
    httpMock = TestBed.inject(HttpTestingController);
  });
  
  it('should fetch users', () => {
    const mockUsers = [
      { id: 1, name: 'John' },
      { id: 2, name: 'Jane' }
    ];
    
    service.getUsers().subscribe(users => {
      expect(users).toEqual(mockUsers);
    });
    
    const req = httpMock.expectOne('/api/users');
    expect(req.request.method).toBe('GET');
    req.flush(mockUsers);
  });
});
```

---

## Tests de composants avec Signals

```typescript
// counter.component.spec.ts
describe('CounterComponent', () => {
  let component: CounterComponent;
  let fixture: ComponentFixture<CounterComponent>;
  
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [CounterComponent]
    }).compileComponents();
    
    fixture = TestBed.createComponent(CounterComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });
  
  it('should increment counter', () => {
    expect(component.count()).toBe(0);
    
    component.increment();
    expect(component.count()).toBe(1);
    
    const button = fixture.debugElement.query(By.css('button'));
    button.triggerEventHandler('click', null);
    expect(component.count()).toBe(2);
  });
  
  it('should update display', () => {
    const display = fixture.debugElement.query(By.css('.count'));
    expect(display.nativeElement.textContent).toContain('0');
    
    component.increment();
    fixture.detectChanges();
    expect(display.nativeElement.textContent).toContain('1');
  });
});
```

---

## Tests d'intégration

```typescript
// app.component.integration.spec.ts
describe('AppComponent (integration)', () => {
  let component: AppComponent;
  let fixture: ComponentFixture<AppComponent>;
  let userService: UserService;
  
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [
        AppComponent,
        HttpClientTestingModule,
        RouterTestingModule
      ],
      providers: [UserService]
    }).compileComponents();
    
    fixture = TestBed.createComponent(AppComponent);
    component = fixture.componentInstance;
    userService = TestBed.inject(UserService);
    fixture.detectChanges();
  });
  
  it('should load and display users', fakeAsync(() => {
    const mockUsers = [{ id: 1, name: 'John' }];
    spyOn(userService, 'getUsers').and.returnValue(of(mockUsers));
    
    component.loadUsers();
    tick();
    fixture.detectChanges();
    
    const userElements = fixture.debugElement.queryAll(By.css('.user-item'));
    expect(userElements.length).toBe(1);
    expect(userElements[0].nativeElement.textContent).toContain('John');
  }));
});
```

---

## Tests E2E avec Cypress

```typescript
// cypress/e2e/login.cy.ts
describe('Login Flow', () => {
  beforeEach(() => {
    cy.visit('/login');
  });
  
  it('should login successfully', () => {
    cy.intercept('POST', '/api/login', {
      statusCode: 200,
      body: { token: 'fake-token' }
    }).as('loginRequest');
    
    cy.get('[data-testid=email-input]')
      .type('user@example.com');
      
    cy.get('[data-testid=password-input]')
      .type('password123');
      
    cy.get('[data-testid=login-button]')
      .click();
      
    cy.wait('@loginRequest');
    cy.url().should('include', '/dashboard');
    cy.get('[data-testid=user-menu]')
      .should('be.visible');
  });
  
  it('should show error message on invalid credentials', () => {
    cy.intercept('POST', '/api/login', {
      statusCode: 401,
      body: { message: 'Invalid credentials' }
    }).as('loginRequest');
    
    cy.get('[data-testid=email-input]')
      .type('wrong@example.com');
      
    cy.get('[data-testid=password-input]')
      .type('wrongpass');
      
    cy.get('[data-testid=login-button]')
      .click();
      
    cy.wait('@loginRequest');
    cy.get('[data-testid=error-message]')
      .should('be.visible')
      .and('contain', 'Invalid credentials');
  });
});
```

---

## Tests de services avec Signals

```typescript
// data.service.spec.ts
describe('DataService', () => {
  let service: DataService;
  
  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [DataService]
    });
    service = TestBed.inject(DataService);
  });
  
  it('should update data and notify subscribers', () => {
    // Test du signal
    expect(service.data()).toEqual([]);
    
    service.addItem({ id: 1, name: 'Test' });
    expect(service.data().length).toBe(1);
    expect(service.data()[0].name).toBe('Test');
    
    // Test du computed
    expect(service.itemCount()).toBe(1);
  });
  
  it('should filter data correctly', () => {
    service.addItem({ id: 1, name: 'Test1', active: true });
    service.addItem({ id: 2, name: 'Test2', active: false });
    
    service.setFilter('active');
    expect(service.filteredData().length).toBe(1);
    expect(service.filteredData()[0].name).toBe('Test1');
  });
});
```

---

## Tests de formulaires réactifs

```typescript
// registration.component.spec.ts
describe('RegistrationComponent', () => {
  let component: RegistrationComponent;
  let fixture: ComponentFixture<RegistrationComponent>;
  
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [
        ReactiveFormsModule,
        RegistrationComponent
      ]
    }).compileComponents();
    
    fixture = TestBed.createComponent(RegistrationComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });
  
  it('should validate form correctly', () => {
    const form = component.form;
    
    // Test champs vides
    expect(form.valid).toBeFalsy();
    
    // Remplissage du formulaire
    form.patchValue({
      email: 'test@example.com',
      password: 'Password123!',
      confirmPassword: 'Password123!'
    });
    
    expect(form.valid).toBeTruthy();
    
    // Test validation personnalisée
    form.patchValue({ confirmPassword: 'different' });
    expect(form.hasError('passwordMismatch')).toBeTruthy();
  });
  
  it('should submit form when valid', () => {
    spyOn(component.submitted, 'emit');
    
    component.form.patchValue({
      email: 'test@example.com',
      password: 'Password123!',
      confirmPassword: 'Password123!'
    });
    
    component.onSubmit();
    expect(component.submitted.emit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'Password123!'
    });
  });
});
```

---

# Exercice : Tests complets d'une fonctionnalité

Créez une suite de tests complète pour une fonctionnalité de panier d'achat :

```typescript
// cart.service.spec.ts
describe('CartService', () => {
  let service: CartService;
  
  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [CartService]
    });
    service = TestBed.inject(CartService);
  });
  
  it('should add items to cart', () => {
    const item = { id: 1, name: 'Test', price: 10 };
    
    service.addItem(item);
    expect(service.items().length).toBe(1);
    expect(service.totalPrice()).toBe(10);
  });
  
  it('should update quantity', () => {
    const item = { id: 1, name: 'Test', price: 10 };
    
    service.addItem(item);
    service.updateQuantity(1, 2);
    
    const cartItem = service.items().find(i => i.id === 1);
    expect(cartItem?.quantity).toBe(2);
    expect(service.totalPrice()).toBe(20);
  });
  
  it('should remove items', () => {
    const item = { id: 1, name: 'Test', price: 10 };
    
    service.addItem(item);
    service.removeItem(1);
    
    expect(service.items().length).toBe(0);
    expect(service.totalPrice()).toBe(0);
  });
  
  it('should clear cart', () => {
    service.addItem({ id: 1, name: 'Test1', price: 10 });
    service.addItem({ id: 2, name: 'Test2', price: 20 });
    
    service.clearCart();
    
    expect(service.items().length).toBe(0);
    expect(service.totalPrice()).toBe(0);
  });
});

// cart.component.spec.ts
describe('CartComponent', () => {
  let component: CartComponent;
  let fixture: ComponentFixture<CartComponent>;
  let cartService: CartService;
  
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [CartComponent],
      providers: [CartService]
    }).compileComponents();
    
    fixture = TestBed.createComponent(CartComponent);
    component = fixture.componentInstance;
    cartService = TestBed.inject(CartService);
    fixture.detectChanges();
  });
  
  it('should display cart items', () => {
    cartService.addItem({ id: 1, name: 'Test', price: 10 });
    fixture.detectChanges();
    
    const items = fixture.debugElement.queryAll(By.css('.cart-item'));
    expect(items.length).toBe(1);
    expect(items[0].nativeElement.textContent).toContain('Test');
  });
  
  it('should update total when changing quantity', () => {
    cartService.addItem({ id: 1, name: 'Test', price: 10 });
    fixture.detectChanges();
    
    const quantityInput = fixture.debugElement.query(By.css('.quantity-input'));
    quantityInput.nativeElement.value = '2';
    quantityInput.nativeElement.dispatchEvent(new Event('input'));
    fixture.detectChanges();
    
    const total = fixture.debugElement.query(By.css('.total'));
    expect(total.nativeElement.textContent).toContain('20');
  });
});
```

Cet exercice vous permettra de pratiquer :
- Les tests unitaires de services
- Les tests de composants
- L'utilisation des Signals dans les tests
- Les tests d'intégration
- Les mocks et les spies 