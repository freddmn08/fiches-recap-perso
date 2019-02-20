# Les Repositories

[cours OCR]()

L'EntityManager est un service proposé par Doctrine.

## 1. Le service Doctrine

On appelle Doctrine depuis un contrôleur avec la commande `$doctrine = $this->getDoctrine();`. C'est ce service qui gère la base de données. Il a deux fonctions :
* gérer les connexions aux bases de données ;
* gérer les entités via le gestionnaire d'entités appelé **EntityManager** (c'est la partie ORM de Doctrine).

## 2. Le service EntityManager

L'EntityManager dispose de la méthode `getManager()` permettant de récupérer un ORM à partir de son nom `$this->getDoctrine()->getManager($name)`. Dans la plupart des cas cependant , on ne disposera que d'un seul EntityManager. On pourra alors omettre son nom en argument et l'appeler directement via la commande `$em = $this->getDoctrine()->getManager();`.

C'est cet EntityManager qui dira à Doctrine de persist un élément en base de données, d'exécuter des requêtes SQL, etc. Il gère l'aspect Create, Update et Delete du CRUD. Il ne sait pas récupérer des entités depuis la base de données (Read), c'est le Repository qui s'en charge.

Les méthodes de l'EntityManager sont accessibles dans la [documentation Doctrine](https://www.doctrine-project.org/api/orm/latest/Doctrine/ORM/EntityManager.html).

### La notion de transaction

Pourquoi deux méthodes `$em->persist()`et `$em->flush()` ? Cela permet entre autres de profiter des **transactions**. Imaginons avoir plusieurs entités à persister en même temps. Par exemple, lorsque l'on crée un sujet sur un forum, il faut enregistrer l'entité Sujet, mais aussi l'entité Message, les deux en même temps. Sans transaction, on fera d'abord la première requête, puis la deuxième. Logique au final. Mais imaginez que vous ayez enregistré votre Sujet et que l'enregistrement de votre Message échoue : vous avez un sujet sans message ! Cela casse votre base de données, car la relation n'est plus respectée.

Avec une transaction, les deux entités sont enregistrées en même temps, ce qui fait que si la deuxième échoue, alors la première est annulée, et vous gardez une base de données propre.

Concrètement, avec notre EntityManager, chaque `$em->persist()` est équivalent à dire : « Garde cette entité en mémoire, tu l'enregistreras au prochain flush(). » Et un `$em->flush()` est équivalent à : « Ouvre une transaction et enregistre toutes les entités qui t'ont été données depuis le dernier `flush()`. »

## 3. Les méthodes Create, Update et Delete

