# Console

[doc console](https://symfony.com/doc/4.1/components/console.html)

Symfony dispose d'un puissant outil de débug (mais pas que !) appelé `console`. Cette console permet d'exécuter des commandes utilitaires en ligne de commandes, on y accède depuis le dossier du projet via la commande `php bin/console`.

>Dans toutes les commandes liées à la console, on peut omettre `php` au début de celles-ci

La console dispose d'un nombre conséquent de commandes, nombre grandissant au fur et à mesure de l'installation de nouveaux packages.

> La console chez Symfony est l'analogue de l'artisan chez Laravel

Il est possible de créer des commandes personnalisées dans la console !

**Exemples de commandes présentes par défaut :**

* Informations sur le projet courant : `php bin/console about`

* Lancer un serveur de développement : `php bin/console server:run`

* Lister toutes les routes de l'application : `php bin/console debug:router`

* Vérifier la syntaxe d'un template Twig :
    
    * Vérification par nom de fichier : `php bin/console lint:twig templates/article/recent_list.html.twig`

    * Vérification par nom de dossier : `php bin/console lint:twig templates/`