---
routeAlias: 'express-framework'
---

# Express.js Framework

- **Qu'est-ce qu'Express.js ?**
  - Framework web minimaliste pour Node.js
  - Facilite la création d'API et d'applications web

- **Routing**
  - Définition des routes et des méthodes HTTP
  - Paramètres de route et query strings

- **Middleware**
  - Fonctions intermédiaires pour traiter les requêtes/réponses
  - Middleware intégré et personnalisé

- **Templates**
  - Intégration avec des moteurs de templates (EJS, Pug, etc.)

---
routeAlias: 'exercice-creation-api-express'
---

## Exercice : Création d'une API simple avec Express

1. Initialisez un nouveau projet avec npm et installez Express.
2. Créez une API avec des routes pour lister, ajouter, mettre à jour et supprimer des éléments.
3. Utilisez un middleware pour logger les requêtes.
4. Implémentez la gestion des erreurs.

---
routeAlias: 'correction-exercice-creation-api-express'
---

## Correction de l'exercice

```javascript
const express = require('express')
const app = express()
const port = 3000

// Middleware pour parser le JSON
app.use(express.json())

// Middleware de logging
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`)
  next()
})

// Données simulées
let items = [
  { id: 1, name: 'Item 1' },
  { id: 2, name: 'Item 2' }
]

// Route pour lister tous les éléments
app.get('/items', (req, res) => {
  res.json(items)
})

// Route pour ajouter un élément
app.post('/items', (req, res) => {
  const newItem = {
    id: items.length + 1,
    name: req.body.name
  }
  items.push(newItem)
  res.status(201).json(newItem)
})

// Route pour mettre à jour un élément
app.put('/items/:id', (req, res) => {
  const id = Number.parseInt(req.params.id, 10)
  const itemIndex = items.findIndex(item => item.id === id)
  if (itemIndex > -1) {
    items[itemIndex] = { ...items[itemIndex], ...req.body }
    res.json(items[itemIndex])
  } else {
    res.status(404).send('Item not found')
  }
})

// Route pour supprimer un élément
app.delete('/items/:id', (req, res) => {
  const id = Number.parseInt(req.params.id, 10)
  items = items.filter(item => item.id !== id)
  res.status(204).send()
})

// Gestion des erreurs
app.use((err, req, res, next) => {
  console.error(err.stack)
  res.status(500).send('Something broke!')
})

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`)
})
```

Pour exécuter ce serveur, sauvegardez ce code dans un fichier `app.js`, installez les dépendances avec `npm install express`, puis exécutez-le avec `node app.js`.
