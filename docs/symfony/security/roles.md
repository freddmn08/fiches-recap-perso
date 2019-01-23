## La gestion des rôles

`IS_AUTHENTICATED_FULLY` : permet de savoir un user est auth ou non

La méthode `is_granted()` permet de tester deux types d'accès :

* generaliste : si mon utilisateur quel que soit son role est authentifié

* par role : si mon utilisateur authentifié a une role precis

Il existe pour les acces generalistes 3 types d'acces :

* `IS_AUTHENTICATED_FULLY` : mon utilisateur est loggé et a une session en cours (plus securisé)

* `IS_AUTHENTICATED_REMEMBERED` : mon utilisateur est loggé car un cookie a été créé suite a une cache coché du type "remember me"

* `IS_AUTHENTICATED_ANONYMOUSLY` : mon utilisateur est anonyme (non loggé)