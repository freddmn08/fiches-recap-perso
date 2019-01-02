# Insérer et remplacer des éléments avec jQuery

[cours OCR](https://openclassrooms.com/fr/courses/1631636-simplifiez-vos-developpements-javascript-avec-jquery/1633537-inserer-et-remplacer-des-elements-dans-le-dom)

## Insérer du contenu

Pour insérer du contenu dans un document, on utilise les méthodes `append()`, `prepend()`, `before()` et `after()` :

* `prepend()`: insère du contenu au début de la sélection

  **Exemple :** `$('h2').prepend('*** ');`

* `append()`: insère du contenu à la fin de la sélection

  **Exemple :** `$('h2').append(' ***');`

* `before()`: insère du contenu avant la sélection

  **Exemple :** `$('#trois').before('<hr>');`

* `after()`: insère du contenu après la sélection

  **Exemple :** `$('hr').after('<br>');`

## Remplacer des éléments

Pour remplacer des éléments, on utilise la méthode `replaceWith()`.

**Exemples :**

```
// remplacer les balises hr par des balises br
$('hr').replaceWith('<br>');

// remplacer des h2 par des h3
$('h2').each(function(){
  var elemH2 = $(this);
  elemH2.replaceWith('<h3>' + elemH2.text() + '</h3>');
});
```

## Insérer des éléments

Pour insérer des éléments dans le DOM, on utilise les méthodes `prependTo()`, `appendTo()`, `insertBefore()` et `insertAfter()`. Dans la suite, `eai` représente l'élément à insérer et `cible` représente l'élément avant ou après lequel doit se faire l'insertion.

* `eai.prependTo(cible)`: insère un élément au début de la cible tout en restant à l'intérieur de celle-ci

  **Exemple :**

  ```
  // ajout d'une balise <hr> avant chaque titre <h2>
  $('<hr>').prependTo($('h2'));
  ```

* `eai.appendTo(cible)`: insère un élément à la fin de la cible tout en restant à l'intérieur de celle-ci

* `eai.insertBefore(cible)`: insère un élément avant la cible (à l'extérieur donc de celle-ci)

* `eai.insertAfter(cible)`: insère un élément après la cible (à l'extérieur de celle-ci)

  **Exemple :**

  ```
  // ajout d'un élément de liste à puces après le deuxième élément
  $('<li>Deuxième élément bis</li>').insertAfter($('li:nth-child(2)'));
  ```

## Déplacer du contenu

Pour déplacer un élément existant dans le document, on dispose des méthodes `append()`, `prepend()`, `before()` et `after()`. En notant `sel` l'élément avant ou après lequel doit se faire le déplacement et `depl` l'élément à déplacer :

* `$('sel').prepend(depl);` : déplace l'élément `depl` après la balise de début de l'élément `sel`

* `$('sel').append(depl);` : déplace l'élément `depl` avant la balise de fin de l'élément `sel`

* `$('sel').before(depl);` : déplace l'élément `depl` avant la balise de début de l'élément `sel`

* `$('sel').after(depl);` : déplace l'élément `depl` après la balise de fin de l'élément `sel`

  **Exemple :**

  ```
  // l'élément d'id un est déplacé après la balise de fin de l'élément d'id deux
  $('#deux').after($('#un'));
  ```

## Dupliquer des éléments

Pour dupliquer des éléments, on dispose de la méthode `clone()` chaînée à la méthode `appendTo()`, `prependTo()`, `insertBefore()` ou `insertAfter()`.

**Exemple :**

```
// duplique l'élément d'id deux et l'insère avant l'élément d'id un
$('#deux').clone().insertBefore($('#un'));
```

Si l'élément dupliqué a un ou plusieurs descendants, ils font eux aussi partie du clonage.

## Supprimer des éléments

Pour supprimer un élément, on utilise la méthode `remove()`.

**Exemples :**

```
// supprime tous les titres de niveau 2
$('h2').remove();

// supprime tous les paragraphes contenant le mot "quelconque"
$('p').remove(':contains("quelconque")');
```

## Entourer des éléments

Les méthodes `wrap()`, `wrapInner()` et `wrapAll()` permettent d'entourer un élément par un ou plusieurs autres éléments créés à la volée :

* `wrapInner()` : entoure le contenu d'un élément par un autre élément créé à la volée

* `wrapAll()` : entoure d'une façon globale les éléments sélectionnés avec un autre élément créé à la volée

**Syntaxe :** `$('sel').wrap('elwrap');` où `sel` est un sélecteur jQuery quelconque et `elwrap` représente le ou les éléments (ouvrants et fermants) à insérer autour de la sélection

**Exemple :**

```
// affiche les éléments d'une liste à puces en italique
$('li').wrap('<i></i>');
```