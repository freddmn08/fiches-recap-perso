# Les formulaires avec Symfony 4

[doc form](https://symfony.com/doc/current/forms.html) - [tuto yt](https://www.youtube.com/watch?v=_cgZheTv-FQ)

Symfony 4 dispose d'un composant nommé Form dédié à la gestion des formulaires. Il s'agit d'une librairie externe qui peut être utilisée en dehors de Symfony.

On se place dans le cas d'un formulaire non relié à une entité.

## 1. Installation

Au besoin, on installe le composant `Form` avec la commande `composer require symfony/form` (inutile dans le cas d'une installation Symfony web-skeleton).

## 2. Création d'un formulaire

[doc building forms](https://symfony.com/doc/current/forms.html#building-the-form)

1. **Création d'une variable formulaire :** on crée une variable "formulaire" `$form` sur laquelle on appelle la méthode `createFormBuilder()` : `$form = $this->createFormBuilder()` (cela crée le formulaire mais ne le rend pas encore exploitable).

1. **Création des inputs :** on appelle sur cette variable la méthode `add()` qui permet d'ajouter des inputs au formulaire :

    * paramètre 1 : attribut `name` de l'input
    * paramètre 2 : attribut `type` de l'input
    * paramètre 3 : passage de paramètres dans un array (placeholder, label, etc)

>On peut générer les contraintes dans un second temps

    **Exemple :** création d'un input de type text

    ```
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
    ```

1. **Génération du formulaire :** une fois les différents inputs créés avec la méthode `add()`, on génère un objet `Form` en appelant la méthode `getForm()` et on retourne la variable `$form`.

    ```
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
    ```

    :warning: On génère l'input du bouton `submit` du formulaire à la main dans la view sans passer par la méthode `add()`.

1. **Récupération des données du formulaire :**

    * On récupère les données du formulaire en appelant la méthode `handleRequest()` sur l'objet `Request` : `$form->handleRequest($request);` (ne pas oublier d'importer la classe `Request`)

    * On traite le formulaire à l'aide des méthodes :
    
        * `isSubmitted()` : vérifie si le formulaire est soumis
        * `isValid()` : vérifie si les contraintes de validation sont en adéquation avec ce qui a été saisi

    * On récupère les datas avec la méthode `getData()` (si formulaire soumis et valide) :

        ```
        // si le formulaire est soumis et valide, alors on en récupère les datas avec getData()
        if ($form->isSubmitted() && $form->isValid()) {
            $formData = $form->getData();
        }
        ```

1. **Création du formulaire en HTML :** on affiche alors le formulaire dans la vue en lui passant le formulaire avec la méthode `createView()` :

    ```
    return $this->render('default/index.html.twig', [
                'formMovie' => $form->createView()
            ]);
    ```

[doc HTML 5 validation](https://symfony.com/doc/current/forms.html)

On pensera à désactiver la validation HTML 5 du formulaire de sorte à gérer les contraintes de validation côté PHP avec Symfony :

```
{# templates/default/new.html.twig #}
{{ form_start(form, {'attr': {'novalidate': 'novalidate'}}) }}
{{ form_widget(form) }}
{{ form_end(form) }}
```

## 3. Création du formulaire avec une fonction dédiée

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