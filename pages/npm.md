---
routeAlias: 'npm'
---

# npm et gestion des dépendances

- **Qu'est-ce que npm ?**
  - Gestionnaire de paquets pour JavaScript
  - Registre de paquets open-source

- **Installation et configuration**
  - Installation avec Node.js
  - Fichier `package.json`

- **Commandes de base**
  - `npm init`
  - `npm install`
  - `npm update`
  - `npm run`

- **Gestion des dépendances**
  - Dépendances de production vs de développement
  - Versions sémantiques

---
routeAlias: 'installation-npm'
---

# Installation de npm

- npm est installé automatiquement avec Node.js
- Vérifier l'installation : `npm --version`

> Si npm n'est pas reconnu, assurez-vous que Node.js est correctement installé et que son répertoire est dans votre PATH.

- Mettre à jour npm : `npm install -g npm@latest`

---
routeAlias: 'exercice-mise-en-place-projet-npm'
---

## Exercice : Mise en place d'un projet avec npm

1. Initialisez un nouveau projet avec npm.
2. Ajoutez quelques dépendances populaires (ex: lodash pour les utilitaires).
3. Créez un script npm personnalisé dans `package.json`.
4. Installez une dépendance de développement (ex: Jest pour les tests).

---
routeAlias: 'correction-exercice-mise-en-place-projet-npm'
---

## Correction de l'exercice

```bash
# Initialiser un nouveau projet
npm init -y

# Ajouter une dépendance
npm install lodash

# Ajouter un script dans package.json
# "scripts": {
#   "start": "node index.js"
# }

# Installer une dépendance de développement
npm install --save-dev jest
```

Contenu de `package.json` après ces opérations :

```json
{
  "name": "mon-projet",
  "version": "1.0.0",
  "scripts": {
    "start": "node index.js",
    "test": "jest"
  },
  "dependencies": {
    "lodash": "^4.17.21"
  },
  "devDependencies": {
    "jest": "^27.0.6"
  }
}
```
