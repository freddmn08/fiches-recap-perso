# Routes

## 1. Routing dans Symfony 4

[doc routing](https://symfony.com/doc/master/routing.html) - [fiche recap routing](https://github.com/O-clock-Alumni/fiches-recap/blob/master/symfony/themes/core-routing-controllers.md) - [wikipedia YAML](https://fr.wikipedia.org/wiki/YAML)

Une route possède un nom et met en jeu un endpoint (une URL simplifiée), un contrôleur et une méthode. Avec Symfony 4, on peut configurer une route en YAML, en XML ou en PHP. Ces différents formats proposent les mêmes fonctionnalités et les mêmes performances, libre choix pour le développeur !

>YAML pour _Yet Another Markup Language_ est un format de données présentées sous forme de tableau associatif clé - valeur. Il est utilisé par Symfony pour ses formats d'entrée et de sortie.

Symfony propose également une fonctionnalité appelée `annotations` permettant de définir les routes dans les contrôleurs (voir §3).

## 2. Création d'une route

### 2.a) Sans paramètre d'URL

#### Codage de la route en YAML

La route se définit dans le fichier `config/routes.yaml`. On commence par écrire le nom de la route en snake case. Ensuite, la clé `path` contient le endpoint et la clé `controller` définit le contrôleur et la méthode associés à la route.

```
// config/routes.yaml

# route "nom_de_la_route"
nom_de_la_route:
    path: /endpoint
    controller: App\Controller\MonControleur::maMethode
```

#### Codage de la route en PHP

La route se définit dans le fichier `config/routes.php`. On instancie un nouvel objet de la classe `RouteCollection` et on lui applique la méthode `add` pour créer une nouvelle route.

```
// config/routes.php
use Symfony\Component\Routing\RouteCollection;
use Symfony\Component\Routing\Route;
use App\Controller\MonControleur;

$routes = new RouteCollection();
$routes->add('nom_de_la_route', new Route('/endpoint', array(
    '_controller' => [MonControleur::class, 'maMethode']
)));

return $routes;
```

#### Codage de la méthode et du contrôleur

On définit une classe `Controleur` dans le dossier `src/Controller/Controleur.php` et on y code une méthode `maMethode`. Cette méthode doit renvoyer un objet de type `Response` (on ne peut renvoyer une simple chaîne de caractères comme dans Laravel).

```
// src/Controller/MonControleur.php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;

class MonControleur extends AbstractController
{

    public function maMethode()
    {
        return new Response(
            '<html><body>Hello world!</body></html>'
        );
    }
}
```

### 2.b) Avec paramètre d'URL

Si l'on souhaite passer un paramètre dans l'URL d'une route, il suffit de le préciser entre `{}` dans le endpoint comme suit : `{param}`. On peut également préciser optionnellement une contrainte sur le paramètre en indiquant une expression régulière à matcher à la clé `requirements`.

#### Codage de la route en YAML

```
# route "nom_de_la_route"
nom_de_la_route:
    path: /endpoint/{param}
    controller: App\Controller\MonControleur::maMethode
    requirements:
        param: '\d+'
```

> `\d+` est une expression régulière signifiant que l'on attend un entier d'au moins un chiffre

#### Codage de la route en PHP

```
// config/routes.php
use Symfony\Component\Routing\RouteCollection;
use Symfony\Component\Routing\Route;
use App\Controller\MonControleur;

$routes = new RouteCollection();
$routes->add('nom_de_la_route', new Route('/endpoint/{param}', array(
    '_controller' => [MonControleur::class, 'maMethode']
)));

return $routes;
```

Avec contrainte sur le paramètre d'URL (on la précise dans un array à la suite de la méthode `add`) :

```
// config/routes.php
use Symfony\Component\Routing\RouteCollection;
use Symfony\Component\Routing\Route;
use App\Controller\MonControleur;

$routes = new RouteCollection();
$routes->add('nom_de_la_route', new Route('/endpoint/{param}', array(
    '_controller' => [MonControleur::class, 'maMethode'],
), array(
    'param' => '\d+'
)));

return $routes;
```

**Codage du contrôleur et de la méthode :** on passe alors en argument de la méthode associée à la route la variable `$name` et on la concatène dans la chaîne retournée (le nom de la variable doit nécessairement être celui du paramètre d'URL noté entre accolades)

```
// src/Controller/MonControleur.php

namespace App\Controller;

use Symfony\Component\HttpFoundation\Response;
// use Symfony\Component\Routing\Annotation\Route;

class MonControleur
{

    public function maMethode($param)
    {
        return new Response(
            '<html><body>Article numéro '.$param.'</body></html>'
        );
    }
}
```

### 2.c) Avec paramètre d'URL et valeur par défaut

#### Codage de la route en YAML

Méthode 1 : on passe la valeur par défaut dans le corps de la méthode à la clé `defaults`.

```
# route "nom_de_la_route"
nom_de_la_route:
    path: /endpoint/{param}
    controller: App\Controller\MonControleur::maMethode
    defaults:
        param: valeur_par_defaut
    requirements:
        param: '\d+'
```

Méthode 2 : on passe la valeur par défaut dans le endpoint

```
# route "nom_de_la_route"
nom_de_la_route:
    path: /endpoint/{param<\d+>?1}
    controller: App\Controller\MonControleur::maMethode
```

#### Codage de la route en PHP

Méthode 1 : on passe la valeur par défaut à la clé `'param'` du tableau définissant le contrôleur et la méthode.

```
// config/routes.php
use Symfony\Component\Routing\RouteCollection;
use Symfony\Component\Routing\Route;
use App\Controller\MonControleur;

$routes = new RouteCollection();
$routes->add('nom_de_la_route', new Route('/endpoint/{param}', array(
    '_controller' => [MonControleur::class, 'maMethode'],
    'param' => valeur_par_defaut
), array(
    'param' => '\d+'
)));

return $routes;
```

Méthode 2 : on passe la valeur par défaut dans le endpoint

```
// config/routes.php
use Symfony\Component\Routing\RouteCollection;
use Symfony\Component\Routing\Route;
use App\Controller\MonControleur;

$routes = new RouteCollection();
$routes->add('nom_de_la_route', new Route('/endpoint/{param<\d+?valeur_par_defaut>}', array(
    '_controller' => [MonControleur::class, 'maMethode'],
)));

return $routes;
```

## 3. Création d'une route avec annotations

### 3.a) Généralités

[doc phpdoc](http://docs.phpdoc.org/references/phpdoc/basic-syntax.html#what-is-a-docblock)

On peut plus simplement définir une route à l'aide de `annotations`. Pour cela, il est nécessaire d'exécuter au moins une fois la commande : `composer require annotations`.

>La documentation de Symfony recommande l'utilisation d'`annotations` comme bonne pratique pour créer une route

En pratique, on importe le namespace `Route` dans le fichier du contrôleur via la commande `use Symfony\Component\Routing\Annotation\Route;`.

Avec `annotations`, on ne définit plus la route dans un fichier dédié (`routes.yaml` ou `routes.php`), mais dans le DocBlock de la méthode qui lui est associée à l'aide de la commande `@Route`. Eh oui, dans Symfony on définit une route dans un... commentaire :flushed: On veillera bien à l'entourer par `/** ... route ... */` et non pas avec des doubles slashes `//`.

**Arguments de la commande `@Route` :**

* 1er argument : endpoint

* argument (optionnel) à la clé `name` : nom de la route

* argument (optionnel) à la clé `methods` : méthode HTTP

* argument (optionnel) à la clé `requirements` : expression régulière que l'éventuel paramètre d'URL doit matcher

On peut également définir l'internationalisation de l'URL de façon unique dans une langue donnée sans duplication ([doc localized routing](https://symfony.com/doc/current/routing.html#localized-routing-i18n))

**Exemple de route :**

```
/**
 * @Route("/product/{id}", name="product_show", requirements={"id"="\d+"}, methods={"GET"})
 */
```

:warning: On délimitera bien les arguments avec des double quotes `"` et non pas avec des simple quotes `'` (ne marche pas !)

**Codage d'une route avec passage de paramètre + méthode :**

```
// src/Controller/MonControleur.php

namespace App\Controller;

use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class MonControleur
{
    /**
     * @Route("/endpoint/{param}", name="nom_de_la_route", methods={"GET"})
     */
    public function maMethode($param)
    {
        return new Response(
            '<html><body>Hello '.$param.' !</body></html>'
        );
    }
}
```

### 3.b) Contraintes sur le paramètre d'URL

On peut définir une contrainte sur un paramètre d'URL dans le BlockDoc de la méthode associée à la route avec la clé `requirements`.

```
class MonControleur
{
    /**
     * @Route("/endpoint/{param}", name="nom_de_la_route", requirements={"param"="\d+"})
     */
    public function maMethode($param)
    {
        return new Response(
            '<html><body>Hello '.$param.' !</body></html>'
        );
    }
}
```

On peut également définir cette contrainte de façon `inline` dans le endpoint en précisant l'expression régulière attendue à la suite du paramètre d'URL dans les accolades entourant celui-ci.

```
class MonControleur
{
    /**
     * @Route("/endpoint/{param<\d+>}", name="nom_de_la_route")
     */
    public function maMethode($param)
    {
        return new Response(
            '<html><body>Hello '.$param.' !</body></html>'
        );
    }
}
```

### 3.c) Valeur par défaut du paramètre d'URL

On peut définir une valeur par défaut d'un paramètre d'URL avec `annotations`.

Méthode 1 : on lui attribue une valeur par défaut en argument de la méthode.

```
class MonControleur
{
    /**
     * @Route("/endpoint/{param}", name="nom_de_la_route", requirements={"param"="\d+"})
     */
    public function maMethode($param = valeur_par_defaut)
    {
        return new Response(
            '<html><body>Hello '.$param.' !</body></html>'
        );
    }
}
```

Méthode 2 : on passe la valeur par défaut dans le endpoint suivie du symbole `?`.

```
class MonControleur
{
    /**
     * @Route("/endpoint/{param<\d+>?valeur_par_defaut}", name="nom_de_la_route")
     */
    public function maMethode($param)
    {
        return new Response(
            '<html><body>Hello '.$param.' !</body></html>'
        );
    }
}
```