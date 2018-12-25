# Guide de démarrage avec Git

## Installation sous Linux

`sudo apt-get install git`

## Configuration

On définit name et email de l'utilisateur git et on attribue ces infos à tout futur projet via le drapeau `global` :

```
# git config <option> <valeur>
git config --global user.name "Mon nom"
git config --global user.email "mon@email.fr"
```

## Initialisation d'un nouveau projet

La commande `git init` permet d'initialiser un nouveau dépôt git vide. Cela aura pour effet de créer un dossier `.git` qui contiendra les informations de versioning. Il ne sera pas possible de versionner sans ce dossier là.

```
cd "mon-projet"
git init
```

## État du versioning

Pour connaître l'état du versioning, on utilise la commande `git status`. Elle donne un résumé des fichiers qui sont en staging (fichiers suivis) et des fichiers non suivis.

## Suivi d'un fichier (mise en staging)

Le staging de git permet de sélectionner les fichiers à suivre lors du prochain commit, c'est une sorte de "zone d'attente" où l'on va lister les fichiers que l'on souhaite voir enregistrer.

```
git add <fichier>   # Permet de *stage* le fichier
git add <dossier>   # Stage tout le dossier
git add *.html      # Stage tous les fichier finissant par .html
git add .           # Stage le dossier courant (sans tenir compte des ajouts / suppressions)
git add --all       # Stage tous les fichiers (même les ajouts et les suppressions)
```

## Premier commit

Une fois que la zone de staging est prête, on peut alors faire un premier commit. Un commit est une étape dans l'historique d'un projet, étape que l'on va pouvoir identifier avec un message particulier.

```
git commit  # Ouvre un éditeur pour insérer le message de notre commit
```

On peut également utiliser le drapeau `-m` qui permet de spécifier directement le message du commit sans passer par un éditeur :

```
git commit -m "Message pour le commit"
```

## Consultation de l'historique des commits

La commande `git log` permet de consulter les informations des différents commits d'un projet :

```
git log # Récupère et affiche les derniers commits
```

On sort du log en appuyant sur la touche `q`

**Quelques options de la commande `log` :**

* `--oneline` : permet d'afficher l'historique avec une ligne par commit (plus lisible)
* `-n <nombre>` : permet de sélectionner le nombre de commits à afficher
* `-p <fichier>` : permet de voir l'historique des commits affectant un fichier en particulier
* `--author <motif>` : permet de voir l'historique par rapport au nom de l'auteur

**À noter :** chaque commit est identifié par une [clé SHA-1](https://fr.wikipedia.org/wiki/SHA-1) unique permettant d'assurer l'intégrité du commit mais aussi son identification

## Comparaison de fichiers / commits

La commande `diff` permet de voir les différences entre deux versions d'un fichier depuis le dernier commit : `git diff <fichier>`.

Si l'on souhaite comparer les différences entre un fichier courant et sa version en staging, on ajoute le drapeau `--cached` ou `--staged` : `git diff --staged <fichier>`.

On peuit également comparer les versions d'un fichier par rapport à un commit donné ou entre deux commits ensemble :

```
git diff <commit>           # comparera l'état actuel au commit <commit>
git diff <commit>..<commit> # Permet de comparer les fichier entre 2 commits
```