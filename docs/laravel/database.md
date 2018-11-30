# Gestion de base de données avec Lumen

[Doc database](https://lumen.laravel.com/docs/5.7/database)

:bulb: Pour dumper une variable, on pourra utiliser la fonction Lumen `dd` pour _dump & die_.

[Doc dd()](https://laravel.com/docs/5.7/helpers#method-dd)

## Préambule - L'ORM Eloquent

[Doc Eloquent](https://laravel.com/docs/5.7/eloquent)

Lumen dispose d'une implémentation ActiveRecord via l'ORM Eloquent facilitant ainsi les échanges avec les bases de données relationnelles (= principe d'un ORM). L'idée : on associe à chaque table de la base un modèle qui va permettre / faciliter l'interaction avec la table. Les modèles permettront donc des requêtes de type CRUD en base.

Pour manipuler des informations en base de données avec Lumen avec cette approche, il est nécessaire de :

* Configurer les accès à la base de données dans le fichier `.env` se trouvant à la racine du projet

* Activer Eloquent en décommentant la ligne `$app->withEloquent();` dans le fichier `bootstrap\app.php`

* Créer un modèle au nom de la table à la racine du dossier `app` (on peut le positionner ailleurs pour peu que `composer.json` arrive à en gérer l'autoload). Chaque modèle Eloquent hérite de la classe parente `Illuminate\Database\Eloquent\Model`

* Redéfinir, au besoin, le nom de la table dans le modèle nouvellement créé via la ligne `protected $table = 'nom_de_ma_table';`. En effet, par défaut et par convention, Lumen considère que le nom de la table associée au modèle est le nom du modèle formaté en snake case et au pluriel 

* Par défaut, Eloquent considère que chaque table possède des champs `created_at` et `updated_at` (il va chercher systématiquement à lire / renseigner ces champs). Si absents de la table en question, on peut désactiver ce comportement par défaut en ajoutant cette ligne dans le modèle `public $timestamps = false;` 

Et ensuite ? En pratique, Lumen met à disposition de chaque modèle un [query builder](https://laravel.com/docs/5.7/queries) (gestionnaire / constructeur de requêtes) permettant de gérer simplement et efficacement (en théorie en tout cas :smiling: ) les requêtes avec la base de données.

Concrètement, voici la syntaxe de récupération de toutes les données de la table `videogame` :

```
use App\Videogame;

$videogame = App\Videogame::all();

```

Les explications suivent ! :heart_eyes:

## 1. Récupération de résultats

[Doc retrieving results](https://laravel.com/docs/5.7/queries#retrieving-results)

### 1.a) Lire toutes les entrées (=lignes) d'une table

Pour se faire, deux approches sont possibles : avec la méthode `all` ou avec la méthode `get`. Attention, la nuance est un poil subtil pour le noob qui passerait par là ! :door: :runner:

[get vs all - stack overflow](https://stackoverflow.com/questions/34587457/difference-between-eloquent-modelget-and-all)

#### Avec la méthode `all`

[Doc all()](https://laravel.com/docs/5.7/collections#method-all)

La méthode `all` est une méthode static de la classe Eloquent, elle retourne tous les résultats de la table en question en créant un nouvel objet de requête (de la classe `Illuminate\Database\Eloquent\Collection`) et en appelant sur celui-ci la méthode `get`. Avec `all`, on ne peut pas faire de tri sur les résultats, on récupère tout ! Au mieux, on peut passer en argument de cette méthode les noms des champs à sélectionner, c'est toujours ça... 

**Exemple.** Sélectionner toutes les lignes de la table `platform` :

<details>
<summary>Table platform</summary>

![platform](https://user-images.githubusercontent.com/1475600/48671835-34b37800-eb2e-11e8-99be-496aeb93ebfd.PNG)

</details>

```
// sélectionne tous les champs
$platformList = Platform::all();

// sélectionne uniquement le champ name
$platformList = Platform::all('name');
```

<details>
<summary>Dump de $platformList avec all()</summary>

![platform_all_dump](https://user-images.githubusercontent.com/1475600/48671741-2c0e7200-eb2d-11e8-9960-032f0804e057.PNG)

</details>

<details>
<summary>Dump de $platformList avec all('name')</summary>

![platform_all_name_dump](https://user-images.githubusercontent.com/1475600/48671780-b6ef6c80-eb2d-11e8-98f4-3c7d40eed23b.PNG)

</details>

#### Avec la méthode `get`

[Doc get()](https://laravel.com/docs/5.7/collections#method-get)

La méthode `get` est une méthode de l'objet `Eloquent\Builder`, elle permet de récupérer toutes les entrées d'une table. Elle tolère en amont le tri des résultats via les méthodes `where` ou `select` (voir ci-après), ce que ne supporte pas `all`.

`get` retourne un objet de la classe `Illuminate\Support\Collection`. La valeur de chaque champ est alors accessible via la propriété de l'objet correspondant à ce champ.

**Exemple.** Afficher toutes les valeurs des champs `id` et `name` dans la table `platform` suite à la requête précédente

<details>
<summary>Table platform</summary>

![platform](https://user-images.githubusercontent.com/1475600/48671835-34b37800-eb2e-11e8-99be-496aeb93ebfd.PNG)

</details>

```
foreach ($platformList as $platform) {
            echo $platform->id;
            echo $platform->name;
}
```

### 1.b) Lire certaines entrées d'une table

La méthode `where` permet de ne sélectionner qu'une partie des entrées de la table (= filtre).

Syntaxe : `where('column', '=', 'value')` sélectionne les entrées dont le champ `column` prend la valeur `value` (l'argument `'='` peut être sous-entendu comme dans l'exemple ci-dessous)

La méthode `get` est appelée à la _suite_ de la méthode `where`.

**Exemple.** Sélectionner toutes les entrées de la table `platform` dont le champ `name` prend la valeur `PC`

<details>
<summary>Table platform</summary>

![platform](https://user-images.githubusercontent.com/1475600/48671835-34b37800-eb2e-11e8-99be-496aeb93ebfd.PNG)

</details>

```
$platformList = Platform::where('name', 'PC')->get();
```

<details>
<summary>Dump de $platformList</summary>

![platform_where_dump](https://user-images.githubusercontent.com/1475600/48672722-c4125880-eb39-11e8-9a23-1aa5c159033e.PNG)

</details>

### 1.c) Lire une seule ligne / colonne d'une table

[Doc first()](https://laravel.com/docs/5.7/collections#method-first)

Appliquée à la méthode `where`, la méthode `first` permet de ne sélectionner que la première entrée (=ligne) filtrée.

**Exemple.** Dans la table `videogame`, sélectionner la _première_ entrée dont le champ `editor` prend la valeur `LucasArts`

<details>
<summary>Table videogame</summary>

![videogame](https://user-images.githubusercontent.com/1475600/48672767-76e2b680-eb3a-11e8-8908-f6692a91f43c.PNG)

</details>

```
$videogame = Videogame::where('editor', 'LucasArts')->first();
echo $videogame->name; // affichage du nom du jeu
```

<details>
<summary>Dump de $videogame</summary>

![videogame_first_dump](https://user-images.githubusercontent.com/1475600/48672796-c45f2380-eb3a-11e8-840c-c8b2c2cc1dcd.PNG)

</details>

### 1.d) Lire une seule valeur d'un champ

Plutôt que d'extraire toute une entrée, on peut n'en extraire que la valeur d'un champ donné via la méthode `value`.

**Exemple.** Dans la table `videogame`, sélectionner la valeur du champ `editor` de l'entrée dont le `name` a pour valeur `Castlevania`

<details>
<summary>Table videogame</summary>

![videogame](https://user-images.githubusercontent.com/1475600/48672767-76e2b680-eb3a-11e8-8908-f6692a91f43c.PNG)

</details>

```
$editor = Videogame::where('name', 'Castlevania')->value('editor');
echo $editor; // affiche 'Konami'
```

### 1.e) Lire toutes les valeurs d'un champ (= d'une colonne)

[Doc pluck()](https://laravel.com/docs/5.7/collections#method-pluck)

On appelle la méthode `pluck` afin de sélectionner toutes les valeurs d'une colonne donnée. Elle renvoie un tableau indexé contenant les valeurs du champ en question.

**Exemple.** Sélectionner toutes les valeurs du champ `name` dans la table `videogame`

<details>
<summary>Table videogame</summary>

![videogame](https://user-images.githubusercontent.com/1475600/48672767-76e2b680-eb3a-11e8-8908-f6692a91f43c.PNG)

</details>

```
$videogameNameList = Videogame::pluck('name');
    dump($videogameNameList);
    // si on veut afficher les noms des jeux
    foreach ($videogameNameList as $videogameName) {
        echo $videogameName;
    }
```

<details>
<summary>Dump de $videogameNameList (retour = tableau indexé)</summary>

![videogame_pluck_dump](https://user-images.githubusercontent.com/1475600/48672868-bd84e080-eb3b-11e8-9b7b-46a3ad07a03e.PNG)

</details>

:tada: Intéressant !!! La méthode `pluck` peut également retourner un tableau associatif dont la clé est la valeur d'un champ de la table passé en second argument.

Syntaxe : `pluck('colum1AsValue', 'column2AsKey')`

**Exemple.** Sélectionner toutes les valeurs du champ `name` dans la table `videogame` en renvoyant un tableau associatif `'id' => 'name'`

<details>
<summary>Table videogame</summary>

![videogame](https://user-images.githubusercontent.com/1475600/48672767-76e2b680-eb3a-11e8-8908-f6692a91f43c.PNG)

</details>

```
$videogameList = Videogame::pluck('name', 'id');
    dump($videogameList);
    // si on veut afficher nom et id des jeux
    foreach ($videogameList as $videogameId => $videogameName) {
        echo $videogameName. ' a pour id '.$videogameId.'<br>';
    }
```

<details>
<summary>Dump de $videogameNameList (retour = tableau associatif 'id' => 'name')</summary>

![videogame_pluck_asso_dump](https://user-images.githubusercontent.com/1475600/48672896-194f6980-eb3c-11e8-8360-0de2d9595652.PNG)

</details>

## 2. Sélection de résultats (select)

[Doc selects](https://laravel.com/docs/5.7/queries#selects)

Il est possible de spécifier une clause de type SELECT par appel de la méthode `select` sur la requête de sorte à ne sélectionner que les champs souhaités. On appelle ensuite la méthode `get`. Elle renvoie une collection d'objet associée à la classe en question.

Syntaxe : `select('column1', 'column2')->get()`

**Exemple.** Sélectionner toutes les valeurs des champs `id`, `name` et `editor` dans la table `videogame`

<details>
<summary>Table videogame</summary>

![videogame](https://user-images.githubusercontent.com/1475600/48672767-76e2b680-eb3a-11e8-8908-f6692a91f43c.PNG)

</details>

```
$videogameList = Videogame::select('id', 'name', 'editor')->get();
    dump($videogameList);
    // si on veut afficher l'éditeur du jeu
    foreach ($videogameList as $videogame) {
        echo $videogame->editor;
    }
```

<details>
<summary>Dump de $videogameList</summary>

![videogame_select_dump](https://user-images.githubusercontent.com/1475600/48672994-30428b80-eb3d-11e8-8a4f-b285e0ab054e.PNG)

</details>

La méthode `distinct` permet de forcer le retour de résultats distincts.

Syntaxe : `distinct()->get()`

## 3. Injection de requêtes SQL brutes

[Doc raw expressions](https://laravel.com/docs/5.7/queries#raw-expressions)

On peut injecter dans les méthodes du framework des portions de reqûetes SQL brutes (gare aux risques d'injection SQL).

**Exemple.** Dans la table `videogame`, compter tous les jeux vidéos dont l'éditeur est `LucasArts` et grouper les résultats par éditeur

<details>
<summary>Table videogame</summary>

![videogame](https://user-images.githubusercontent.com/1475600/48672767-76e2b680-eb3a-11e8-8908-f6692a91f43c.PNG)

</details>

```
$videogameList = Videogame::select(DB::raw('count(*) as editor_count, editor'))
                                    ->where('editor', 'LucasArts')
                                    ->groupBy('editor')
                                    ->get();
dump($videogameList);
```

<details>
<summary>Dump de $videogameList</summary>

![videogame_raw_dump](https://user-images.githubusercontent.com/1475600/48673102-3a18be80-eb3e-11e8-991f-c93e10844e2a.PNG)


</details>

## 4. Jointures

[Doc Joins](https://laravel.com/docs/5.7/queries#joins)

### 4.a) Inner join

Il est possible de faire une jointure interne (inner join) à l'aide de la méthode `join` sur une instance du model Query\Builder

Syntaxe : `join('table', constraints)`

On peut faire suivre la méthode `join` des méthodes `select` ou `where`.

**Exemple.** Afficher le nom, l'éditeur et la plate-forme de chaque jeu vidéo

:warning: Si des champs ont des noms ambigus, on veillera à utiliser des alias via le mot-clé `as` afin de les nommer de façon distincte (c'est le cas ici du champ `name` présent dans les tables `videogame` et `platform`). Autrement, Lumen risque de manifester son mécontentement :japanese_goblin:

<details>
<summary>Table videogame</summary>

![videogame](https://user-images.githubusercontent.com/1475600/48672767-76e2b680-eb3a-11e8-8908-f6692a91f43c.PNG)

</details>

<details>
<summary>Table platform</summary>

![platform](https://user-images.githubusercontent.com/1475600/48671835-34b37800-eb2e-11e8-99be-496aeb93ebfd.PNG)

</details>

```
$videogameList = Videogame::join('platform', 'videogame.platform_id', '=', 'platform.id')
                            ->select('videogame.name as videogame_name', 'platform.name as platform_name', 'videogame.editor')
                            ->get();
dump($videogameList);
```

<details>
<summary>Dump de $videogameList</summary>

![videogame_join_dump](https://user-images.githubusercontent.com/1475600/48673219-83b5d900-eb3f-11e8-933d-c87e74a68ae0.PNG)

</details>

On peut bien sûr inverser les rôles joués par les deux tables : instancier un modèle `Platform` et faire une jointure sur la table `videogame`, cela donne exactement le même retour

<details>

<summary> Code de la requête </summary>

```
$videogameListBis = Platform::join('videogame', 'videogame.platform_id', '=', 'platform.id')
                            ->select('videogame.name as videogame_name', 'platform.name as platform_name', 'videogame.editor')
                            ->get();
dump($videogameListBis);
```
</details>

### 4.b) Left join

[Doc left join](https://sql.sh/cours/jointures/left-join)

**Rappel :** LEFT JOIN (aussi appelée LEFT OUTER JOIN) est un type de jointure entre 2 tables permettant de lister tous les résultats de la table de gauche (left = gauche) même s’il n’y a pas de correspondance dans la deuxième table.

On utilise la méthode `leftJoin` de façon similaire à la méthode `join`.

### 4.c) Cross join

[Doc cross join](https://sql.sh/cours/jointures/cross-join)

**Rappel :** CROSS JOIN est un type de jointure sur 2 tables SQL permettant de retourner le produit cartésien des deux tables, c'est-à-dire chaque ligne d’une table avec chaque ligne d’une autre table. Ainsi, effectuer le produit cartésien d’une table A qui contient 30 résultats avec une table B de 40 résultats va produire 1200 résultats (30 x 40 = 1200). En général la commande CROSS JOIN est combinée avec la commande WHERE pour filtrer les résultats qui respectent certaines conditions..

On utilise la méthode `crossJoin` de façon similaire à la méthode `join`.

**Exemple.** Afficher le produit cartésien des tables `videogame` et `platform`

```
$videogameAndPlatformList = Videogame::crossJoin('platform')
                                       ->get();
dump($videogameAndPlatformList);
```

### 4.d) Jointures avancées

Pour se faire, on passe une `Closure` (= fonction anonyme) en second argument de la maéthode `join`, le premier argument étant la table sur laquelle faire la jointure.

Syntaxe : `join('tableToJoin', function ($arg) {
            $arg->on('users.id', '=', 'contacts.user_id')->orOn(...);
        })
        ->get();`

### 4.e) Jointutes avec sous-requêtes

[OCR sous-requêtes](https://openclassrooms.com/fr/courses/1959476-administrez-vos-bases-de-donnees-avec-mysql/1964181-sous-requetes)

**Rappel :** Une sous-requête est une requête à l'intérieur d'une autre requête. Une sous-requête peut être faite dans une requête de type SELECT, INSERT, UPDATE  ou DELETE.

On peut utiliser les méthodes `joinSub`, `leftJoinSub` et `rightJoinSub` pour joindre une requête à une sous-requête.

## 5. Union de plusieurs requêtes

[Doc unions](https://laravel.com/docs/5.7/queries#unions)
[OCR unions](https://openclassrooms.com/fr/courses/1959476-administrez-vos-bases-de-donnees-avec-mysql/1965156-union-de-plusieurs-requetes)

**Rappel :** faire l'union de deux requête signifie réunir les résultats de la première requête et les résultats de la seconde requête

On utilise la méthode `union` pour réunir deux requêtes.

:warning: Les tables réunies via `join` doivent avoir le même nombre de champs !

**Exemple.** Afficher les jeux ayant pour éditeur LucasArts ou Bullfrog (seul intérêt ici, illustrer la syntaxe de la méthode `union`, une clause WHERE avec OR ferait bien sûr parfaitement l'affaire)

```
$first = Videogame::where('editor', '=', 'LucasArts');

$second = Videogame::where('editor', '=', 'Bullfrog')
                   ->union($first)
                   ->get();
```

## 6. Clauses Where

[Doc where](https://laravel.com/docs/5.7/queries#where-clauses)

### 6.a) Clauses Where simples

Comme vu précédement, la méthode `where()` permet de ne sélectionner qu'une partie des entrées d'une table.

Syntaxe : `where('column', 'value')` sélectionne les entrées dont le champ `column` prend la valeur `value`

La méthode `get()` s'applique sur la méthode `where()`.

**Exemple.** Sélectionner l'entrée dont l'id est égal à 1 dans la table `platform`

```
$platformGivenId = Platform::where('id', '=', 1)->get();

Ou encore plus simplement (`'='` sous-entendu)

$platformGivenId = Platform::where('id', 1)->get();
```

On peut également passer un array de conditions à la méthode `where` en second argument.

**Exemple générique.** Sélectionner les utilisateurs dont le status vaut 1 et le champ subscribed est différent de 1

```
$users = Users::where([
    ['status', '=', '1'],
    ['subscribed', '<>', '1'],
])->get();
```

### 6.b) Clauses Or

Pour gérer des clauses `where` avec un opérateur OU, on utilise la méthode `orWhere` qui prend les mêmes arguments que la méthode `where`.

**Exemple.** Afficher les jeux ayant pour éditeur LucasArts ou Bullfrog

```
$videogameList = Videogame::where('editor', '=', 'LucasArts')
                            ->orWhere('editor', '=', 'Bullfrog')
                            ->get();
```

### 6.c) Autres clauses Where

Il existe de nombreuses autres possibilités plus avancées avec les clauses `where`, on peut en particulier leur passer une Closure.

TODO

## 7. Tri et regroupement de résultats

[Doc ordering-grouping-limit-and-offset](https://laravel.com/docs/5.7/queries#ordering-grouping-limit-and-offset)

* La méthode `orderBy` permet de trier les résultats sur un champ donné.

    Syntaxe : `orderBy('ColumnToSort', 'sortDirection')` où `'sortDirection'` est `'asc'` ou `'desc'`

* Les méthodes `groupBy` et `having` permettent de grouper les résultats sur un champ donné.

    Syntaxe : `$users = Users::groupBy('account_id')
                ->having('account_id', '>', 100)
                ->get();`

## 8. Clauses conditionnelles

[Doc conditions](https://laravel.com/docs/5.7/queries#conditional-clauses)

Si l'on souhaite appliquer une clause à une requête uniquement lorsqu'une condition est vérifiée, on peut utiliser la méthode `when`.

**Exemple.** Sélectionner les utilisateurs dont le role_id est égal à la valeur de $role

```
$role = $request->input('role');

$users = Users::when($role, function ($query, $role) {
                    return $query->where('role_id', $role);
                })
                ->get();
```

Dans l'exemple précédent, la méthode `when` exécute uniquement la Closure lorsque le premier argument vaut true. Sinon, la Closure ne sera pas exécutée.

On peut également passer une Closure en troisième paramètre de `when`.

## 9. Insertion de données (insert)

[Doc inserts](https://laravel.com/docs/5.7/queries#inserts)

La méthode `insert` permet d'insérer de nouvelles entrées dans une table. Elle prend en paramètre un tableau associatif (clé = champs => valeur).

### 9.a) Cas d'un ajout en dur

**Exemple.** Ajouter dans la table `videogame` le jeu Warcraft: Orcs and Humans, éditeur Blizzard, sorti le 08-10-1994 sur PC

:bulb: Comme la table `videogame` possède un id en AI, on ne renseigne pas ce champ à l'insertion.

<details>
<summary>Table videogame</summary>

![videogame](https://user-images.githubusercontent.com/1475600/48672767-76e2b680-eb3a-11e8-8908-f6692a91f43c.PNG)

</details>

```
Videogame::insert([
            'name' => 'Warcraft: Orcs and Humans',
            'editor' => 'Blizzard',
            'release_date' => '1994-10-08',
            'platform_id' => 1
]);
```

Si l'on souhaite récupérer l'id de la nouvelle entrée, on utilise la méthode `insertGetId` pour _à la fois_ insérer l'entrée et récupérer son id.

```
$id = Videogame::insertGetId([
            'name' => 'Warcraft: Orcs and Humans',
            'editor' => 'Blizzard',
            'release_date' => '1994-10-08',
            'platform_id' => 1
]);
```

On peut également insérer plusieurs entrées à la fois en base en passant un tableau multidimensionnel à la méthode `insert`.

**Exemple.** Ajouter dans la table `videogame` les jeux :

* Warcraft: Orcs and Humans et Warcraft II, éditeur Blizzard, sorti le 08-10-1994 sur PC

* Warcraft II: Beyond the Dark Portal, éditeur Ubisoft, sorti le 12-04-1996 sur PC

```
Videogame::insert([
            ['name' => 'Warcraft: Orcs and Humans',
            'editor' => 'Blizzard',
            'release_date' => '1994-10-08',
            'platform_id' => 1],
            ['name' => 'Warcraft II: Beyond the Dark Portal',
            'editor' => 'Ubisoft',
            'release_date' => '1996-04-12',
            'platform_id' => 1]
]);
```

### 9.b) Cas d'un ajout en GET / POST

On reprend l'exemple de l'ajout d'une nouvelle entrée dans la table `videogame`.

### Approche objet

```
/*
Pour récuperer les données passées en GET ou en POST, on utilise la méthode commune $request->input('monParam');
*/

// on récupère les valeurs des champs de la requête HTTP
// on peut utiliser une valeur en 2e paramètre qui sera retournée par défaut si l'input côté form est envoyé à vide (POST)
$name = $request->input('name', '');
$editor = $request->input('editor', '');
$release = $request->input('release_date', '');
$platformId = $request->input('platform_id', '');

/*
Pour insérer en DB :
- on crée un objet du modèle souhaité à vide
- on remplit ses champs
- on sauvegarde
*/

// création d'une instance du model Videogame
$videogame = new Videogame();

// on renseigne ses champs
$videogame->name = $name;
$videogame->editor = $editor;
$videogame->release_date = $release;
$videogame->platform_id = $platformId;
// on sauvegarde en base de données
// la méthode save tout comme la méthode all existe déjà dans le model
$videogame->save();
```

### Approche static

Grâce à `Facades`, une approche static plus efficace est possible pour ajouter en base des données reçues en GET ou en POST.

```
Videogame::insert([
            'name' => 'Warcraft: Orcs and Humans',
            'editor' => 'Blizzard',
            'release_date' => '1994-10-08',
            'platform_id' => 1
]);
```

## 10. Mise à jour de données (update)

[Doc updates](https://laravel.com/docs/5.7/queries#updates)

### 10.a) Cas général

On met à jour des entrées en base de données via la méthode `update`. Elle prend en argument un tableau associatif clé = champ => valeur, tout comme la méthode `insert`.

On peut contraindre la mise à jour avec la méthode `where`.

**Exemple générique.** 

```
Users::where('id', 1)
       ->update(['votes' => 1]);
```

### 10.b) Cas d'une colonne de type JSON

[lien working with json in my sql](https://scotch.io/tutorials/working-with-json-in-mysql)

TODO

### 10.c) Incrémenter / décrémenter la valeur d'un champ

On peut incrémenter / décrémenter la valeur d'un champ à l'aide des méthodes `increment` / `decrement`. Elles prennent en premier argument le nom du champ à incrémenter / décrémenter. Un second argument optionnel peut préciser la valeur de cet incrément / décrément.

**Exemples génériques.** 

```
Users::increment('votes');

Users::increment('votes', 5);

Users::decrement('votes');

Users::decrement('votes', 5);
```

## 11. Suppression de données (delete)

[Doc deletes](https://laravel.com/docs/5.7/queries#deletes)

On supprimer une entrée en base à l'aide de la méthode `delete`. On peut contraindre cette suppression avec la méthode `where`.

**Exemple générique.** 

```
// on supprime toutes les entrées de la table users
Users::delete();

// on supprime les entrées de la table users pour lesquelles votes > 100
DB::table('users')->where('votes', '>', 100)->delete();
```

:warning: Pour supprimer toutes les entrées d'une table et remettre un id en AI à zéro, on utilise la méthode `truncate`.

**Exemple générique.** 

```
Users::truncate();
```