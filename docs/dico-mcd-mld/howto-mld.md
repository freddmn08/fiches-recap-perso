# Howto MLD avec MySQL Workbench

## Installation de MySQL Workbench

1. Installation en ligne de commandes sous linux (apt-get est le gestionnaire de logiciels). Depuis n'importe quel dossier, taper la commande : `sudo apt-get install mysql-workbench`

2. Pour lancer MySQL workbench :
  * Dans le terminal, taper : `mysql-workbench`
  * Dans l'environnement graphique de Linux, touche Windows puis chercher `mysql workbench`

## Création de Models dans MySQL Workbench

* Double cliquer sur le nom de MYSQL schema, puis on entre le nom du modèle (celui de la database), `okanban` en l'occurrence

* Ajouter un diagramme EER (double clic en haut)

* Outils :
  * place a new table (touche T)
  
  * pour les relations de type 1:n, prendre celui avec traits pointillés (touche 2)

## Ajout d'une table

  * ajout d'une table (touche t), puis clic sur la grille pour afficher la table

  * pour renommer une table, double clic puis fenêtre en bas avec onglets

  * Onglet Table : utf_8_general_ci /  InnoDB

  * Ajout des columns (champ) : losange bleu à coté du champ dans la représentation de la table si champ non null (losange blanc sinon)

  Ne pas mettre les () pour TIMESTAMP ni pour TINYINT dans le Datatype

## Création des relations entre les tables

Une relation est notée avec `:`. On prend le max de chaque cardinalité de part et d'autre du bloc entité.

Types de relations :

* 1:n one to many

* m:n many to many

/!\ Trait plein si FK est encore une PK sinon pointillés

**Symboles graphiques d'un champ dans une table**

  * clé jaune : PK

  * losange rouge : FK

  * clé rouge : à la fois PK et FK

### Exemple : relation entre list et card 1:n

**Création d'une FK entre `list` et `card`**

1. Clic sur l'icone `1:n` avec pointillés (touche 2)

1. Clic sur la table du côté du 1 (card) PUIS sur la table du côté du n (list)

/!\ ordre important, on clique donc d'abord sur `card` puis sur `list`

Nomenclature de nommage des FK : nom de la table de référence contenant la PK + nom du champ dans la table de la PK

Ce qui donne ici : `list_id`

### Exemple : relation entre card et label n:m

Obligatoirement dans ce cas : table de correspondance

Clic sur l'icone `n:m` (touche 5)

les cards ont des labels donc :

clic d'abord sur `card` puis sur `label`

apparaît une table intermédiaire de correspondance `card_has_label`

## Export du MLD

```
File > Export > Export as PNG...
```

Ne pas saisir l'extension (png par défaut)

## Export du script SQL

```
Menu > File > Export > Forward Engineering SQL Create Script
Sélectionner un fichier SQL via les ... en haut à droite
Allez dans docs et saisir S06-E01-export-okanban (ne pas saisir d'extension, .sql mis par défaut)
```

Cocher uniquement :
```
Skip creation of Foreign Keys (on évite la création des contraintes de fk)
Ommit schema qualifier in Object Names (nom de la DB)
Generate INSERT statements for tables (lignes à insérer dans la table lors de l'export)
Next
Next
Finish
```

## Import du script SQL

1. Dans PMA, on crée un nouveau compte d'utilisateurs en allant dans l'onglet Compte d'utilisateurs

2. Cliquer sur Ajouter un compte d'utilisateur

  ```
  nom d'utilisateur : okanban
  LOCAL
  password : okanban
  ```

3. Cliquer sur Créer une base portant son nom et donner à cet utilisateur tous les privilèges sur cette base.

4. Ajouter (exécuter)