# Bootstrap Datepicker

[doc datepicker](https://bootstrap-datepicker.readthedocs.io/en/latest/)

Le plugin Bootstrap-datepicker est un plugin JavaScript d'ajout de calendrier. Il  permet de sélectionner une date lorsque l'on clique sur un champ de saisie. Il s'intègre parfaitement dans un champ de type `DateType` dans un formulaire Symfony.

![image datepicker](https://bootstrap-datepicker.readthedocs.io/en/latest/_images/demo_head.png)

## 1. Installation

Ce composant nécessite l'intégration de Bootstrap, de jQuery, jQuery UI et de composant propres à Bootstrap-datepicker.

**Code à intégrer dans le template parent :**

```
{# templates/base.html.twig #}
<!DOCTYPE html>
<html>
    <head>
        // ...
        {% block stylesheets %}
            {# Boostrap 4 Latest compiled and minified CSS #}
            <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
            {# Bootstrap optional theme #}
            <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap-theme.min.css" integrity="sha384-rHyoN1iRsVXV4nD0JutlnGaslCJuC7uwjduW9SVrLvRYooPp2bWYgmgJQIXwl/Sp" crossorigin="anonymous">
            {# Bootstrap datepicker CSS #}
            <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-datepicker/1.8.0/css/bootstrap-datepicker.css">
            {# Personal CSS style file #}
            <link href="{{ asset('css/style.css') }}" rel="stylesheet" />
        {% endblock %}
    </head>
    // ...
    <body>
    // ...
        {% block javascripts %}
            {# jQuery 3.x uncompressed #}
            <script src="https://code.jquery.com/jquery-3.3.1.js" integrity="sha256-2Kok7MbOyxpgUVvAk/HJ2jigOSYS2auK4Pfzbm7uH60=" crossorigin="anonymous"></script>
            {# jQuery UI 1.12 uncompressed #}
            <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js" integrity="sha256-T0Vest3yCU7pafRw9r+settMBX6JkKN06dqBnpQ8d30=" crossorigin="anonymous"></script>
            {# Boostrap 4 Latest compiled and minified JavaScript #}
            <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
            {# Boostrap datepicker #}
            <script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-datepicker/1.8.0/js/bootstrap-datepicker.js"></script>
        {% endblock %}
    </body>
</html>
```

## 2. Intégration dans un formulaire Symfony

Dans Symfony 4, on place  nécessairement les assets dans le dossier `public`, par exmple en créant le sous-dossier `public/assets` qui contiendra les images, fichiers CSS, JS et autres du projet.

**Code à intégrer dans le contrôleur :**

```
<?php

namespace App\Controller;

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\Routing\Annotation\Route;
use Symfony\Component\Validator\Constraints\NotBlank;
use Symfony\Component\Form\Extension\Core\Type\DateType;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;

class DefaultController extends AbstractController
{
    /**
     * @Route("/", name="home", methods={"GET", "POST"})
     */
    public function index(Request $request)
    {
        // génération du formulaire via fonction idoine
        $form = $this->createTaskForm();

        // on récupère les données du formulaire via l'objet Request
        $form->handleRequest($request);

        if ($form->isSubmitted() && $form->isValid()) {
            
            //si soumis & valide OK alors je recupere les data de mon form avec getData()
            // Note: actuellement je recupere un tableau de donnée car je n'ai pas specifié un objet de structure a respecter en entrée de mon createFormBuilder
            
            $formData = $form->getData();

            dump($formData);
        }

        return $this->render('default/index.html.twig', [
            'formMovie' => $form->createView()
        ]);
    }

    private function createTaskForm()
    {
        $form = $this->createFormBuilder(null,[])
        ->add('viewedAt', DateType::class, [
            'label' => 'Vous avez vu ce film le :',
            'widget' => 'single_text',
            'format' => 'dd/MM/yyyy',
            'html5' => false, // prevents rendering it as type="date", to avoid HTML5 date pickers
            'attr' => [
                'class' => 'js-datepicker' // adds a class that can be selected in JavaScript
            ],
            'constraints' => [
                new NotBlank([
                    'message' => 'Veuillez renseigner une date'
                ])
            ]
        ])
        ->getForm();

        return $form;
    }
}
```

**Code à intégrer dans le template enfant :**

```
{% extends 'base.html.twig' %}

{% block title %}{% endblock %}

{% block body %}
    <div class="container">

        {# désactivation de la validation HTML 5 sur le form : {'attr': {'novalidate': 'novalidate'}} #}
        {{ form_start(formMovie, {'attr': {'novalidate': 'novalidate'}}) }}

        <div class="row">
            <div class="col-lg-4 col-md-4 col-xs-4">
                {{ form_label(formMovie.viewedAt) }}
                {{ form_widget(formMovie.viewedAt) }}
            </div>
        </div>

        <div class="row">
            <div class="col-lg-3 col-md-3 col-xs-3">
                <input type="submit" name="comment" id="comment" value="Envoyer" class="form-control btn btn-primary col-lg-2 col-md-2 col-xs-2">
            </div>
        </div>

        {# fermeture du formulaire #}
        {{ form_end(formMovie) }}
    </div>
{% endblock %}

{% block javascripts %}
    {{ parent() }}
    <script>
        {# doc bootstrap datepicker: https://bootstrap-datepicker.readthedocs.io/en/latest/index.html #}
        $.fn.datepicker.dates['fr'] = {
            days: ["dimanche", "lundi", "mardi", "mercredi", "jeudi", "vendredi", "samedi"],
            daysShort: ["dim.", "lun.", "mar.", "mer.", "jeu.", "ven.", "sam."],
            daysMin: ["d", "l", "ma", "me", "j", "v", "s"],
            months: ["janvier", "février", "mars", "avril", "mai", "juin", "juillet", "août", "septembre", "octobre", "novembre", "décembre"],
            monthsShort: ["janv.", "févr.", "mars", "avril", "mai", "juin", "juil.", "août", "sept.", "oct.", "nov.", "déc."],
            today: "Aujourd'hui",
            monthsTitle: "Mois",
            clear: "Effacer",
            weekStart: 1, // 1er jour de la semaine = lundi (si c'était dimanche weekStart: 0)
            format: "dd/mm/yyyy"
            };

        $(document).ready(function() {
            // you may need to change this code if you are not using Bootstrap Datepicker
            $('.js-datepicker').datepicker({
                language: 'fr',
                autoclose: true, // fermeture auto quand l'user a cliqué sur une date
                todayHighlight: true, // highlights the current date
                endDate: '0d' // dernière date sélectionnable : date courante
            });
        });
    </script>
{% endblock %}
```