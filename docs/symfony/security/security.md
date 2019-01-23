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

### Gestion des rôles

l'admin a accès à tout,
le user a accès au backend mais pas à edit, show et new
l'anonyme a accès qu'à la page d'accueil (et le détail d'un film)

bonne pratique : dans la table app_users, un seul champ au type json

OU

Créer une table de rôles et faire une jointure

On crée une table role dans notre cas

Owner : user

Un user a un rôle et un seul

Un role peut être associé à plusieurs users.

console make:entity Role

code / string / 25 / no

name / string / 25 / no

console make:migration
console doc:mig:mig

console make:entity User

role / relation / ManyToOne / Role / yes / yes / users / no

console make:migration
console doc:mig:mig

Dans `User.php` :

:warning: Attention aux setters

getRoles() est appelé à l'auth, pas du tout par doctrine. Il retourne un tableau avec les roles de l'user via user->getRoles()

getRole() et setRole() sont gérés par doctrine

```
/**
* @ORM\ManyToOne(targetEntity="App\Entity\Role", inversedBy="users")
*/
private $role;

//ATTENTION : cette fonction doit retourne obligatoirement un tableau et est necessaire pour la gestion des utilisateurs symfony
    // note: getRoleS = ACL security.yml + webtoolbar
    public function getRoles()
    {
        return ['ROLE_MON_ADMIN_LUNAR']; // problem : le role est definit en dur dans cette fonction
    }

    //Note: getRole / set role = Doctrine / Mysql
    public function getRole(): ?Role
    {
        return $this->role;
    }

    public function setRole(?Role $role): self
    {
        $this->role = $role;

        return $this;
    }
```

Dans `UserType.php` :

```
function buildForm

->add('role')//ajout du champs role a afficher dans le formulaire
```

Cette méthode addrole() va chercher à afficher une représentation textuelle de "role", d'où la nécessité de définir la méthode __toString() :

```
// Role.php
public function __toString(){
        return $this->name;
    }
```

On définit les roles dans l'`access_control` du fichier `security.yaml`, on les configure en commençant par les endpoints associés au plus grand nombre de droit jusqu'au endpoint correspondant aux droits les plus restreints :

methods: nom_de_la_methode indique la route à matcher quand plusieurs routes sont possibles, on les différencie du coup par la méthode associée (plus bas, on cible la méthode DELETE par exemple)

```
access_control: #ACL - section qui permet de lister les url concernée par la securité + le role que l'utilisateur doit avoir pour consulter l'url
        - { path: '^/backend/[a-z]+/new', roles: ROLE_ADMIN } 
        - { path: '^/backend\/[a-z]+\/[0-9]+\/edit', roles: ROLE_ADMIN } 
        - { path: '^/backend\/[a-z]+\/[0-9]+', roles: ROLE_ADMIN, methods: DELETE  } 
        - { path: '^/backend\/[a-z]+', roles: ROLE_USER } # expression reguliere : url qui commence par /backend (^)
        - { path: ^/ezadmin, roles: ROLE_ADMIN }
```

**Hiérarchie des rôles :** principe des matriochka

On ajoute une clé `role_hierarchy` dans le security.yaml :

```
role_hierarchy: # quel role sont inclus dans tel ou tel role
        ROLE_ADMIN: ROLE_USER #ici je definit que Role admin contient role user
        ROLE_LUNAR_SUPER_ADMIN: ROLE_ADMIN
```

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