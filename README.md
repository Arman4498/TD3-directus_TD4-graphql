# TD3 - Gestion de Praticiens de Santé avec Directus

Ce projet utilise Directus, un CMS headless, pour gérer une base de données de praticiens de santé via Docker.

## 📋 Prérequis

Avant de commencer, assurez-vous d'avoir installé :

- [Docker](https://www.docker.com/get-started) 
- [Docker Compose](https://docs.docker.com/compose/install/)

Vérifiez l'installation avec :
```bash
docker --version
docker-compose --version
```

## 🚀 Démarrage rapide

### 1. Cloner ou télécharger le projet

Si vous avez le projet dans un dépôt Git :
```bash
git clone <url-du-repo>
cd TD3-directus
```

### 2. Lancer l'application

Démarrez les conteneurs Docker :
```bash
docker-compose up -d
```

### 3. Accéder à l'application

Une fois les conteneurs démarrés, attendez quelques secondes que Directus soit complètement initialisé, puis accédez à :

**Interface d'administration :**
- URL : http://localhost:8055
- Email : `admin@example.com`
- Mot de passe : `admin123`

**API REST :**
- Base URL : http://localhost:8055
- Documentation API : http://localhost:8055/server/specs/openapi

## ⚙️ Configuration

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

## 📊 Restauration de la base de données 

Vous pouvez restaurer les données avec (`sauvegarde_td3.sql`) :

### Méthode 1 : Via PowerShell (Windows) -

```powershell
# Copier le fichier SQL dans le conteneur PostgreSQL
docker cp sauvegarde_td3.sql directus-postgres:/tmp/sauvegarde_td3.sql

# Restaurer la base de données
docker exec -i directus-postgres psql -U directus -d directus_db -f /tmp/sauvegarde_td3.sql
```

### Méthode 2 : Via Bash/Linux/Mac

```bash
docker exec -i directus-postgres psql -U directus -d directus_db < sauvegarde_td3.sql
```

## 🛠️ Commandes utiles

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

# Arrêter et supprimer les conteneurs ET les volumes (⚠️ supprime les données)
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

## 📁 Structure du projet

```
TD3-directus/
├── docker-compose.yml          # Configuration Docker Compose
├── sauvegarde_td3.sql          # Sauvegarde de la base de données
├── COMPTE_RENDU_TD3.md         # Compte rendu du TD
├── README.md                   # Ce fichier
└── directus/
    ├── uploads/                # Fichiers uploadés (persistants)
    └── extensions/             # Extensions Directus (persistantes)
```

## 🔍 Vérification de l'installation

Pour vérifier que tout fonctionne correctement :

1. **Vérifier que les conteneurs sont en cours d'exécution :**
   ```bash
   docker-compose ps
   ```
   Vous devriez voir deux conteneurs : `directus-app` et `directus-postgres` avec le statut "Up".

2. **Tester l'API REST :**
   ```bash
   curl http://localhost:8055/server/health
   ```
   Devrait retourner `{"status":"ok"}`

3. **Accéder à l'interface web :**
   Ouvrez http://localhost:8055 dans votre navigateur et connectez-vous avec les identifiants par défaut.

## 📚 Documentation

- [Documentation Directus](https://docs.directus.io/)
- [Guide Docker Directus](https://docs.directus.io/self-hosted/docker-guide.html)
- [API REST Directus](https://docs.directus.io/reference/introduction.html)


## 🎯 Utilisation de l'API REST

Une fois l'application démarrée, vous pouvez utiliser l'API REST. Consultez le fichier `COMPTE_RENDU_TD3.md` pour des exemples de requêtes.

Exemple de requête pour obtenir la liste des praticiens :
```bash
curl http://localhost:8055/items/Praticien
```

---

