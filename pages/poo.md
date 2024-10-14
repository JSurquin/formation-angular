---
routeAlias: 'poo-javascript'
---

# Programmation Orientée Objet en JavaScript

- **Classes et objets**
  - Définition de classes (ES6+)
  - Création d'instances

- **Propriétés et méthodes**
  - Constructeur
  - Méthodes de classe et d'instance

- **Héritage**
  - Mot-clé `extends`
  - Surcharge de méthodes avec `super`

- **Prototypes**
  - Chaîne de prototypes
  - Modification de prototypes

---
routeAlias: 'exercice-creation-classe-heritage'
---

## Exercice : Création d'une hiérarchie de classes

1. Créez une classe `Vehicule` avec des propriétés comme `marque` et `modele`.
2. Créez des sous-classes `Voiture` et `Moto` qui héritent de `Vehicule`.
3. Ajoutez des méthodes spécifiques à chaque sous-classe.
4. Créez des instances et appelez leurs méthodes.

---
routeAlias: 'correction-exercice-creation-classe-heritage'
---

## Correction de l'exercice

```javascript
class Vehicule {
  constructor(marque, modele) {
    this.marque = marque
    this.modele = modele
  }

  afficherInfo() {
    console.log(`${this.marque} ${this.modele}`)
  }
}

class Voiture extends Vehicule {
  constructor(marque, modele, nombreDePortes) {
    super(marque, modele)
    this.nombreDePortes = nombreDePortes
  }

  demarrer() {
    console.log(`La voiture ${this.marque} démarre.`)
  }
}

class Moto extends Vehicule {
  demarrer() {
    console.log(`La moto ${this.marque} démarre.`)
  }
}

const voiture = new Voiture('Toyota', 'Corolla', 4)
const moto = new Moto('Honda', 'CBR')

voiture.afficherInfo()
voiture.demarrer()
moto.afficherInfo()
moto.demarrer()
```