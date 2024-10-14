---
routeAlias: 'deploiement-javascript'
---

# Déploiement d'applications JavaScript

- **Hébergement statique**
  - GitHub Pages, Netlify pour les applications React

- **Hébergement de serveurs Node.js**
  - Heroku, DigitalOcean, AWS

- **Conteneurisation**
  - Docker pour les applications Node.js
  - Docker Compose pour les environnements multi-conteneurs

- **Intégration continue et déploiement continu (CI/CD)**
  - GitHub Actions, GitLab CI, Jenkins

---
routeAlias: 'exercice-mise-en-place-pipeline-deploiement'
---

## Exercice : Mise en place d'un pipeline de déploiement

1. Créez un Dockerfile pour votre application Node.js.
2. Configurez un fichier docker-compose.yml pour l'application et ses dépendances (ex: base de données).
3. Mettez en place un script de déploiement automatique.
4. Configurez un pipeline CI/CD simple (ex: avec GitHub Actions) pour tester et déployer automatiquement.

---
routeAlias: 'correction-exercice-mise-en-place-pipeline-deploiement'
---

## Correction de l'exercice

```dockerfile
# Dockerfile
FROM node:14

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

```yaml
# docker-compose.yml
version: '3'
services:
  app:
    build: .
    ports:
      - '3000:3000'
    depends_on:
      - db
  db:
    image: mongo
    volumes:
      - ./data:/data/db
```

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '14'
      - run: npm ci
      - run: npm test
      - name: Deploy to Heroku
        uses: akhileshns/heroku-deploy@v3.12.12
        with:
          heroku_api_key: ${{secrets.HEROKU_API_KEY}}
          heroku_app_name: 'your-app-name'
          heroku_email: 'your-email@example.com'
```

Ce pipeline CI/CD exécute les tests à chaque push sur la branche principale et déploie automatiquement sur Heroku si les tests réussissent.
