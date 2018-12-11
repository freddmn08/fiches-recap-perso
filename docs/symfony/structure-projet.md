# Structure d'un projet Symfony 4

[doc structure projet](https://symfony.com/doc/master/page_creation.html#checking-out-the-project-structure)

## Les dossiers principaux

* `config/` : contient les fichiers de configuration de l'application. On y configure les routes (dans le sous-dossier `routes/`), les services et les packages.

* `src/` : contient tout le code PHP de l'application (entre autres, `Controllers`, `Entity`, `Migrations` et `Repository`).

* `templates/` : contient les templates Twig.

## Les autres dossiers

* `bin/` : contient le fichier `bin/console` de la console Symfony

* `var/` : contient les fichiers générés automatiquement comme les fichiers de cache (`var/cache/`) et de logs (`var/log/`).

* `vendor/` : contient les librairies externes (les "vendor") téléchargeables via Composer

* `public/` : contient le point d'entrée de l'application, c'est la racine du projet. On y place les documents publiquement accessibles

En cas d'installation de nouveaux packages, de nouveaux dossiers seront créés automatiquement.

## Les fichiers importants à la racine du projet

* `.env` : fichier de configuration du projet. On y configure, entre autres, l'accès à la base de données via la variable d'environnement `DATABASE_URL`

* `composer.json` : fichier de configuration des dépendances du projet