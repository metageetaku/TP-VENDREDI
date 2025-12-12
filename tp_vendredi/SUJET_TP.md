# TP CI/CD - Intégration des Tests dans une Pipeline

## Objectif pédagogique

Mettre en place une pipeline CI/CD complète intégrant des tests automatisés Cypress. À la fin de ce TP, vous serez capable de :

- Configurer une pipeline CI/CD avec GitHub Actions (ou GitLab CI)
- Intégrer des tests E2E Cypress dans la pipeline
- Construire et tagger une image Docker conditionnellement au succès des tests
- Comprendre le workflow de déploiement continu

---

## Contexte

Vous rejoignez une équipe de développement qui a créé **BugTrack**, une application de suivi de bugs. L'application est fonctionnelle et des tests Cypress ont été écrits, mais **aucune pipeline CI/CD n'est en place**.

Votre mission : **Configurer la pipeline pour automatiser les tests et le build de l'image Docker**.

---

## Prérequis

- Git installé
- Node.js 18+ installé
- Docker installé
- Un compte GitHub ou GitLab

---

## Structure du projet fourni

```
bug-tracker/
├── public/
│   ├── index.html           # Interface utilisateur
│   ├── css/
│   │   └── styles.css       # Styles (dark theme)
│   └── js/
│       └── app.js           # Logique frontend
├── src/
│   └── server.js            # API REST Express.js
├── cypress/
│   ├── e2e/
│   │   └── bug-tracker.cy.js  # 50+ Tests E2E (FOURNIS)
│   ├── fixtures/
│   │   └── bugs.json
│   └── support/
│       ├── commands.js      # Commandes personnalisées
│       └── e2e.js
├── cypress.config.js
├── Dockerfile
├── package.json
└── .gitignore
```

---

## Fonctionnalités de l'application BugTrack

- **Gestion des bugs** : Création, modification, suppression
- **Workflow de statuts** : Open → In Progress → Resolved → Closed (+ Reopened)
- **Priorités** : Critical, High, Medium, Low
- **Sévérités** : Blocker, Major, Minor, Trivial
- **Filtres avancés** : Par statut, priorité, sévérité, assigné
- **Recherche** : Par titre, description ou ID
- **2 vues** : Liste et Tableau Kanban
- **Commentaires** : Sur chaque bug
- **3 utilisateurs** : Alice (Dev), Bob (QA), Charlie (Lead)

---

## Étape 0 : Découverte du projet (20 min)

### 0.1 Cloner et lancer le projet

```bash
# Initialiser le dépôt git
cd bug-tracker
git init
git add .
git commit -m "Initial commit"

# Installer les dépendances
npm install

# Lancer l'application
npm start
```

Ouvrez http://localhost:3000 et testez l'application :
- Créez un bug
- Changez son statut
- Filtrez par priorité
- Ajoutez un commentaire
- Testez la vue Kanban

### 0.2 Lancer les tests en local

```bash
# Ouvrir Cypress en mode interactif
npm run test:open

# Ou lancer les tests en mode headless
npm run test:ci
```

**Questions à vous poser :**
- Combien de tests passent ? 
- Quels scénarios sont testés ?
- Comment les tests sont-ils structurés ?
- Quelles commandes personnalisées sont disponibles ?

---

## Étape 1 : Créer le dépôt distant (10 min)

1. Créez un nouveau dépôt sur GitHub (ou GitLab)
2. Poussez votre code :

```bash
git remote add origin <URL_DE_VOTRE_DEPOT>
git branch -M main
git push -u origin main
```

---

## Étape 2 : Configurer la Pipeline CI/CD (45 min)

### Option A : GitHub Actions

Créez le fichier `.github/workflows/ci-cd.yml` :

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  # ===========================================
  # JOB 1 : Tests
  # ===========================================
  test:
    runs-on: ubuntu-latest

    steps:
      # TODO: Checkout du code

      # TODO: Setup Node.js (version 20)

      # TODO: Installer les dépendances

      # TODO: Lancer les tests Cypress
      # Indice: Utilisez l'action officielle cypress-io/github-action@v6

      # TODO: Uploader les artefacts (vidéos/screenshots) en cas d'échec

  # ===========================================
  # JOB 2 : Build Docker
  # ===========================================
  build:
    runs-on: ubuntu-latest
    needs: test  # Ce job dépend du succès des tests
    if: github.ref == 'refs/heads/main'  # Seulement sur main

    steps:
      # TODO: Checkout du code

      # TODO: Login au registry Docker (GitHub Container Registry)

      # TODO: Build de l'image Docker avec les tags appropriés
      # Tags suggérés:
      #   - latest
      #   - v1.0.0-${{ github.run_number }}

      # TODO: Push de l'image vers le registry
