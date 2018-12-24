# Notes sur l'outil GitHub

## Documentations / liens :

* Livre de référence (Pro Git) : https://git-scm.com/book/en/v2

* Fiche recap git : https://github.com/O-clock-Alumni/fiches-recap/blob/master/ldc/git.md

* Fiche versioning GitHub : https://openclassrooms.com/fr/courses/2342361-gerez-votre-code-avec-git-et-github/2433586-quest-ce-que-versionner-son-code

* Bitbucket : alternative à GitHub (gratuit pour les projets perso) https://bitbucket.org/

* Commandes  GitHub : https://services.github.com/on-demand/downloads/fr/github-git-cheat-sheet.pdf

* Démarrage rapide de git en ligne de commande : https://git-scm.com/book/fr/v2/D%C3%A9marrage-rapide-La-ligne-de-commande

* Memento Git : http://slam5.lmdsio.fr/lessons/memento-git

## Vocabulaire

* **Git** est l'outil

* **GitHub** est une plateforme publique en ligne mettant à disposition des dépôts centralisés (et bien d'autres)

* **Repository** (dépôt en français) : il représente plus ou moins la totalité de la base de données que représente un système de gestion de version. Le dépôt contient toutes les versions (tout l’historique) d’un projet

    1 dépôt = 1 version de projet sur 1 serveur

* **Directory / working copy** (copie de travail en français) : il représente le système de fichiers actuel du projet (l’ensemble des fichiers dans le dossier et les sous-dossiers du projet)

* **Commit** (validation en français) : représente un ensemble de changements réalisés sur le dépôt (repository) et qui constitue une nouvelle version atomique du dépôt

    1 commit = 1 ensemble de modifications entre 2 versions

* **Branch** (branche en français) : ensemble de validations (commits) ayant divergé de la branche d’origine

## Mémo concept Git

* **Workspace** : fichiers modifiés en local, les modifs ne sont pas prises en compte par Git.
* **Index (ou stage)** : les modifs sont prises en compte pour le prochain commit mais pas encore commitées.
* **Local repository** : les modifs sont prises en compte dans le commit.
* **Remote repository** : le dépôt distant (sur GitHub par exemple), sur lequel est relié votre dépôt local. Les commits doivent être pushés pour mettre le dépôt distant à jour.