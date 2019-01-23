# Le fichier security.yaml

[doc]() - [cours OCR - Sécurité symfo 4]()

Le composant `Security` dispose de son propre fichier de configuration nommé `security.yaml`, il se trouve dans le dossier `config/packages/`. On en liste ci-dessous les principales sections.

La clé principale est la clé `security`.

## Section `encoders`

Un encodeur est un objet qui encode les mots de passe des utilisateurs. Cette section de configuration permet ainsi de modifier l'encodeur utilisé pour les utilisateurs et donc la façon dont sont encodés les mots de passe dans l'application.

**Configuration par défaut :**

```
security:
    encoders:
        Symfony\Component\Security\Core\User\User: plaintext
```

L'encodeur utilisé par défaut, `plaintext`, n'encode rien du tout. Il laisse les mots de passe en clair.

Dans cette section, on peut définir l'entité User, le choix de l'algorithme de hachage et un cost.

**Exemple d'encodage des mots de passe avec l'algorithme bcrypt et un cost de 12 :**

```
# config/packages/security.yaml
security:
    # ...

    encoders:
        # use your user class name here
        App\Entity\User:
            # bcrypt or argon2i are recommended
            # argon2i is more secure, but requires PHP 7.2 or the Sodium extension
            algorithm: bcrypt
            cost: 12
```

## Section `role_hierarchy`

La notion de « rôle » est au centre du processus d'autorisation. On assigne un ou plusieurs rôles à chaque utilisateur et, pour accéder aux ressources, on demande que l'utilisateur ait un ou plusieurs rôles. Ainsi, lorsqu'un utilisateur tente d'accéder à une ressource, le contrôleur d'accès vérifie s'il dispose du ou des rôles requis par la ressource. Si c'est le cas, l'accès est accordé. Sinon, l'accès est refusé.

Cette section de la configuration dresse la hiérarchie des rôles. Ainsi, le rôle `ROLE_USER` est compris dans le rôle `ROLE_ADMIN`. Cela signifie que si une page requiert le rôle `ROLE_USER` et qu'un utilisateur disposant du rôle `ROLE_ADMIN` tente d'y accéder, il sera autorisé car en disposant du rôle d'administrateur, il dispose également du rôle `ROLE_USER`.

Les noms des rôles n'ont pas d'importance, si ce n'est qu'ils doivent commencer par «`ROLE_`».

**Exemple de configuration des rôles :**

```
security:
    role_hierarchy:
        ROLE_ADMIN:       ROLE_USER
        ROLE_SUPER_ADMIN: [ROLE_USER, ROLE_ADMIN, ROLE_ALLOWED_TO_SWITCH]
```

## Section `firewalls`

C'est la section la plus importante du système de sécurité.

```
# config/packages/security.yaml
security:
    firewalls:
        dev:
            pattern: ^/(_(profiler|wdt)|css|images|js)/
            security: false
        main:
            anonymous: ~
```

Dans Symfony, un "firewall" est un système d'authentification, la configuration à la clé `firewalls` définit le mode d'authentification des users (formulaire de login, API token, etc).

Un seul firewall est actif par requête : Symfony utilise la clé `pattern` pour trouver le premier match.

* **dev firewall :** le `dev` firewall est fake : il permet simplement d'assurer que les dev tools (ils se trouvent aux URLs /_profiler et /_wdt) ne soient pas bloqués accidentellement.

* **main firewall :** toutes les autres URLs sont gérées par le `main` firewall. On n'y trouve pas de clé `pattern`, cela signifie que ce firewall matche toutes les URLs. Cela ne signifie pas pour autant que toute URL nécessite une authentification. En effet, via la clé `anonymous`, le main firewall est accessible anonymement. Sur la home, apparaît dans la wdt (=web debug toolbar) "Authenticated Yes, Logged as anon.".

    * **form_login :**

    ```
    form_login:
                login_path: login #url de redirection vers le formulaire de login
                check_path: login #url de vérification du formulaire
    ```

    * **logout :** pour gérer le logout, ça se passe à la clé `logout`

    ```
    logout:
            path:   app_logout

            # where to redirect after logout
            target: app_any_route
    ```

* form_login firewall option : https://symfony.com/doc/current/security/form_login.html

## Section `providers`

Un provider est un fournisseur d'utilisateurs. Les firewalls s'adressent aux providers pour récupérer les utilisateurs et les identifier.

Un `User provider` est une classe en charge d'un certain nombre de fonctionnalités concernant la partie sécurité :

* Il charge les données utilisateurs depuis la session et vérifie qu'elles sont bien à jour.

* Il gère des fonctionnalités comme [remember me](https://symfony.com/doc/current/security/remember_me.html) ou [impersonation](https://symfony.com/doc/current/security/impersonating_user.html).

**Configuration par défaut :**

```
providers:
    in_memory:
        memory:
            users:
                user:  { password: userpass, roles: [ 'ROLE_USER' ] }
                admin: { password: adminpass, roles: [ 'ROLE_ADMIN' ] }
```

Par défaut, un seul provider est défini, nommé `in_memory` (nom arbitraire). C'est un fournisseur assez particulier dans le sens où les utilisateurs sont directement listés dans le fichier de configuration : il s'agit des utilisateurs « user » et « admin ». C'est en fait un fournisseur pour faire du développement, pour tester la couche sécurité sans avoir besoin d'une quelconque base de données derrière. Par la suite, on le supprimera afin d'utiliser un fournisseur permettant de récupérer les utilisateurs en base de données.

## Section `access_control`

**Configuration par défaut :**

```
security:
    access_control:
        #- { path: ^/login, roles: ROLE_ADMIN }
```

Le contrôle d'accès (access control en anglais) s'occupe de déterminer si le visiteur a les bons droits (rôles) pour accéder à la ressource demandée. Il y a différents moyens d'utiliser les contrôles d'accès :

* Soit ici, depuis la configuration, en appliquant des règles sur des URL. On sécurise ainsi un ensemble d'URL en une seule ligne, par exemple toutes celles qui commencent par  `/admin`.

* Soit directement dans les contrôleurs, en appliquant des règles sur les méthodes des contrôleurs. On peut ainsi appliquer des règles différentes selon les paramètres (grande liberté).

Ces deux moyens d'utiliser la même protection par rôle sont très complémentaires et offrent une flexibilité intéressante.