```

### Option B : GitLab CI

Créez le fichier `.gitlab-ci.yml` :

```yaml
stages:
  - test
  - build

variables:
  NODE_VERSION: "20"

# ===========================================
# JOB 1 : Tests
# ===========================================
test:
  stage: test
  image: cypress/browsers:node-20.9.0-chrome-118.0.5993.88-1-ff-118.0.2-edge-118.0.2088.46-1

  script:
    # TODO: Installer les dépendances

    # TODO: Lancer les tests Cypress
    # Indice: npm run test:ci

  artifacts:
    # TODO: Configurer les artefacts pour les vidéos/screenshots
    when: on_failure
    paths:
      - cypress/videos
      - cypress/screenshots
    expire_in: 1 week

# ===========================================
# JOB 2 : Build Docker
# ===========================================
build:
  stage: build
  image: docker:latest
  services:
    - docker:dind

  # TODO: Configurer les conditions d'exécution
  # Ce job ne doit s'exécuter que si:
  # - Les tests sont passés
  # - On est sur la branche main

  script:
    # TODO: Login au registry

    # TODO: Build de l'image

    # TODO: Push de l'image avec les tags appropriés
```

---

## Étape 3 : Résolution des TODO (60 min)

### Aide pour GitHub Actions

#### Checkout du code
```yaml
- uses: actions/checkout@v4
```

#### Setup Node.js
```yaml
- uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'
```

#### Action Cypress
```yaml
- uses: cypress-io/github-action@v6
  with:
    build: npm ci
    start: npm start
    wait-on: 'http://localhost:3000'
    wait-on-timeout: 120
```

#### Upload d'artefacts
```yaml
- uses: actions/upload-artifact@v4
  if: failure()
  with:
    name: cypress-results
    path: |
      cypress/videos
      cypress/screenshots
```

#### Docker Build & Push (GitHub Container Registry)
```yaml
- uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}

- uses: docker/build-push-action@v5
  with:
    context: .
    push: true
    tags: |
      ghcr.io/${{ github.repository }}:latest
      ghcr.io/${{ github.repository }}:v1.0.0-${{ github.run_number }}
```

---

## Étape 4 : Tester la Pipeline (30 min)

1. Commitez et poussez votre configuration :

```bash
git add .
git commit -m "feat: add CI/CD pipeline with Cypress tests"
git push origin main
```

2. Observez l'exécution de la pipeline dans l'interface GitHub/GitLab

3. Vérifiez que :
   - [ ] Les tests s'exécutent correctement
   - [ ] L'image Docker est construite après le succès des tests
   - [ ] L'image est taguée avec `latest` et un numéro de version
   - [ ] L'image est poussée vers le registry

---

## Étape 5 : Bonus - Améliorer la Pipeline (Optionnel)

### 5.1 Ajouter un job de lint

```yaml
lint:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: '20'
    - run: npm ci
    - run: npm run lint  # À ajouter dans package.json
```

### 5.2 Paralléliser les tests Cypress

```yaml
test:
  runs-on: ubuntu-latest
  strategy:
    matrix:
      containers: [1, 2, 3]
  steps:
    - uses: cypress-io/github-action@v6
      with:
        record: true
        parallel: true
        group: 'E2E Tests'
      env:
        CYPRESS_RECORD_KEY: ${{ secrets.CYPRESS_RECORD_KEY }}
```

### 5.3 Ajouter des notifications

```yaml
- uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    fields: repo,message,commit,author
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
  if: always()
```

### 5.4 Déployer automatiquement (exemple avec Render/Heroku)

```yaml
deploy:
  needs: build
  runs-on: ubuntu-latest
  steps:
    - uses: akhileshns/heroku-deploy@v3.13.15
      with:
        heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
        heroku_app_name: "votre-app-name"
        heroku_email: "votre@email.com"
```

