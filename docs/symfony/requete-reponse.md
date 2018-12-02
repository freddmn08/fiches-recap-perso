# Requête et réponse HTTP avec Symfony 4

[doc ]()

## 1. Le modèle client-serveur

Symfony est basé sur le modèle client-serveur dont les interactions se font au travers du protocole HTTP. Un utilisateur (le client) envoie une requête au serveur et le serveur lui renvoie une réponse.

### 1.a) L'envoi de la requête

La requête envoyée par le client contient l'information nécessaire pour que le serveur puisse retourner une réponse.

**Exemple de requête côté client :**

```
GET / HTTP/1.1
Host: openclassrooms.com
Accept: text/html
User-Agent: Mozilla/5.0 (Macintosh)
```

* La première ligne contient la méthode HTTP (ici GET) et l'URL (ici `/`)

* La deuxième ligne contient l'hôte de la requête

* La troisième ligne contient le type de données attendu (ici HTML)

* La dernière ligne désigne le navigateur (le client)

Les méthodes HTTP les plus utilisées sont GET, POST, PUT et DELETE.

### 1.b) La réponse du serveur

Une fois que le serveur a analysé la requête du client, celui-ci retourne un résultat sous forme de réponse HTTP.

**Exemple de réponse du serveur :** dans cet exemple, le serveur renvoie une page HTML avec un code de retour HTTP 200 (succès de la requête)

```
HTTP/1.1 200 OK
Date: Sat, 28 Jul 2018 21:05:05 GMT
Server: cloudfare
Content-Type: text/html

<html>
 <!-- ... HTML de la page d'accueil d'OpenClassrooms -->
</html>
```

La liste des codes HTTP de retour est disponible [ici](https://fr.wikipedia.org/wiki/Liste_des_codes_HTTP).

On peut manipuler une requête HTTP avec le langage PHP côté serveur. Il permet de récupérer la réponse du client et de formuler et retourner une réponse au format HTML.

**Exemple de réponse en PHP :**

```
<?php

// en utilisant l'url localhost?name=Zozor
$name = $_GET['name'];

header('Content-Type: text/html');
echo '<html>';
echo '<body>Bonjour '. $name . '</body>';
echo '</html>'
```

```
HTTP/1.1 200 OK
Date: Sat, 28 Jul 2018 02:14:33 GMT
Server: Apache/2.2.17 (Unix)
Content-Type: text/html

<html>
<body>Bonjour Zozor</body>
</html>
```

## 2. Requêtes et réponses avec Symfony

[doc HttpFoundation](https://symfony.com/doc/current/components/http_foundation.html)

En PHP, la requête est généralement représentée par des variables superglobales comme `$_GET`, `$_POST`, `$_FILES`, etc. et la réponse est générée avec des fonctions comme `echo`, `header()`, `setcookie()`, etc.

Symfony propose de "remplacer" ces variables et fonctions PHP en mettant à disposition une couche d'abstraction orientée objet appelée `HttpFoundation` gérant la requête et la réponse de ce modèle client-serveur.

On installe ce composant via Composer : `composer require symfony/http-foundation`

### 2.a) La classe Request

[doc Request](https://symfony.com/doc/current/components/http_foundation.html#request)

La classe Symfony `Request` fournit une abstraction objet permettant de manipuler toutes les informations issues de la requête client.

**Exemple :**

```
<?php
use Symfony\Component\HttpFoundation\Request;

// Récupération des valeurs accessibles dans les superglobales
$request = Request::createFromGlobals();

// Récupérer l'url
$request->getPathInfo();

// récupérer des attributs en GET ou POST
$request->query->get('name');
$request->request->get('name', 'nom par défaut');

$request->getMethod();    // e.g. GET, POST, PUT, DELETE ou HEAD

```

### 2.b) La classe Response

[doc Response](https://symfony.com/doc/current/components/http_foundation.html#response)

La classe Symfony `Response` construit une réponse HTTP valide et la retourne au client via la méthode `send()`. L'objet Response contient toutes les informations à renvoyer au client pour une requête donnée. Il prend jusqu'à trois arguments :

* Le contenu de la réponse

* Le code de retour

* Les headers HTTP

**Exemple :**

```
<?php
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

$request = Request::createFromGlobals();
$name = $request->get('name');

// construction d'une réponse HTTP valide
$response = new Response();

$response->setContent(
    '<html><body>Hello'
    . $name
    .'</body></html>'
);
$response->setStatusCode(Response::HTTP_OK);
$response->headers->set('Content-Type', 'text/html');

// retour de la réponse HTTP à l'utilisateur
$response->send();

```

>Il est possible de retourner une réponse au format JSON ([doc JSON Response](https://symfony.com/doc/current/components/http_foundation.html#creating-a-json-response)).