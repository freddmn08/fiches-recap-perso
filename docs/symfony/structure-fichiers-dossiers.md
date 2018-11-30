# Structure d'un projet Symfony 4

[doc structure projet](https://symfony.com/doc/master/page_creation.html#checking-out-the-project-structure)

## Les dossiers principaux

* `config/` : contient les fichiers de configuration de l'application. On y configure les routes, les services et les packages.

* `src/` : contient tout le code PHP de l'application.

* `templates/` : contient les templates Twig.

## Les autres dossiers

* `bin/` : contient les fichiers `bin/console`

* `var/` : contient les fichiers générés automatiquement comme les fichiers de cache (`var/cache/`) et de logs (`var/log/`).

* `vendor/` : contient les librairies externes (les "vendor") téléchargeable via Composer

* `public/` : contient le point d'entrée de l'application, c'est la racine du projet. On y place les documents publiquement accessibles

En cas d'installation de nouveaux packages, de nouveaux dossiers seront créés automatiquement.