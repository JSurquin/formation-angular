---
layout: new-section
routeAlias: 'forms-validation'
---

# Formulaires Angular

---

## Types de formulaires

# Formulaires dans Angular 18/19

## Formulaires réactifs modernes avec Signals

```typescript
@Component({
  selector: 'app-signup',
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      <div>
        <label for="email">Email</label>
        <input id="email" type="email" formControlName="email">
        @if (emailErrors()) {
          <span class="error">{{ emailErrors() }}</span>
        }
      </div>
      
      <div>
        <label for="password">Mot de passe</label>
        <input id="password" type="password" formControlName="password">
        @if (passwordErrors()) {
          <span class="error">{{ passwordErrors() }}</span>
        }
      </div>
      
      <button type="submit" [disabled]="!form.valid">
        S'inscrire
      </button>
    </form>
  `
})
export class SignupComponent {
  form = new FormGroup({
    email: new FormControl('', [Validators.required, Validators.email]),
    password: new FormControl('', [
      Validators.required,
      Validators.minLength(8)
    ])
  });
  
  emailErrors = computed(() => {
    const control = this.form.get('email');
    if (control?.errors && control.touched) {
      if (control.errors['required']) return 'Email requis';
      if (control.errors['email']) return 'Email invalide';
    }
    return null;
  });
  
  passwordErrors = computed(() => {
    const control = this.form.get('password');
    if (control?.errors && control.touched) {
      if (control.errors['required']) return 'Mot de passe requis';
      if (control.errors['minlength']) return 'Minimum 8 caractères';
    }
    return null;
  });
  
  onSubmit() {
    if (this.form.valid) {
      console.log(this.form.value);
    }
  }
}
```

---

## Validation personnalisée

```typescript
// Validateur personnalisé
function passwordStrength(): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const value = control.value;
    
    if (!value) return null;
    
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
  };
}

// Utilisation
@Component({
  template: `
    <input formControlName="password">
    @if (passwordErrors()) {
      <ul class="errors">
        @for (error of passwordErrors(); track error) {
          <li>{{ error }}</li>
        }
      </ul>
    }
  `
})
export class PasswordComponent {
  passwordControl = new FormControl('', [
    Validators.required,
    passwordStrength()
  ]);
  
  passwordErrors = computed(() => {
    const errors = [];
    const control = this.passwordControl;
    
    if (control.errors?.['passwordStrength']) {
      const strength = control.errors['passwordStrength'];
      if (!strength.hasNumber) errors.push('Doit contenir un chiffre');
      if (!strength.hasUpper) errors.push('Doit contenir une majuscule');
      if (!strength.hasLower) errors.push('Doit contenir une minuscule');
      if (!strength.hasSpecial) errors.push('Doit contenir un caractère spécial');
    }
    
    return errors;
  });
}
```

---

## Formulaires dynamiques

```typescript
interface DynamicField {
  name: string;
  label: string;
  type: 'text' | 'email' | 'number';
  validators: ValidatorFn[];
}

