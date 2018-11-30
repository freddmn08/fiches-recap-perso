# Notes de cours -- GitHub --

## Documentations / liens :

* Fiche recap git : https://github.com/O-clock-Alumni/fiches-recap/blob/master/ldc/git.md

* Fiche versioning GitHub : https://openclassrooms.com/fr/courses/2342361-gerez-votre-code-avec-git-et-github/2433586-quest-ce-que-versionner-son-code

* Bitbucket : alternative à GitHub (gratuit pour les projets perso) https://bitbucket.org/

* Commandes  GitHub : https://services.github.com/on-demand/downloads/fr/github-git-cheat-sheet.pdf

* Démarrage rapide de git en ligne de commande : https://git-scm.com/book/fr/v2/D%C3%A9marrage-rapide-La-ligne-de-commande

* Memento Git : http://slam5.lmdsio.fr/lessons/memento-git

## Vocabulaire

* Git est l'outil
* GitHub est une plateforme publique en ligne qui met à dispo des dépôts centralisés (et bien d'autre)
* Repo(sitory) = 1 dépôt = 1 version de projet sur 1 serveur
* 1 commit = 1 ensemble de modifications entre 2 versions

## Mémo concept Git

* **Workspace** : fichiers modifiés en local, les modifs ne sont pas prises en compte par Git.
* **Index (ou stage)** : les modifs sont prises en compte pour le prochain commit mais pas encore commitées.
* **Local repository** : les modifs sont prises en compte dans le commit.
* **Remote repository** : le dépôt distant (sur GitHub par exemple), sur lequel est relié votre dépôt local. Les commits doivent être pushés pour mettre le dépôt distant à jour.

## Clonage d'un repositery GitHub

### En https

1. Sur GitHub, copier l'adresse du repositery (méthode https)
2. Dans le terminal, dans le dossier où le repositery sera colné, taper `git clone` + collage lien (avec `Ctrl + Shift + V`)
3. Renseigner alors username et password GitHub (la saisie du mot de passe est masquée dans le terminal)

/!\ Dans le terminal :

* `Ctrl + Shift + C` : copier
* `Ctrl + Shift + V` : coller

### Avec une clé SSH

https://github.com/O-clock-Alumni/fiches-recap/blob/master/ldc/git.md

* `cd /` : on revient à la racine du serveur

* `ssh-keygen -t rsa`

* faire entrée pour valider le chemin
* enter passphrase: faire entrée
* enter same passphrase again: faire entrée
* taper `cat /home/mint/.ssh/id_rsa.pub` (clef publique)
* Ajout de cette clé publique dans notre compte GitHub : Avatar > Settings > SSH and GPG keys > New SSH key :
  * Titre : clé du téléporteur
  * Copier la clé dans le terminal et la coller dans GitHub
  * Saisir au besoin son mdp GitHub
* Clonage en SSH :
  * Copier le lien du repo en SSH
  * De la racine : `cd /var/www/html/s01/e08`
  * Puis `git clone Ctrl + Shift + V (lien clone repo SSH)`

## Pusher sur GitHub

```
git add .
git commit -m "mon message de modifs"
git push
```

* `git add .` : ajoute à l'index
* `git status` : affiche l'état de l'index
* `git commit -m (message d'info)` : met le contenu de l'index dans le local repositery
* `git push` : permet d'envoyer les sauvegardes lcoales (commit) sur le serveur distant (GitHub)

Procédure pour pusher sur GitHub avec terminal :

1. être dans le dossier qui a été cloné
2. pour vérifier faire un git status (si fatal ouille)
3. faire une modif dans le code source
4. git add . (ajout de la modif à l'index)
5. git status (au besoin) : pour avoir l'état de l'indexbo
6. git commit -m (message d'info)
7. git push

## Puller sur GitHub

Dans le dossier courant : `git pull`

Permet d'obtenir la dernière version du repo en local (télécharge les données des commits qui n'ont pas encore été récupérées dans votre branche locale)

## Création d'une branche / Pull d'une branche

Fiche méthode GitHub : https://github.com/O-clock-Alumni/fiches-recap/blob/master/ldc/git.md

Fiche https://github.com/O-clock-Alumni/fiches-recap/blob/master/ldc/git.md

`git branch` : indique la branche sur laquelle on se trouve actuellement

On liste toutes les branches (locales / distantes) avec le paramètre -a : `git branch -a`

1. On crée une nouvelle branche christophe : `git branch christophe`

1. On se rend sur la branche christophe : `git checkout christophe`

1. On crée un nouveau fichier christophe.php

1. Pour confirmer qu'on est sur la branche christophe : `git status`

1. On ajoute dans l'index le fichier nouvellement créé : `git add contacts/christophe.php`

1. On commit la nouvelle version : `git commit -m "Ajout de mon fichier christophe.php"`

1. On pushe ce commit : `git push`
-> message d'erreur, on copie colle la commande déclarant une nouvelle branche `git push --set-upstream origin fred`
(on fera des push classique par la suite)

1. On pull le repo du master

1. On se place sur la branche locale qui entrera dans la fusion

1. On fusionne la branche christophe avec la branche master, on se place par exemple sur la branche master et on fusionne la branche christophe (quand on merge, un commit se crée automatiquement) : `git merge christophe`

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
Bien qu'il soit incroyablement utile de pouvoir organiser les commits exactement comme on l'entend, la gestion de la zone d'index est parfois plus complexe que nécessaire dans le cadre d'une utilisation normale. Si vous souhaitez éviter la phase de placement des fichiers dans la zone d'index, Git fournit un raccourci très simple. L'ajout de l'option -a à la commande git commit ordonne à Git de placer automatiquement tout fichier déjà en suivi de version dans la zone d'index avant de réaliser la validation, évitant ainsi d'avoir à taper les commandes git add :

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

## Pull request

Lien vers la fiche recap : https://github.com/O-clock-Alumni/fiches-recap/blob/master/ldc/git-pull-request.md

## Repartir d'un certain commit

copier les 7 premiers chiffres

git checkout + 7 chiffres

créer une nouvelle branche

git branch mabranche

Revenir à la racine

git branch master


## Lister l'historique des commits dans le terminal

On modifie la config de github afin de voir l'historique des commits dans un log amélioré

1. On va dans le fichier de config (ouverture d'une interface) et on tape : `nano ~/.gitconfig`

2. On crée un alias en se plaçant au clavier sous la partie [user] et on copie-colle :

```
[alias]
  l = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n''          %C(white)%s%C(reset) %C(dim white)- %an%C(reset)' --all
```

Ctrl + O pour écrire

Ctrl + X pour quitter

3. Une fois sortie de l'interface du nano, on tape la commande `git l` pour avoir un git log amélioré et se promener dans l'historique des commits

`q` pour quitter le log

# Gestion des branches

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