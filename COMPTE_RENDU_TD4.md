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

<img width="910" height="566" alt="image" src="https://github.com/user-attachments/assets/43fc26f4-46f6-4ce3-af0f-23d1a28ea1e6" />


**Explication :** Requête simple qui récupère les champs de base de tous les praticiens sans relations ni filtres.

---

### 2.2 Liste des praticiens avec le libellé de la spécialité

**Requête GraphQL :**
```json
{
  "query": "query { Praticien { id nom prenom telephone ville specialite_id { id libelle } } }"
}
```

<img width="910" height="563" alt="image" src="https://github.com/user-attachments/assets/4e2f660c-60c5-4d2d-a629-c12f161b6095" />


**Explication :** Ajout de la relation `specialite_id` pour récupérer les informations de la spécialité de chaque praticien. La notation avec accolades permet d'accéder aux champs de la relation Many-to-One.

---

### 2.3 Liste des praticiens avec filtre sur la ville

**Requête GraphQL :**
```json
{
  "query": "query { Praticien(filter: { ville: { _eq: \"Paris\" } }) { id nom prenom telephone ville specialite_id { id libelle } } }"
}
```

<img width="910" height="566" alt="image" src="https://github.com/user-attachments/assets/19d6e9a9-dc30-4845-b90a-4cd194821808" />


**Explication :** Utilisation du paramètre `filter` avec l'opérateur `_eq` pour filtrer les praticiens dont la ville est égale à "Paris".

---

### 2.4 Liste des praticiens avec la structure d'appartenance

**Requête GraphQL :**
```json
{
  "query": "query { Praticien { id nom prenom telephone ville specialite_id { id libelle } structure_id { id nom ville } } }"
}
```

<img width="910" height="567" alt="image" src="https://github.com/user-attachments/assets/e8af84c7-3569-46b7-bfd0-f46606f75ac0" />


**Explication :** Ajout de la relation `structure_id` pour récupérer le nom et la ville de la structure à laquelle appartient chaque praticien.

---

### 2.5 Liste des praticiens avec filtre sur l'email contenant ".fr"

**Requête GraphQL :**
```json
{
  "query": "query { Praticien(filter: { email: { _contains: \".fr\" } }) { id nom prenom telephone ville email specialite_id { id libelle } structure_id { id nom ville } } }"
}
```

<img width="910" height="566" alt="image" src="https://github.com/user-attachments/assets/66fce8f9-0ed9-48e7-ae23-0f69ccd54cd5" />


**Explication :** Utilisation de l'opérateur `_contains` pour filtrer les praticiens dont l'email contient la chaîne ".fr". Le champ `email` a été ajouté dans la sélection pour voir le résultat du filtre.

---

### 2.6 Praticiens rattachés à une structure dont la ville est "Paris"

**Requête GraphQL :**
```json
{
  "query": "query { Praticien(filter: { structure_id: { ville: { _eq: \"Paris\" } } }) { id nom prenom telephone ville structure_id { nom ville } } }"
}
```

<img width="910" height="568" alt="image" src="https://github.com/user-attachments/assets/43493d71-25e3-435d-af06-0d3f635e7e5c" />


**Explication :** Filtrage sur une relation imbriquée. On filtre les praticiens dont la structure associée (`structure_id`) a une ville égale à "Paris".

---

### 2.7 Requête avec alias pour deux listes de praticiens

**Requête GraphQL :**
```json
{
  "query": "query { praticiensParis: Praticien(filter: { ville: { _eq: \"Paris\" } }) { id nom prenom telephone ville } praticiensBourdon: Praticien(filter: { ville: { _eq: \"Bourdon-les-Bains\" } }) { id nom prenom telephone ville } }"
}
```

<img width="910" height="571" alt="image" src="https://github.com/user-attachments/assets/e79a7bd5-d88f-4c94-9f70-607bd44a80e1" />


**Explication :** Utilisation d'alias (`praticiensParis` et `praticiensBourdon`) pour différencier deux requêtes sur la même collection avec des filtres différents. Cela permet de récupérer deux listes distinctes dans une seule requête.

---

### 2.8 Requête avec fragment GraphQL

**Requête GraphQL :**
```json
{
  "query": "query { praticiensParis: Praticien(filter: { ville: { _eq: \"Paris\" } }) { ...champsPraticien } praticiensBourdon: Praticien(filter: { ville: { _eq: \"Bourdon-les-Bains\" } }) { ...champsPraticien } } fragment champsPraticien on Praticien { id nom prenom telephone ville }"
}
```

<img width="910" height="566" alt="image" src="https://github.com/user-attachments/assets/c55d5242-0d91-44d7-82cf-13adf1b86265" />


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

<img width="910" height="570" alt="image" src="https://github.com/user-attachments/assets/e6913a91-7cab-4bdc-994e-b6f8039f0c2d" />


**Explication :** Utilisation d'une variable `$ville` de type `String!` (non-nullable) pour paramétrer la requête. Les variables permettent de rendre les requêtes réutilisables et dynamiques. La variable est définie dans la déclaration de la query et utilisée dans le filtre.

---

### 2.10 Liste des structures avec leurs praticiens et spécialités

**Requête GraphQL :**
```json
{
  "query": "query { Structure { id nom ville praticien_id { id nom prenom email specialite_id { id libelle } } } }"
}
```

<img width="910" height="565" alt="image" src="https://github.com/user-attachments/assets/b33d6685-2c0d-4b57-abc0-5aa9fb523215" />


**Explication :** Requête sur la collection `Structure` avec navigation dans la relation inverse `praticien_id` (One-to-Many) pour récupérer tous les praticiens attachés à chaque structure. Pour chaque praticien, on récupère également sa spécialité via la relation `specialite_id`.

---

## 3. Autorisations dans Directus

