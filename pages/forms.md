---
layout: new-section
routeAlias: 'forms-validation'
---

# Formulaires Angular

---

## Commandes CLI pour les Formulaires

```bash
# Générer un composant de formulaire réactif
ng generate component features/contact-form --type form

# Générer un validator personnalisé
ng generate validator shared/validators/password-strength

# Générer un formulaire avec validation
ng generate component features/registration --type form --spec

# Générer un formulaire avec états asynchrones
ng generate component features/signup --type form --signals

# Générer un service de validation
ng generate service shared/services/form-validation

# Générer un composant de formulaire dynamique
ng generate component shared/components/dynamic-form --type form

# Générer un formulaire avec gestion d'état
ng generate component features/checkout --type form --state
```

---

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

---

Cet exercice vous à permis de pratiquer :
- Les formulaires imbriqués
- La validation personnalisée
- La validation croisée
- La gestion des FormArray
- Les états de chargement avec Signals 

---
layout: exercices
routeAlias: 'exercice-formulaire-post'
---

# Exercice : Formulaire de Post

---

## 1. Structure du Template

```typescript
// features/posts/post-form.component.ts
@Component({
  selector: 'app-post-form',
  standalone: true,
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      <div class="form-field">
        <label for="title">Titre</label>
        <input id="title" type="text" formControlName="title">
        @if (titleErrors()) {
          <span class="error">{{ titleErrors() }}</span>
        }
      </div>

      <div class="form-field">
        <label for="content">Contenu</label>
        <textarea id="content" formControlName="content" rows="10"></textarea>
        @if (contentErrors()) {
          <span class="error">{{ contentErrors() }}</span>
        }
      </div>

      <button type="submit" [disabled]="form.invalid || saving()">
        {{ saving() ? 'Enregistrement...' : 'Publier' }}
      </button>
    </form>
  `
})
```

---

## 2. Configuration du Formulaire

```typescript
export class PostFormComponent {
  private postService = inject(PostService)
  private router = inject(Router)

  form = new FormGroup({
    title: new FormControl('', [
      Validators.required,
      Validators.minLength(3)
    ]),
    content: new FormControl('', [
      Validators.required,
      Validators.minLength(50)
    ])
  })

  saving = signal(false)
```

---

## 3. Gestion des Erreurs

```typescript
  titleErrors = computed(() => {
    const control = this.form.get('title')
    if (control?.errors && control.touched) {
      if (control.errors['required']) return 'Le titre est requis'
      if (control.errors['minlength']) return 'Le titre doit faire au moins 3 caractères'
    }
    return null
  })

  contentErrors = computed(() => {
    const control = this.form.get('content')
    if (control?.errors && control.touched) {
      if (control.errors['required']) return 'Le contenu est requis'
      if (control.errors['minlength']) return 'Le contenu doit faire au moins 50 caractères'
    }
    return null
  })
```

---

## 4. Soumission du Formulaire

```typescript
  async onSubmit() {
    if (this.form.valid) {
      this.saving.set(true)
      try {
        await firstValueFrom(this.postService.createPost({
          ...this.form.value,
          author: 'Utilisateur actuel',
          date: new Date(),
          excerpt: this.form.value.content?.slice(0, 100) + '...'
        }))
        this.router.navigate(['/posts'])
      } finally {
        this.saving.set(false)
      }
    }
  }
}
```

---

## Exercice : Formulaires du Mini-Blog

### Formulaire de connexion (Template-driven)

```typescript
@Component({
  selector: 'app-login-form',
  template: `
    <form #loginForm="ngForm" (ngSubmit)="onSubmit(loginForm)">
      <div class="form-group">
        <label for="email">Email</label>
        <input
          type="email"
          id="email"
          name="email"
          [(ngModel)]="loginData.email"
          required
          email
          #email="ngModel"
        >
        @if (email.invalid && email.touched) {
          <span class="error">Email invalide</span>
        }
      </div>

      <div class="form-group">
        <label for="password">Mot de passe</label>
        <input
          type="password"
          id="password"
          name="password"
          [(ngModel)]="loginData.password"
          required
          minlength="6"
          #password="ngModel"
        >
        @if (password.invalid && password.touched) {
          <span class="error">
            Le mot de passe doit contenir au moins 6 caractères
          </span>
        }
      </div>

      <button type="submit" [disabled]="loginForm.invalid">
        Se connecter
      </button>
    </form>
  `
})
export class LoginFormComponent {
  loginData = {
    email: '',
    password: ''
  };

