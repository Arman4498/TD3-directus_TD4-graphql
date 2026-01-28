# Compte Rendu - TD3 : Gestion de Praticiens de Santé avec Directus

## 1. Préparation : Installation de Directus avec Docker

### 1.1 Configuration Docker Compose

Pour installer Directus en version Docker, nous avons créé un fichier `docker-compose.yml` qui configure deux services :

- **PostgreSQL** : Base de données pour stocker les données de l'application
- **Directus** : Application CMS headless

### 1.2 Personnalisation des données de connexion

Le fichier `docker-compose.yml` a été personnalisé avec les paramètres suivants :

**Service PostgreSQL :**
- Utilisateur : `directus` (configurable via variable d'environnement `DB_USER`)
- Mot de passe : `directus123` (configurable via variable d'environnement `DB_PASSWORD`)
- Base de données : `directus_db` (configurable via variable d'environnement `DB_DATABASE`)
- Port : `5432` (configurable via variable d'environnement `DB_PORT`)

**Service Directus :**
- Port d'accès : `8055` (configurable via variable d'environnement `DIRECTUS_PORT`)
- Clé secrète : `your-secret-key-change-this` (configurable via variable d'environnement `KEY`)
- Secret : `your-secret-secret-change-this` (configurable via variable d'environnement `SECRET`)
- Email administrateur : `admin@example.com` (configurable via variable d'environnement `ADMIN_EMAIL`)
- Mot de passe administrateur : `admin123` (configurable via variable d'environnement `ADMIN_PASSWORD`)
- URL publique : `http://localhost:8055` (configurable via variable d'environnement `PUBLIC_URL`)

### 1.3 Volumes persistants

Les volumes suivants ont été configurés pour la persistance des données :
- `postgres_data` : Stockage des données PostgreSQL
- `./directus/uploads` : Fichiers uploadés dans Directus
- `./directus/extensions` : Extensions personnalisées Directus

### 1.4 Démarrage de l'application

Pour démarrer l'application, il suffit d'exécuter :
```bash
docker-compose up -d
```

L'application est alors accessible à l'adresse `http://localhost:8055`.

---

## 2. Création des données : Modèle de domaine

### 2.1 Schéma UML du modèle

Le modèle de domaine représente une application de gestion de praticiens de santé avec les entités suivantes :

- **Praticien** : Informations sur les praticiens de santé
- **Spécialité** : Spécialités médicales
- **Structure** : Structures de santé (hôpitaux, cliniques, etc.)
- **MotifVisite** : Motifs de consultation
- **MoyenPaiement** : Moyens de paiement acceptés
<img width="624" height="295" alt="image" src="https://github.com/user-attachments/assets/d2862297-e80d-4a16-9119-839e909c8cf6" />



### 2.2 Structure des tables créées

#### Table `Praticien`
- `id` (UUID) : Identifiant unique
- `nom` (VARCHAR) : Nom du praticien
- `prenom` (VARCHAR) : Prénom du praticien
- `rpps_id` (VARCHAR) : Identifiant RPPS
- `titre` (VARCHAR) : Titre du praticien
- `ville` (VARCHAR) : Ville d'exercice
- `email` (VARCHAR) : Adresse email
- `telephone` (VARCHAR) : Numéro de téléphone
- `organisation` (BOOLEAN) : Indique si c'est une organisation
- `accepte_nouveau_patient` (BOOLEAN) : Accepte les nouveaux patients
- `structure_id` (UUID) : Référence vers la Structure (relation Many-to-One)
- `specialite_id` (INTEGER) : Référence vers la Spécialité (relation Many-to-One)

#### Table `Specialite`
- `id` (INTEGER) : Identifiant unique
- `libelle` (VARCHAR) : Libellé de la spécialité
- `description` (VARCHAR) : Description de la spécialité

#### Table `Structure`
- `id` (UUID) : Identifiant unique
- `nom` (VARCHAR) : Nom de la structure
- `adresse` (VARCHAR) : Adresse de la structure
- `ville` (VARCHAR) : Ville de la structure

#### Table `MotifVisite`
- `id` (INTEGER) : Identifiant unique
- `libelle` (VARCHAR) : Libellé du motif de visite
- `specialite_id` (INTEGER) : Référence vers la Spécialité (relation Many-to-One)

#### Table `MoyenPaiement`
- `id` (INTEGER) : Identifiant unique
- `libelle` (VARCHAR) : Libellé du moyen de paiement

#### Tables de jointure (relations Many-to-Many)
- `Praticien_MotifVisite` : Relation entre Praticien et MotifVisite
- `Praticien_MoyenPaiement` : Relation entre Praticien et MoyenPaiement

### 2.3 Création du modèle dans Directus

Le modèle a été créé dans le backoffice Directus en suivant ces étapes :

1. **Accès au backoffice** : Connexion à `http://localhost:8055` avec les identifiants administrateur
2. **Création des collections** : Création de chaque table en tant que collection dans Directus
3. **Configuration des relations** : Définition des relations entre les collections :
   - Praticien → Structure (Many-to-One)
   - Praticien → Spécialité (Many-to-One)
   - MotifVisite → Spécialité (Many-to-One)
   - Praticien ↔ MotifVisite (Many-to-Many)
   - Praticien ↔ MoyenPaiement (Many-to-Many)
4. **Insertion des données** : Ajout des données de test via l'interface Directus

---

## 3. Utilisation de l'API REST

Directus génère automatiquement une API REST pour chaque collection. Voici les requêtes utilisées pour obtenir les données demandées :

### 3.1 Liste des praticiens

**Requête :**
```http
http://localhost:8055/items/Praticien
```

<img width="1487" height="769" alt="image" src="https://github.com/user-attachments/assets/80c1c07c-9077-4b18-a686-7b1b896533c1" />


**Réponse :** Retourne la liste complète de tous les praticiens avec tous leurs champs.

### 3.2 La spécialité d'ID 2

**Requête :**
```http
http://localhost:8055/items/Specialite/2
```

<img width="377" height="761" alt="image" src="https://github.com/user-attachments/assets/7aa7a6a3-0530-4b9a-b0ed-fa6533e8db98" />


**Réponse :** Retourne les informations complètes de la spécialité avec l'identifiant 2 (id, libelle, description).

### 3.3 La spécialité d'ID 2, avec uniquement son libellé

**Requête :**
```http
http://localhost:8055/items/Specialite/2?fields[]=libelle
```

<img width="460" height="182" alt="image" src="https://github.com/user-attachments/assets/e985a020-10a1-40e0-863a-d61f363d03eb" />


**Réponse :** Retourne uniquement le champ `libelle` de la spécialité d'ID 2.

### 3.4 Un praticien avec sa spécialité (libellé)

**Requête :**
```http
http://localhost:8055/items/Praticien?fields[]=nom&fields[]=prenom&fields[]=specialite_id.libelle&limit=1
```

<img width="534" height="873" alt="image" src="https://github.com/user-attachments/assets/677d0a78-d282-459e-b824-162af01ab6c3" />


**Réponse :** Retourne un praticien avec son nom, prénom et le libellé de sa spécialité (via la relation `specialite_id`).

### 3.5 Une structure (nom, ville) et la liste des praticiens rattachés (nom, prénom)

**Requête :**
```http
GET http://localhost:8055/items/Structure?fields[]=nom&fields[]=ville&fields[]=praticien_id.nom&fields[]=praticien_id.prenom
```

<img width="771" height="873" alt="image" src="https://github.com/user-attachments/assets/be954c22-38b2-42ad-84f7-961f733b6fae" />


**Réponse :** Retourne les structures avec leur nom, ville et la liste des praticiens rattachés (nom et prénom) via la relation inverse `praticien_id`.


### 3.6 Structure avec praticiens et spécialité (libellé)

**Requête :**
```http
http://localhost:8055/items/Structure?fields[]=nom&fields[]=ville&fields[]=praticien_id.nom&fields[]=praticien_id.prenom&fields[]=praticien_id.specialite_id.libelle
```

<img width="1001" height="873" alt="image" src="https://github.com/user-attachments/assets/c63399eb-3d64-445d-a1e3-b33723b5298e" />


**Réponse :** Retourne les structures avec leur nom, ville, la liste des praticiens (nom, prénom) et le libellé de la spécialité de chaque praticien.

### 3.7 Les structures dont le nom de la ville contient "sur" avec la liste des praticiens (nom, prénom, spécialité)

**Requête :**
```http
http://localhost:8055/items/Structure?filter[ville][_contains]=sur&fields[]=nom&fields[]=ville&fields[]=praticien_id.nom&fields[]=praticien_id.prenom&fields[]=praticien_id.specialite_id.libelle
```

<img width="940" height="873" alt="image" src="https://github.com/user-attachments/assets/141e3311-85f0-44dd-acf3-22d15e2d80f5" />


**Réponse :** Retourne uniquement les structures dont le nom de la ville contient "sur", avec leur nom, ville, et la liste des praticiens (nom, prénom, libellé de la spécialité).

---

## 4. Notes techniques

### 4.1 Syntaxe de l'API REST Directus

- **Endpoint de base** : `http://localhost:8055/items/{collection}`
- **Filtrage** : Utilisation du paramètre `filter` avec des opérateurs comme `_contains`, `_eq`, `_in`, etc.
- **Sélection de champs** : Utilisation du paramètre `fields[]` pour spécifier les champs à retourner
- **Relations** : Accès aux relations via la notation pointée (ex: `specialite_id.libelle`)
- **Limite** : Utilisation du paramètre `limit` pour limiter le nombre de résultats

### 4.2 Relations dans Directus

Les relations sont automatiquement détectées par Directus lors de la création des collections. Pour accéder aux données liées :
- **Many-to-One** : Utiliser `{champ_relation}.{champ_souhaité}` (ex: `specialite_id.libelle`)
- **One-to-Many** : Utiliser le nom du champ de relation défini dans Directus (ex: `praticien_id.nom` pour accéder aux praticiens depuis une Structure)

### 4.3 Sauvegarde

Une sauvegarde complète de la base de données PostgreSQL a été effectuée dans le fichier `sauvegarde_td3.sql`, permettant de restaurer l'ensemble du modèle et des données à tout moment.

