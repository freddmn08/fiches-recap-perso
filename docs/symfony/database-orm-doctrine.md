# Databases avec l'ORM Doctrine

[doc](https://symfony.com/doc/current/doctrine.html)

Doctrine est une librairie externe permettant une gestion ORM des bases de données avec Symfony.

## 1. Installation de Doctrine

On installe Doctrine via le pack ORM ainsi que MakerBundle.

```
composer require symfony/orm-pack
composer require symfony/maker-bundle --dev
```

## 2. Configuration de la base de données

Les informations de connexion à la base de données sont à renseigner dans la variable d'environnement `DATABASE_URL` du fichier de configuration `.env` se trouvant à la racine du projet.

```
# Configure your db driver and server_version in config/packages/doctrine.yaml
DATABASE_URL=mysql://root:Ereul9Aeng@127.0.0.1:3306/symfony
```

Une fois cette configuration faite, ces paramètres seront utilisés par Doctrine dans `config/packages/doctrine.yaml`.

On peut alors créer la base de données avec la console :

```
php bin/console doctrine:database:create
```

## 3. Création des classes Entity

Les classes Entity sont les classes Model associées à chacune des tables de la base de données. On crée les Entity avec la console : `php bin/console make:entity`. On répond alors à une suite de questions :

* Nom de la table

* Nom du premier champ / Type / Null or not, etc

* Nom du deuxième champ / etc

Les classes Entity sont alors créées dans `src/Entity/NomDeLEntite.php`. La console crée automatiquement les getters et les setters associés à chaque champ de la table en question.

## 4. Création des tables en base de données avec Migrations

La commande précédente crée le modèle mais pas la table en base, cela se fait avec l'outil Migrations dans la console : `php bin/console make:migration`

En cas de réussite, apparaît un message de ce type :

```
 Success! 

 Next: Review the new migration "src/Migrations/Version20181130214930.php"
 Then: Run the migration with php bin/console doctrine:migrations:migrate
 See https://symfony.com/doc/current/bundles/DoctrineMigrationsBundle/index.html
 ```

 Le fichier php alors créé contient les informations permettant de mettre de créer (méthode up) ou de supprimer (méthode down) la table associée à l'entity créée précédemment.

 On crée ensuite la table depuis la console avec la commande : `php bin/console doctrine:migrations:migrate` .

 > Cette commande exécute toutes les migrations qui n'avaient pas encore été exécutées

 ## 5. Ajout d'un champ après la création de la table

 Pour cela, on peut éditer à la mano la classe Entity associée à la table. On peut également utiliser la console avec `make:entity`. En effet, cette commande permet de créer ou de mettre à jour une entity existante ! Il suffit alors de renseigner le nom de la table, puis de préciser les paramètres du nouveau champ :

INSERER CAP MAKE:ENTITY_UPDATE

Cela crée un nouvel attribut dans la classe Entity avec les getter et setter associés. Il faut ensuite générer une nouvelle migration via la commande `php bin/console make:migration`.

Cela génère un fichier PHP contenant une requête SQL de mise à jour de la table du type `ALTER TABLE product ADD description LONGTEXT NOT NULL`.

On insère enfin ce nouveau champ dans la table avec `php bin/console doctrine:migrations:migrate`.

## 6. Ecriture de données en base

Il est nécessaire d'écrire un contrôleur associé à la table. Pas de panique, la console s'occupe de tout ! On crée par exemple un contrôleur `ProductController` associé à la table `product` avec la commande `php bin/console make:controller ProductController`.

INSERER CAP make_controller

Cela crée une classe Controller dans `src/Controller/ProductController.php`. Il est alors nécessaire d'y instancier un objet vide de la classe Product et d'y setter les valeurs des champs à sauvegarder en base.

@TODO