# Evénements

C'est le serveur (apache par exemple) qui interprète le code Symfony

Trois types d'événements :

* liés au kernel
* liés à ???
* liés au form

## Events liés au kernel

[doc](https://symfony.com/doc/current/event_dispatcher.html)

Kernel est le point d'entrée de Symfo. Kernel = coeur

[doc HttpKernel component](https://symfony.com/doc/current/components/http_kernel.html)

**Fonctionnement :** requete - reponse

1. The user asks for a resource in a browser;
1. The browser sends a request to the server;
1. Symfony gives the application a Request object;
1. The application generates a Response object using the data of the Request object;
1. The server sends back the response to the browser;
1. The browser displays the resource to the user.

Des événements sont déclenchés à chaque étape du processus requete - reponse.

Principe de listeners (security listeners, routing listeners)

1. kernel.request

2. Resolve the controller

    La route est étape, cherche le controller à appeler, fais l'association entre 

3. kernel.controller event

    on prépare le controller avant qu'il ne soit appelé

    Le param converter est déclenché à cette étape (la 3e)

4. Getting the controller arguments

5. Calling the controller

    pas d'events en particulier pour cette étape

    Job du controller : construire une réponse

6. kernel.view

## Création d'un event

[doc](https://symfony.com/doc/current/event_dispatcher#creating-an-event-listener)

Events visibles dans la wdt menu Events

**Exemple :** on veut générer du code au moment d'une exception

Error pages for the production environment can be customized in different ways depending on your needs:

1. If you just want to change the contents and styles of the error pages to match the rest of your application, override the default error templates;
2. If you also want to tweak the logic used by Symfony to generate error pages, override the default exception controller;
3. If you need total control of exception handling to execute your own logic use the kernel.exception event.

On crée le code du listener d'event dans le dossier `src/EventListener` :

```
// src/EventListener/ExceptionListener.php
namespace App\EventListener;

use Symfony\Component\HttpKernel\Event\GetResponseForExceptionEvent;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\HttpKernel\Exception\HttpExceptionInterface;

class ExceptionListener
{
    public function onKernelException(GetResponseForExceptionEvent $event)
    {
        // You get the exception object from the received event
        $exception = $event->getException();
        $message = sprintf(
            'My Error says: %s with code: %s',
            $exception->getMessage(),
            $exception->getCode()
        );

        // Customize your response object to display the exception details
        $response = new Response();
        $response->setContent($message);

        // HttpExceptionInterface is a special type of exception that
        // holds status code and header details
        if ($exception instanceof HttpExceptionInterface) {
            $response->setStatusCode($exception->getStatusCode());
            $response->headers->replace($exception->getHeaders());
        } else {
            $response->setStatusCode(Response::HTTP_INTERNAL_SERVER_ERROR);
        }

        // sends the modified response object to the event
        $event->setResponse($response);
    }
}
```

On déclare les listeners dans `config/services.yaml` :

```
# config/services.yaml
services:
    App\EventListener\ExceptionListener:
        tags:
            - { name: kernel.event_listener, event: kernel.exception }
```

Subscriber / listener : même combat

Suscriber : inscription volontaire dans l'event

Listener : inscription imposée

`console make:subscriber`

Crée un dossier `src/EventSubscriber`

Nom de classe : toto

subscribe to kernel.exception

## Les events liés aux formulaires

[doc form events](https://symfony.com/doc/current/form/events.html)

addEventListenser()

* param 1

* param 2 : fonction anonyme

IMPORTANT `PRE_SET_DATA` : ajout / suppression de champ, de contraintes (admin / user) avant construction du formulaire (avant que la data soit set dans le form)

`POST_SET_DATA` : sert avant tout à lire la data une fois le formulaire setté, pour des modèles dénormalisés (hors doctrine ?)

Les données sont settées grâce à `handleRequest()`, ensuite à la soumission du formulaire, dans cet ordre :

IMPORTANT * `PRE_SUBMIT` : on clique sur le bouton submit, avant la soumission réelle des datas

    * changer les datas

    * ajouter / supprimer des champs

    * fait le trim des champs

IMPORTANT * `SUBMIT` : compléter une donnée

* `POST_SUBMIT` (peu utile) : lancé juste après la soumission réelle du form après dénormalisation des données

    * collect d'infos au sujet des forms

    * validation de données dénormalisées (générer erreurs le cas échéant)

### Création de listeners, subscribers pour un form

Préférer cette méthode :

```
$listener = function () {

}

PRE_SUBMIT, $listener
```

## Les events avec Doctrine

[doc](https://symfony.com/doc/current/doctrine/lifecycle_callbacks.html) - [doc Doctrine](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/events.html#lifecycle-events)

Lifecycle callbacks = analogue des events pour Doctrine

trigger sur une base de données : surveille les modifs des bdd

Exemple : l'autoincrement s'appuie sur un trigger

On active les lifecycles avec une annotation :

```
/**
 * @ORM\Entity()
 * @ORM\HasLifecycleCallbacks()
 */
class Product
{
    // ...
}
```

Création des méthodes : exemple `created_at` setté juste avant l'appel à la méthode `persist()`

```
// src/Entity/Product.php

/**
 * @ORM\PrePersist
 */
public function setCreatedAtValue()
{
    $this->createdAt = new \DateTime();
}
```

Cas avec un listener :

On va chercher la fonction qui a pour nom le param 2 dans `services.yaml`

Update SQL = Delete + Create

## Gestion des rôles :

A la création d'un user, mettre un rôle par défaut se fait avec un PrePersist dans l'entité

Liste déroulante des rôles à faire dans le FormType (exemple : l'admin peut modifier les rôles des users mais pas le modo)

si user loggé et role = admin
    afficher champ roles

la connexion est géré dans le SecurityController
l'inscription est géré dans UserController
form login a faire en dur dans un template

## Events et Doctrine

**Correction de l'exerice :** slugifier le titre d'un film à la création ou à l'update

1. Activation des evants associés aux fonctions de callback

Problématique :

* On ne peut pas injecter de services dans une Entity (une entité n'est pas un service). Une entité ne sert que de classe passe-plat entre la base de données et les contrôleurs

On doit donc faire un new Slugger dans la classe de l'entité (dans la méthode appliquant le slug)

On active via annotation le(s) évenement(s) qui vont déclencher la fonction sluggify(). On appelle applySlug() juste avant la persistence en base

:warning: en edit, il n'y a pas de persist, donc le prepersist ne marche pas

```
/**
 * Si je souhaite effectuer une action via une methode sur un ou des event particuliers
 * il faut que je specifie en annotation chaque event sur lequel je dois effectuer ma fonction (ici creation ET update).
 * On notera que lorsqu'un objet existe deja en DB, nous n'effectuons pas de persist donc l'evenement PrePresist ne s'effectuera pas : il faudra rajoute l'event PreUpdate
 * 
 * @ORM\PrePersist
 * @ORM\PreUpdate
*/
public function applySlug(){

    $slugger = new Slugger();

    $this->slug = $slugger->sluggify($this->title);
}
```

Avantages d'un event listener par rapport à un subscriber :

* injection de services, de constantes

* 