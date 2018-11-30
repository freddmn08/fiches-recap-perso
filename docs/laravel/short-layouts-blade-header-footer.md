# Structure des vues avec Blade

[doc Blade templates](https://laravel.com/docs/5.7/blade) - [Tuto layout blade](https://www.formation-laravel.fr/articles/decouverte/2017-11-08-structure-de-nos-vues.html)

## 1. A propos de Blade

Blade est le moteur de templating de Laravel. Les fichiers de views prennent l'extension `.blade.php` et sont stockés dans le dossier `resources/views`. On peut parfaitement écrire du PHP dans les views gérées par Blade.

## 2. Utilisation des includes

On peut insérer header et footer dans une vue pour en structurer le HTML via la propriété `@include('viewToInclude')` de Blade.

On peut ainsi structurer le code HTML d'une view en y incluant les views header et footer.

**Exemple** : structure d'une view avec inclusion du header et du footer

```
@include('header')

<div class="title m-b-md">
    Bonjour
</div>

@include('footer')
```

## 3. Utilisation des layouts

Plutôt que d'inclure header et footer dans une view Blade, on peut utiliser un layout. C'est une simple view Blade contenant le code HTML complet d'une page (header et footer à la suite). Ce layout sera alors hérité par la view qui en reprendra la structure (= view enfant).

Les layouts se placent dans le dossier `resources/views/layouts`.

La propriété `@yield('')` permet alors d'indiquer à Blade un emplacement où ajouter une certaine section. Il est possible d'écrire plusieurs `@yield('')` dans un seul layout, chacun avec un nom différent.

**Exemple :**

```
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
    @yield('contenu')
</body>
</html>
```

On fait appel à un layout dans une view avec la propriété `@extends('nom-du-fichier-contenant-mon-layout')` lorsque `ressources/views/nom-du-fichier-contenant-mon-layout.blade.php` est le nom de la view layout.

La section est alors appelée avec la propriété `@section('contenu')` et `@endsection`. Elle s'insère en lieu et place du `@yield('contenu')` défini dans le layout.

TODO : @parent, @show, @section dans le parent (layout)

**Exemple :** structure d'une view s'appuyant sur un layout

```
@extends('nom-du-fichier-contenant-mon-layout')

@section('contenu')
    <div class="title m-b-md">
        Bonjour
    </div>
@endsection
```
