---
routeAlias: 'tendances-futures'
---

# Tendances futures en JavaScript

- **ECMAScript proposals**
  - Nouvelles fonctionnalités de langage en cours de discussion

- **WebAssembly**
  - Exécution de code à haute performance dans le navigateur

- **Serverless JavaScript**
  - AWS Lambda, Azure Functions, Google Cloud Functions

- **JAMstack**
  - JavaScript, APIs, et Markup pour des sites web performants et sécurisés

- **Progressive Web Apps (PWA)**
  - Applications web qui offrent une expérience similaire aux applications natives

---
routeAlias: 'exercice-exploration-nouvelles-fonctionnalites'
---

## Exercice : Exploration des nouvelles fonctionnalités

1. Explorez une proposition ECMAScript récente et discutez de son impact potentiel.
2. Créez une petite fonction serverless et déployez-la sur AWS Lambda ou similaire.
3. Convertissez une application React existante en Progressive Web App.
4. Discutez de l'impact potentiel de ces nouvelles technologies sur vos projets actuels ou futurs.

---
routeAlias: 'correction-exercice-exploration-nouvelles-fonctionnalites'
---

## Correction de l'exercice

Voici un exemple d'une fonction serverless simple pour AWS Lambda :

```javascript
exports.handler = async (event) => {
  const name = event.queryStringParameters.name || 'World'
  const response = {
    statusCode: 200,
    body: JSON.stringify(`Hello, ${name}!`)
  }
  return response
}
```

Pour convertir une application React en PWA, vous devrez ajouter un fichier manifest.json et un service worker. Voici un exemple de service worker basique :

```javascript
globalThis.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open('v1').then((cache) => {
      return cache.addAll([
        '/',
        '/index.html',
        '/styles.css',
        '/app.js'
      ])
    })
  )
})

globalThis.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then((response) => {
      return response || fetch(event.request)
    })
  )
})
```

Ces exemples illustrent comment commencer à explorer et implémenter certaines des tendances futures en JavaScript.
