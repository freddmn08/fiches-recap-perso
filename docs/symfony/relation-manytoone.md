# Création d'une relation ManyToOne avec Symfony 4

A guise d'exemple, on crée une relation ManyToOne entre les entités Author et Article.

## Création de la base de données et des entités

1. Configuration de la connexion à la bdd dans le fichier `.env`

1. Création de la base de données : `php bin/console doctrine:database:create`

1. Création des entités :

* **Article** `php bin/console make:entity`

    * Article

    * title - type string - 200 - non null
    
    * published_at
    
    * body
    
    * image

* **Author** `php bin/console make:entity`

    * Author

    * name - type string - 100 - non null

    * nb_article (champ incongru car valeur calculée)

1. Création de la migration `php bin/console make:migration`

1. Envoi de la migration `php bin/console doctrine:migrations:migrate`

>Une seule migration pour une modif donnée sinon erreur !

**En cas de besoin de rollback :**

pour revenir à la migration précédente : `php bin/console make:migration prev`

Supprimer la dernière migration

### Création de la relation ManyToOne entre Article et Author

[lien doctrine relations](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/association-mapping.html)

**Postulat :**

* Un article est lié à un seul **(one)** auteur ;
* Un auteur peut avoir écrit plusieurs **(many)** articles.

`Author` : 1,n <-> 1,1 : `Article`

`Article` est le côté fort de la relation (owner de la relation, celui du côté 1:1).

ManyToOne `Article` vers `Author`

OneToMany `Author` vers `Article`

**Création de la relation avec la console :**

1. On se place dans l'entité côté owner de la relation, ie dans Article : `php bin/console make:entity Article`

1. New property name : `author`

1. Field type : `relation`

1. What class should it be related to? : `Author`

1. Relation type?

    ```
    ManyToOne   Each Article relates to (has) one Author.
                Each Author can relate/has to (have) many Article objects
    ```

1. Is the Article.author property allowed to be null? `no`

1. Do you want to add a new property to Author so that you can access/update Article objects from it - e.g. $author->getArticles()? `yes`

1. New field name inside Author [articles]: `articles`

1. Do you want to automatically delete orphaned App\Entity\Article objects (orphanRemoval)? (yes/no) [no]: `no`

### Validation du mapping

Pour tester le mapping : `php bin/console doctrine:schema:validate`

Si "Error The database is not in sync with the current mapping file" : `php bin/console doctrine:schema:update`

### Gestion des migrations

`php bin/console make:migration`

`php bin/console doctrine:migrations:migrate`