### 3.1 Création d'un rôle et d'une policy

Pour sécuriser l'accès aux données via l'API GraphQL, nous avons créé :

1. **Un nouveau rôle** dans Directus (ex: "api_reader")

<img width="1003" height="61" alt="image" src="https://github.com/user-attachments/assets/baf92313-e460-498c-b572-f5188477bcf9" />

2. **Une policy** dans Directus (ex: "read_all_collections") associée à ce rôle avec les droits de lecture sur toutes les collections :
   - Praticien
   - Specialite
   - Structure
   - MotifVisite
   - MoyenPaiement

<img width="423" height="57" alt="image" src="https://github.com/user-attachments/assets/4c7126db-9d81-4489-9642-2ba050a3d297" />

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

     <img width="910" height="469" alt="image" src="https://github.com/user-attachments/assets/84bdd66b-6e50-4e65-bd1f-628720b870a7" />
     

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

<img width="910" height="600" alt="image" src="https://github.com/user-attachments/assets/62fe408e-9862-4d11-9513-2efd03150252" />


**Résultat :** Erreur d'autorisation car le rôle Public n'a plus accès à cette collection.

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

<img width="910" height="597" alt="image" src="https://github.com/user-attachments/assets/7c10a21a-c103-4a7a-9be4-9e9bc22d0bea" />


**Résultat :** Succès, retourne la liste des moyens de paiement.

#### Requête 2 : Lister les spécialités avec motifs de visite

**Sans authentification :**
```json
{
  "query": "query { Specialite { id libelle motifvisite_id { id libelle } } }"
}
```

<img width="910" height="596" alt="image" src="https://github.com/user-attachments/assets/46156532-f75e-4133-ad03-0b2540c1dae2" />

**Résultat :** Erreur d'autorisation sur la relation `motifvisite_id` car MotifVisite n'est plus accessible publiquement.

**Avec authentification (Token JWT) :**
```json
{
  "query": "query { Specialite { id libelle motifvisite_id { id libelle } } }"
}
```
<img width="910" height="595" alt="image" src="https://github.com/user-attachments/assets/095cf6a7-667d-4958-8742-1845474b0e58" />


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
<img width="910" height="598" alt="image" src="https://github.com/user-attachments/assets/60f47bdf-7e48-40cd-96d8-52955c2ec84b" />


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

<img width="910" height="593" alt="image" src="https://github.com/user-attachments/assets/c7f20700-6151-4337-bce7-0f9e176223f2" />


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
<img width="910" height="599" alt="image" src="https://github.com/user-attachments/assets/575697bb-f6d6-447e-bfd7-1487f0dd4fe1" />


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

<img width="910" height="598" alt="image" src="https://github.com/user-attachments/assets/dc9a5b59-5dd3-4361-8d61-9f2ccf73350b" />


**Explication :** Création d'un praticien avec la relation `specialite_id` définie dès la création. Plus efficace que de créer puis modifier.

---

### 4.5 Créer un praticien et créer sa spécialité "chirurgie" en même temps

<img width="910" height="597" alt="image" src="https://github.com/user-attachments/assets/fd1542e1-294b-43eb-a831-0ec5c1bae889" />

---

### 4.6 Ajouter un praticien à la spécialité "chirurgie"

**Méthode HTTP :** `POST` 

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

<img width="910" height="611" alt="image" src="https://github.com/user-attachments/assets/46ee5faf-73c3-4274-ad44-134c11efe380" />


**Explication :** Modification d'un praticien existant pour le rattacher à la spécialité "chirurgie" créée précédemment (ID : 9). Notez que dans les mutations `update`, Directus attend un objet `{ id: 9 }` pour les relations, pas juste la valeur numérique.

---

### 4.7 Modifier le premier praticien pour le rattacher à une structure existante

**Méthode HTTP :** `POST` 

**Mutation GraphQL :**
```json
{
  "query": "mutation { update_Praticien_item(id: \"180e1b20-a4f5-467b-b725-afeea1766172\", data: { structure_id: { id: \"5f7d3d80-3c4b-386a-a23e-74de4a1e0b19\" } }) { id nom prenom structure_id { nom ville } } }"
}
```

<img width="910" height="579" alt="image" src="https://github.com/user-attachments/assets/de98f9d4-fa3e-4f35-80a3-547317800c50" />


**Explication :** Mise à jour du champ `structure_id` du premier praticien créé (ID : `180e1b20-a4f5-467b-b725-afeea1766172`) pour le rattacher à une structure existante (ID : `5f7d3d80-3c4b-386a-a23e-74de4a1e0b19`). Comme pour les autres relations dans les mutations `update`, il faut utiliser le format objet `{ id: "..." }`.

---

### 4.8 Supprimer les deux derniers praticiens créés

**Méthode HTTP :** `POST`

**Note :** Le deuxième praticien (créé en 4.5 avec la spécialité "chirurgie") n'a pas pu être créé avec succès, donc nous ne le supprimons pas. Seul le praticien créé en 4.4 sera supprimé.

**Mutation GraphQL :**
```json
{
  "query": "mutation { delete_Praticien_item(id: \"180e1b20-a4f5-467b-b725-afeea1766172\") { id } }"
}
```

<img width="718" height="768" alt="image" src="https://github.com/user-attachments/assets/dc95cf36-b417-4d3f-8744-add2c516ee65" />


**Explication :** Utilisation de la mutation `delete_Praticien_item` pour supprimer le praticien créé précédemment (ID : `180e1b20-a4f5-467b-b725-afeea1766172`). 

**Note importante sur les méthodes HTTP en GraphQL :**
- Toutes les requêtes GraphQL (queries et mutations) utilisent la méthode HTTP **`POST`**
- L'endpoint est toujours : `http://localhost:8055/graphql`


