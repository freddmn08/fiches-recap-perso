# Les annotations de références de Doctrine

[doc](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/annotations-reference.html) - [cours OCR Repository Symfo 2](https://openclassrooms.com/fr/courses/2078536-developpez-votre-site-web-avec-le-framework-symfony2-ancienne-version/2081287-recuperer-ses-entites-avec-doctrine2) - [cours Isis](http://www.lsis.org/elmouelhia/sf/coursSymfonyDoctrine.pdf) - [doc fr](http://guidella.free.fr/General/symfony2AnnotationsReference.html)

Les annotations sont des métadonnées écrites dans des commentaires spéciaux dans le DocBlock des attributs d'une entité.

## Un DocBlock, c'est quoi au fait ? :nerd_face:

PHPDoc est une adaptation de Javadoc au langage PHP. C'est un standard dans la façon de commenter du code PHP. Parmi les composants de PHPDoc, on trouve des blocs de commentaires spéciaux appelés _DocBlocks_.

Un DocBlock est un commentaire débutant par `/**`, chaque nouvelle ligne démarrant avec `*` et se terminant par `*/`.

**Exemple de DocBlock :**

```
/**
 * This is a DocBlock comment
 */
function foo()
{
}
```

C'est dans ces DocBlocks que Symfony via Doctrine définit des annotations permettant de fixer les caractéristiques d'une entité. On en liste ci-après quelques unes de référence.

## :pencil2: L'annotation @Entity ([doc](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/annotations-reference.html#entity))

**Rôle :** définit une classe PHP en tant qu'entité.

**Attributs optionnels :**

* `repositoryClass` : permet de recupérer les entités depuis la base de données, il a comme valeur le nom du namespace complet du repository (Le nom du repository est composé du nom de l’entité + repository (exemple : `ArticleRepository`)
* `readonly` : précise que l'entité est en lecture seule

**Exemple :**

```
<?php
/**
 * @Entity(repositoryClass="MyProject\UserRepository")
 */
class User
{
    //...
}
```

## :pencil2: L'annotation @Table ([doc](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/annotations-reference.html#table))

**Rôle :** décrit la table d'une entité persistante. Il est placé sur le DocBlock entité classe PHP et est facultatif. Si cela n'est pas spécifié, le nom de table par défaut sera le nom de classe d'entités non qualifiées.

**Attribut obligatoire :**

* `name` : définit le nom de la table

**Attributs optionnels :**

* `indexes` : tableau d’annotations `@Index`
* `uniqueConstraints` : tableau d’annotations `@UniqueConstraint`
* `schema` : nom du schéma dans lequel la table repose

**Exemple :**

```
<?php
/**
 * @Entity
 * @Table(name="user",
 *      uniqueConstraints={@UniqueConstraint(name="user_unique",columns={"username"})},
 *      indexes={@Index(name="user_idx", columns={"email"})}
 *      schema="schema_name"
 * )
 */
class User { }
```

## :pencil2: L'annotation @Column ([doc](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/annotations-reference.html#column))

**Rôle :** marque une variable d'instance annotée comme "persistante".

**Attribut obligatoire :**

* `type` : nom du type Doctrine

**Attributs optionnels :**

* `name` : nom du champ
* `length` : longueur pour les chaînes de caractère
* `unique` : précise l’unicité des valeurs du champ
* `nullable` : indique si les valeurs null sont acceptées
* `precision` : détermine la précision (nombre total de chiffres significatifs) pour un champ de type SQL `NUMERIC`
* `scale` : détermine l'échelle (nombre de chiffres dans la partie décimale, scale <= precision) pour un champ de type SQL `NUMERIC`
* `options` : à choisir parmi default / unsigned / fixed / comment / collation / check
* `columnDefinition` : DDL SQL snippet qui démarre après le nom du champ et qui spécifie la définition complète du champ

**Exemple :**

```
<?php
/**
 * @Column(type="string", length=32, unique=true, nullable=false)
 */
protected $username;

/**
 * @Column(type="string", columnDefinition="CHAR(2) NOT NULL")
 */
protected $country;

/**
 * @Column(type="decimal", precision=2, scale=1)
 */
protected $height;

/**
 * @Column(type="string", length=2, options={"fixed":true, "comment":"Initial letters of first and last name"})
 */
protected $initials;

/**
 * @Column(type="integer", name="login_count" nullable=false, options={"unsigned":true, "default":0})
 */
protected $loginCount;
```

## :pencil2: L'annotation @Id ([doc](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/annotations-reference.html#id))

**Rôle :** la variable d'instance annotée sera marquée comme identification de l'entité, c'est-à-dire la clé primaire dans la base de données. Cette annotation est un marqueur seulement et n'a pas d'attributs obligatoires ni facultatifs. Pour les entités qui ont des champs multiples d'identifiants, chaque champ doit être marqué avec `@Id`.

**Exemple :**

```
<?php
/**
 * @Id
 * @Column(type="integer")
 */
protected $id = null;
```

## :pencil2: L'annotation @GeneratedValue ([doc](https://www.doctrine-project.org/projects/doctrine-orm/en/2.6/reference/annotations-reference.html#generatedvalue))

**Rôle :** indique quelle stratégie est utilisée pour générer des identifiants pour une variable d'instance qui est annotée par `@Id`. Cette annotation est facultative et n'a de sens que lorsqu'elle est utilisée en conjonction avec `@Id`.

Si cette annotation n'est pas spécifiée avec `@Id`, la stratégie NONE est utilisée par défaut.

**Attribut obligatoire :**

* `strategy` : nom de la stratégie (AUTO, NONE, etc)

**Exemple :**

```
<?php
/**
 * @Id
 * @Column(type="integer")
 * @GeneratedValue(strategy="IDENTITY")
 */
protected $id = null;
```