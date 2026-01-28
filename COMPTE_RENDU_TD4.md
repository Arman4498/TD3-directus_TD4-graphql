# Compte Rendu - TD4 : Requêtes GraphQL avec Directus

## 1. Préparation

### 1.1 Utilisation de l'API GraphQL de Directus

Directus propose une API GraphQL automatique pour toutes les collections créées dans le TD3. Cette API est accessible à l'adresse :

- **Endpoint GraphQL** : `http://localhost:8055/graphql`

### 1.2 Clients API GraphQL

Pour adresser des requêtes GraphQL, nous avons utilisé un client API capable de gérer GraphQL. Les options disponibles incluent :

- **Postman** : Client API populaire avec support GraphQL natif

l'outil permet d'exécuter des requêtes GraphQL, de définir des variables, et de visualiser les résultats de manière structurée.

---

## 2. Requêtes Query

### 2.1 Liste des praticiens (id, nom, prénom, téléphone, ville)

**Requête GraphQL :**
```json
{
  "query": "query { Praticien { id nom prenom telephone ville } }"
}
```

**Explication :** Requête simple qui récupère les champs de base de tous les praticiens sans relations ni filtres.

---

### 2.2 Liste des praticiens avec le libellé de la spécialité

**Requête GraphQL :**
```json
{
  "query": "query { Praticien { id nom prenom telephone ville specialite_id { id libelle } } }"
}
```

**Explication :** Ajout de la relation `specialite_id` pour récupérer les informations de la spécialité de chaque praticien. La notation avec accolades permet d'accéder aux champs de la relation Many-to-One.

---

### 2.3 Liste des praticiens avec filtre sur la ville

**Requête GraphQL :**
```json
{
  "query": "query { Praticien(filter: { ville: { _eq: \"Paris\" } }) { id nom prenom telephone ville specialite_id { id libelle } } }"
}
```

**Explication :** Utilisation du paramètre `filter` avec l'opérateur `_eq` pour filtrer les praticiens dont la ville est égale à "Paris".

---

### 2.4 Liste des praticiens avec la structure d'appartenance

**Requête GraphQL :**
```json
{
  "query": "query { Praticien { id nom prenom telephone ville specialite_id { id libelle } structure_id { id nom ville } } }"
}
```

**Explication :** Ajout de la relation `structure_id` pour récupérer le nom et la ville de la structure à laquelle appartient chaque praticien.

---

### 2.5 Liste des praticiens avec filtre sur l'email contenant ".fr"

**Requête GraphQL :**
```json
{
  "query": "query { Praticien(filter: { email: { _contains: \".fr\" } }) { id nom prenom telephone ville email specialite_id { id libelle } structure_id { id nom ville } } }"
}
```

**Explication :** Utilisation de l'opérateur `_contains` pour filtrer les praticiens dont l'email contient la chaîne ".fr". Le champ `email` a été ajouté dans la sélection pour voir le résultat du filtre.

---

### 2.6 Praticiens rattachés à une structure dont la ville est "Paris"

**Requête GraphQL :**
```json
{
  "query": "query { Praticien(filter: { structure_id: { ville: { _eq: \"Paris\" } } }) { id nom prenom telephone ville structure_id { nom ville } } }"
}
```

**Explication :** Filtrage sur une relation imbriquée. On filtre les praticiens dont la structure associée (`structure_id`) a une ville égale à "Paris".

---

### 2.7 Requête avec alias pour deux listes de praticiens

**Requête GraphQL :**
```json
{
  "query": "query { praticiensParis: Praticien(filter: { ville: { _eq: \"Paris\" } }) { id nom prenom telephone ville } praticiensBourdon: Praticien(filter: { ville: { _eq: \"Bourdon-les-Bains\" } }) { id nom prenom telephone ville } }"
}
```

**Explication :** Utilisation d'alias (`praticiensParis` et `praticiensBourdon`) pour différencier deux requêtes sur la même collection avec des filtres différents. Cela permet de récupérer deux listes distinctes dans une seule requête.

---

### 2.8 Requête avec fragment GraphQL

**Requête GraphQL :**
```json
{
  "query": "query { praticiensParis: Praticien(filter: { ville: { _eq: \"Paris\" } }) { ...champsPraticien } praticiensBourdon: Praticien(filter: { ville: { _eq: \"Bourdon-les-Bains\" } }) { ...champsPraticien } } fragment champsPraticien on Praticien { id nom prenom telephone ville }"
}
```

**Explication :** Utilisation d'un fragment `champsPraticien` pour éviter la répétition des champs sélectionnés. Le fragment définit une structure réutilisable qui peut être appliquée à plusieurs requêtes sur le même type (`Praticien`).

---

### 2.9 Requête avec variable pour paramétrer la ville

**Requête GraphQL :**
```json
{
  "query": "query($ville: String!) { Praticien(filter: { ville: { _eq: $ville } }) { id nom prenom telephone ville specialite_id { id libelle } } }",
  "variables": {
    "ville": "Paris"
  }
}
```

