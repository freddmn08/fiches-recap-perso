# Twig, le moteur de templates de Symfony

[Twig](https://twig.symfony.com/) - [doc symfony Twig](https://symfony.com/doc/master/templating.html) - [cours OCR Twig](https://openclassrooms.com/fr/courses/5489656-construisez-un-site-web-a-l-aide-du-framework-symfony-4/5517021-dynamisez-vos-vues-a-l-aide-de-twig)

## 1. Présentation de Twig

Symfony dispose d'un puissant moteur de templates appelé Twig, il a été pensé et codé par le créateur de Symfony, Fabien Potencier. Twig se présente sous la forme d'une librairie externe à Symfony (on peut très bien utilisé Twig comme moteur de templates dans un projet PHP en dehors de Symfony).

Cet outil est plus adapté que PHP en tant que moteur de templates pour au moins trois raisons :

* Twig a une syntaxe plus concise et plus claire (in ne contient pas de PHP, facile donc d'accès pour un intégrateur !)

* Par défaut, il supporte de nombreuses fonctionnalités telles que la notion d'héritage

* Il sécurise automatiquement les variables

On rappelle qu'un moteur de template n'est pas censé contenir du code logique (ou le moins possible, aux contrôleurs de traiter celui-ci), il a pour but de gérer le rendu d'une view.

## 2. Installation du bundle Twig

Pour utiliser Twig comme moteur de rendu, il est nécessaire de l'installer : `composer require symfony/twig-bundle`

Les views se stockent alors dans le dossier `/templates` et se suffixent avec `.twig`

**Exemple :** `hello.html.twig`

## 3. Syntaxe Twig

Twig apporte trois nouvelles syntaxes utilisables dans les views :

* `{{ ... }}` pour _afficher_ quelque chose, la valeur d'une variable ou le résultat d'une expression

* `{% ... %}` pour _exécuter_ quelque chose, une boucle for par exemple

* `{# ... #}` pour _commenter_ quelque chose sur une ou plusieurs lignes, c'est l'équivalent de la syntaxe PHP native `/* commentaire */`

**Exemple de template avec Twig :**

```
{# Ceci est un exemple de template Twig #}
{% set collection = [1, 2, 3] %}

<ul>
{% for item in collection %}
    <li>{{ item }}</li>
{% endfor %}
</ul>

{# 
    Va afficher le code HTML suivant :

    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
#}
```

## 4. Exemple d'un Hello world! avec view + controller

La fonction `render()` gère l'affichage de la view, elle prend en paramètre le chemin vers la view et un tableau de paramètres qu'on lui passe (ceux-ci sont alors disponibles dans la view).

**Code du contrôleur :**

```
<?php

// src/Controller/HelloController.php
namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Routing\Annotation\Route;

class HelloController extends AbstractController
{
    /**
     * Hello world avec Twig
     *
     * @Route("/hello/{name}", name="hello")
     */
    public function hello($name)
    {
        $this->render('hello.html.twig', ['name' => $name]);
    }
}
```

**Code de la view :**

```
{# /templates/hello.html.twig #}

Hello {{ name }}!
```

## 5. Fonctionnalités par défaut

Twig dispose par défaut de [filtres](https://twig.symfony.com/doc/2.x/filters/index.html), de [tags](https://twig.symfony.com/doc/2.x/tags/index.html), d'opérateurs et de [fonctions](https://twig.symfony.com/doc/2.x/functions/index.html) permettant de modifier le contenu d'une view avant son rendu. Il est également possible d'en créer de façon custom via l'[extension Twig](https://symfony.com/doc/master/templating/twig_extension.html).

### 5.a) Les opérateurs

Les opérateurs PHP habituels sont disponibles dans Twig.

* Opérateur `in` : effectue un test d'appartenance. Il renvoie true si l'opérande de gauche est contenu dans la quantité de droite

**Exemple :**

```
{# returns true #}

{{ 1 in [1, 2, 3] }}

{{ 'cd' in 'abcde' }}
```


* Opérateur `is` : permet de tester une variable vis-à-vis d'une expression. L'opérande de droite est le nom du test

**Exemple :**

```
{# find out if a variable is odd #}

{{ name is odd }}

{# autres possibilités :
is null: si est null
is constant: comparer si est une constante
is divisible by(x): si est divisible par x
is even: si est pair
is odd: si est impair
is iterable: si est du type itérable (comme une liste)
is same as: comparer 2 variables (en php correspond ===)
}
```

* Opérateurs mathématiques : Twig supporte les opérateurs mathématiques usuels (+, -, /, %, //, *, **, %)

**Exemple :**

```
+: Adds two objects together (the operands are casted to numbers). {{ 1 + 1 }} is 2.
-: Subtracts the second number from the first one. {{ 3 - 2 }} is 1.
/: Divides two numbers. The returned value will be a floating point number. {{ 1 / 2 }} is {{ 0.5 }}.
%: Calculates the remainder of an integer division. {{ 11 % 7 }} is 4.
//: Divides two numbers and returns the floored integer result. {{ 20 // 7 }} is 2, {{ -20 // 7 }} is -3 (this is just syntactic sugar for the round filter).
*: Multiplies the left operand with the right one. {{ 2 * 2 }} would return 4.
**: Raises the left operand to the power of the right operand. {{ 2 ** 3 }} would return 8.
```

* Opérateurs de logique : `and`, `or`, `not`, etc

* Opérateurs de comparaison : Twig supporte les opérateurs de comparaison classiques (==, !=, <, >, >=, <=)

**Exemple :**

```
{% if ((a==1 and b>0) or not c==0) and d is defined %} 
    {% set resultat = (d + a * b) / c %}
    {{ resultat }}
{% endif %}
```

On peut également tester si une expression matche une expression régulière.

**Exemple :**

```
{% if phone matches '/^[\\d\\.]+$/' %}
{% endif %}
```

* Autres opérateurs : [ici](https://twig.symfony.com/doc/2.x/templates.html#other-operators)

### 5.b) Les tags

[doc tags](https://twig.symfony.com/doc/2.x/tags/index.html)

Les tags sont des éléments de langage propres à Twig. On donne la liste des tags les plus utilisés :

* `block` ([doc](https://twig.symfony.com/doc/2.x/functions/block.html#block)) : définit un espace surchargeable, i.e. un placeholder dans lequel on peut faire figurer du contenu, élément essentiel des templates Twig

**Exemple :**

```
<title>{% block title %}{% endblock %}</title>

<h1>{{ block('title') }}</h1>
{% block body %}{% endblock %}
```

* `do`, `if`, `else`, `for`, (`with`) : éléments de langage identiques à PHP

* `import` : permet d'importer un fichier comprenant des macros

* `set` ([doc](https://twig.symfony.com/doc/2.x/tags/set.html#set)) : permet de définir une ou plusieurs variables

**Exemple :**

```
{# dans le layout}
{% set foo = 'bar' %}

{#
After the set call, the foo variable is available in the template like any other ones:
displays bar
#}
{{ foo }}
```

* `spaceless` : supprime tous les espaces entre les tags HTML

* `verbatim` ([doc](https://twig.symfony.com/doc/2.x/tags/verbatim.html#verbatim)): ne sera pas pris en compte par Twig (autrement dit, marque une section comme étant un textee brut)

**Exemple :**

```
{% verbatim %}
    <ul>
    {% for item in seq %}
        <li>{{ item }}</li>
    {% endfor %}
    </ul>
{% endverbatim %}
```

### 5.c) Les fonctions

[doc fonctions](https://twig.symfony.com/doc/2.x/functions/index.html)

Une fonction peut changer la valeur d'une variable, elle prend un ou plusieurs paramètres.

**Liste des fonctions les plus courantes dans Symfony :**

* `dump()` ([doc](https://twig.symfony.com/doc/2.x/functions/dump.html#dump)): fonction de debug juste indispensable :heart_eyes:

**Exemple :**

```
{{ dump(user) }}
```

:warning: La fonction dump n'est pas disponible par défaut. Il est nécessaire d'ajouter l'extension `Twig_Extension_Debug` explicitement à la création de l'environnement Twig :

```
$twig = new Twig_Environment($loader, array(
    'debug' => true,
    // ...
));
$twig->addExtension(new Twig_Extension_Debug());
```

Même une fois activée, la fonction `dump` n'affichera rien tant que l'option de debug est activé dans l'environnement (prévient l'affichage de dumps en prod).

* `parent()` : lorsqu'un template met en jeu la notion d'héritage, il est possible d'afficher le contenu d'un block parent à l'aide de cette fonction `parent()`

**Exemple :**

```
{% extends "base.html" %}

{% block sidebar %}
    <h3>Table Of Contents</h3>
    ...
    {# affiche le contenu du bloc parent #}
    {{ parent() }}
{% endblock %}
```

### 5.d) Les filtres

[doc filtres](https://twig.symfony.com/doc/2.x/filters/index.html)

Alors qu'une fonction peut modifier la valeur d'une variable, un filtre n'altère que son affichage. On utilise l'opérateur "pipe" `|`  pour appliquer un filtre. Les filtres peuvent être chaînés.

**Exemples de filtre :**

```
{# met un titre en majuscule #}
{{ title|upper }}

{# met une chaîne en majuscule et l'écrit en sens inverse "OOF" #}
{{ 'foo'|capitalize|reverse }}

{# affichera "<h1>Foo</h1>" dans le navigateur #}
{{ '<h1>Foo</h1>' }}

{# affichera "Bar" tel qu'attendu #}
{{ '<h1>Bar</h1>'|raw }}
```

## 6. Héritage de layouts

[doc héritage et layouts](https://symfony.com/doc/current/templating.html#template-inheritance-and-layouts)

### 6.a) Un principe calqué sur l'héritage de classes en PHP

À l'image des classes en PHP, la notion d'héritage de templates avec Twig consiste à définir un template de base appelé _layout_ contenant les éléments communs aux pages du site (nav, header, footer en sont des exemples). Ces éléments sont définis dans des _blocks_ (un peu l'analogue des méthodes de base d'une classe PHP parent). Un template enfant hérite alors du layout de base (le template parent en fait) et il est possible de réécrire le contenu de chacun des blocks du parent dans l'enfant (comme en PHP lorsque des méthodes enfant viennent écraser les méthodes parent). Il est possible également de conserver le contenu du block parent et du block enfant à la fois !

### 6.b) L'héritage en pratique

En pratique, on commence par définir un layout de base, typiquement dans le dossier `templates` du projet. L'usage est de le nommer `base.html.twig`.

**Exemple de layout :**

```
{# templates/base.html.twig #}
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>{% block title %}Test Application{% endblock %}</title>
    </head>
    <body>
        <div id="sidebar">
            {% block sidebar %}
                <ul>
                    <li><a href="/">Home</a></li>
                    <li><a href="/blog">Blog</a></li>
                </ul>
            {% endblock %}
        </div>

        <div id="content">
            {% block body %}{% endblock %}
        </div>
    </body>
</html>
```

Ce template d'exemple définit la trame HTML d'un document à deux colonnes. On y trouve trois blocks `{% block %}` pour le `title`, la `sidebar` et le `body` (ces blocks sont en fait des tags comme évoqués ci-dessus).

Chaque block peut être écrasé par le template enfant, on peut également affiché le contenu de l'enfant et du parent. Il est également possible d'afficher ce layout tel quel, il affichera alors les valeurs par défaut des blocks dans le parent.

**Exemple de template enfant :**

```
{# templates/blog/index.html.twig #}
{% extends 'base.html.twig' %}

{% block title %}My cool blog posts{% endblock %}

{% block body %}
    {% for entry in blog_entries %}
        <h2>{{ entry.title }}</h2>
        <p>{{ entry.body }}</p>
    {% endfor %}
{% endblock %}
```

Comme on peut le voir dans le code ci-dessus, c'est le tag `{% extends %}` en début de template enfant qui indique le chemin vers le layout parent dont il hérite. Ce chemin se fait relativement au dossier `templates` contenant le layout `base.html.twig` (dans le tag `extends`, on n'écrit donc pas `'templates/base.html.twig'`, mais simplement `'base.html.twig'`).

### 6.c) Quelques bonnes pratiques

Quand on travaille avec la notion d'héritage de templates avec Twig, on veillera à respecter les règles suivantes :

* si usage du tag `{% extends %}`, celui-ci doit être écrit _en premier_

* on pourra abuser des tags `{% blocks %}` dans le layout, plus il y en a, plus le layout sera flexible (on rappelle que si un block n'est pas redéfini dans l'enfant, c'est sa valeur par défaut définie dans le parent qui sera affichée)

* on évitera la duplication de codes dans les templates enfant, si c'est le cas, penser à faire figurer ces blocks dans le parent

* si l'on a besoin de faire figurer dans un template enfant le contenu d'un block du template parent, on utilisera alors la fonction `{{ parent() }}`

**Exemple d'utilisation de la fonction `parent` :**

```
{% block sidebar %}
    <h3>Table of Contents</h3>

    {# ... #}

    {{ parent() }}
{% endblock %}
```

### 6.d) Localisation et nommage des templates

[doc](https://symfony.com/doc/current/templating.html#template-naming-and-locations)

## 7. Génération d'URLs dans un template

### 7.a) Cas d'une URL associée à une route

[doc génération URL](https://symfony.com/doc/current/templating.html#linking-to-pages)

Cela se fait à l'aide de la fonction Twig `path()` qui prend deux arguments :

* premier argument : le nom de la route

* deuxième argument : la valeur du paramètre si la route en possède

**Exemple de route sans paramètre :**

```
// code de la route
// src/Controller/WelcomeController.php

// ...
use Symfony\Component\Routing\Annotation\Route;

class WelcomeController extends AbstractController
{
    /**
     * @Route("/", name="welcome")
     */
    public function index()
    {
        // ...
    }
}

{# génération de l'URL associée à cette route dans un template Twig #}
<a href="{{ path('welcome') }}">Home</a>
```

**Exemple de route avec paramètre :**

```
// code de la route
// src/Controller/ArticleController.php

// ...
use Symfony\Component\Routing\Annotation\Route;

class ArticleController extends AbstractController
{
    /**
     * @Route("/article/{slug}", name="article_show")
     */
    public function show($slug)
    {
        // ...
    }
}

{# génération de l'URL associée à cette route dans un template Twig #}
{# templates/article/recent_list.html.twig #}
{% for article in articles %}
    <a href="{{ path('article_show', {'slug': article.slug}) }}">
        {{ article.title }}
    </a>
{% endfor %}
```

>Il est également possible d'utiliser la fonction Twig `url()` afin de générer une URL.

### 7.b) Cas de l'inclusion d'un asset

[doc inclusion assets](https://symfony.com/doc/current/templating.html#linking-to-assets)

Il est possible de générer dans un template un lien vers un asset (image, feuille CSS, fichier JS, etc). Pour cela, il faut que le package asset soit installé avec la commande `composer require symfony/asset`. On dispose alors de la fonction `asset()` permettant l'inclusion des assets.

**Exemple d'inclusion d'assets dans un template Twig :**

```
<img src="{{ asset('images/logo.png') }}" alt="Symfony!" />

<link href="{{ asset('css/blog.css') }}" rel="stylesheet" />
```

>Si l'application se trouve dans un sous-dossier, on veillera à faire figurer ce sous-dossier dans le chemin donné à la fonction `asset` (voir la [doc](https://symfony.com/doc/current/templating.html#linking-to-assets))

Si l'on a besoin d'une URL absolue vers un asset, on peut utiliser la fonction `absolute_url()`.

**Exemple avec URL absolue vers un asset :**

```
<img src="{{ absolute_url(asset('images/logo.png')) }}" alt="Symfony!" />
```

### 7.c) Cas de l'inclusion d'une feuille de style ou d'un fichier JS

[doc](https://symfony.com/doc/current/templating.html#including-stylesheets-and-javascripts-in-twig)

Pour cela, on inclut deux blocks dans le template de base, l'un appelé `stylesheets` dans le `head` et l'autre baptisé `javascripts` à la fin du `body`

**Exemple de layout de base avec inclusions CSS et JS :**

```
{# templates/base.html.twig #}
<html>
    <head>
        {# ... #}

        {% block stylesheets %}
            <link href="{{ asset('css/main.css') }}" rel="stylesheet" />
        {% endblock %}
    </head>
    <body>
        {# ... #}

        {% block javascripts %}
            <script src="{{ asset('js/main.js') }}"></script>
        {% endblock %}
    </body>
</html>
```

Cette approche a l'avantage de gérer la possibilité de l'ajout d'une feuille de style pour une page donnée _en plus_ des feuilles de style globales !

**Exemple d'inclusion dans un template enfant d'une feuille de style pour la page associée :**

```
{# templates/contact/contact.html.twig #}
{% extends 'base.html.twig' %}

{% block stylesheets %}
    {{ parent() }}

    <link href="{{ asset('css/contact.css') }}" rel="stylesheet" />
{% endblock %}

{# ... #}
```

### 7.d) Cas de l'inclusion de Bootstrap 4 CDN + feuille de style locale

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>{% block title %}Welcome!{% endblock %}</title>
        {% block stylesheets %}
            {# Boostrap 4 Latest compiled and minified CSS #}
            <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
            {# Optional theme #}
            <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap-theme.min.css" integrity="sha384-rHyoN1iRsVXV4nD0JutlnGaslCJuC7uwjduW9SVrLvRYooPp2bWYgmgJQIXwl/Sp" crossorigin="anonymous">
            
            {# Boostrap 4 Personal CSS style file #}
            <link href="{{ asset('css/style.css') }}" rel="stylesheet" />
        {% endblock %}
    </head>
    <body>
        {% block body %}{% endblock %}
        {% block javascripts %}
            {# Boostrap 4 Latest compiled and minified JavaScript #}
            <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
        {% endblock %}
    </body>
</html>

```

## 8. Pour aller plus loin avec Twig

[doc aller plus loin](https://symfony.com/doc/current/templating.html#learn-more)

* How to Use PHP instead of Twig for Templates
* How to Access the User, Request, Session & more in Twig via the app Variable
* How to Dump Debug Information in Twig Templates
* How to Embed Controllers in a Template
* How to Escape Output in Templates
* How to Work with Different Output Formats in Templates
* How to Inject Variables into all Templates (i.e. global Variables)
* How to Embed Asynchronous Content with hinclude.js
* How to Organize Your Twig Templates Using Inheritance
* How to Use and Register Namespaced Twig Paths
* How to Render a Template without a custom Controller
* How to Check the Syntax of Your Twig Templates
* How to Write a custom Twig Extension