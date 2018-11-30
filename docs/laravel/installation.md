## Installation de Laravel sous Linux

[Doc installation](https://laravel.com/docs/5.7/installation) - 
[Tuto installation](https://www.formation-laravel.fr/articles/decouverte/2017-11-03-installation-d-un-projet-laravel-avec-ubuntu.html)

### 1. Préalable à l'installation

Pour installer Laravel, vérifier au préalable que sont installés :

* PHP version >= 7.1.3 (le vérifier avec `php -v`)

* les extensions PHP XML et Mbstring (le vérifier avec `php -m`)

* Composer (le vérifier en tapant `composer`)

**Installation de PHP et de ses extensions**

Dans le terminal : `sudo apt install php-cli php-mbstring php-xml`

**Installation de Composer**

Composer est le gestionnaire de dépendances de PHP

Se rendre sur la page [https://getcomposer.org/download/](https://getcomposer.org/download/) et y copier la ligne de commande d'installation de composer ci-dessous. L'exécuter dans le dossier de travail (ligne de commandes une par une) :

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === '93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
sudo php composer-setup.php --filename=composer --install-dir=/usr/local/bin
php -r "unlink('composer-setup.php');"
```

`sudo` permet d'obtenir les droits administrateurs car le dossier /usr/local/bin n'est pas accessible sans les droits administrateur

### 2. Installation de Laravel

Deux façons d'installer Laravel dans le dossier de travail :

* Directement en ligne de commande : `composer create-project --prefer-dist laravel/laravel monDossierDeTravail`

* Avec composer :
    
    * On commence par télécharger `Laravel installer` à l'aide de `Composer` via la commande : `composer global require laravel/installer`

    * Make sure to place composer's system-wide vendor bin directory in your $PATH so the laravel executable can be located by your system. This directory exists in different locations based on your operating system; however, some common locations include:

        GNU / Linux Distributions: $HOME/.config/composer/vendor/bin

    * On dispose alors de la commande `laravel new` permettant de créer une nouvelle installation de Laravel dans le dossier de travail : `laravel new monDossierDeTravail`

**Création d'un serveur local de développement (optionnel)** : se fait via `artisan` à l'aide de la commande `php artisan serve`

Permet d'accéder au point d'entrée du projet via l'URL `http://localhost:8000`

Si le port 8000 est déjà écouté par une autre application, spécifier le port dans la commande.

Exemple : `php artisan serve --port=8001`

Le terminal est alors "gelé", en ouvrir un autre au besoin avec Ctrl + Alt + T (on quitte ce mode avec la commande Ctrl + C)

En cas d'erreur Laravel du type "failed to open stream: Permission denied", donner les droits en écriture avec les commandes successives :

```
php artisan cache:clear 
sudo chmod -R 777 storage/
composer dump-autoload
```


## 2. Configuration

TODO