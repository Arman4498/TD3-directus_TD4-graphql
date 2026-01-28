# TP Docker : Directus (TD3) et API GraphQL (TD4)

Ce projet regroupe deux travaux dirigés autour de Directus et d'une base de données de praticiens de santé exécutés avec Docker :
- **TD3** : mise en place de Directus (CMS headless) pour la gestion des praticiens.
- **TD4** : exploitation de l'API GraphQL de Directus autour du même jeu de données.

## PDF des TP

- [TD3-directus.pdf](./TD3-directus.pdf)
- [TD4-graphql.pdf](./TD4-graphql.pdf)

---

### Prérequis

Installez au minimum :

- [Docker](https://www.docker.com/get-started) 
- [Docker Compose](https://docs.docker.com/compose/install/)

Vérifiez l'installation avec :
```bash
docker --version
docker-compose --version
```

---

## Structure globale du projet

```text
TD3-directus_TD4-graphql/
├── COMPTE_RENDU_TD3.md
├── COMPTE_RENDU_TD4.md
├── TD3-directus.pdf
├── TD4-graphql.pdf
├── docker-compose.yml
├── sauvegarde_td3.sql
├── sauvegarde_td4.sql
├── README.md
└── directus/
    ├── uploads/
    └── extensions/
```

## Liste des TP / exercices


### TP 3 : Directus - Gestion de praticiens de santé

- **Fichiers spécifiques** : `sauvegarde_td3.sql`, `COMPTE_RENDU_TD3.md`
- **Objectif** : installer Directus et manipuler l’API REST sur les praticiens.
- **Dépendances** : Docker, Docker Compose, Directus, PostgreSQL

[Voir le compte rendu du TD3](./COMPTE_RENDU_TD3.md)

---

### TP 4 : API GraphQL avec Directus

- **Fichiers spécifiques** : `sauvegarde_td4.sql`, `COMPTE_RENDU_TD4.md`
- **Objectif** : interroger les mêmes données via l’API GraphQL de Directus.
- **Dépendances** : Docker, Docker Compose, Directus (API REST et GraphQL), PostgreSQL

[Voir le compte rendu du TD4](./COMPTE_RENDU_TD4.md)

---

## Démarrage des services Docker (commun TD3 / TD4)

### 1. Cloner ou télécharger le projet

Si vous avez le projet dans un dépôt Git :
```bash
git clone <url-du-repo>
cd TD3-directus_TD4-graphql
```

### 2. Lancer l'application

Démarrez les conteneurs Docker :
```bash
docker-compose up -d
```

### 3. Accéder à Directus

Une fois les conteneurs démarrés, attendez quelques secondes que Directus soit complètement initialisé, puis accédez à :

**Interface d'administration :**
- URL : http://localhost:8055
- Email : `admin@example.com`
- Mot de passe : `admin123`

**API REST :**
- Base URL : http://localhost:8055
- Documentation API : http://localhost:8055/server/specs/openapi

**API GraphQL :**
- Endpoint : http://localhost:8055/graphql

---

## Configuration

### Variables d'environnement (optionnel)

Vous pouvez personnaliser la configuration en créant un fichier `.env` à la racine du projet :

```env
# Base de données PostgreSQL
DB_USER=directus
DB_PASSWORD=directus123
DB_DATABASE=directus_db
DB_PORT=5432

# Directus
DIRECTUS_PORT=8055
DIRECTUS_KEY=your-secret-key-change-this
DIRECTUS_SECRET=your-secret-secret-change-this
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=admin123
PUBLIC_URL=http://localhost:8055
```

Si vous ne créez pas de fichier `.env`, les valeurs par défaut du `docker-compose.yml` seront utilisées.

---

## Restauration des bases de données

Les fichiers suivants contiennent des sauvegardes PostgreSQL :
- `sauvegarde_td3.sql` : données pour le TD3 (API REST / Directus).
- `sauvegarde_td4.sql` : données pour le TD4 (requêtes GraphQL).

### Méthode 1 : via PowerShell (Windows)

```powershell
# Copier le fichier SQL dans le conteneur PostgreSQL
docker cp sauvegarde_td3.sql directus-postgres:/tmp/sauvegarde_td3.sql

# Restaurer la base de données TD3
docker exec -i directus-postgres psql -U directus -d directus_db -f /tmp/sauvegarde_td3.sql
```

Pour le TD4, adaptez simplement le nom du fichier :
```powershell
docker cp sauvegarde_td4.sql directus-postgres:/tmp/sauvegarde_td4.sql
docker exec -i directus-postgres psql -U directus -d directus_db -f /tmp/sauvegarde_td4.sql
```

### Méthode 2 : via Bash (Linux / macOS)

```bash
# TD3
docker exec -i directus-postgres psql -U directus -d directus_db < sauvegarde_td3.sql

# TD4
docker exec -i directus-postgres psql -U directus -d directus_db < sauvegarde_td4.sql
```

---

## Commandes Docker utiles

### Voir les logs
```bash
# Logs de tous les services
docker-compose logs

# Logs en temps réel
docker-compose logs -f

# Logs d'un service spécifique
docker-compose logs directus
docker-compose logs postgres
```

### Arrêter l'application
```bash
# Arrêter et supprimer les conteneurs (conserve les volumes)
docker-compose down

# Arrêter et supprimer les conteneurs ET les volumes (supprime les données)
docker-compose down -v
```

### Redémarrer l'application
```bash
docker-compose restart
```

### Vérifier le statut des conteneurs
```bash
docker-compose ps
```

### Accéder au shell PostgreSQL
```bash
docker exec -it directus-postgres psql -U directus -d directus_db
```


---

## Vérification de l'installation

Pour vérifier que tout fonctionne correctement :

1. **Vérifier que les conteneurs sont en cours d'exécution :**
   ```bash
   docker-compose ps
   ```
   Vous devriez voir au moins deux conteneurs : `directus-app` et `directus-postgres` avec le statut "Up".

2. **Tester l'API REST :**
   ```bash
   curl http://localhost:8055/server/health
   ```
   Devrait retourner `{"status":"ok"}`.

3. **Tester rapidement l'API GraphQL :**
   ```bash
   curl -X POST http://localhost:8055/graphql \
     -H "Content-Type: application/json" \
     -d '{"query":"{ __typename }"}'
   ```

4. **Accéder à l'interface web :**
   Ouvrez http://localhost:8055 dans votre navigateur et connectez-vous avec les identifiants par défaut.

---

## Documentation

- [Documentation Directus](https://docs.directus.io/)
- [Guide Docker Directus](https://docs.directus.io/self-hosted/docker-guide.html)
- [API REST Directus](https://docs.directus.io/reference/introduction.html)
