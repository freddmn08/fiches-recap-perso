# Création d'une commande console custom

[doc Console commands](https://symfony.com/doc/current/console.html)

Avantages :

* l'efficatité de la ligne de commandes

* intérêt en dev

## La console

La console loade les valeurs des variables `APP_ENV` et `APP_DEBUG` (elles se trouvent dans lee fichier `.env`).

* Variable `APP_ENV` : on lui associe `dev` ou `prod`

* Variable `APP_DEBUG` : on lui associe true or false (0 ou 1 en pratique)

Selon les valeurs de ces variables, certaines commandes console ne sont pas/plus disponibles.

**Exemple :** avec `APP_ENV=prod`, `php bin/console doctrine:load:fixtures` n'est plus disponible

## Création d'une commande

Symfo permet de créer des commandes custom

**Exemple de création de commande :** on crée une commande afin d'appeler une API de sorte à générer des images dans l'application MovieDB.

On crée un dossier `src/Command`, puis dans celui-ci une classe `MovieCommand.php`.

On y crée une commande console appelée `'app:import-poster-lunar'`

Une fonction `configure()` de configuration de commandes

Une fonction `execute()` contenant les instructions à exécuter par la commande

Ne pas oublier le constructeur ! On appelle le constructeur parent avant de setter nos propres propriétés

```
// src/Command/MovieCommand.php

<?php

namespace App\Command;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;

class MovieCommand extends Command
{
    // je definit la commande qui lancera mon code de la methode execute()
    protected static $defaultName = 'app:import-poster-lunar';

    /*
     Optionnel mais neanmoins souvent pratique , rajouter le constructeur
     a la classe commande afin de pouvoir y injecter facilement des services.

     IMPORTANT  : ne pas oublier le prant:::construct car on souhaite garder la construction du constructeur parent et non l'ecraser 
    */
    public function __construct(bool $requirePassword = false)
    {
        // B.P. - rajouter l'appel au constructeur parent pour pouvoir se servir de nos propriété 
        parent::__construct();
    }

    protected function configure()
    {
        $this
        // descris lorsque je tape "bin/console" ce que fais ma commande 
        ->setDescription('J\'importe des poster et c\'est chouette <3 ')

        // affiche l'aide de la commande si je passe l'option --help
        // the "--help" option
        ->setHelp('Pas de helper')
    ;
    }

    protected function execute(InputInterface $input, OutputInterface $output)
    {
        // ...
    }
}
```

Affichage dans la console : OutputInterface

## CURL

Utilisé pour passer des requêtes HTTP dans PHP

Utile également pour débug un serveur via une connexion à distance

en ldc : `curl https://adresse`

omDBAPI : API de films

paramètre `t` : title

URL pour requête API :

`www.omdbapi.com/?apikey=[yourkey]&t=[yourtitle]`

Récupération des affiches :

`www.img.omdbapi.com/?apikey=[yourkey]&t=[yourtitle]`

[doc curl php](http://php.net/manual/fr/curl.examples-basic.php)

## Réupérer le titre d'un film de façon dynamique via CURL

Dans le fichier `src/Command/MovieCommand.php` :

* On crée une propriété protected définissant la commande qui lancera la requete CURL

* On crée une propriété privée `$movieRepository`

* Dans le constructeur, ... et on place l'appel au constructeur parent à la fin

* On stocke la variable 

* Dans la méthode `execute()`, on crée un `findAll()` pour récupérer tous les films, on parcourt le résultat avec un foreach. Pour chaque film, on récupère le titre du film que l'on passe à la méthode `getApiResponse()` faisant l'appel à l' API. On code un if avec un marqueur dynamique sur la donnée via la méthode `writeln()`

* On utilise l'`EntityManager` (en injectant l'`EntityManagerInterface` dans le constructeur. Rappel : les services s'injectent dans le constructeur) car on doit insérer en base les posters afin de les afficher par la suite.