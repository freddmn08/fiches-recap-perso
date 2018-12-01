# Twig, le moteur de templates de Symfony

[doc Twig](https://symfony.com/doc/master/templating.html) - [cours OCR Twig](https://openclassrooms.com/fr/courses/5489656-construisez-un-site-web-a-l-aide-du-framework-symfony-4/5517021-dynamisez-vos-vues-a-l-aide-de-twig)

## 1. Présentation de Twig

Symfony dispose d'un puissant moteur de templating appelé Twig. Cet outil est plus adapté que PHP en tant que moteur de template pour au moins trois raisons :

* Twig a une syntaxe plus concise et plus claire

* Par défaut, il supporte de nombreuses fonctionnalités utiles telles que la notion d'héritage

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

La fonction `render` gère l'affichage de la view, elle prend en paramètre le chemin vers la view et un tableau de paramètres qu'on lui passe. Les paramètres sont alors disponibles dans la view.

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

Twig dispose par défaut de [filtres](https://twig.symfony.com/doc/2.x/filters/index.html), de [tags](https://twig.symfony.com/doc/2.x/tags/index.html) et de [fonctions](https://twig.symfony.com/doc/2.x/functions/index.html) permettant de modifier le contenu d'une view avant son rendu. Il est également possible d'en créer de façon custom via l'[extension Twig](https://symfony.com/doc/master/templating/twig_extension.html).

**Exemple de filtre :** `{{ title|upper }}` met un titre en majuscule 

## 6. Héritage de layouts