  onSubmit(form: NgForm) {
    if (form.valid) {
      // Traitement du formulaire
      console.log(this.loginData);
    }
  }
}
```

---

### Formulaire d'article (Reactive Form)

```typescript
@Component({
  selector: 'app-post-form',
  template: `
    <form [formGroup]="postForm" (ngSubmit)="onSubmit()">
      <div class="form-group">
        <label for="title">Titre</label>
        <input
          type="text"
          id="title"
          formControlName="title"
        >
        @if (titleErrors()) {
          <span class="error">{{ titleErrors() }}</span>
        }
      </div>

      <div class="form-group">
        <label for="content">Contenu</label>
        <textarea
          id="content"
          formControlName="content"
          rows="10"
        ></textarea>
        @if (contentErrors()) {
          <span class="error">{{ contentErrors() }}</span>
        }
      </div>

      <button type="submit" [disabled]="!postForm.valid || submitting()">
        {{ submitting() ? 'Publication...' : 'Publier' }}
      </button>
    </form>
  `
})
export class PostFormComponent {
  submitting = signal(false);
  
  postForm = new FormGroup({
    title: new FormControl('', [
      Validators.required,
      Validators.minLength(5)
    ]),
    content: new FormControl('', [
      Validators.required,
      Validators.minLength(50)
    ])
  });

  titleErrors = computed(() => {
    const control = this.postForm.get('title');
    if (control?.errors && control.touched) {
      if (control.errors['required']) return 'Le titre est requis';
      if (control.errors['minlength']) return 'Le titre doit contenir au moins 5 caractères';
    }
    return null;
  });

  contentErrors = computed(() => {
    const control = this.postForm.get('content');
    if (control?.errors && control.touched) {
      if (control.errors['required']) return 'Le contenu est requis';
      if (control.errors['minlength']) return 'Le contenu doit contenir au moins 50 caractères';
    }
    return null;
  });

  async onSubmit() {
    if (this.postForm.valid) {
      this.submitting.set(true);
      try {
        // Traitement du formulaire
        console.log(this.postForm.value);
      } finally {
        this.submitting.set(false);
      }
    }
  }
}
```

---
layout: exercices
routeAlias: 'exercice-blog-forms'
---

## Mini-Blog : Formulaires Réactifs

---

### Formulaire de connexion

```typescript
// login.component.ts
@Component({
  template: `
    <div class="max-w-md mx-auto mt-8 p-6 bg-white rounded-lg shadow-lg">
      <h2 class="text-2xl font-bold mb-6">Connexion</h2>
      
      <form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
        <div class="mb-4">
          <label class="block text-gray-700 mb-2" for="email">
            Email
          </label>
          <input
            id="email"
            type="email"
            formControlName="email"
            class="w-full px-3 py-2 border rounded"
            [class.border-red-500]="email.invalid && email.touched"
          >
          @if (email.invalid && email.touched) {
            <p class="text-red-500 text-sm mt-1">
              Email invalide
            </p>
          }
        </div>
        
        <div class="mb-6">
          <label class="block text-gray-700 mb-2" for="password">
            Mot de passe
          </label>
          <input
            id="password"
            type="password"
            formControlName="password"
            class="w-full px-3 py-2 border rounded"
            [class.border-red-500]="password.invalid && password.touched"
          >
          @if (password.invalid && password.touched) {
            <p class="text-red-500 text-sm mt-1">
              Le mot de passe doit faire au moins 6 caractères
            </p>
          }
        </div>
        
        <button
          type="submit"
          [disabled]="loginForm.invalid"
          class="w-full bg-blue-500 text-white py-2 px-4 rounded
                 hover:bg-blue-600 disabled:bg-gray-400"
        >
          Se connecter
        </button>
        
        @if (error()) {
          <p class="text-red-500 mt-4">
            {{ error() }}
          </p>
        }
      </form>
    </div>
  `
})
export class LoginComponent {
  private authService = inject(AuthService)
  private router = inject(Router)
  private route = inject(ActivatedRoute)
  
  loginForm = new FormGroup({
    email: new FormControl('', [
      Validators.required,
      Validators.email
    ]),
    password: new FormControl('', [
      Validators.required,
      Validators.minLength(6)
    ])
  })
  
  error = signal<string | null>(null)
  
