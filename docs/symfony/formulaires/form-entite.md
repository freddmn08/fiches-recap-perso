# Les formulaires avec Symfony 4

[doc form](https://symfony.com/doc/current/forms.html) - [tuto yt](https://www.youtube.com/watch?v=_cgZheTv-FQ)

Symfony 4 dispose d'un composant nommé Form dédié à la gestion des formulaires. Il s'agit d'une librairie externe qui peut être utilisée en dehors de Symfony.

## 1. Installation

On installe le composant `Form` avec la commande `composer require symfony/form` (inutile dans le cas d'une installation Symfony web-skeleton)

## 2. Création et traitement d'un formulaire non relié à une entité

[doc building forms](https://symfony.com/doc/current/forms.html#building-the-form)

Un formulaire pouvant nécessiter un code conséquent, on peut déléguer la partie création du formulaire à une fonction dédiée externe à celle associée à la route traitant le formulaire.

1. **Création d'une fonction de traitement du formulaire :** on crée une fonction private `createForm()` dans le contrôleur traitant le formulaire.

1. **Création de l'objet formulaire :** on crée une variable "formulaire" `$form` sur laquelle on appelle la méthode `createFormBuilder()` : `$form = $this->createFormBuilder()`

1. On appelle sur cette variable la méthode `add()` qui permet d'ajouter des inputs au formulaire :

    * paramètre 1 : attribut `name` de l'input
    * paramètre 2 : attribut `type` de l'input
    * paramètre 3 : passage de paramètres dans un array (placeholder, label, etc)

    **Exemple :** création d'un input de type text

    ```
    private function createForm()
    {
        $form = $this->createFormBuilder(null,[])
                ->add('lastname', TextType::class, [
                    'label' => 'Nom :',
                    'attr' => [
                        'placeholder' => 'Votre nom',
                    ],
                    'constraints' => [
                        new NotBlank([
                            'message' => 'Veuillez renseigner un nom'
                        ])
                    ]
                ])
                // ...
    }
    ```

1. Une fois les différents inputs créés avec la méthode `add()`, on génère un objet `Form` en appelant la méthode `getForm()` et on retourne la variable `$form`.

    ```
    private function createForm()
    {
        $form = $this->createFormBuilder(null,[])
                ->add('lastname', TextType::class, [
                    'label' => 'Nom :',
                    'attr' => [
                        'placeholder' => 'Votre nom',
                    ],
                    'constraints' => [
                        new NotBlank([
                            'message' => 'Veuillez renseigner un nom'
                        ])
                    ]
                ])
                // ...
                ->getForm();

        return $form;
    }
    ```

:warning: On génère l'input du bouton `submit` du formulaire à la main sans passer par la méthode `add()`.

1. Dans la méthode associée à la route traitant le formulaire :
    
    * On génère le formulaire en appelant la fonction `createForm()` dédiée : `$form = $this->createForm();`

    * On récupère les données du formulaire en appelant la méthode `handleRequest()` sur l'objet `Request` : `$form->handleRequest($request);`

    * On traite le formulaire :

        ```
        // si le formulaire est soumis et valide, alors on en récupère les datas avec getData()
        if ($form->isSubmitted() && $form->isValid()) {
            $formData = $form->getData();
        }
        ```

1. On affiche alors le formulaire dans la vue en lui passant le formulaire avec la méthode `createView()` :

    ```
    return $this->render('default/index.html.twig', [
                'formMovie' => $form->createView()
            ]);
    ```

### Dans la view

[doc HTML 5 validation](https://symfony.com/doc/current/forms.html)

On désactive la validation HTML 5 du formulaire de sorte à gérer les contraintes de validation côté PHP avec Symfony :

```
{# templates/default/new.html.twig #}
{{ form_start(form, {'attr': {'novalidate': 'novalidate'}}) }}
{{ form_widget(form) }}
{{ form_end(form) }}
```

## 3. Création d'un formulaire associé à une entité

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