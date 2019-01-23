# Les services

[doc Service Container](https://symfony.com/doc/current/service_container.html) - [injection de dépendances](https://putaindecode.io/fr/articles/php/injection-dependances/) - [cours OCR services](https://openclassrooms.com/fr/courses/2078536-developpez-votre-site-web-avec-le-framework-symfony2-ancienne-version/2080362-les-services-theorie-et-creation)

## Généralités

Un **service** est un objet PHP qui remplit une fonction, associé à une configuration. Un service repose sur l'injection de dépendances.

Se fait grâce au **conteneur de services**, sorte de sac à dos contenant des objets déjà instanciés (fait un new de tous les objets qu'il contient).

Si objets avec dépendances entre eux, le conteneur de services instancie les objets dans le bon ordre de sorte à pouvoir les utiliser

`php bin/console debug:autowiring` : donne la liste des services dans le conteneur de services

Se configure dans le fichier `services.yaml` dans le dossier `config/`

Les objects spécifiques dans Symfo sont taggués, c'est entre autres le cas des contrôleurs

## Création d'un service

[doc Service Container](https://symfony.com/doc/current/service_container.html)

On se propose de créer un service appelé `MessageGenerator`. Il a pour simple but d'afficher un message.

1. Au besoin, on crée un controller `DefaultController` via `console make:controller`

1. On crée un dossier `src/Utils`

1. On y crée une classe `MessageGenerator.php`. On y importe le namespace `App\Utils`.

1. On y crée une méthode `getHappyMessage()` qui affiche un message :

```
<?php
// historiquement App\Service
namespace App\Utils;
class MessageGenerator
{
    public function getHappyMessage()
    {
        $messages = [
            'Lunar est super',
            'Merci pour l\'exemple de la lampe de poche Seb <3',
            'Youpi j\'ai un service',
        ];
        $index = array_rand($messages);
        return $messages[$index];
    }
}
```

1. On vérifie que le service est bien présent dans le conteneur de services avec : `php bin/console debug:autowiring --all` (--all liste même les services qu n'ont pas d'alias)

1. On appelle le service nouvellement créé dans le controleur DefautlController par injection de dépendance en argument d'une méthode `index(MessageGenerator $messageGen)` :

```
// src/Controller/DefaultController

<?php
namespace App\Controller;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Routing\Annotation\Route;
// en important le namespace de la classe je crée un alias pour LoggerInterface
use Psr\Log\LoggerInterface;
//j'importe mon service custom
use App\Utils\MessageGenerator;
class DefaultController extends AbstractController
{
    /**
     * Lorsque je prefix ma variable par la classe en question j'apelle le service
     * present dans le container de service et je recupere dans $logger un objet LoggerInterface instancié (le new LoggerInterface() a déja a ce stade été effectué)
     * @Route("/", name="default")
     */
    public function index(LoggerInterface $logger, MessageGenerator $messageGen)
    {
        $message = $messageGen->getHappyMessage();
        //je peux utiliser librement mon objet (ici le logger)
        $logger->info($message);
        return $this->render('default/index.html.twig', [
            'message' => $message
        ]);
    }
}
```

Si erreur "Cannot autowire argument $em of..."

## Configuration des services

[doc Service Container](https://symfony.com/doc/current/service_container.html)

Dans `security.yaml`, on y définit à la clé `parameters` des paramètres que l'on peut alors utiliser partout (exemple : une adresse e-mail)

Les alias permettent d'avoir plusieurs configurations dont une par défaut.

**Exemple :**

```
# valeur par defaut si type-hint utilisé dans une fonction (controller) ou un constructeur
    App\Utils\Slugger: '@lunar_service.slugger'
```

**Définition d'une constante applicative :**

On définit une constante applicative à la clé `parameters`

**Exemple :** définition de la constante applicative `slugger_to_lower`

```
// config/security.yaml
parameters:
    slugger_to_lower: false # on peut créer une constante applicative afin de pouvoir à termes la réutiliser en cas de besoin
```

**Appel d'une constante applicative :**

On appelle une constante applicative à la clé `arguments` en l'entourant de `%` afin de préciser que l'on appelle une constante applicative et non une chaîne de caractères

**Exemple :** appel de la constante applicative `slugger_to_lower`

```
// config/security.yaml
App\Utils\Slugger:
        arguments:
            $sluggerToLower: '%slugger_to_lower%' # pour recup la valeur d'une constante '%nomConstanteDansParameters%'
```

**Passage explicite d'un service :**

Si alias défini pour un service, il prend le pas sur le nom de la classe pour appeler le service : `@aliasDuService` permet d'injecter directement un service

```
App\Utils\Slugger:
        arguments:
            $sluggerToLower: '%slugger_to_lower%' # pour recup la valeur d'une constante '%nomConstanteDansParameters%'
            $logger: '@monolog.logger' #'@aliasduservice' : permet d'injecter directement un service en tant qu'argument
```

autowire: false

### Nommage d'un service

[doc Public vs Private](https://symfony.com/doc/current/service_container.html#public-versus-private-services)

```
# DECLARATION EXPLICITE DU SERVICE AVEC ALIAS (avancé) 
    lunar_service.slugger:
        class: App\Utils\Slugger
        arguments:
            $sluggerToLower: '%slugger_to_lower%'
            $logger: '@monolog.logger'

    App\Utils\Slugger: '@lunar_service.slugger'

App\Utils\Slugger: '@lunar_service.slugger'
```

## Gestion des images

[doc upload file](https://symfony.com/doc/current/controller/upload_file.html) - [wikipedia type media](https://fr.wikipedia.org/wiki/Type_de_m%C3%A9dias)

`$file` est un objet de type UploadedFile

`guessExtension()` devine l'extension du fichier uploadé

Méthode `generateUniqueFileName()` : génère un nom de fichier auquel on concatène l'extension de fichier

## Création d'un event listener avec Doctrine

Déclaration d'un service `Sluggifier` : clé `tags` sur le Sluggifier

On crée un dossier `src/EventListener`, on met un écouteur sur des événements Doctrine sur le `PrePersist` et sur le `PreUpdate`

Dans la classe `Sluggifier`, dans son constructeur, on peut passer le service `Slugger` en argument afin de sluggifier les titres des movies.

On stocke le slugger dans une propriété privée

Des fonctions associées aux événements surveillés sont créées

on récupère l'entité concernée

on slugge le titre

juste avant le flush, on passe par le sluggifier et le flush se fait ensuite

