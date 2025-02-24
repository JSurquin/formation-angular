---
layout: new-section
routeAlias: 'deployment'
---

# Déploiement

---

## Préparation au déploiement

# Déploiement d'applications Angular 18/19

## Build de production

```bash
# Build standard
ng build --configuration production

# Build avec SSR
ng build && ng run my-app:server
```

---

## Configuration des environnements

```typescript
// environment.prod.ts
export const environment = {
  production: true,
  apiUrl: 'https://api.production.com',
  features: {
    analytics: true,
    monitoring: true
  }
};

// app.config.ts
import { environment } from './environments/environment';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(
      withInterceptors([authInterceptor])
    ),
    {
      provide: APP_CONFIG,
      useValue: environment
    }
  ]
};
```

---

## Optimisation du bundle

```json
// angular.json
{
  "projects": {
    "my-app": {
      "architect": {
        "build": {
          "configurations": {
            "production": {
              "optimization": true,
              "outputHashing": "all",
              "sourceMap": false,
              "namedChunks": false,
              "aot": true,
              "extractLicenses": true,
              "vendorChunk": false,
              "buildOptimizer": true,
              "budgets": [
                {
                  "type": "initial",
                  "maximumWarning": "2mb",
                  "maximumError": "5mb"
                }
              ]
            }
          }
        }
      }
    }
  }
}
```

---

## Server-Side Rendering (SSR)

```typescript
// server.ts
import { APP_BASE_HREF } from '@angular/common';
import { CommonEngine } from '@angular/ssr';
import express from 'express';
import { fileURLToPath } from 'url';
import { dirname, join, resolve } from 'path';
import bootstrap from './src/main.server';

const app = express();
const port = process.env.PORT || 4000;
const __dirname = dirname(fileURLToPath(import.meta.url));
const distFolder = join(__dirname, '../dist/my-app/browser');

// Servir les fichiers statiques
app.use(express.static(distFolder));

// SSR avec compression
app.get('*', async (req, res, next) => {
  try {
    const engine = new CommonEngine();
    const html = await engine.render({
      bootstrap,
      documentFilePath: resolve(distFolder, 'index.html'),
      url: req.url,
      providers: [{ provide: APP_BASE_HREF, useValue: req.baseUrl }]
    });
    
    res.send(html);
  } catch (err) {
    next(err);
  }
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

---

## Docker

```dockerfile
# Dockerfile
# Stage 1: Build
FROM node:20-alpine as builder

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Run
FROM node:20-alpine

WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package*.json ./
RUN npm install --production

EXPOSE 4000
CMD ["npm", "run", "serve:ssr"]
```

---

## CI/CD avec GitHub Actions

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run tests
      run: npm test -- --no-watch --no-progress
      
    - name: Build
      run: npm run build
      
    - name: Deploy to production
      uses: azure/webapps-deploy@v2
      with:
        app-name: 'my-angular-app'
        publish-profile: ${{ secrets.AZURE_PUBLISH_PROFILE }}
        package: ./dist/my-app
```

---

## Nginx Configuration

```nginx
# nginx.conf
server {
    listen 80;
    server_name example.com;
    root /usr/share/nginx/html;
    index index.html;

    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript;
    gzip_min_length 1000;

    # Cache control
    location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
        expires 1y;
        add_header Cache-Control "public, no-transform";
    }

    # Angular routes
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

---

## Monitoring et Analytics

```typescript
// app.config.ts
import { ApplicationConfig } from '@angular/core';
import { provideClientHydration } from '@angular/platform-browser';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideClientHydration(),
    // Monitoring
    {
      provide: ErrorHandler,
      useClass: GlobalErrorHandler
    },
    // Analytics
    {
      provide: APP_INITIALIZER,
      useFactory: (analytics: AnalyticsService) => () => {
        analytics.initialize({
          trackingId: environment.analyticsId
        });
      },
      deps: [AnalyticsService],
      multi: true
    }
  ]
};
```

---

# Exercice : Déploiement complet

Configurez un déploiement complet avec :
- Build optimisé
- SSR
- Docker
- CI/CD
- Monitoring

```bash
# 1. Préparation du build
npm run lint
npm test
npm run build:ssr

# 2. Construction de l'image Docker
docker build -t my-angular-app .
docker run -p 4000:4000 my-angular-app

# 3. Déploiement avec monitoring
```

```typescript
// monitoring.service.ts
@Injectable({
  providedIn: 'root'
})
export class MonitoringService {
  private errorCount = signal(0);
  private performanceMetrics = signal<PerformanceMetrics>({
    fcp: 0,
    lcp: 0,
    cls: 0
  });
  
  trackError(error: Error) {
    this.errorCount.update(count => count + 1);
    // Envoi à un service de monitoring
    this.sendToMonitoring({
      type: 'error',
      message: error.message,
      stack: error.stack
    });
  }
  
  trackPerformance() {
    if ('performance' in window) {
      // Web Vitals
      const observer = new PerformanceObserver((list) => {
        for (const entry of list.getEntries()) {
          this.updateMetrics(entry);
        }
      });
      
      observer.observe({ entryTypes: ['largest-contentful-paint'] });
    }
  }
  
  private updateMetrics(entry: PerformanceEntry) {
    this.performanceMetrics.update(metrics => ({
      ...metrics,
      [entry.entryType]: entry.startTime
    }));
  }
}
```

Cet exercice vous permettra de mettre en place un déploiement professionnel avec :
- Tests automatisés
- Build optimisé
- Containerisation
- Monitoring en production
- Métriques de performance 

---

layout: new-section
routeAlias: 'deploiement-vercel'
---

# Déploiement avec Vercel

---

## Configuration Vercel

1. Installez Vercel CLI :
```bash
npm i -g vercel
```

2. Connectez-vous à votre compte :
```bash
vercel login
```

---

## Déploiement automatique

1. Créez un fichier `vercel.json` à la racine :
```json
{
  "version": 2,
  "builds": [
    {
      "src": "dist/*",
      "use": "@vercel/static"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/index.html"
    }
  ]
}
```

2. Configurez le build :
```json
{
  "scripts": {
    "build": "ng build --configuration production"
  }
}
```

---

## Intégration Continue

1. Connectez votre repo GitHub à Vercel

2. Activez les déploiements automatiques :
- Sur chaque push sur main
- Preview sur les Pull Requests
- Rollback automatique en cas d'erreur

3. Variables d'environnement :
```bash
vercel env add PRODUCTION_API_URL
```

---

## Optimisations Vercel

- Edge Functions pour l'API
- Image Optimization
- Analytics intégrées
- Monitoring temps réel
- Certificats SSL automatiques

```typescript
// next.config.js
module.exports = {
  images: {
    domains: ['assets.example.com'],
  },
}
```

---

## Commandes utiles

```bash
# Déploiement manuel
vercel

# Déploiement en production
vercel --prod

# Voir les logs
vercel logs

# Lister les déploiements
vercel list

# Supprimer un déploiement
vercel remove <deployment-id>
```
