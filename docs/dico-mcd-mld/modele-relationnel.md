# Modèle relationnel

[cours YouTube E mds](https://youtu.be/VFHVNA8xgK0)

Il permet d'organiser les données (attributs) en _relations_ qui se transforment en _tables_ dans la base de données.

Il se base sur le concept de _dépendances fonctionnelles_. On l'appelle encore Modèle Logique de Données (MLD).

## Dépendance fonctionnelle

### Définition et exemples

Soit deux attributs A et B, on dit que B dépend fonctionnellement de A (A -> B) si, à chaque valeur de A, correspond une unique valeur de B. On parle de dépendance fonctionnelle et on note DF.

**Exemples :**

* n° insee -> nom : à chaque numéro insee correspond un nom unique, autrement dit deux personnes différentes ont des numéros insee différents. Le nom dépend donc fonctionnellement du numéro insee

* nom -> n° insee : pour un nom donné (homonymes), plusieurs numéros insee sont possibles, autrement dit deux numéros insee différents peuvent correspondre à un même nom. Le numéro insee ne dépend donc pas fonctionnellement du nom

### Dépendance fonctionnelle élémentaire

Soit deux attributs A et B, la DF (A -> B) est élémentaire s'il n'existe pas C inclus dans A tel que C->B.

**Exemples :**

* n° commande, n° article -> nom_article : DF non élémentaire car seul n° article suffit à caractériser nom_article (n° commande est inutile). Ainsi, n° article -> nom_article est élémentaire

* n° commande, n° article -> qté_commandée : DF élémentaire car les deux informations de base sont nécessaires à la caractérisation de qté_commandée

### Dépendance fonctionnelle directe

Soit deux attributs A et B, la DF (A -> B) est directe s'il n'existe pas C tel que A->C et C->B.

**Exemple :** on dispose des attributs n° client, nom_client, n° commande

n° commande -> nom_client est une DF non directe car on passe par l'attribut n° client afin d'atteindre nom_client. Intervention d'un intermédiaire :

n° commande -> n° client (DF directe) et n° client -> n° nom_client (DF directe)

## Relations

### Définition et exemples

Une relation est un sous-ensemble du produit cartésien d'une liste d'attributs. Elle possède un nom et contient une liste d'attributs

**Exemple :** relation Client

* représentation en compréhension : Client (n° client, nom_client, adr_client)

* représentation en extension : une ligne est appelée un tuple, une colonne représente un attribut

| n° client | nom_client | adr_client |
|-|-|-|-|
| 45 | BERTRAND | Nice |
| 62 | DUPOND | Marseille |
| ... | ... | ... |

### Clé primaire d'une relation (identifiant)

La clé primaire d'une relation est l'attribut ou l'ensemble d'attributs dont tous les autres attributs de la relation sont en DF.

Une relation ne possède qu'une seule clé primaire.

### Clé(s) étrangère(s) d'une relation

Une clé étrangère d'une relation est un attribut ou un ensemble d'attributs qui a un rôle de clé primaire dans une autre relation.

Une relation possède 0 ou plusieurs clés étrangères.

Ce sont les clés étrangères qui permettent de faire les liens entre les relations.

**Exemples :**

* relation Client (n° client, nom_client, adr_client) :
  * n° client est la clé primaire
  * pas de clé étrangère

* relation Commande (n° commande, date_commande, n° client) :
  * n° commande : clé primaire
  * n° client : clé étrangère en référence avec n° client de Client

### Formes normales d'une relation

#### Première forme normale (1NF)

A 26:19 du cours

#### Deuxième forme normale (2NF)

#### Troisième forme normale (3NF)