  get email() { return this.loginForm.get('email')! }
  get password() { return this.loginForm.get('password')! }
  
  onSubmit() {
    if (this.loginForm.valid) {
      const { email, password } = this.loginForm.value
      
      if (this.authService.login(email!, password!)) {
        const returnUrl = this.route.snapshot.queryParams['returnUrl'] || '/'
        this.router.navigateByUrl(returnUrl)
      } else {
        this.error.set('Email ou mot de passe incorrect')
      }
    }
  }
}
```

---

### Formulaire de création d'article

```typescript
// post-form.component.ts
@Component({
  template: `
    <div class="max-w-2xl mx-auto mt-8 p-6 bg-white rounded-lg shadow-lg">
      <h2 class="text-2xl font-bold mb-6">
        {{ isEditing ? 'Modifier l\'article' : 'Nouvel article' }}
      </h2>
      
      <form [formGroup]="postForm" (ngSubmit)="onSubmit()">
        <div class="mb-4">
          <label class="block text-gray-700 mb-2" for="title">
            Titre
          </label>
          <input
            id="title"
            type="text"
            formControlName="title"
            class="w-full px-3 py-2 border rounded"
            [class.border-red-500]="title.invalid && title.touched"
          >
          @if (title.invalid && title.touched) {
            <p class="text-red-500 text-sm mt-1">
              Le titre est requis et doit faire au moins 3 caractères
            </p>
          }
        </div>
        
        <div class="mb-4">
          <label class="block text-gray-700 mb-2" for="content">
            Contenu
          </label>
          <textarea
            id="content"
            formControlName="content"
            rows="6"
            class="w-full px-3 py-2 border rounded"
            [class.border-red-500]="content.invalid && content.touched"
          ></textarea>
          @if (content.invalid && content.touched) {
            <p class="text-red-500 text-sm mt-1">
              Le contenu est requis et doit faire au moins 10 caractères
            </p>
          }
        </div>
        
        <div class="mb-6">
          <label class="block text-gray-700 mb-2" for="image">
            Image (optionnelle)
          </label>
          <input
            id="image"
            type="file"
            accept="image/*"
            (change)="onFileSelected($event)"
            class="w-full"
          >
        </div>
        
        <div class="flex gap-4">
          <button
            type="submit"
            [disabled]="postForm.invalid"
            class="bg-blue-500 text-white py-2 px-4 rounded
                   hover:bg-blue-600 disabled:bg-gray-400"
          >
            {{ isEditing ? 'Mettre à jour' : 'Publier' }}
          </button>
          
          <button
            type="button"
            (click)="cancel()"
            class="bg-gray-500 text-white py-2 px-4 rounded
                   hover:bg-gray-600"
          >
            Annuler
          </button>
        </div>
      </form>
    </div>
  `
})
export class PostFormComponent implements OnInit {
  private postService = inject(PostService)
  private router = inject(Router)
  private route = inject(ActivatedRoute)
  
  postForm = new FormGroup({
    title: new FormControl('', [
      Validators.required,
      Validators.minLength(3)
    ]),
    content: new FormControl('', [
      Validators.required,
      Validators.minLength(10)
    ])
  })
  
  selectedImage: File | null = null
  isEditing = false
  
  get title() { return this.postForm.get('title')! }
  get content() { return this.postForm.get('content')! }
  
  ngOnInit() {
    const id = this.route.snapshot.paramMap.get('id')
    if (id) {
      this.isEditing = true
      const post = this.postService.getPost(Number(id))
      if (post) {
        this.postForm.patchValue({
          title: post.title,
          content: post.content
        })
      }
    }
  }
  
  onFileSelected(event: Event) {
    const file = (event.target as HTMLInputElement).files?.[0]
    if (file) {
      this.selectedImage = file
    }
  }
  
  onSubmit() {
    if (this.postForm.valid) {
      const { title, content } = this.postForm.value
      
      if (this.isEditing) {
        const id = Number(this.route.snapshot.paramMap.get('id'))
        this.postService.updatePost(id, {
          title: title!,
          content: content!
        })
      } else {
        this.postService.addPost({
          title: title!,
          content: content!,
          author: 'Utilisateur connecté' // À améliorer avec l'authentification
        })
      }
      
      this.router.navigate(['/blog'])
    }
  }
  
  cancel() {
    this.router.navigate(['/blog'])
  }
}