**Explication :** Utilisation d'une variable `$ville` de type `String!` (non-nullable) pour paramétrer la requête. Les variables permettent de rendre les requêtes réutilisables et dynamiques. La variable est définie dans la déclaration de la query et utilisée dans le filtre.

---

### 2.10 Liste des structures avec leurs praticiens et spécialités

**Requête GraphQL :**
```json
{
  "query": "query { Structure { id nom ville praticien_id { id nom prenom email specialite_id { id libelle } } } }"
}
```

**Explication :** Requête sur la collection `Structure` avec navigation dans la relation inverse `praticien_id` (One-to-Many) pour récupérer tous les praticiens attachés à chaque structure. Pour chaque praticien, on récupère également sa spécialité via la relation `specialite_id`.

---

## 3. Autorisations dans Directus

### 3.1 Création d'un rôle et d'une policy

Pour sécuriser l'accès aux données via l'API GraphQL, nous avons créé :

1. **Un nouveau rôle** dans Directus (ex: "api_reader")
2. **Une policy** dans Directus (ex: "read_all_collections") associée à ce rôle avec les droits de lecture sur toutes les collections :
   - Praticien
   - Specialite
   - Structure
   - MotifVisite
   - MoyenPaiement

**Configuration des permissions :**
- Accès en lecture (`read`) sur toutes les collections
- Pas d'accès en écriture pour ce rôle (sécurité)

### 3.2 Création de deux utilisateurs

Deux utilisateurs ont été créés appartenant au rôle créé :

#### Utilisateur 1 : Token statique
- **Email** : `api_static@test.com`
- **Mot de passe** : `test123`
- **Type d'authentification** : Token statique
- **Token** : `E6T4a1HLZKCY7YM80VxnozGbnF1z3-qc` 
- **Utilisation** : Ajout du header `Authorization: Bearer E6T4a1HLZKCY7YM80VxnozGbnF1z3-qc` dans les requêtes GraphQL

#### Utilisateur 2 : Token JWT
- **Email** : `api_JWT@test.com`
- **Mot de passe** : `test123`
- **Type d'authentification** : JWT 
- **Obtention du token** : 
  1. Requête POST à `/auth/login` avec :
     ```json
     {
       "email": "api_JWT@test.com",
       "password": "test123"
     }
     ```
  2. Le token JWT est retourné dans la réponse et doit être utilisé dans le header `Authorization: Bearer <token_jwt>`
- **Utilisation** : Le token JWT est obtenu après authentification et utilisé de la même manière que le token statique

### 3.3 Restriction des droits du rôle Public

Les droits de lecture ont été retirés au rôle `Public` sur les collections suivantes :
- `MotifVisite`
- `MoyenPaiement`

Cela signifie que ces collections ne sont plus accessibles sans authentification.

### 3.4 Vérification de l'authentification

#### Requête 1 : Lister les moyens de paiement

**Sans authentification :**
```json
{
  "query": "query { MoyenPaiement { id libelle } }"
}
```
**Résultat :** Erreur d'autorisation (403 Forbidden) car le rôle Public n'a plus accès à cette collection.

**Avec authentification (Token statique) :**
```json
{
  "query": "query { MoyenPaiement { id libelle } }"
}
```
**Header :**
```
Authorization: Bearer E6T4a1HLZKCY7YM80VxnozGbnF1z3-qc
```
**Résultat :** Succès, retourne la liste des moyens de paiement.

#### Requête 2 : Lister les spécialités avec motifs de visite

**Sans authentification :**
```json
{
  "query": "query { Specialite { id libelle motifvisite_id { id libelle } } }"
}
```
**Résultat :** Erreur d'autorisation sur la relation `motifvisite_id` car MotifVisite n'est plus accessible publiquement.

**Avec authentification (Token JWT) :**
```json
{
  "query": "query { Specialite { id libelle motifvisite_id { id libelle } } }"
}
```
**Obtention du token JWT :**
1. Requête POST à `http://localhost:8055/auth/login` :
```json
{
  "email": "api_JWT@test.com",
  "password": "test123"
}
```
2. Utiliser le token retourné dans le header :
```
Authorization: Bearer <token_jwt_obtenu>
```
**Résultat :** Succès, retourne les spécialités avec leurs motifs de visite associés.

---

## 4. Mutations GraphQL

### 4.1 Créer la spécialité "cardiologie"

**Méthode HTTP :** `POST`

**Mutation GraphQL :**
```json
{
  "query": "mutation { create_Specialite_item(data: { libelle: \"cardiologie\", description: \"Spécialité médicale de cardiologie\" }) { id libelle } }"
}
```

**Explication :** Utilisation de la mutation `create_Specialite_item` pour créer un nouvel élément dans la collection Specialite. Le préfixe `create_` suivi du nom de la collection et `_item` est la convention de nommage de Directus pour les mutations de création.

**Note :** En GraphQL, toutes les requêtes (queries et mutations) utilisent la méthode HTTP `POST`. L'endpoint est `http://localhost:8055/graphql`.

---

### 4.2 Créer un praticien (sans spécialité)

