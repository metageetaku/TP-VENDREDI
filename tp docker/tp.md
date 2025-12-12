#### Structure de l’application

L'application est composée de plusieurs services, chacun développé avec des technologies différentes. Voici un détail de chaque partie :

---

### **Partie 1 : Application de vote (Frontend)**

- **Technologie** : Python (Flask)
- **Emplacement** : `/vote`
- **Base de données utilisée** : Redis (décrite dans la partie 4)
- **Exigences** :

  - Utilisation d'une image Docker Python.
  - Installation des dépendances via `requirements.txt`.

- **Lancement de l'application** :
  ```bash
  gunicorn app:app -b 0.0.0.0:80 --log-file - --access-logfile - --workers 4 --keep-alive 0
  ```

---

### **Partie 2 : Application de résultats (Backend)**

- **Technologie** : Node.js (Express)
- **ports** : 4000
- **Emplacement** : `/result`
- **Base de données utilisée** : PostgreSQL (décrite dans la partie 5)
- **Exigences** :

  - Utilisation d'une image Docker Node.js.
  - Installation des dépendances via `package.json` avec `npm install`.

- **Lancement de l'application** :
  ```bash
  node server.js
  ```

---

### **Partie 3 : Worker (Synchronisation des bases de données)**

- **Technologie** : .NET Core
- **Emplacement** : `/worker`
- **Fonction** : Synchronisation entre la base de données Redis (non-relationnelle) et PostgreSQL (relationnelle).
- **Exigences** :

  - Utilisation de deux images Docker :
    - `mcr.microsoft.com/dotnet/core/sdk:3.1` pour le build.
    - `mcr.microsoft.com/dotnet/core/runtime:3.1` pour l'exécution.

- **Commandes de build et de lancement** :
  ```bash
  dotnet restore
  dotnet publish -c Release -o /out Worker.csproj
  dotnet Worker.dll
  ```

---

### **Partie 4 : Base de données non-relationnelle (Redis)**

- **Technologie** : Redis
- **Image Docker** : `redis:5.0-alpine3.10`
- **Nom du service** : `redis`

---

### **Partie 5 : Base de données relationnelle (PostgreSQL)**

- **Technologie** : PostgreSQL
- **Image Docker** : `postgres:9.4`
- **Nom du service** : `db`
- POSTGRES_USER: postgres
- POSTGRES_PASSWORD: postgres

---

### **Partie 6 : Docker Compose**

Un fichier `docker-compose.yml` sera utilisé pour orchestrer tous les services ensemble. Il permettra de démarrer les applications de vote, de résultat, le worker, ainsi que les bases de données Redis et PostgreSQL.

### **Objectifs du TP :**

1. Créer les Dockerfiles pour les trois parties principales (vote, résultat, worker).
2. Configurer un fichier `docker-compose.yml` pour lancer l'ensemble des services.
3. Déployer et tester l'application localement avec Docker Compose.
