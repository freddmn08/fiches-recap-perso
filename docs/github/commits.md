# Gestion des commits avec GitHub

## Voir l'état d'un ancien commit

La commande `checkout` permet de voir l'état du dépôt tel qu'il était pour un commit donné sans perdre les modifications effectuées mais en tant qu'observateur (_pas pour revenir réellement en arrière et de sauvegarder des modifications_)

**Pour voir l'état d'un précédent commit :**

Depuis GitHub, 

1. Récupérer la clé SSH1 du commit en question (clé de 7 caractères) :

    * Depuis GitHub : cliquer sur  le lien indiquant le nombre de commits en haut à gauche
    * Dans la liste des commits, copier-coller la clé du commit en question

1. Dans le terminal : `git checkout <clé de 7 chiffres>`. On revient alors à l'état de ce commit dans l'état "detached HEAD", une sorte d'état spectateur. Il n'est pas possible de faire des modifications et de les commit.

<!-- créer une nouvelle branche

`git branch mabranche`

Revenir à la racine

`git branch master` -->

## Restaurer un fichier depuis un commit antérieur

Se fait à l'aide de la commande checkout et de la clé SSH du commit en question : `git checkout <clé à 7 chiffres> <nom du fichier>`

Cela restaure le fichier à un état antérieur. On peut alors commit cette version antérieure avec la commande : `git commit -m "nom du nouveau commit"`

Pour visualiser l'historique des commits : `git log --oneline` (on doit y voir apparaître ce nouveau commit)

## Restaurer un commit antérieur

La tâche `revert` permet d'inverser un commit. La commande `git revert <clé du commit>` va défaire ce qui avait été fait au moment du commit en question en créant un nouveau commit. Cela n'altère pas l'historique, mais va ajouter un nouveau commit d'inversion (les lignes ajoutées seront supprimées, les fichiers supprimés seront recréés...).

:warning: La tâche `revert` ne restaure pas le projet dans l'état antérieur correspondant au commit en question

S'ouvre alors une fenêtre demandant de laisser un message pour valider ces changements (on peut laisser le message par défaut), puis `:wq`.

En faisant alors `git log --oneline`, on observe l'ajout du commit revert que l'on vient d'effectuer.

On peut appliquer la tâche `revert` à un fichier en particulier : `git revert <clé du ommit> <nom du fichier à restaurer>`

**Avantage du revert :** rien ne disparaît de l'historique des commits

## Revenir à un précédent commit

Se fait à l'aide de la commande `reset`. Attention, cela supprime tout l'historique postérieur au commit que l'on va restaurer !

**Utilisation de `reset` :**

* `git reset <fichier>` : supprime un fichier de la zone de staging, mais ne supprime pas les modifications qui sont faites

* `git reset` : supprime tous les fichiers de la zone de staging, sans supprimer les modifications

* `git reset --hard` : renvoie le dossier de travail au niveau du dernier commit. Toutes les modifications non commit seront perdues

* `git reset <commit>` : permet de revenir en arrière jusqu'au `<commit>`, réinitialise la zone de staging tout en laissant le dossier de travail en l'état. L'historique sera perdu (les commits suivant `<commit>` seront perdus, mais pas les modifications). Cette commande permet surtout de nettoyer l'historique en resoumettant un commit unique à la place de commit trop éparses.

* `git reset <commit> --hard` : permet de revenir au `<commit>` et réinitialise la zone de staging et le dossier de travail pour correspondre. Toutes les modifications, ainsi que tous les commits faits après le `<commit>` seront supprimés. A utiliser avec une extrême précaution !

:warning La commande `reset` ne devra jamais être utilisée après avoir publié (push) des modifications. En revanche, elle peut être utile pour nettoyer l'historique local avant de l'envoyer en ligne

### Revenir au commit précédent

`git reset HEAD <nom du fichier à enlever du stage>`

La commande `git reset --hard` permet de revenir au dernier commit de façon irréversible.

### Revenir à un commit antérieur

Pour revenir à un commit donné : `git reset <clé du commit>`

Cela efface l'historique des commits jusqu'à ce commit donné, mais tout en préservant l'espace de travail (cela laisse en staging les modifications de fichier postérieures à cet ancien commit).



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

3. Une fois sortie de l'interface de nano, on tape la commande `git l` pour avoir un git log amélioré et se promener dans l'historique des commits

Touche `q` pour quitter le log

## Modifier un commit non encore push

On a déjà add et commit mais pas encore push, on se rend compte qu'il y a une autre modif faite sur un fichier. Plutôt que de créer un nouveau commit, on peut modifier le dernier commit non encore push avec la commande : `git commit --append`

On peut modifier le nom du commit, y ajouter des fichiers.

## Mettre en attente un fichier non encore push

Modifier un fichier en cours

`git stash pop`