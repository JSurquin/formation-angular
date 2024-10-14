---
routeAlias: 'bonnes-pratiques'
---

# Bonnes pratiques de développement JavaScript

- **ESLint et Prettier**
  - Linting et formatage automatique du code

- **Design Patterns**
  - Module, Factory, Observer, etc.
  - Quand et comment les utiliser

- **Clean Code**
  - Nommage significatif
  - Fonctions courtes et focalisées

- **Principes SOLID**
  - Adaptés au contexte JavaScript/TypeScript

---
routeAlias: 'principes-solid-javascript'
---

# Parlons des principes SOLID en JavaScript

- **Single Responsibility Principle (SRP)**
  - Une fonction ou classe ne doit avoir qu'une seule raison de changer

- **Open/Closed Principle (OCP)**
  - Les entités logicielles doivent être ouvertes à l'extension, mais fermées à la modification

- **Liskov Substitution Principle (LSP)**
  - Les objets d'une superclasse doivent pouvoir être remplacés par des objets de ses sous-classes sans altérer le fonctionnement du programme

- **Interface Segregation Principle (ISP)**
  - Préférer plusieurs interfaces spécifiques plutôt qu'une interface générale

- **Dependency Inversion Principle (DIP)**
  - Dépendre des abstractions, pas des implémentations concrètes

---
routeAlias: 'exercice-refactoring-code-existant'
---

## Exercice : Refactoring d'un code existant

1. Prenez un morceau de code JavaScript existant (peut être fourni ou de votre propre projet).
2. Identifiez les violations des principes SOLID et des bonnes pratiques.
3. Refactorisez le code pour le rendre plus propre et maintenable.
4. Appliquez un ou deux design patterns appropriés.

---
routeAlias: 'correction-exercice-refactoring-code-existant'
---

## Correction de l'exercice

Voici un exemple de refactoring appliquant le principe de responsabilité unique (SRP) et le pattern Factory :

```javascript
// Avant
class User {
  constructor(name, email) {
    this.name = name
    this.email = email
  }

  save() {
    // Logique pour sauvegarder l'utilisateur dans la base de données
  }

  sendEmail(subject, body) {
    // Logique pour envoyer un email
  }
}

// Après
class User {
  constructor(name, email) {
    this.name = name
    this.email = email
  }
}

class UserRepository {
  save(user) {
    // Logique pour sauvegarder l'utilisateur dans la base de données
  }
}

class EmailService {
  sendEmail(to, subject, body) {
    // Logique pour envoyer un email
  }
}

class UserFactory {
  static createUser(name, email) {
    return new User(name, email)
  }
}

// Utilisation
const user = UserFactory.createUser('John Doe', 'john@example.com')
const userRepo = new UserRepository()
userRepo.save(user)

const emailService = new EmailService()
emailService.sendEmail(user.email, 'Bienvenue', 'Bienvenue sur notre plateforme !')
```

Ce refactoring sépare les responsabilités en différentes classes et utilise un Factory pour la création d'utilisateurs.