@Component({
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      @for (field of fields; track field.name) {
        <div>
          <label [for]="field.name">{{ field.label }}</label>
          <input
            [id]="field.name"
            [type]="field.type"
            [formControlName]="field.name"
          >
          @if (getFieldErrors(field.name)()) {
            <span class="error">
              {{ getFieldErrors(field.name)() }}
            </span>
          }
        </div>
      }
      <button type="submit">Envoyer</button>
    </form>
  `
})
export class DynamicFormComponent {
  fields: DynamicField[] = [
    {
      name: 'name',
      label: 'Nom',
      type: 'text',
      validators: [Validators.required]
    },
    {
      name: 'email',
      label: 'Email',
      type: 'email',
      validators: [Validators.required, Validators.email]
    },
    {
      name: 'age',
      label: 'Âge',
      type: 'number',
      validators: [Validators.required, Validators.min(18)]
    }
  ];
  
  form = new FormGroup(
    this.fields.reduce((group, field) => ({
      ...group,
      [field.name]: new FormControl('', field.validators)
    }), {})
  );
  
  getFieldErrors(fieldName: string) {
    return computed(() => {
      const control = this.form.get(fieldName);
      if (control?.errors && control.touched) {
        return Object.keys(control.errors)
          .map(key => this.getErrorMessage(key))
          .join(', ');
      }
      return null;
    });
  }
  
  private getErrorMessage(key: string): string {
    const messages: Record<string, string> = {
      required: 'Ce champ est requis',
      email: 'Email invalide',
      min: 'Valeur minimale non atteinte'
    };
    return messages[key] || 'Erreur de validation';
  }
}
```

---

## Formulaires imbriqués

```typescript
@Component({
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      <div formGroupName="personal">
        <input formControlName="firstName">
        <input formControlName="lastName">
      </div>
      
      <div formGroupName="address">
        <input formControlName="street">
        <input formControlName="city">
        <input formControlName="zipCode">
      </div>
      
      <div formArrayName="phones">
        @for (phone of phones.controls; track phone) {
          <input [formControl]="phone">
        }
        <button type="button" (click)="addPhone()">
          Ajouter téléphone
        </button>
      </div>
    </form>
  `
})
export class NestedFormComponent {
  form = new FormGroup({
    personal: new FormGroup({
      firstName: new FormControl(''),
      lastName: new FormControl('')
    }),
    address: new FormGroup({
      street: new FormControl(''),
      city: new FormControl(''),
      zipCode: new FormControl('')
    }),
    phones: new FormArray([
      new FormControl('')
    ])
  });
  
  get phones() {
    return this.form.get('phones') as FormArray;
  }
  
  addPhone() {
    this.phones.push(new FormControl(''));
  }
}
```

---

# Exercice : Formulaire d'inscription complet

Créez un formulaire d'inscription avec validation avancée :

```typescript
interface RegistrationForm {
  personal: {
    firstName: string;
    lastName: string;
    email: string;
  };
  credentials: {
    password: string;
    confirmPassword: string;
  };
  preferences: {
    newsletter: boolean;
    notifications: string[];
  };
}

@Component({
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      <!-- Informations personnelles -->
      <div formGroupName="personal">
        <!-- ... champs personnels ... -->
      </div>
      
      <!-- Credentials avec validation croisée -->
      <div formGroupName="credentials">
        <input type="password" formControlName="password">
        <input type="password" formControlName="confirmPassword">
        @if (passwordMatchError()) {
          <span class="error">
            Les mots de passe ne correspondent pas
          </span>
        }
      </div>
      
      <!-- Préférences -->
      <div formGroupName="preferences">
        <input type="checkbox" formControlName="newsletter">
        
        <div formArrayName="notifications">
          @for (notification of notificationTypes; track notification) {
            <label>
              <input type="checkbox" 
                     [value]="notification"
                     (change)="onNotificationChange($event, notification)">
              {{ notification }}
            </label>
          }
        </div>
      </div>
      
      <button type="submit" [disabled]="!form.valid || submitting()">
        @if (submitting()) {
          Inscription en cours...
        } @else {
          S'inscrire
        }
      </button>
    </form>
  `
})
export class RegistrationComponent {
  form = new FormGroup({
    personal: new FormGroup({
      firstName: new FormControl('', Validators.required),
      lastName: new FormControl('', Validators.required),
      email: new FormControl('', [Validators.required, Validators.email])
    }),
    credentials: new FormGroup({
      password: new FormControl('', [
        Validators.required,
        passwordStrength()
      ]),
      confirmPassword: new FormControl('', Validators.required)
    }, { validators: passwordMatch() }),
    preferences: new FormGroup({
      newsletter: new FormControl(false),
      notifications: new FormArray([])
    })
  });
  
  notificationTypes = ['email', 'sms', 'push'];
  submitting = signal(false);
  
  // ... logique de validation et de soumission
}
```

Cet exercice vous permettra de pratiquer :
- Les formulaires imbriqués
- La validation personnalisée
- La validation croisée
- La gestion des FormArray
- Les états de chargement avec Signals 