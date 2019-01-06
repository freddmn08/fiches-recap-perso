# Création d'une relation ManyToMany avec Symfony 4

[lien symfony relations](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/association-mapping.html) - [doc doctrine ManyToMany](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/association-mapping.html#many-to-many-bidirectional) - [tuto site web](https://zestedesavoir.com/tutoriels/1713/doctrine-2-a-lassaut-de-lorm-phare-de-php/les-relations-avec-doctrine-2/relation-manytomany-n-m/)

<!-- ## 1. Relation ManyToMany réelle entre les entités Movie et Genre

**Postulat : relation Movie - Genre**

* Un movie peut posséder plusieurs **(many)** genres ;
* Un genre peut être associé à plusieurs **(many)** movies.

`Movie` : 1,n <-> 1,n : `Genre`

**Désignation du owner d'une relation ManyToMany :**

Pas de côtés fort / faible, on décide du owner en fonction des requêtes à effectuer.

**Cas du projet Movie :** site traitant de films, on choisit Movie comme owner des relations Movie - Genre et Movie - Person

Dans la console, on se place sur l'entité à modifier (celle sur laquelle ajouter la relation)

Dans l'annotation, Inversed by du côté du owner

**Création de la relation Movie - Genre avec la console :**

1. On se place dans l'entité côté owner de la relation, ie dans Movie : `php bin/console make:entity Movie`

1. New property name : `genres`

1. Field type : `relation`

1. What class should it be related to? : `Genre`

1. Relation type?

    ```
    ManyToMany  Each Movie relates to (has) one Author.
                Each Author can relate/has to (have) many Article objects
    ```

On répond yes et yes

### Jeu des migrations

`php bin/console doctrine:databe:create`

`php bin/console make:migration`

`php bin/console doctrine:migrations:migrate`

Dans PMA, apparaît une table `movie_genre` entre les tables movie et genre -->

## 1. Relation ManyToMany entre les entités Movie et Person

**Création de la relation Movie - Person avec la console :**

**Postulat : relation Movie - Person**

* Un movie peut posséder plusieurs **(many)** persons ;
* Une person peut être associée à plusieurs **(many)** movies.

`Movie` : 1,n <-> 1,n : `Person`

**Désignation du owner d'une relation ManyToMany :**

Pas de côtés fort / faible, on décide du owner en fonction des requêtes à effectuer.

**Cas du projet Movie :** site traitant de films, on choisit Movie comme owner des relations Movie - Genre et Movie - Person

Dans la console, on se place sur l'entité à modifier (celle sur laquelle ajouter la relation)

Dans l'annotation, Inversed by du côté du owner

1. On se place dans l'entité côté owner de la relation, ie dans Movie : `php bin/console make:entity Movie`

1. New property name : `persons`

1. Field type : `relation`

1. What class should it be related to? : `Person`

1. Relation type? ManyToMany

    <!-- ```
    ManyToMany  Each Movie relates to (has) one Author.
                Each Author can relate/has to (have) many Article objects
    ``` -->

On répond yes et yes

### Jeu des migrations :

`php bin/console doctrine:databe:create`

`php bin/console make:migration`

`php bin/console doctrine:migrations:migrate`

Dans PMA, apparaît une table `movie_person` entre les tables movie et person

## 2. Transformation d'une relation ManyToMany en deux relations ManyToOne et OneToMany

La relation entre les entités Movie et Person est de type ManyToMany. Symfony crée une table de correspondance sur laquelle on n'a pas la main. On peut alors supprimer cette relation et par conséquent cette table pour créer deux relations ManyToOne et OneToMany via la table intermédiaire `Casting`.

### Création de la relation ManyToOne Casting - Person

**Postulat :**

* Un casting est lié à une **(one)** person ;
* Une person peut intervenir dans plusieurs **(many)** casting.

`Person` : 1,n <-> 1,1 : `Casting`

`Casting` est le côté fort de la relation (owner de la relation, celui du côté 1:1).

* Relation OneToMany dans le sens `Person` vers `Casting`

* Relation ManyToOne dans le sens `Casting` vers `Person`.

**Création de la relation avec la console :**

1. On se place dans l'entité côté owner de la relation, ie dans `Casting` : `php bin/console make:entity Casting`

1. New property name : `person`

1. Field type : `relation`

1. What class should it be related to? : `Person`

1. Relation type?

    ```
    ManyToOne   Each Casting relates to (has) one Person
                Each Person can relate/has to (have) many Casting objects
    ```

1. Is the Casting.person property allowed to be null (nullable)? (yes/no) [yes]: `no`

1. Do you want to add a new property to Person so that you can access/update Casting objects from it - e.g. $person->getCastings()? (yes/no) [yes]: `yes`

1. A new property will also be added to the Person class so that you can access the related Casting objects from it.

    New field name inside Person [castings]: `castings`

1. Do you want to automatically delete orphaned App\Entity\Casting objects (orphanRemoval)? (yes/no) [no]: `no`

>On crée de façon analogue la relation OneToMany entre Movie et Casting