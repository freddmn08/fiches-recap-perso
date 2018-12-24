# Gestion des branches avec GitHub

## Création d'une branche / Pull d'une branche

Fiche méthode GitHub : https://github.com/O-clock-Alumni/fiches-recap/blob/master/ldc/git.md

Fiche https://github.com/O-clock-Alumni/fiches-recap/blob/master/ldc/git.md

`git branch` : indique la branche sur laquelle on se trouve actuellement

On liste toutes les branches (locales / distantes) avec le paramètre `-a` : `git branch -a`

**Procédure de création d'une nouvelle branche (branche christophe pour l'exemple) :**

1. On crée une nouvelle branche `christophe` : `git branch christophe`

1. On se positionne sur cette nouvelle branche : `git checkout christophe`

1. On crée un nouveau fichier `christophe.php`

1. Pour confirmer qu'on est sur la branche `christophe` : `git status`

1. On ajoute à l'index le fichier nouvellement créé : `git add contacts/christophe.php`

1. On commit la nouvelle version : `git commit -m "ajout de mon fichier christophe.php"`

1. On push ce commit : `git push`

    >En cas de message d'erreur, on copie colle la commande déclarant une nouvelle branche `git push --set-upstream origin fred` (on fera des push classique par la suite)

**Procédure de fusion d'une branche avec la branche master (branche christophe pour l'exemple) :**

1. On pull le repo du master : `git pull`

1. On se place sur la branche master : `git checkout master`

1. On fusionne la branche christophe avec la branche master : `git merge christophe` (quand on merge, un commit se crée automatiquement)

## Configuration du Git local

```
git config --global user.name "freddmn08"
git config --global user.email "freddmn08@gmail.com"
git config --global push.default simple
git config --global color.ui true
```

Vérificaton de la config :

```
git config --global user.name
git config --global user.email
```

`git log` permet de voir les commits

Q pour sortir

espace pour dérouler d'un écran

Ajout uniquement des fichiers modifiés :

Éliminer la phase d'indexation
Bien qu'il soit incroyablement utile de pouvoir organiser les commits exactement comme on l'entend, la gestion de la zone d'index est parfois plus complexe que nécessaire dans le cadre d'une utilisation normale. Si vous souhaitez éviter la phase de placement des fichiers dans la zone d'index, Git fournit un raccourci très simple. L'ajout de l'option `-a` à la commande `git commit` ordonne à Git de placer automatiquement tout fichier déjà en suivi de version dans la zone d'index avant de réaliser la validation, évitant ainsi d'avoir à taper les commandes `git add` :

`git commit -a -m "message"`

Se réduit en : `git commit -am "message"`

## Procédure changement de _Driver/Observer_

- _Driver_ : commit et push toutes les modifs sur GitHub
- _Driver_ : ajoute son binôme comme _Collaborator_ sur GitHub
   - sur le dépôt GitHub > _Settings_ > _Collaborators & teams_
   - saisir le mot de passe du compte GitHub s'il est demandé
   - saisir le pseudo GitHub de l' _Observer_ > _Add collaborator_
- _Observer_ : accepte l'invitation à être _Collaborator_ (email ou notification)
- _Observer_ : clone le dépôt du _Driver_

## Gestion des branches

*Créer et merger une branche*
1/ `git branch`
• master (là où nous sommes)
2/ `git branch <newbranch>`
newbranch
• master
3/ `git checkout <newbranch>`
• newbranch
master
> On peut maintenant coder sur la branche nouvellement créée.
4/ `git status` (pour éventuellement vérifier si du code est à valider dans la branche)
5/ `git add .`
6/ `git commit -m "<commentaire associé au code de la branche>"`
7/ `git push` => Git renvoie un message d'erreur : "Pour pousser la branche courante et définir la distante comme amont, utilisez : *git push --set-upstream origin <newbranch>*"
8/ `git push --set-upstream origin <newbranch>`
9/ `git checkout master` : pour retourner sur la branche principale. A ce niveau là, nous n'avons plus le code codé sur la nouvelle branche (normal).
• master
<newbranch>
10 / `git merge <newbranch>` On se positionne sur master pour fusionner la nouvelle branche et on récupère le code de la nouvelle branche (normalement, même pas besoin de git pull à ce niveau là).
*En complément*
`git branch -a` pour voir toutes les branches (en local *et* à distance)
`git branch -d <branch>` pour supprimer une branche en local