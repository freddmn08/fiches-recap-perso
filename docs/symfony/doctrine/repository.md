# Les Repositories

[cours OCR - Récupérer ses entités avec Doctrine2](https://openclassrooms.com/fr/courses/3619856-developpez-votre-site-web-avec-le-framework-symfony/3622991-recuperer-ses-entites-avec-doctrine2)

## 1. Le rôle des repositories

### 1.1. La récupération des données

Récupérer des données est l'une des principales fonctions de la couche Modèle dans une application MVC. Récupérer seulement certaines données, les classer selon des critères, etc, se fait grâce aux repositories. Il gère donc l'aspect **Read** des méthodes CRUD.

Toutes les requêtes SQL se font dans les repositories et pas ailleurs. On y construit des méthodes permettant de récupérer une entité par son id, par la valeur de l'un de ses champs, etc.

Il existe un repository par entité. Un repository peut cependant faire appel à plusieurs entités dans le cas d'une jointure par exemple.

Les repositories fonctionnent en appelant dans leur méthode l'EntityManager, ces deux notions sont indissociables l'une de l'autre.

### 1.2. Deux façons de construire des requêtes de récupération d'entités

#### 1.2.1. Le Doctrine Query Language (DQL)

Le DQL est un langage proche du SQL et adapté à l'approche objet utilisée par Doctrine.

Exemple : `SELECT a FROM OCPlatformBundle:Advert a`

#### 1.2.2. Le QueryBuilder

Le QueryBuilder sert à construire une requête par étape.

**Exemple :**

```
<?php
$queryBuilder
  ->select('a')
  ->from('OCPlatformBundle:Advert', 'a')
;
```

Son intérêt réside dans le fait que l'on puisse construire la requête en plusieurs étapes. L'utilisation de la méthode `andWhere()` permet par exemple de gérer une nouvelle condition dans les filtres à appliquer aux résultats de la requête.

**Exemple :**

```
<?php

$queryBuilder = ...;

// Je rajoute ma condition, quel que soit ce que j'ai déjà dans mon QueryBuilder :
$queryBuilder->andWhere('champ = 1');
```

## 2. Les méthodes de base de récupération d'entités

Les repositories héritent de la classe `Doctrine\ORM\EntityRepository` qui propose quelques méthodes très utiles pour récupérer des entités.Voyons-les plus en détail.

### 2.1. Les méthodes de base

Elles sont au nombre de quatre : `find()`, `findAll()`, `findBy()` et `findOneBy()`.

:arrow_forward: **find($id)**

La méthode `find($id)` récupère l'entité correspondante à l'id `$id`. Dans le cas de `AdvertRepository`, elle retourne une instance d'`Advert`.

**Exemple :**

```
<?php
$repository = $this
  ->getDoctrine()
  ->getManager()
  ->getRepository('OCPlatformBundle:Advert')
;

$advert = $repository->find(5);
// $advert est une instance de OC\PlatformBundle\Entity\Advert
// Correspondant à l'id 5
```

:warning: Si aucune entité ne correspond à l'id passé en argument, alors la méthode `find` retourne `null`.

:arrow_forward: **findAll()**

La méthode `findAll()` retourne toutes les entités contenues dans la base de données. Le format du retour est un tableau PHP normal (un array) que l'on peut parcourir (avec un `foreach` par exemple) pour utiliser les objets qu'il contient.

**Exemple :**

```
<?php

// dans un contrôleur
$repository = $this
  ->getDoctrine()
  ->getManager()
  ->getRepository('OCPlatformBundle:Advert');


$listAdverts = $repository->findAll();

foreach ($listAdverts as $advert) {
  // $advert est une instance de Advert
  echo $advert->getContent();
}

// dans une vue Twig
<ul>
  {% for advert in listAdverts %}
    <li>{{ advert.content }}</li>
  {% endfor %}
</ul>
```

:arrow_forward: **findBy()**

&Agrave; l'image de la méthode `findAll()`, la méthode `findBy()` permet de retourner une liste d'entités, sauf que cette dernière est capable d'effectuer un filtre pour ne retourner que les entités correspondant à un ou plusieurs critère(s). Elle peut aussi trier les entités et même n'en récupérer qu'un certain nombre (pour une pagination par exemple).

**Syntaxe :**

```
<?php
$repository->findBy(
  array $criteria,
  array $orderBy = null,
  $limit  = null,
  $offset = null
);
```

Cette méthode retourne un array également. On peut mettre plusieurs entrées dans le tableau des critères afin d'appliquer plusieurs filtres (qui seront associés avec un `AND` et non un `OR`).

**Exemple :** récupération de toutes les entités ayant comme auteur « Alexandre » en les classant par date décroissante et en en sélectionnant 5 à partir du début (0)

```
<?php

$listAdverts = $repository->findBy(
  ['author' => 'Alexandre'], // Critere
  ['date' => 'DESC'],        // Tri
  5,                         // Limite
  0                          // Offset
);

foreach ($listAdverts as $advert) {
  // $advert est une instance de Advert
 echo $advert->getContent();
}
```

:arrow_forward: **findOneBy()**

La méthode `findOneBy(array $criteria, array $orderBy = null)` fonctionne sur le même principe que la méthode `findBy()`, sauf qu'elle ne retourne qu'une seule entité. Les arguments `limit` et `offset` n'existent donc pas.

**Exemple :**

```
<?php

$advert = $repository->findOneBy(['author' => 'Marine']);
// $advert est une instance de Advert
```

A l'instar de la méthode `find`, la méthode `findOneBy` retourne `null` si aucune entité ne correspond aux critères demandés. Si plusieurs entités correspondent aux critères, alors c'est la première dans l'ordre que vous avez demandé (argument `$orderBy`) qui sera retournée.

### 2.2. Les méthodes magiques

[cours OCR - Les méthodes magiques en PHP](https://openclassrooms.com/fr/courses/1665806-programmez-en-oriente-objet-en-php/1666950-les-methodes-magiques)

Si les quatre méthodes de base ne suffisent pas, Doctrine offre deux autres méthodes "magiques" : `findByX()` et `findOneByX()`.

**Rappel :** en PHP, une méthode magique est une méthode qui est appelée automatiquement lorsqu'un certain évènement est déclenché. Toutes les méthodes magiques commencent par deux underscores.

:arrow_forward: **findByX()**

En remplaçant « X » par le nom d'une propriété de l'entité en question. Dans notre cas, pour l'entité `Advert`, on dispose de plusieurs méthodes : `findByTitle()`, `findByDate()`, `findByAuthor()`, `findByContent()`, etc.

Cette méthode fonctionne comme la méthode `findBy()` avec un seul critère, celui du nom de la méthode.

**Exemple :** chercher toutes les annonces dont l'auteur est Alexandre

```
<?php

$listAdverts = $repository->findByAuthor('Alexandre');
// $listAdverts est un Array qui contient toutes les annonces
// écrites par Alexandre
```

Cette requête est parfaitement équivalente à `findBy(['author' => 'Alexandre'])`.

:arrow_forward: **findOneByX()**

En remplaçant « X » par le nom d'une propriété de l'entité en question. Dans le cas de l'entité `Advert`, on dispose de plusieurs méthodes : `findOneByTitle()`, `findOneByDate()`, `findOneByAuthor()`, `findOneByContent()`, etc.

Cette méthode fonctionne comme `findOneBy()`, sauf qu'on ne peut mettre qu'un seul critère, celui du nom de la méthode.

**Exemple :**

```
<?php

$advert = $repository->findOneByTitle('Recherche développeur.');
// $advert est une instance d'Advert dont le titre
// est "Recherche développeur." ou null si elle n'existe pas
```

## 3. Les méthodes custom de récupération d'entités

Les méthodes de base vues ci-dessus permettent de récupérer des entités dans la plupart des cas. Simplement, elles montrent rapidement leurs limites lorsqu'on doit faire des jointures ou effectuer des conditions plus complexes. Pour cela — et cela arrive très souvent — il faudra faire des méthodes custom de récupération.

Pour cela, il est nécessaire de distinguer trois types d'objets jouant un rôle central dans la création de requêtes custom : le QueryBuilder, la Query et les résultats.

### 3.1. Le QueryBuilder

Le `QueryBuilder` permet de constuire une `Query` (i.e. une requête) sans en être une pour autant !

Pour récupérer un `QueryBuilder`, on peut simplement utiliser l'`EntityManager`. En effet, il dispose d'une méthode `createQueryBuilder()` qui retourne une instance de `QueryBuilder`. L'EntityManager est accessible depuis un repository en utilisant l'attribut `_em`, soit via `$this->_em`. Le code complet pour récupérer un `QueryBuilder` neuf depuis une méthode d'un repository est donc : `$this->_em->createQueryBuilder()`.

>En l'absence d'argument passé à la méthode `createQueryBuilder()`, celle-ci retourne un `QueryBuilder` vide sans rien de prédéfini (c'est dommage car lorsqu'on récupère un `QueryBuilder` depuis un repository, c'est dans l'idée de faire une requête sur l'entité gérée par ce repository). Donc si l'on pouvait définir la partie `SELECT advert FROM OCPlatformBundle:Advert` sans trop d'effort, cela serait bien pratique, car ce qui est intéressant, c'est le reste de la requête.

Heureusement, le repository contient également une méthode `createQueryBuilder($alias)` qui utilise la méthode de l'`EntityManager`, mais en définissant pour nous le `SELECT` et le `FROM`.

[Code source de la méthode createQueryBuilder()](https://github.com/doctrine/orm/blob/master/lib/Doctrine/ORM/EntityRepository.php#L58)

L'alias en argument de la méthode `createQueryBuilder()` est le raccourci que l'on donne à l'entité du repository. On utilise souvent la première lettre du nom de l'entité, dans notre exemple de l'annonce (entité Advert), cela serait donc un « a ».

**Exemple :** recréation de la méthode `findAll()` dans le repository `Advert` 

```
<?php
// src/OC/PlatformBundle/Entity/AdvertRepository.php

namespace OC\PlatformBundle\Entity;

use Doctrine\ORM\EntityRepository;

class AdvertRepository extends EntityRepository
{
  public function myFindAll()
  {
    // Méthode 1 : en passant par l'EntityManager
    $queryBuilder = $this->_em->createQueryBuilder()
      ->select('a')
      ->from($this->_entityName, 'a')
    ;
    // Dans un repository, $this->_entityName est le namespace de l'entité gérée
    // Ici, il vaut donc OC\PlatformBundle\Entity\Advert

    // Méthode 2 : en passant par le raccourci (je recommande)
    $queryBuilder = $this->createQueryBuilder('a');

    // On n'ajoute pas de critère ou tri particulier, la construction
    // de notre requête est finie

    // On récupère la Query à partir du QueryBuilder
    $query = $queryBuilder->getQuery();

    // On récupère les résultats à partir de la Query
    $results = $query->getResult();

    // On retourne ces résultats
    return $results;
  }
}
```