**Méthode HTTP :** `POST`

**Mutation GraphQL :**
```json
{
  "query": "mutation { create_Praticien_item(data: { nom: \"Dupont\", prenom: \"Jean\", ville: \"Paris\", email: \"jean.dupont@example.com\", telephone: \"0123456789\" }) { id nom prenom } }"
}
```

**Explication :** Création d'un praticien avec les champs de base. Notez que `id` n'est pas fourni car il sera généré automatiquement (UUID).

---

### 4.3 Modifier un praticien pour le rattacher à la spécialité "cardiologie"

**Méthode HTTP :** `POST`

**Note :** Lors de la création des deux requêtes précédentes (4.1 et 4.2), nous avons obtenu :
- ID praticien créé : `180e1b20-a4f5-467b-b725-afeea1766172`
- ID spécialité "cardiologie" créée : `6`

Ces IDs seront utilisés dans les mutations suivantes.

**Mutation GraphQL :**
```json
{
  "query": "mutation { update_Praticien_item(id: \"180e1b20-a4f5-467b-b725-afeea1766172\", data: { specialite_id: 6 }) { id nom prenom specialite_id { id libelle } } }"
}
```

**Explication :** Utilisation de la mutation `update_Praticien_item` avec l'ID du praticien à modifier. On met à jour le champ `specialite_id` avec l'ID de la spécialité "cardiologie" créée précédemment. En GraphQL, toutes les mutations utilisent `POST`, mais cette mutation correspond conceptuellement à un `PATCH` en REST.

---

### 4.4 Créer un praticien en le rattachant directement à la spécialité "cardiologie"

**Méthode HTTP :** `POST`

**Mutation GraphQL :**
```json
{
  "query": "mutation { create_Praticien_item(data: { nom: \"Martin\", prenom: \"Sophie\", ville: \"Lyon\", email: \"sophie.martin@example.com\", telephone: \"0987654321\", specialite_id: 6 }) { id nom prenom specialite_id { libelle } } }"
}
```

**Explication :** Création d'un praticien avec la relation `specialite_id` définie dès la création. Plus efficace que de créer puis modifier.

---

### 4.5 Créer un praticien et créer sa spécialité "chirurgie" en même temps


---

### 4.6 Ajouter un praticien à la spécialité "chirurgie"

**Méthode HTTP :** `POST` (équivalent à `PATCH` en REST)

**Note :** Lors de la création des deux requêtes précédentes, nous avons obtenu :
- ID spécialité "chirurgie" créée : `9`
- ID praticien créé : `180e1b20-a4f5-467b-b725-afeea1766172`

Cet ID sera utilisé dans cette mutation.

**Mutation GraphQL :**
```json
{
  "query": "mutation { update_Praticien_item(id: \"180e1b20-a4f5-467b-b725-afeea1766172\", data: { specialite_id: { id: 9 } }) { id nom prenom specialite_id { libelle } } }"
}
```

**Explication :** Modification d'un praticien existant pour le rattacher à la spécialité "chirurgie" créée précédemment (ID : 9). Notez que dans les mutations `update`, Directus attend un objet `{ id: 9 }` pour les relations, pas juste la valeur numérique.

---

### 4.7 Modifier le premier praticien pour le rattacher à une structure existante

**Méthode HTTP :** `POST` (équivalent à `PATCH` en REST)

**Mutation GraphQL :**
```json
{
  "query": "mutation { update_Praticien_item(id: \"180e1b20-a4f5-467b-b725-afeea1766172\", data: { structure_id: { id: \"5f7d3d80-3c4b-386a-a23e-74de4a1e0b19\" } }) { id nom prenom structure_id { nom ville } } }"
}
```

**Explication :** Mise à jour du champ `structure_id` du premier praticien créé (ID : `180e1b20-a4f5-467b-b725-afeea1766172`) pour le rattacher à une structure existante (ID : `5f7d3d80-3c4b-386a-a23e-74de4a1e0b19`). Comme pour les autres relations dans les mutations `update`, il faut utiliser le format objet `{ id: "..." }`.

---

### 4.8 Supprimer les deux derniers praticiens créés

**Méthode HTTP :** `POST` (équivalent à `DELETE` en REST)

**Note :** Le deuxième praticien (créé en 4.5 avec la spécialité "chirurgie") n'a pas pu être créé avec succès, donc nous ne le supprimons pas. Seul le praticien créé en 4.4 sera supprimé.

**Mutation GraphQL :**
```json
{
  "query": "mutation { delete_Praticien_item(id: \"180e1b20-a4f5-467b-b725-afeea1766172\") { id } }"
}
```

**Explication :** Utilisation de la mutation `delete_Praticien_item` pour supprimer le praticien créé précédemment (ID : `180e1b20-a4f5-467b-b725-afeea1766172`). 

**Note importante sur les méthodes HTTP en GraphQL :**
- Toutes les requêtes GraphQL (queries et mutations) utilisent la méthode HTTP **`POST`**
- L'endpoint est toujours : `http://localhost:8055/graphql`


