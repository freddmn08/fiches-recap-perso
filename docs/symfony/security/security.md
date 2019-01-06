# Le composant Security

[doc security](https://symfony.com/doc/current/security.html)

## 1. Installation du bundle security

`composer require symfony/security-bundle`

## 2. Entité User

### 2.a) Création avec le MakerBundle

**Exemple dans le cas de données user stockées en base avec Doctrine :**

```
php bin/console make:user

The name of the security user class (e.g. User) [User]:
> User

Do you want to store user data in the database (via Doctrine)? (yes/no) [yes]:
> yes

Enter a property name that will be the unique "display" name for the user (e.g.
email, username, uuid [email]
> email

Does this app need to hash/check user passwords? (yes/no) [yes]:
> yes

created: src/Entity/User.php
created: src/Repository/UserRepository.php
updated: src/Entity/User.php
updated: config/packages/security.yaml
```

Il est bien sûr possible d'ajouter d'autres champs à l'entité via la commande `make:entity`. On joue ensuite la migration :

```
php bin/console make:migration
php bin/console doctrine:migrations:migrate
```

### 2.b) Configuration du User provider

C'est une classe qui apporte certaines fonctionnalités comme le rechargement des données utilisateurs depuis la session et quelques autres options comme [remember me](https://symfony.com/doc/current/security/remember_me.html) ou [impersonation](https://symfony.com/doc/current/security/impersonating_user.html).

Symfony configure un tel provider automatiquement lors de la création de l'entité `User` via la commande `make:user`. Cela se traduit par la présence de la clé `providers` dans le fichier `security.yaml`.

Si la classe User est une entité, rien de plus à faire concernant la configuration du [User provider](https://symfony.com/doc/current/security/user_provider.html).

### 2.c) Hachage des mots de passe

Si la connexion des users requiert un mot de passe, on peut configurer leur encodage dans le ficher `security.yaml`. La commande `make:user` pré-configure cet encodage via la clé `security -encoders`.

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

Symfony sachant alors comment encoder les mots de passe, c'est le service `UserPasswordEncoderInterface` qui assurera cet encodage avant de sauvegarder les users en base de données.

On peut encoder des mots de passe avec la commande `php bin/console security:encode-password`.

On peut également créer des utilisateurs "bouchon" avec le bundle `DoctrineFixtures` :

```
php bin/console make:fixtures

The class name of the fixtures to create (e.g. AppFixtures):
> UserFixtures
```

On utilise alors ce service pour encoder les mots de passe :

```
// src/DataFixtures/UserFixtures.php

+ use Symfony\Component\Security\Core\Encoder\UserPasswordEncoderInterface;
// ...

class UserFixtures extends Fixture
{
+     private $passwordEncoder;

+     public function __construct(UserPasswordEncoderInterface $passwordEncoder)
+     {
+         $this->passwordEncoder = $passwordEncoder;
+     }

        public function load(ObjectManager $manager)
        {
        $user = new User();
        // ...

+       $user->setPassword($this->passwordEncoder->encodePassword(
+             $user,
+             'the_new_password'
+       ));

        // ...
        }
}
```

## 3. Authentification et firewalls

([doc authenticating your users](https://symfony.com/doc/current/security.html#b-authenticating-your-users))

### 3.a) La notion de firewalls

Cf. [Le fichier security.yaml](docs/symfony/security/security.yaml.md)

### 3.b) Authentification des users

Plutôt que de construire une route et un contrôleur dédié à l'authentification, Symfony active un _authentification provider_ : du code qui s'exécute avant que le contrôleur ne soit appelé. Symfony dispose de nombreux [built-in authentication providers](https://symfony.com/doc/current/security/auth_providers.html).

Il est cependant conseillé de plutôt construire un _Guard Authenticator_ : c'est une classe qui permet de contrôler chaque étape du processus d'authentification. Il y a différentes façons d'en construire un. Il peut être utile dans le cas d'une connexion via un formulaire de login ou pour un API token.

## 4. Denying access, rôles et autres autorisations

## 5. Fetching the current object

### Gestion du logout

Dans le fichier de configuration, on définit à la clé `logout` la route de déconnexion et la route de redirection.

```
# config/packages/security.yaml
security:
    # ...

    firewalls:
        main:
            # ...
            logout:
                path:   app_logout

                # where to redirect after logout
                target: app_any_route
```

Dans le contrôleur, on définit une route pour l'endpoint `/logout`.

```
// src/Controller/SecurityController.php
/**
     * @Route("/logout", name="app_logout")
     */
    public function logout(): void
    {
        throw new \Exception('This should never be reached!');
    }
```