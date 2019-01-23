# Générations de fixtures avec Faker

[doc Fixtures](https://symfony.com/doc/current/bundles/DoctrineFixturesBundle/index.html) - [doc Faker](https://github.com/fzaninotto/Faker)

1. Installation du bundle `Fixtures` : `composer require --dev doctrine/doctrine-fixtures-bundle`

    Cela génère la classe `src/DataFixtures/AppFixtures` dans laquelle on va pouvoir créer et persister en base des données bouchon (les fixtures)

1. Installation de `Faker` (librairie PHP) : `composer require fzaninotto/faker`

1. On code alors des fixtures dans la méthode `load()` de la classe `AppFixtures`

1. On charge ces fixtures avec la commande `php bin/console doctrine:fixtures:load`

>Si erreur "Attempted to load class Factory from namespace App\DataFixtures\Faker", c'est que la classe `Faker` n'a pas été importée via `use Faker;`

