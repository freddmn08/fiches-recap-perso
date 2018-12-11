# Gestion des Assets dans Symfony 4

Un asset est un fichier informatique "basique" destiné à être lu par un navigateur, ce peut-être un fichier CSS, un fichier JS, une image, un fichier multimédia...

Dans Symfony, on peut soit gérer les assets "manuellement", soit en déléguant au webpack Encore.

## 1. Le composant asset

### Installation

[doc asset](https://symfony.com/doc/current/components/asset.html)

Ce composant gère les feuilles de style css et les fichiers JS du projet Symfony. On l'installe avec la commande `composer require symfony/asset`.

## 2. Gestion "manuelle" des assets

Dans Symfony 4, on doit nécessairement placer les assets dans le dossier `public` en y créant, par exemple, le sous-dossier `public/assets` qui contiendra les images, fichiers CSS, JS, etc du projet.

Il n'est ainsi plus nécessaire de définir en dur les chemins vers ces fichiers externes dans les habituelles balises `<link>` dans le head ou `<script>` dans le body du fichier HTML.

## 2.a) Configuration du chemin vers les assets

Dans Symfony 4, on définit le chemin vers le dossier `assets` dans le fichier `config/packages/framework.yaml` à la clé `assets` :

```
# config/packages/framework.yaml
framework:
    # ...
    assets:
        base_path: '/assets'
```

>Le chemin du `base_path` se fait relativement au `public`

### 2.b) Importation d'un asset dans un template

En créant un sous-dossier `public/assets/css`, on peut y placer une feuille de style css `style.css` et l'appeler dans un template Twig avec la fonction `asset()` qui va établir le chemin vers les assets relativement au dossier `public/` :

```
<!DOCTYPE html>
<html>
    <head>
        // ...
        {% block stylesheets %}
            <link href="{{ asset('css/style.css') }}" rel="stylesheet" />
        {% endblock %}
    </head>
    // ...
```

## 3. Gestion avancée des assets avec le webpack Encore

[doc Encore]()

Dans Symfony 4, la gestion des assets peut être déléguée à un puissant outil appelé **webpack Encore**. Ce composant sera alors en charge de la gestion des assets du projet (fichiers CSS, SASS et JS).

### 3.a) Installation d'Encore

* Dans le dossier du projet, installer Encore en ligne de commandes avec `composer require encore`.

* On installe ensuite `yarn`, le gestionnaire de dépendances de Symfony ([installation ici](https://yarnpkg.com/lang/en/docs/install/#debian-stable)), avec la ligne de commandes : `yarn install`

Suite à l'installation d'Encore, de nouveaux dossiers et fichiers sont créés à la racine du projet :

* un dossier `assets` contenant les sous-dossiers `assets/css` et `assets/js`

* deux fichiers `assets/js/app.js` et `assets/css/app.css`

* un fichier `webpack.config.js`

Cela ajoute par la même occasion un certain nombre d'éléments dans le fichier `.gitignore` :

```
###> symfony/webpack-encore-bundle ###
/node_modules/
/public/build/
npm-debug.log
yarn-error.log
###< symfony/webpack-encore-bundle ###
```

Avec Encore, le fichier `app.js` va se comporter comme une application autonome qui fera appel aux dépendances nécessaires (jQuery ou React), incluant les fichiers CSS. Encore va gérer toutes ces inclusions et produire in fine un seul fichier `app.js` et un seul fichier `app.css` synthétisant tous les besoins de l'application. Encore peut faire bien plus : minifier les fichiers, traiter les fichiers Sass, supporter React, etc.

### 3.b) Configuration d'Encore

[doc](https://symfony.com/doc/current/frontend/encore/simple-example.html)

La configuration d'Encore se fait dans le fichier `webpack.config.js` se trouvant à la racine du projet.

```
// webpack.config.js
var Encore = require('@symfony/webpack-encore');

Encore
    // directory where compiled assets will be stored
    .setOutputPath('public/build/')
    // public path used by the web server to access the output path
    .setPublicPath('/build')

    .addEntry('app', './assets/js/app.js')

    // ...
;

// ...
```

La méthode `.addEntry()` est essentielle. Elle indique à Encore de charger le fichier `./assets/js/app.js` (second argument de la méthode) et de suivre toutes les inclusions qu'il peut contenir. Il traite alors globlament ces déclarations et retourne un fichier `app` (premier argument de la méthode) au format js et un au format css (autrement dit, les deux fichiers `app.js` et `app.css`) dans le dossier `public/build`.

Pour construire ces fichiers, on procède en ligne de commandes :

```
# compile assets once
yarn encore dev

# or recompile assets automatically when files change
yarn encore dev --watch

# on deploy, create a production build
yarn encore production
```

>Il est nécessaire d'arrêter et de redémarrer Encore suite à chaque modification du fichier `webpack.config.js`. On peut le faire en "surveillant" les modifications du fichier de configuration avec la commande `yarn encore dev --watch`

:warning: En cas d'erreur `warning package.json: No license field error Command "encore" not found` à l'exécution de la commande `yarn encore dev`, c'est certainement parce qu'un fichier `pakage.json` est déjà présent à la racine du projet (le supprimer ou le mettre en .bak) dufait d'une installation préalable de Bootstrap par exemple.

[issue](https://github.com/symfony/webpack-encore/issues/376)

Moralité : installer Encore de façon préalable à toute autre installation avec `yarn`.

```
// package.json
{
    "devDependencies": {
        "@symfony/webpack-encore": "^0.22.0",
        "webpack-notifier": "^1.6.0"
    },
    "license": "UNLICENSED",
    "private": true,
    "scripts": {
        "dev-server": "encore dev-server",
        "dev": "encore dev",
        "watch": "encore dev --watch",
        "build": "encore production --progress"
    }
}
```

Une fois la commande `yarn encore dev` exécutée avec succès, on dispose de trois nouveaux fichiers :

* `public/build/app.js` (holds all the JavaScript for your "app" entry)
* `public/build/app.css` (holds all the CSS for your "app" entry)
* `public/build/runtime.js` (a file that helps Webpack do its job)

### 3.c) Inclusion des assets dans le layout de base

Pour cela, il suffit d'inclure les fichiers `app.css` et `app.js` dans le layout respectivement via les blocks `{% block stylesheets %}` dans le `head` et `{% block javascripts %}` à la fin du `body`.

On notera la présence des fonctions `encore_entry_link_tags()` et `encore_entry_script_tags()` dont l'argument doit matcher avec le premier argument de la méthode `addEntry` du fichier `webpack.config.json` (cet argument est noté `'app'` dans cette fiche récap). Ces deux fonctions lisent dans le fichier `public/build/entrypoints.json` (généré par Encore) les fichiers js et css à interpréter.

**Exemple :**

```
{# templates/base.html.twig #}
<!DOCTYPE html>
<html>
    <head>
        <!-- ... -->

        {% block stylesheets %}
            {# 'app' must match the first argument to addEntry() in webpack.config.js #}
            {{ encore_entry_link_tags('app') }}

            <!-- Renders a link tag (if your module requires any CSS)
                 <link rel="stylesheet" src="/build/app.css"> -->
        {% endblock %}
    </head>
    <body>
        <!-- ... -->

        {% block javascripts %}
            {{ encore_entry_script_tags('app') }}

            <!-- Renders app.js & a webpack runtime.js file
                <script src="/build/runtime.js"></script>
                <script src="/build/app.js"></script> -->
        {% endblock %}
    </body>
</html>
```

### 3.d) Installation de Sass

[doc Sass Encore](https://symfony.com/doc/current/frontend/encore/simple-example.html#using-sass-less-stylus)

Pour utiliser Sass plutôt que CSS, on procède par étape :

1. On renomme le fichier `assets/css/app.css` en `assets/scss/app.scss`

```
// assets/js/app.js
- import '../css/app.css';
+ import '../scss/app.scss';
```

2. Dans le fichier `webpack.config.json`, on décommente la ligne de chargement du pré-processeur Sass :

```
// webpack.config.js
Encore
    // ...

+    .enableSassLoader()
;
```

3. Au besoin, on redémarre Encore avec la commande `yarn encore dev --watch`. Cela entraîne l'erreur `Error: Install sass-loader & node-sass to use enableSassLoader()`

4. On installe Sass avec yarn avec la commande `yarn add sass-loader@^7.0.1 node-sass --dev`

5. Au besoin, on recompile les assets avec `yarn encore dev --watch`

### 3.e) Import de Bootstrap 4

[doc bootstrap encore](https://symfony.com/doc/current/frontend/encore/bootstrap.html)

#### Installation de Bootstrap

On installe Bootstrap en ligne de commandes en tapant à la racine du projet la commande `yarn add bootstrap --dev`.

Bootstrap s'installe alors dans le dossier `node_modules/bootstrap`.

#### Importation des feuilles de style Bootstrap

On se place dans le "point d'entrée" SCSS se trouvant dans `assets/scss/app.scss` et on importe les fichiers SCSS de Bootstrap avec la commande :

```
// the ~ allows you to reference things in node_modules
// on aurait pu également écrire @import "../../node_modules/bootstrap/scss/bootstrap";
@import "~bootstrap/scss/bootstrap";
```

#### Cas des CDN

[tuto](https://www.jdecool.fr/blog/2017/09/30/tutorial-jobeet-symfony-4-partie-4b-la-gestion-des-assets-avec-twig.html)

@TODO

### 3.f) Import de jQuery

@TODO