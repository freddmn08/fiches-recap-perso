# Relation ternaire entre entités

Cas de la table `movie_crew` entre les entités `Movie` d'une part, et `Person` et `Job` de l'autre

On crée une table `Team` et trois relations ManyToOne sur les tables `Movie`, `Person` et `Job`

1. **Création de l'entité Team :**

`console make:entity`

Team

2. **Ajout d'une relation ManyToOne entre Team et Movie :**

movie

ManyToOne

Movie

yes

yes

teams

3. **Ajout d'une relation ManyToOne entre Team et Person :**

person

ManyToOne

Person

yes

yes

teams

4. **Ajout d'une relation ManyToOne entre Team et Job :**

job

ManyToOne

Job

yes

yes

teams

5. On met à jour la base de données :

`console make:migration`

`console doctrine:migrations:migrate`

## Relation Job - Department

make:entity Job

relation

Department

ManyToOne

nullable? yes

Do you want to add a property t o Department so thaht you can acess update Jobs? No

/!\ utilisation de `php bin/console make:entity --regenerate`

[doc --regenerate](https://symfony.com/doc/current/doctrine/reverse_engineering.html#generating-the-getters-setters-or-php-classes)

à lancer quand on veut regénérer les getters et setters si modification manuelle d'une propriété ou d'un getter/setter. Migration inutile car on ne modifie pas la structure de la table

`-- overwrite` pour surcharger

## Création des formulaires liés aux entités avec make:form

Préfixe des routes avec le controller [doc](https://symfony.com/blog/new-in-symfony-4-1-prefix-imported-route-names)

## Si erreur en cas de suppresion d'un department

Violation constraints FK

Permet de modifier la table sur la clef etrangere afin d'y autoriser le null en cas de suppression d'un id deja relié via la FK

```
* @ORM\JoinColumn(onDelete="SET NULL")
```

## Créaztion d'un form associé à une entité

Exemple avec l'entité Department

console make:form

Nom du form : DepartmentType

Entité associée : Department

Celma crée un dossier `src/Form/DepartmentType.php`

## Erreur ParamConverter

Si erreur "App\Entity\Department object not found by the @ParamConverter annotation.
", souci de match d'url (mettre celle avec des id en dernier OU requirement pour les param d'url)

## Création crud avec la console

`console make:crud`

Job

Problème : manque de souplesse au niveau du nom des routes, routes à changer manuellement

## S03-E04

### Installation du bundle Easy admin

Alternative à Easy admin : [sonata](https://sonata-project.org/bundles/)

Installation : `composer require admin`

Une ligne apparaît dans `config/bundles.php` : `EasyCorp\Bundle\EasyAdminBundle\EasyAdminBundle::class => ['all' => true],`

Dans `config/packages/easy_admin.yaml` : clé `easy_admin_bundle`

Décommenter le contenu du fichier

Préfixe d'url : `/admin`

Installation de symlink : `php bin/console assets:install --symlink`

Gère les assets pour l'admin

### Config des routes

Dans `routes/easy_admin.yaml`, on peut changer le préfixe des routes du backend, on remplace `/admin` par `/ezadmin`

```
easy_admin_bundle:
    resource: '@EasyAdminBundle/Controller/AdminController.php'
    prefix: /ezadmin
    type: annotation
```

### Config de ezadmin

[doc](https://symfony.com/doc/master/bundles/EasyAdminBundle/book/design-configuration.html#loading-the-entire-bootstrap-framework)

Dans `config/packages/easy_admin.yaml`, on peut configurer le bundle :

```
easy_admin:
    site_name: 'MovieDB Lunar BO' #nom du backoffice
    design:
        brand_color: 'red'
    entities:
        # List the entity class name you want to manage
        Equipe:
                class: App\Entity\Team
        Casting:
                class: App\Entity\Casting
        Departement:
                class: App\Entity\Department
        Type:
                class: App\Entity\Genre
        Movie:
                class: App\Entity\Movie
        Metier:
                class: App\Entity\Job
                label: 'Metier'
```

On peut aussi y gérer les assets :

```
    assets:
          css:
              # ...
              - 'bundles/easyadmin/bootstrap-all.css'
          js:
              - 'bundles/easyadmin/bootstrap-all.js'
```

Ou des feuilles perso :

```
easy_admin:
    # ...
    design:
        assets:
            css:
                # ...
                - 'css/admin.css'
```

---

Si erreur "Catchable Fatal Error: Object of class App\Entity\Team could not be converted to string", définir la méthode __toString() dans l'entité Team. Si liste de Team à afficher dans un menu déroulant par exemple, on affichera le titre du film associé (choix arbitraire)

```
public function __toString(){
        return $this->movie->getTitle();
    }
```

Si préfixe dans l'interface avec des action. etc, soit modifier le framework.yaml ou installer symlink

```
config/packages/framework.yaml 
translator:
        fallbacks: "%locale%"
```

Checker également config/translations.yaml :

```
framework:
    default_locale: '%locale%'
    translator:
        default_path: '%kernel.project_dir%/translations'
        fallbacks:
            - '%locale%'
```

## Sécurité

On commence à le faire à la main en 3.4 [doc 3.4](https://symfony.com/doc/3.4/security.html)

`config/packages/security.yaml`

On ne peut pas activer à la fois à `http_basic` et `form_login`

```
http_basic: true
# https://symfony.com/doc/current/security.html#a-configuring-how-your-users-will-authenticate

# form_login: true
```

Utilisateurs définis dans `config/security.yaml`

## Provider

[doc provider](https://symfony.com/doc/3.4/security/entity_provider.html)

Création du User entity à partir du modèle de la doc (:warning: il manque des getters / setters). A copier-coller sans passer par le `console make:user`

`User implements UserInterface` obligatoire

Ensuite `console make:migration`, puis `console doctrine:migrations:migrate`

`getRoles()` : permet à symfo de récupérer la propriété Roles dans la wdt, retourne un tableau. Au log, retourne la liste des rôles associés à l'utilisateur loggé et en session

eraseCredentials() : pour se delog, supprime les accès

`serializable()` : utile pour la session (stocke en session serialize, récupère les infos étant en session, déserialize)

