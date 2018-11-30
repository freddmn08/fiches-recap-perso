# Dossier et fichiers importants de Laravel

[Doc installation](https://laravel.com/docs/5.7/installation) - 
[Tuto vidéo](https://www.formation-laravel.fr/articles/decouverte/2017-11-04-explications-sur-les-fichiers-et-les-dossiers-de-laravel.html)

## Dossier app

Le plus important du projet, il contiendra toutes les classes PHP.

## Dossier bootstrap

Dossier propre à Laravel (ne pas en modifier son contenu).

## Dossier config

Contient les fichiers de configuration de l'application (timezone locale, langue par défaut, etc).

## Dossier database

### Sous-dossier factories

TODO

### Sous-dossier migrations

Contient les migrations (description des tables présentes en base de données et des champs de chacune de celles-ci)

## Dossier public

Seul dossier accessible depuis l'extérieur de l'application (contiendra les images, css et js)

## Dossier resources

Dossier fourre-tout contenant tout ce qui n'est pas du PHP (fichiers de langues, traductions, les views HTML, etc)

## Dossier routes

Contient les points d'entrée de l'application :

* le fichier `web.php` contient les points d'entrée pour les visiteurs web (depuis un navigateur)

* le fichier `api.php` ceux pour les appli mobiles par exemple

* le fichier `channels.php` ceux pour les appli en temps réel

* le fichier `console.php` ceux pour la ligne de commande

## Dossier storage

### Sous-dossier app

Contient les fichiers générés par l'application (exemple : avatar des utilisateurs)

### Sous-dossier framework

Géré par Laravel

## Sous-dossier logs

Contient les événements, exceptions, rapports d'erreur, etc

## Dossier tests

Contient tous les tests automatisés de l'application

## Dossier vendor

Contient les dépendances de l'application (ne pas modifier son contenu)

## Fichier .env

Contient les variables d'environnement de l'application. Variables secrètes (ce fichier n'est pas versionner)

## Fichier .env.example

Modèle du fichier .env (à ne pas remplir avec les infos de config, ce fichier est versionné)

## Fichier .gitattributes et .gitignore

Ces fichiers sont utilisés par le logiciel Git

## Fichier artisan

Contient les lignes de commande à exécuter dans artisan dans le terminal

## Fichiers composer.json et composer.lock

Contiennent les dépendances de l'application au niveau de PHP

## Fichier package.json

Idem côté JS et CSS

## Fichier phpunit.xml

Contient la configuration permettant de lancer les tests automatisés

## Fichier server.php

Utile à Laravel pour la commande `php artisan serve` (ne pas toucher à ce fichier)

## Fichier webpack.mix.js

Contient les informations nécessaires à la compilation des fichiers JS et SASS