# Les formulaires avec Symfony 4

[doc form](https://symfony.com/doc/current/forms.html) - [tuto yt](https://www.youtube.com/watch?v=_cgZheTv-FQ)

Symfony 4 dispose d'un composant nommé Form dédié à la gestion des formulaires. Il s'agit d'une librairie externe qui peut être utilisée en dehors de Symfony.

## Installation

On installe le composant Form avec la commande `composer require symfony/form`

## Création d'un formulaire associé à une entité

On se place dans le cas de la création d'un formulaire associé à la table `videogame`.

1. On commence par créer un nouvel objet à vide de l'entité ciblée par le formulaire.

```
$videogame = new Videogame();
```

2. On crée ensuite un formulaire à l'aide de la méthode `createFormBuilder()`, elle prend en argument une entité (celle créée précédemment). Le formulaire ainsi créé est donc lié à l'entité Videogame.

```
$formVideogame = $this->createFormBuilder($videogame);
```

3. On ajoute des champs au formulaire avec la méthode `add()` qui prend trois arguments :

* argument 1 : nom du champ
* argument 2 : 
* argument 3 :

Une fois configuré, on affiche le formulaire avec la méthode `getForm()`.

4. On passe le formulaire à la view Twig via la méthode `createView()` :

```
return $this->render('videogame/create.html.twig', [
    'formVideogame' => $formVideogame->createView()
]);
```

5. Dans la view, on affiche le formulaire avec la fonction Twig nommée `form()`, on lui passe en argument la clé à laquelle le formulaire a été transmis dans la méthode `createView` :

```
{% extends 'base.html.twig' %}

{% block body %}
    <h1>Création d'un nouveau jeu vidéo</h1>

    {{ form(formVideogame) }}
{% endblock %}
```