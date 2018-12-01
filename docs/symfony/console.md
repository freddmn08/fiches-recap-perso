# Console

[doc console](https://symfony.com/doc/4.1/components/console.html)

Symfony dispose d'un puissant outil de débug (mais pas que !) appelé `console`, on y accède via la commande `php bin/console`. La console dispose d'un nombre conséquent de commandes, nombre grandissant au fur et à mesure de l'installation de nouveaux packages.

> La console chez Symfony est l'analogue de l'artisan chez Laravel

Il est possible de créer des commandes personnalisées dans la console !

**Exemples de commandes présentes par défaut :**

* Informations sur le projet courant : `php bin/console about`

* Lancer un serveur de développement : `php bin/console server:run`

* Lister toutes les routes de l'application : `php bin/console debug:router`