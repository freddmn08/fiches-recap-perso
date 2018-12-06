# L'ORM Doctrine

[doc repository]() - [cours OCR Repository Symfo 2](https://openclassrooms.com/fr/courses/2078536-developpez-votre-site-web-avec-le-framework-symfony2-ancienne-version/2081287-recuperer-ses-entites-avec-doctrine2) - [cours Isis](http://www.lsis.org/elmouelhia/sf/coursSymfonyDoctrine.pdf)

## 1. Un ORM, c'est quoi ?

Un ORM pour Object-Relational Mapping (lien objet-relation) est une couche d'abstraction orientée objet à une base de données relationnelle. Elle permet de faire communiquer et cohabiter ces deux univers a priori incompatibles.

Plus précisément, un ORM est une classe permettant à l'utilisateur de manipuler les tables d'une base données comme des objets. En pratique, cela se réalise en associant :

* à chaque table, une (ou plusieurs) classe appelée Entity (entité) ;

* pour une table donnée, un attribut de classe à chaque champ de la table.

L'objectif d'un ORM est aussi que l'utilisateur n'ait plus à écrire de requêtes à la mano en mettant à sa disposition des objets et méthodes.

Chaque langage de POO dispose d'ORM, c'est le cas de PHP avec **Doctrine** proposé en 2006 et  utilisé par Symfony depuis sa version 1.3.

## 2. La notion d'Entity

Comme vu précédemment, une entité est associée à une table d'une base de données relationnelle. C'est un objet contenant des informations appelées **annotations** permettant de faire le mapping (= le lien) avec la base de données.

### 2.a) Annotations

Avec Doctrine, des méta-données appelées annotations sont écrites dans des commentaires spéciaux dans le DocBlock des attributs de l'entité.

**Exemple d'annotations :**

| Annotation | Désignation |
| :-- |--|--|
| @ORM\Entity | marque qu’une classe PHP est une entité
| @ORM\Table | décrit la table d’une entité persistante
| @ORM\Column | définit les caractéristiques d’une colonne
| @ORM\Id | marque l’identifiant de l’entité
| @ORM\GeneratedValue | utilisé pour générer des identifiants annotés par @Id
| @ORM\OneToOne | entité en relation avec une seule entité
| @ORM\OneToMany | entité en relation avec plusieurs entités
| @ORM\ManyToMany | entités en relation avec plusieurs entités

>ORM\ sera rajouté par Symfony

Toutes les annotations sont disponibles dans [la documentation de Doctrine](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/annotations-reference.html#annotations-reference).

>Les annotations @var, @return, @param, @author... sont des
annotations phpdoc et non pas Doctrine.

Exemples d'annotations dans la fiche récap annotations.

### 2.b) La création d'une Entity

On crée une entité à l'aide de la console de Symfony. On détermine ses caractéristiques en répondant à des questions permettant de renseigner le nom de l'entité puis les informations des différents champs (nom, type et sa longueur au besoin).

Les types définis dans les annotations Doctrine ne sont ni les types PHP ni ceux de MySQL, mais il existe un mapping entre ces différents types.

**Exemples de mapping Doctrine :**

| Type      |     Désignation    |   Type MySQL |
| :--: |--|--|
| string | chaîne avec au plus 255 caractères | VARCHAR |
| boolean | valeurs booleennes true et false | BOOLEAN |
| integer | entier inférieur à 2 147 483 647 | INT |
| date | date et heure | DATETIME |
| datetime | durée | TIMESTAMP |
|object | objet / tableau / text | clob |
| float | nombres décimaux | float |

>Les types Doctrine sont sensibles à la casse

La liste complète est disponible dans [la documentation Doctrine](https://www.doctrine-project.org/projects/doctrine-dbal/en/2.6/reference/types.html#types).

**Exemple d'Entity avec Doctrine :**

```
<?php

namespace AppBundle\Entity;
use Doctrine\ORM\Mapping as ORM;

/**
* @ORM\Entity()
* @ORM\Table(name="blog_article")
* */
class Article
{
    /**
    * @ORM\Id()
    * @ORM\GeneratedValue(strategy="AUTO")
    * @ORM\Column(type="integer")
    */
    public $id;

    /**
    * @ORM\Column(type="string")
    */
    public $title;

    /**
    * @ORM\Column(type="text")
    */
    public $content;

    /**
    * @ORM\Column(type="datetime", name="date")
    */
    public $date;
}
```

## 3. L'EntityManager

C'est un service de Doctrine permettant la manipulation des entités. Il fait le lien entre les entités qui sont des objets PHP et la base de données. On peut accéder à l'EntityManager partout depuis le contrôleur.

L'EntityManager ne synchronise pas directement chaque changement avec la base de données et cela pour des raisons de performance et d'intégrité. La synchronisation en base ne s'effectue que lors de l'exécution de la méthode `flush()` et est effectuée sous forme d'une transaction qui est annulée en cas d'échec.

L'EntityManager dispose de fonctions permettant la manipulation des données en base :

* la fonction `find` : elle permet de trouver et de mapper un objet à partir d'informations retrouvées en base ;

* la fonction `persist` : elle permet d'ajouter l'objet manipulé dans l'Unit of Work ;

* la fonction `flush` : elle exécute une transaction avec la base de données pour tous les objets "marqués" pour être ajoutés, mis à jour ou supprimés.

## 4. Un repository, c'est quoi ?

Repository est un design pattern (patron de conception en français) proposé par Symfony via Doctrine. On a déjà manipulé des design patterns comme MVC ou ActiveRecord... Dans Symfony, Repository concerne la couche Model de l'architecture MVC.

Il existe un repository par entité, il centralise tout ce qui touche à la récupération des entités. C'est un objet qui permet de récupérer les entités précisées en argument.

>Rappel : une entité est un objet que l'ORM Doctrine va manipuler et enregistrer en base de données

**Exemples :**

* récupérer une entité par son id

* récupérer une liste d'entités suivant un critère donné

Pour toute récupération d'entités en base de données, on utilisera le repository de l'entité associée.

>On ne fait pas de requêtes SQL en dehors d'un repository.

Dans un repository, on construit des méthodes permettant de récupérer une entité par son id, pour récupérer des entités selon certains critères, etc.

Les repositories utilisent l'EntityManager pour fonctionner.

## 2. Deux méthodes de récupération d'une entité

### 2.a) Avec le Doctrine Query Language (DQL)

Le DQL est une adaptation du langage SQL à Doctrine.

### 2.b) Avec le QueryBuilder

Le QueryBuilder est un outil permettant de constuire une requête par étape en "empilant" des méthodes les unes à la suite des autres.

## 3. Les méthodes de manipulation des données en base

Les repositories héritent de la classe `Doctrine\ORM\EntityRepository` qui propose déjà quelques méthodes très utiles pour récupérer des entités. En voici quelques exemples.

### 3.a) find($id)

La méthode `find($id)` récupère l'entité correspondant à l'id `$id`, elle retourne une instance de la classe correspondant à l'entité en question.

**Exemple :**

### 3.b) findAll()

La méthode `findAll()` retourne toutes les entités sous la forme d'un tableau.

**Exemple :**

### 3.c) findBy()

La méthode `findBy()` retourne les entités respectant certains critères sous la forme d'un tableau.

### 3.d) findOneBy()

La méthode `findOneBy()` retourne l'entité (une seule donc !) respectant certains critères sous la forme d'un tableau.