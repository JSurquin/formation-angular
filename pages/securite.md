---
routeAlias: 'securite-javascript'
---

# Sécurité en JavaScript

- **Cross-Site Scripting (XSS)**
  - Échappement des sorties
  - Content Security Policy (CSP)

- **Cross-Site Request Forgery (CSRF)**
  - Tokens CSRF
  - SameSite cookies

- **Injection de code**
  - Éviter l'utilisation de `eval()`
  - Validation et assainissement des entrées utilisateur

- **Sécurité des dépendances**
  - Audit régulier des dépendances avec npm audit
  - Mise à jour des packages vulnérables

---
routeAlias: 'exercice-securisation-application-web'
---

## Exercice : Sécurisation d'une application web

1. Implémentez une protection contre les attaques XSS dans une application React.
2. Ajoutez des en-têtes de sécurité à une API Express (ex: helmet middleware).
3. Mettez en place une validation robuste des entrées utilisateur côté client et serveur.
4. Configurez CORS correctement pour votre API.

---
routeAlias: 'correction-exercice-securisation-application-web'
---

## Correction de l'exercice

```javascript
// Protection XSS dans React
import DOMPurify from 'dompurify'

function SafeHTML({ html }) {
  return <div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(html) }} />
}

// En-têtes de sécurité avec Express
const express = require('express')
const helmet = require('helmet')

const app = express()
app.use(helmet())

// Validation des entrées avec Joi
const Joi = require('joi')

const schema = Joi.object({
  username: Joi.string().alphanum().min(3).max(30).required(),
  email: Joi.string().email().required()
})

app.post('/user', (req, res) => {
  const { error } = schema.validate(req.body)
  if (error) {
    return res.status(400).send(error.details[0].message)
  }
  // Traitement de la requête...
})

// Configuration CORS
const cors = require('cors')

app.use(cors({
  origin: 'https://monapp.com',
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization']
}))
```

Ces exemples montrent comment implémenter diverses mesures de sécurité dans une application web JavaScript.
