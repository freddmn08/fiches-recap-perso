# Clonage - Push - Pull avec GitHub

## Clonage d'un repository GitHub

### En HTTPS

1. Sur GitHub, copier l'adresse du repository (méthode https)
2. Dans le terminal, dans le dossier où le repository sera colné, taper `git clone` + collage lien (avec `Ctrl + Shift + V`)
3. Renseigner alors username et password GitHub (la saisie du mot de passe est masquée dans le terminal)

:warning: Dans le terminal :

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

## Push sur GitHub

**Procédure résumée :**

```
git add .
git commit -m "mon message de modifs"
git push
```

**Explications :**

* `git add .` : ajoute à l'index
* `git status` : affiche l'état de l'index
* `git commit -m (message d'info)` : met le contenu de l'index dans le local repository
* `git push` : permet d'envoyer les sauvegardes lcoales (commit) sur le serveur distant (GitHub)

**Procédure détaillée de push avec GitHub dans le terminal :**

1. Être dans le dossier qui a été cloné
2. Pour vérifier faire un git status (si fatal ouille)
3. Faire une modif dans le code source
4. `git add .` (ajout de la modif à l'index)
5. `git status` (au besoin) : pour avoir l'état de l'indexbo
6. `git commit -m <nom du commit>`
7. `git push`

## Pull sur GitHub

Dans le dossier courant : `git pull`

Permet d'obtenir la dernière version du repo en local (télécharge les données des commits qui n'ont pas encore été récupérés dans votre branche locale)