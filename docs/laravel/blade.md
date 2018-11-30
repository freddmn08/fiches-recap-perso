# Structure des vues avec Blade

[doc Blade templates](https://laravel.com/docs/5.7/blade) - [Tuto layout blade](https://www.formation-laravel.fr/articles/decouverte/2017-11-08-structure-de-nos-vues.html)

Blade est le moteur de templating de Laravel. Les fichiers de views prennent l'extension `.blade.php` et sont stockés dans le dossier `resources/views`. On peut parfaitement écrire du PHP dans les views gérées par Blade.

## 1. La notion d'héritage de templates

### 1.a) Définir un layout

Deux des atouts de Blade sont l'héritage de templates et la notion de sections. On peut définir un layout comme une simple view Blade contenant le code HTML complet d'une page (header et footer à la suite). Ce layout est un template HTML qui sera hérité par la view qui en reprendra la structure (= view enfant).

Les layouts se placent dans le dossier `resources/views/layouts`.

**Exemple de layout :**

```
<!-- Stored in resources/views/layouts/app.blade.php -->

<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

### 1.b) Les propriétés @yield et @section

La propriété Blade `@section('')` définit un contenu. Cette propriété peut se placer aussi bien dans le layout que dans la view enfant.

**Dans le layout :**

* La propriété Blade `@yield('monContenu')` indique un emplacement où ajouter le contenu de la section associée dans la view (dans la view, ce contenu est délimité par les propriétés `@section('monContenu')` et `@endsection`)

* Il est possible d'écrire plusieurs `@yield('')` dans un seul layout, chacun ayant un nom différent

* Une section débute par `@section('')` et se termine par `@show`

**Dans la view :**

Le contenu d'une section du layout peut être inclus dans une view enfant en plaçant la propriété `@parent` dans la-dite section. Elle n'écrase pas le contenu de la section dans la view enfant mais s'y ajoute.

### 1.c) L'héritage d'un layout

Une view peut hériter d'un layout avec la propriété `@extends('nomDuLayout')`. Cette propriété doit être écrite _en premier_ dans la view !

**Exemple de view héritant du layout précédent :**

```
<!-- Stored in resources/views/child.blade.php -->

@extends('layouts.app')

@section('title', 'Page Title')

@section('sidebar')
    @parent

    <p>This is appended to the master sidebar.</p>
@endsection

@section('content')
    <p>This is my body content.</p>
@endsection
```

Rappel : dans une route, on retourne une view via la commande `return view('maVue')`

## 2. Components et slots

### 2.a) Généralités

[doc laravel](https://laravel.com/docs/5.7/blade#components-and-slots)

Les components et slots sont des alternatives à `@yield` et `@section`.

**Exemple :** si l'on souhaite définir une alerte réutilisable, on y définit un `$slot`

```
<!-- /resources/views/alert.blade.php -->

<div class="alert alert-danger">
    {{ $slot }}
</div>
```

La variable `{{ $slot }}` contiendra le contenu à insérer dans le component. Cela se fait via la commande `@component` de Blade.

```
@component('alert')
    <strong>Whoops!</strong> Something went wrong!
@endcomponent
```

Il est possible de définir plusieurs slots pour un même component.

**Exemple :** on souhaite insérer un titre dans l'alerte précédente, on y définit une variable `$title` en plus de `$slot`

```
<!-- /resources/views/alert.blade.php -->

<div class="alert alert-danger">
    <div class="alert-title">{{ $title }}</div>

    {{ $slot }}
</div>
```

On peut alors insérer le titre dans un slot nominatif reprenant le nom de la variable (`$title` dans l'exemple précédent) en fermant l'information avec `@endslot` (`@slot('title') Mon titre @endslot`). Le contenu du component sera lui positionné dans le `@slot` plus bas comme vu précédemment.

```
@component('alert')
    @slot('title')
        Forbidden
    @endslot

    You are not allowed to access this resource!
@endcomponent
```

### 2.b) Passage de paramètres à un component

Pour passer des données à un component, on le fait en mettant un tableau associatif en second argument de `@component`.

**Exemple :** passer au component `alert` la valeur `bar` à la clé `foo` 

```
@component('alert', ['foo' => 'bar'])
    ...
@endcomponent
```

### 2.c) Alias d'un component

Il est possible de définir un alias pour un component pour en faciliter l'accès (utile si le component se trouve par exemple dans un sous-dossier). On dispose alors d'une propriété `@monAlias` pour définir le component.

**Exemple :** un component Blade se trouve se trouve dans `resources/views/components/alert.blade.php`. On peut utiliser la méthode `@component` pour définir un alias d'un component de `components.alert` en `alert` (à faire dans le boot method de AppServiceProvider)

```
use Illuminate\Support\Facades\Blade;

Blade::component('components.alert', 'alert');
Once the component has been aliased, you may render it using a directive:

@alert(['type' => 'danger'])
    You are not allowed to access this resource!
@endalert
You may omit the component parameters if it has no additional slots:

@alert
    You are not allowed to access this resource!
@endalert
```

## 3. Passage d'une variable à une view

[doc laravel](https://laravel.com/docs/5.7/blade#displaying-data)

### 3.a) Généralités

On peut transmettre des données à une view Blade dans l'idée de les y afficher. Pour cela, on passe un tableau associatif à la view.

**Exemple :** on passe la valeur Samantha à la clé name (la variable `$name` sera dispo dans la view et prendra pour valeur `Samantha`)

```
// définition d'une route
Route::get('greeting', function () {
    return view('welcome', ['name' => 'Samantha']);
});

// affichage de la variable dans la view
Hello, {{ $name }}.
```

On peut bien sûr passer n'importe quel code PHP à une view et pas uniquement une variable (retour de fonction, etc).

**Remarques :**

* Le timestamp UNIX courant s'affiche avec `{{ time() }}`.

* Les informations passées dans Blade entre `{{ }}` sont automatiquement protégées des attaques XSS avec la fonction PHP `htmlspecialchars`.

### 3.b) Encodage d'un json

On peut vouloir passer un tableau à une view dans l'idée d'en produire un json à transmettre à une variable JavaScript.

**Exemple :**

```
<script>
    var app = <?php echo json_encode($array); ?>;
</script>
```
Plutôt que d'encoder le json à la mano, on peut utiliser la commande Blade `@json` :

```
<script>
    var app = @json($array);
</script>
```

@TODO

## 4. Structure de contrôle dans une view

@TODO

## 5. Formulaires

[doc formulaire](https://laravel.com/docs/5.7/blade#forms)

### 5.a) Protection contre CSRF

Un formulaire HTML nécessite de définir une route en GET pour afficher le formulaire et une route en POST pour le traiter.

Pour se protéger des attaques de CSRF liées aux formulaires, par défaut dans Laravel, l'envoi d'un formulaire en POST renvoie l'erreur `MethodNotAllowedHttpException`.

En écrivant la route en POST liée au traitement du formulaire, on obtient alors l'erreur `This page has expired due to inactivity`.

En insérant dans le code HTML du formulaire la commande `@csrf`, Laravel ajoute au formulaire un champ caché (de type hidden) contenant une chaîne de caractères aléatoires. Ce champ va lui permettre de protéger le formulaire automatiquement contre les attaques de type CSRF.

On peut alors récupérer les valeurs des champs du formulaire avec la fonction `request()` de Laravel.

```
Route::post('/inscription', function () {
    return request('email');
});
```

### 5.b) Un champ définissant la méthode HTTP

Un formulaire HTML ne pouvant générer de requêtes HTTP de type PUT, PATCH ou DELETE, Laravel permet d'ajouter au formulaire un champ caché définissant l'une de ses méthodes HTTP via la méthode Blade `@method` :

**Exemple :** formulaire HTML en PUT

```
<form action="/foo/bar" method="POST">
    @method('PUT')

    ...
</form>
```

@TODO

## 6. Inclusion de sous-vues

@TODO