# Tests

[doc Testing](https://symfony.com/doc/current/testing.html)

Dans le développement d'une appli, il est bon de tester son code avec des fonctions et des tests unitaires.

## Tests techniques purs

Comment se comporte une fonctionnalité

TDD : Test Driven Development

On anticipe une fonctionnalité avant de la développer

Tests unitaires = long. Cela double ou triple le temps de dév dans le chiffrage

## Le PHP Unit Testing Framework

Symfo intègre une librairie indépendante appelée [PHPUnit](https://phpunit.de/), c'est un réel framework dédié aux tests.

[doc en fr](https://phpunit.readthedocs.io/fr/latest/)

Installation du composant [PHPUnit Bridge](https://symfony.com/components/PHPUnit%20Bridge) : `composer require --dev symfony/phpunit-bridge`

Chaque test (test unitaire ou fonction) est une classe PHP à placer dans le dossier `/tests`. On peut alors lancer tous les tests de l'appli en ldc avec : `./bin/phpunit`

### Tests unitaires

[doc Unit Tests](https://symfony.com/doc/current/testing.html#unit-tests)

Un test unitaire est un test vis-à-vis d'une classe PHP, appelée _unit_. L'écriture d'un test unitaire chez Symfony ne diffère pas de celle d'une ordinaire classe PHP.

1 fichier de test = 1 classe

Respect de l'arborescence dans laquelle se trouve la classe :



**Exemple de test d'une classe PHP `Calculator` :**

```
// src/Util/Calculator.php
namespace App\Util;

class Calculator
{
    public function add($a, $b)
    {
        return $a + $b;
    }
}
```

Pour la tester, on crée une classe `CalculatorTest` dans le dossier `tests/Util` :

>Par convention, le dossier `tests/` doit répliquer l'arborescence du dossier du composant dédié aux tests unitaires. Exemple : si on teste une classe se trouvant dans `src/Util`, on codera le test dans le dossier `tests/Util`.

```
// tests/Util/CalculatorTest.php
namespace App\Tests\Util;

use App\Util\Calculator;
use PHPUnit\Framework\TestCase;

class CalculatorTest extends TestCase
{
    public function testAdd()
    {
        $calculator = new Calculator();
        $result = $calculator->add(30, 12);

        // assert that your calculator added the numbers correctly!
        $this->assertEquals(42, $result);
    }
}
```
On peut exécuter tous les tests d'une appli, tous les tests d'un dossier ou un test en particulier :

```
# run all tests of the appli
./bin/phpunit

#run all tests in the Util/ directory
./bin/phpunit tests/Util

# run tests for the Calculator class
./bin/phpunit tests/Util/CalculatorTest.php
```

### Test du Slugger

On lance le test en ldc :

`console make:unit-test`

Nom de la classe : Slugger

On déplace Slugger.php dans le dossier `tests/Utils/` et on renomme le namespace

le point `.` dans la console indique que le test s'est fait avec succès

Si erreur, au lieu du ., on a un `F` (comme fail) sur fond rouge

## Tests fonctionnels

[doc ](https://symfony.com/doc/current/testing.html#functional-tests)

`console make:functional-test`

`MovieControllerTest`

A déplacer dans `tests/Controller/MovieControllerTest`

Langage de requête xpath : [doc](http://jean-luc.massat.perso.luminy.univ-amu.fr/ens/xml/04-xpath.html)

**Exemple de test fonctionnel :** tester gloabl sur l'ensemble des pages de l'appli (toutes les routes sont-elles accessibles ?)

On utilise la méthode `urlFrontProvider()` qui permet de tester une à une les routes de l'appli avec la commande `yield`



Constantes d'environnement à définir dans (`phpunit.xml.dist` à la base) et `.env`

`APP_ENV=test`

```
// .env
# define your env variables for the test env here
KERNEL_CLASS='App\Kernel'
APP_SECRET='s$cretf0rt3st'
SYMFONY_DEPRECATIONS_HELPER=999999

#pour lancer une commande notamment pour creer la db sur un autre environnement que test definit dans .env
# console doc:dat:create --env=test <--- et l'environnement de mon choix en minuscule
DATABASE_URL=mysql://admin:admin@127.0.0.1:3306/moviedblunartest

```

On crée une base de données de test (clone de celle de dev)

`console doctrine:database:create --env=test` 

On joue les migrations pour cette nouvelle DB :

`console doctrine:migrations:migrate --env=test`

On recharge les fixtures pour cette DB :

`console doctrine:fixtures:load`

Il est nécessaire de modifier la DATABASE_URL

On crée une classe `ApplicationAvailabilityFunctionalTest` dans `tests/`

```
<?php

namespace App\Tests;

use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

//ici nous effectuons un test global sur l'appel de l'ensemble des pages voir si toutes sont accessibles
class ApplicationAvailabilityFunctionalTest extends WebTestCase
{
    /**
     * @dataProvider urlProvider
     */
    public function testFrontPageIsSuccessful($url)
    {
        $client = self::createClient();
        $client->request('GET', $url);

        $this->assertTrue($client->getResponse()->isSuccessful());
    }

    // pour lister les url , une methode simple comprenant des yield qui va permettre de tester une a une chaque url avec la fonction ci dessus
    // Note : pour utiliser cette fonction, symfony utilise l'annotation dataProvider
    public function urlFrontProvider()
    {
        yield ['/'];
        yield ['/movie/Cars'];
        yield ['/login'];
    }
}
```

On lance alors les tests : `./bin/phpunit`

### Voters

[doc How to Use Voters to Check User Permissions](https://symfony.com/doc/current/security/voters.html)

On considère l'user loggé. Les Voters servent à organiser les droits sur du contenu notamment. Exemple : envoyer un objet, sert à faire le tri sur les droits d'édition selon qui est connecté et sur quoi

### PHPUnit en pratique

Pour installer les librairies manquantes : `./bin/phpunit`

Installation : `sudo apt-get install phpunit`

Installer bridge php : composer require --dev symfony/phpunit-bridge

si erreur zip `sudo apt-get install php7.2-zip`
tester la commandes php `./bin/phpunit`
si erreur `mbstring sudo apt-get install php7.2-mbstring`

