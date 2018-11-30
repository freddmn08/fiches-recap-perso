## Installation de Symfony 4 sous Linux

[doc installation](https://symfony.com/doc/current/setup.html) - 
[fiche recap installation](https://github.com/O-clock-Alumni/fiches-recap/blob/master/symfony/themes/symfony-basics.md)

### 1. Préalable à l'installation

Pour installer Symfony 4, vérifier au préalable que sont installés :

* PHP version >= 7.1 (le vérifier avec `php -v`)

* Installer si besoin l'extension PHP `intl` via la commande `sudo apt install php-intl`

* Composer (le vérifier en tapant `composer`)

**Installation de Composer (si besoin)**

Composer est le gestionnaire de dépendances de PHP

Se rendre sur la page [https://getcomposer.org/download/](https://getcomposer.org/download/) et y copier la ligne de commande d'installation de composer ci-dessous. L'exécuter dans le dossier de travail (ligne de commandes une par une) :

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === '93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
sudo php composer-setup.php --filename=composer --install-dir=/usr/local/bin
php -r "unlink('composer-setup.php');"
```

`sudo` permet d'obtenir les droits administrateurs car le dossier /usr/local/bin n'est pas accessible sans les droits administrateur

Au besoin, on redémarrera Apache avec la commande `sudo systemctl restart apache2`

### 2. Mise à jour (version mineure)

[doc upgrade minor version](https://symfony.com/doc/current/setup/upgrade_minor.html)

Dans le cas d'une mise à jour mineure (changement du chiffre du milieu, exemple : 4.1.1 vers 4.2.0), on procède en deux étapes :

* Mise à jour des librairies Symfony via Composer : `composer update "symfony/*" --with-all-dependencies`

* Mise à jour du code pour le rendre fonctionnel avec la nouvelle version de Symfony