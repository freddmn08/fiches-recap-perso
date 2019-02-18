# L'ORM Doctrine

[doc](https://symfony.com/doc/current/doctrine.html)

## 0. Préambule

### 0.1. Notion d'ORM

L'objectif d'un ORM (pour Object-Relation Mapper, soit en français « lien objet-relation ») est simple : se charger de l'enregistrement des données en faisant oublier qu'on a une base de données. Comment ? En s'occupant de tout ! Plus d'écriture de requêtes, ni de création de tables via phpMyAdmin.

### 0.2. L'ORM Doctrine 2

Doctrine est une librairie externe permettant une gestion ORM des bases de données avec Symfony. C'est l'ORM par défaut de Symfony.

> Doctrine chez Symfony est l'équivalent d'Eloquent pour Laravel

Doctrine ORM implémente 2 patterns objets pour mapper un objet PHP à des éléments d'un système de persistance :

* le pattern "Data Mapper" ;
* le pattern "Unit of Work".

**Le Data Mapper :**

Le Data Mapper est une couche qui synchronise la donnée stockée en base avec les objets PHP. En d'autres termes :

* il peut insérer, mettre à jour des entrées en base de données à partir de données contenues dans les propriétés d'un objet ;
* il peut supprimer des entrées en base de données si les "entités" liées sont identifiées pour être supprimées ;
* il "hydrate" des objets en mémoire à partir d'informations contenues en base.

L'implémentation dans le projet Doctrine de ce Data Mapper s'appelle l'Entity Manager, les entités ne sont que de simples objets PHP mappés.

Le grand avantage d'utiliser un Data Mapper, c'est que les objets sont complètement indépendants du système de stockage.

Pour une raison de performance et d'intégrité, l'Entity Manager ne synchronise pas directement chaque changement avec la base de données.

**L'Unit of work :**

L'Unit of Work est lui utilisé pour gérer l'état des différents objets hydratés par l'Entity Manager. La synchronisation en base ne s'effectue que quand on exécute la méthode "flush" et est effectuée sous forme d'une transaction qui est annulée en cas d'échec.

L'Entity Manager fait donc le lien entre les "Entités", qui sont de simples objets PHP, et la base de données :

* à l'aide de la fonction find, il retrouve et hydrate un objet à partir d'informations retrouvées en base ;
* à l'aide de la fonction persist, il ajoute l'objet manipulé dans l'Unit of Work ;
* à l'aide de la fonction flush, tous les objets "marqués" pour être ajoutés, mis à jour et supprimés conduiront à l'exécution d'une transaction avec la base de données.

Pour marquer un objet pour la suppression, il faut utiliser la fonction remove.

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

[doc types Doctrine](https://www.doctrine-project.org/projects/doctrine-dbal/en/2.8/reference/types.html)

Les classes Entity sont les modèles associés à chacune des tables de la base de données. On crée les entités avec la console : `php bin/console make:entity`. On répond alors à une suite de questions du genre :

* Class name of the entity to create or update (= nom de l'entité, ie celui de la table)

* New property name (= nom du premier champ)

* Field type (= type Doctrine)

* Field length (si type string))

* Can this field be null in the database (nullable) (yes/no)

* Nom du deuxième champ / etc

Les classes Entity sont alors créées dans `src/Entity/NomEntite.php`. La console crée automatiquement les getters et les setters associés à chaque champ de la table en question.

## 4. Création des tables en base de données avec Migrations

La commande précédente crée le modèle mais pas la table en base, cela se fait avec l'outil Migrations dans la console : `php bin/console make:migration`

En cas de réussite, apparaît un message du type :

```
 Success! 

 Next: Review the new migration "src/Migrations/Version20181130214930.php"
 Then: Run the migration with php bin/console doctrine:migrations:migrate
 See https://symfony.com/doc/current/bundles/DoctrineMigrationsBundle/index.html
 ```

 Le fichier PHP alors créé contient les informations permettant de créer (méthode `up`) ou de supprimer (méthode `down`) la table associée à l'entité créée précédemment.

 On crée ensuite la table depuis la console avec la commande : `php bin/console doctrine:migrations:migrate` .

 > Cette commande exécute toutes les migrations qui n'avaient pas encore été exécutées

 ## 5. Ajout d'un champ après la création de la table

 Pour cela, on peut éditer à la mano la classe Entity associée à la table. On peut également utiliser la console avec `make:entity`. En effet, cette commande permet de _créer ou de mettre à jour_ une entité existante ! Il suffit alors de renseigner le nom de la table, puis de préciser les paramètres du nouveau champ :

INSERER CAP MAKE:ENTITY_UPDATE

Cela crée un nouvel attribut dans la classe Entity avec les getter et setter associés. Il faut ensuite générer une nouvelle migration via la commande `php bin/console make:migration`.

Cela génère un fichier PHP contenant une requête SQL de mise à jour de la table du type `ALTER TABLE product ADD description LONGTEXT NOT NULL`.

On insère enfin ce nouveau champ dans la table avec `php bin/console doctrine:migrations:migrate`.

## 6. Ecriture de données en base

Il est nécessaire d'écrire un contrôleur associé à la table. Pas de panique, la console s'occupe de tout ! On crée par exemple un contrôleur `ProductController` associé à la table `product` avec la commande `php bin/console make:controller ProductController`.

INSERER CAP make_controller

Cela crée une classe Controller dans `src/Controller/ProductController.php`. Il est alors nécessaire d'y instancier un objet vide de la classe Product et d'y setter les valeurs des champs à sauvegarder en base.

```
// src/Controller/ProductController.php
namespace App\Controller;

// ...
use App\Entity\Product;

class ProductController extends AbstractController
{
    /**
     * @Route("/product", name="product")
     */
    public function index()
    {
        // on appelle le Manager de Doctrine, permet la lecture, la sauvegarde d'infos en bdd
        $entityManager = $this->getDoctrine()->getManager();

        // on instancie un objet de la classe Product
        $product = new Product();
        // on sette les valeurs des champs à sauvegarder en bdd
        $product->setName('Keyboard');
        $product->setPrice(1999);
        $product->setDescription('Ergonomic and stylish!');

        // on indique à Doctrine de "gérer" l'objet $product (pas de requêtes SQL pour le moment)
        $entityManager->persist($product);

        // on insère en base l'objet product (crée une nouvelle entrée dans la table product)
        $entityManager->flush();

        // on renvoie un message de succès de l'écriture en base et on récupère l'id de la nouvelle entrée
        return new Response('Produit sauvegardé en base avec l\'id '.$product->getId());
    }
}
```

## 7. Lecture de données en base

On crée une méthode dans le contrôleur associé à l'entité afin de pouvoir lire des données en base. Cela se fait via le repository associé à l'entité. Un repository se comporte comme une classe PHP dont le rôle est d'aller lire en base les entités d'une certaine classe (la classe Product en l'occurrence). 

```
// Lecture d'une entrée dans la table product
    /**
     * @Route("/product/{id}", name="product_show")
     */
    public function show($id)
    {
        // on récupère le produit d'id passé dans l'url
        $product = $this->getDoctrine()->getRepository(Product::class)->find($id);

        // si le produit n'est pas trouvé, on renvoie une erreur
        if (!$product) {
            throw $this->createNotFoundException(
                'Aucun produit trouvé avec l\'id '.$id
            );
        }

        // sinon on retourne une réponse avec le nom du produit
        return new Response('Le produit d\'id '.$id.' a pour nom '.$product->getName());
    }
```

Un objet Repository possède des méthodes permettant la sélection des informations en base de données :

```
$repository = $this->getDoctrine()->getRepository(Product::class);

// look for a single Product by its primary key (usually "id")
$product = $repository->find($id);

// look for a single Product by name
$product = $repository->findOneBy(['name' => 'Keyboard']);
// or find by name and price
$product = $repository->findOneBy([
    'name' => 'Keyboard',
    'price' => 1999,
]);

// look for multiple Product objects matching the name, ordered by price
$products = $repository->findBy(
    ['name' => 'Keyboard'],
    ['price' => 'ASC']
);

// look for *all* Product objects
$products = $repository->findAll();
```

## 8. Mise à jour de données en base

On met à jour une entrée en base de données à l'aide de Doctrine en procédant en trois étapes :

1. Lire / trouver l'objet avec Doctrine

1. Modifier l'objet

1. Appeler la méthode `flush()` sur l'EntityManager

Il est inutile d'appeler la méthode `persist()` car Doctrine est déjà en "surveillance" des modifications à effectuer.

```
// Update d'une entrée dans la table product
    /**
     * @Route("/product/edit/{id}")
     */
    public function update($id)
    {
        $entityManager = $this->getDoctrine()->getManager();
        $product = $entityManager->getRepository(Product::class)->find($id);

        // si le produit n'est pas trouvé en base, on renvoie une erreur
        if (!$product) {
            throw $this->createNotFoundException(
                'Aucun produit trouvé pour l\'id '.$id
            );
        }

        // sinon on met à jour le produit 
        $product->setName('Nouveau nom de produit');
        $entityManager->flush();

        // on redirige vers la route product_show associée au endpoint /product/{id}
        return $this->redirectToRoute('product_show', [
            'id' => $product->getId()
        ]);
    }
```

## 8. Suppression d'une entrée en base

On procède de façon similaire au cas de la mise à jour. On utilise la méthode `remove()` qui notifie à Doctrine la volonté de supprimer une entrée de la base. La requête `DELETE` n'est réellement exécutée qu'avec l'appel à la méthode `flush()`.

```
// Delete d'une entrée de la table product
    /**
     * @Route("/product/delete/{id}")
     */
    public function delete($id)
    {
        $entityManager = $this->getDoctrine()->getManager();
        $product = $entityManager->getRepository(Product::class)->find($id);

        // si le produit n'est pas trouvé en base, on renvoie une erreur
        if (!$product) {
            throw $this->createNotFoundException(
                'Aucun produit trouvé pour l\'id '.$id
            );
        }

        // sinon on supprime l'entrée de la table produit 
        $entityManager->remove($product);
        $entityManager->flush();

        // on redirige vers la route product_show associée au endpoint /product/{id}
        return new Response('Le produit d\'id '.$product->getId().' a bien été supprimé de la table product');
    }
```