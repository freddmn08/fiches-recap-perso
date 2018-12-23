## Création d'une clé étrangère associée à une clé primaire dans PMA

__Préalables__ :
* S'assurer que le moteur de BDD des deux tables concernées est bien `InnoDB` (sélectionner la table, onglet Structure, colonne `Type`)

* Les données des deux champs concernés (celui de la clé primaire et celui de la clé étrangère) doivent contenir les mêmes types de données (exemple : SMALLINT(6)) et posséder toutes deux un index

* Convention de nommage de la clé étrangère : `FK_ForeignKeyTable_PrimaryKeyTable`
    * `FK` : Foreign Key

    * `ForeignKeyTable` : nom de la table contenant la clé étrangère

    * `PrimaryKeyTable` : nom de la table contenant la clé primaire à référencer (= table de référence)

_Exemple_ : dans la table `post`, on veut créer une clé étrangère faisant référénce à la clé primaire `id` de la table `author` (= table de référence), on nommera le champ associé à cette clé étrangère `FK_post_author`

Référence : https://stackoverflow.com/questions/199498/foreign-key-naming-scheme

---

### Méthode 1 - En passant par le menu "Vue relationnelle" d'une table

(le symbole % marque l'exemple de la création d'une clé étrangère `FK_post_author` dans la table `post` référençant la clé primaire `id` de la table `author`)


1. Sélectionner la table dans laquelle la clé étrangère va être créée (% table `post`)

1. Y créer un champ nommé `FK_ForeignKeyTable_PrimaryKeyTable` et lui associer un index (% nommer ce champ `FK_post_author`) 

1. Cliquer sur l'onglet `Structure`, puis sur le bouton `Vue relationnelle`

1. Dans le menu déroulant se trouvant sous `colonne`, choisir le nom du champ associé à la clé étrangère précédemment nommée (% champ `FK_post_author`)

1. Dans le menu déroulant `Base de données` se trouvant à droite du champ `Colonne`, vérifier que le nom de la base de données est correct (% table `blog`)

1. Dans le menu déroulant `Table` (se trouvant à droite du champ `Base de données`), choisir le nom de la table de référence contenant la clé primaire (% table `author`)

1. Dans le dernier menu déroulant tout à droite, choisir le nom du champ contenant la clé primaire dans la table de référence (% champ `id`)

Aperçu de la syntaxe SQL correspondante :

```ALTER TABLE `post` ADD FOREIGN KEY (`FK_post_author`) REFERENCES `author`(`id`) ON DELETE RESTRICT ON UPDATE RESTRICT;```

_Remarques_ :

* le champ `RESTRICT` dans ON DELETE empêche la suppression d'une entrée dans la table de référence (% table `author`) mais pas dans la table contenant la clé étrangère (% table `post`). Même comportement pour `CASCADE` dans ON UPDATE dans le cas non plus d'une suppression mais d'une mise à jour de la clé primaire

* le champ `CASCADE` dans ON DELETE autorise la suppression d'une entrée dans la table de référence mais supprime alors les données correspondantes dans la table contenant la clé étrangère (de même avec `CASCADE` dans ON UPDATE)

* avec le paramètre `SET NULL`, même idée sauf qu'au lieu par exemple de supprimer une entrée, on passe sa clé à `NULL`

---

### Méthode 2 - En passant par l'onglet "Concepteur de la base"

(le symbole % marque l'exemple de la création d'une clé étrangère `FK_post_category` dans la table `post` référençant la clé primaire `id` de la table `category`)

1. Sélectionner la table dans laquelle la clé étrangère va être créée (% table `post`)

1. Y créer un champ nommé `FK_ForeignKeyTable_PrimaryKeyTable` et lui associer un index (% nommer ce champ `FK_post_category`) 

1. Cliquer sur la base de données puis sur l'onglet Concepteur

1. Dans le menu sur la gauche, cliquer sur `Nouvelle relation`

1. Sélectionner la clé référencée, ie la clé primaire dans la table de référence (% clé `id` dans la table `category`)

1. Sélectionner la clé, ie la clé étrangère dans la table (% clé `FK_post_category` dans la table `post`)

1. Choisir alors les paramètres de `ON DELETE` et `ON UPDATE`
