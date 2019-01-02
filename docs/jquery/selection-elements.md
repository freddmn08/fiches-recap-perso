# Sélection d'éléments avec jQuery

[cours OCR](https://openclassrooms.com/fr/courses/1631636-simplifiez-vos-developpements-javascript-avec-jquery/1632814-plus-loin-dans-la-selection-delements)

Pour attendre la disponibilité du DOM, on utilise le code suivant :

```
$(function() {
    // code jQuery s'exécutant une fois le DOM chargé
});
```

## Sélecteurs CSS

On sélectionne un nœud du DOM en utilisant la syntaxe `$(sélection)` où `sélection` représente un sélecteur CSS.

### Quelques sélecteurs CSS à connaître

* **Sélecteur de balise :** un nom de balise, sans les caractères `<` et `>`, permet de sélectionner cette balise. Si plusieurs balises de même nom se trouvent dans le document, toutes ces balises sont sélectionnées

    **Exemple :** si le document contient plusieurs balises `<div>`, le sélecteur CSS div sélectionne toutes ces balises

* **Sélecteur d'identifiant :** le signe `#` fait référence à l'attribut id d'une balise. Deux balises ne peuvent pas avoir le même identifiant.

    **Exemple :** si on définit la balise `<p id="premier">`, le sélecteur `#premier` sélectionne cette balise

* **Sélecteur de classe :** le point fait référence à l'attribut **class** d'une balise. Plusieurs balises peuvent avoir la même classe. Un même traitement peut donc être appliqué à ces deux balises.

    **Exemple :** si le code HTML contient la balise `<h2 class="rouge">`, le sélecteur `.rouge` sélectionne cette balise

* Le sélecteur `nom_balise.nom_classe` permet de sélectionner les balises `nom_balise` de classe `nom_classe`.

    **Exemple :** pour différencier les balises `<h2>` de classe rouge des balises `<p>` de classe rouge, on utilise les sélecteurs `h2.rouge` et `p.rouge`

* **Sélecteur descendant :** supposons que l'on ait défini une liste à puces `<ul></ul>` et une liste numérotée `<ol></ol>`. Chacun des éléments des deux listes est repéré par des balises `<li>`. Pour différencier les éléments `<li>` de la liste à puces des éléments `<li>` de la liste numérotée, on utilise un « sélecteur descendant ». Ainsi, le sélecteur `ul li` s'adresse à tous les éléments `<li>` de la liste à puces `<ul>`, et le sélecteur `ol li` s'adresse à tous les éléments `<li>` de la liste numérotée `<ol>`.

* **Sélecteur d'attribut :** certaines balises HTML peuvent contenir un ou plusieurs attributs. Pour sélectionner toutes les balises qui contiennent un attribut `attribut`, on utilise le sélecteur `[attribut]`

    **Exemple :** la balise `<img src="chien.jpg" width="40" height="30">` contient trois attributs : `src`, `width` et `height`. Pour sélectionner toutes les balises qui contiennent un attribut `src`, on utilise le sélecteur `[src]`.

* **Sélecteur d'attribut de valeur donnée :** on peut sélectionner les balises dont un attribut a une certaine valeur.

    **Exemple :** pour sélectionner toutes les balises dont l'attribut `width` a pour valeur 40, on utilise le sélecteur `[width="40"]`.

* Le caractère `*` représente toutes les balises du document.

### Exemples de sélecteurs CSS

![jquery_selecteurs_css](https://user-images.githubusercontent.com/1475600/50575311-1dd69580-0dfd-11e9-9b4c-eddf802abb36.PNG)

### Nature de l'objet retourné

Le résultat retourné par la fonction `$()` est un objet jQuery. Cet objet ressemble à un tableau : il a une propriété `length` et les éléments sélectionnés peuvent être accédés par un indice.

**Exemples :**

* `$('a').length` retourne le nombre de liens hypertextes contenus dans la page.

* `$('ul.bleu').length` retourne le nombre de balises `<ul>` de classe bleu.

* `$('li[class="impair"]').length` retourne le nombre de balises `<li>` qui ont un attribut class de valeur impair.

* `$('body').length` retourne « 1 » car le document contient une seule balise `<body>`.

### Accès aux élément retournés

Pour accéder à un des éléments sélectionnés, on précise son indice entre crochets à la suite du sélecteur.

**Exemples :**

* `$('a')[0]` retourne le premier lien hypertexte de la page.

* `$('ul.bleu')[3]` retourne la quatrième balise `<ul>` de classe bleu.

* `$('body')[0]` est équivalent à `document.body`.

## Sélecteur d'attributs

On sélectionne les éléments qui contiennent :

* un attribut donné en utilisant le sélecteur `$('[nom]');`

* un attribut donné qui a une certaine valeur en utilisant le sélecteur `$('[nom=valeur]')`.

**Liste des sélecteurs d'attributs évolués :**

![jquery_selecteurs_attributs](https://user-images.githubusercontent.com/1475600/50575392-10baa600-0dff-11e9-9b8d-e73fda75cca6.PNG)

**Exemples d'utilisation :**

On peut modifier la mise en forme d'éléments avec la méthode `css()` :

```
// trace une bordure rouge autour de l'élément sélectionné
$('selecteur').css('border-color','red');
```

![jquery_selecteurs_attr_exemples](https://user-images.githubusercontent.com/1475600/50575454-79565280-0e00-11e9-8db6-12d1d0474a5f.PNG)

## Sélecteurs hiérarchiques

Les sélecteurs hiérarchiques permettent de sélectionner les enfants d'un certain parent, l'énième enfant d'un parent, les enfants uniques, etc.

**Liste des sélecteurs d'attributs évolués :**

![jquery_selec_hierarchiques](https://user-images.githubusercontent.com/1475600/50575566-144f2c80-0e01-11e9-93d6-047fdd8c8ca0.PNG)

**Exemples d'utilisation :**

![jquery_selec_hierarchiques_exemples](https://user-images.githubusercontent.com/1475600/50575577-6d1ec500-0e01-11e9-8604-2bf2e58606a2.PNG)

## Pseudo-sélecteurs d'éléments sélectionnés

Lors de l'utilisation d'un sélecteur CSS, un ou plusieurs éléments sont sélectionnés dans le DOM. En ajoutant un pseudo-sélecteur au sélecteur, on peut filtrer la sélection en ne conservant que les éléments pairs, impairs, ayant un certain index, etc.

**Liste de pseudo-sélecteurs :**

![jquery_pseudo_selec](https://user-images.githubusercontent.com/1475600/50575645-309f9900-0e02-11e9-96a2-0f68e268092a.PNG)

**Exemples d'utilisation :**

![jquery_pseudo_selec_exemples](https://user-images.githubusercontent.com/1475600/50576272-c2150800-0e0e-11e9-8219-a60349413496.PNG)

## Sélecteurs d'éléments particuliers

**Liste de sélecteurs d'éléments en particulier :**

![jquery_selec_particuliers](https://user-images.githubusercontent.com/1475600/50576307-9e05f680-0e0f-11e9-80de-75e1a1f174dd.PNG)

**Exemples d'utilisation :**

![jquery_selec_particuliers_exemples](https://user-images.githubusercontent.com/1475600/50576356-985ce080-0e10-11e9-9a7c-b90196ecc440.PNG)

## Pseudo-sélecteurs spécifiques aux formulaires

Les formulaires ont leur propre jeu de pseudo-sélecteurs CSS. En les utilisant, il est très simple de s'adresser à un élément ou un type d'élément en particulier.

**Liste des pseudo-sélecteurs dédiés aux formulaires :**

![jquery_pseudo_selec_form](https://user-images.githubusercontent.com/1475600/50576469-9c89fd80-0e12-11e9-86e9-d7a8fefbc655.PNG)

**Exemples d'utilisation :**

## Sélecteurs utilisés dans les tableaux

## Parcourir les éléments sélectionnés

## Conversion jQuery/DOM