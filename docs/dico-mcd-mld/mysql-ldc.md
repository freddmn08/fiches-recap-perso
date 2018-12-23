# MySQL en ligne de commandes

Connexion : `mysql - u root -p`

Saisir alors le mot de passe associé au compte root (la saisie est masquée)

Voir la liste des bases de données présentes : `SHOW DATABASES;`

Créer une base de données : `CREATE DATABASE musiclunar;`

Utiliser une base en particulier : `USE musiclunar;`

Lister les tables d'une database en particulier : `SHOW TABLES;`

Pour quitter MySQL en ldc : `exit;`

## Import de tables

En se plaçant dans le dossier contenant l'import SQL
`mysql -u root -p musiclunar < musicdq.sql`

Attention au sens du chevron :

* base < .sql : import de datas

* base > .sql : export de datas

## Exemple de création de tables de correspondance (lookup table)

```
CREATE TABLE IF NOT EXISTS concert (
    singer_id INT NOT NULL,
    concert_hall_id INT NOT NULL,
    concert_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (singer_id, concert_hall_id),
    CONSTRAINT fk_concert_singer_id_comment FOREIGN KEY (singer_id) REFERENCES singer(id),
    CONSTRAINT fk_concert_concert_all_id FOREIGN KEY (concert_hall_id) REFERENCES concert_hall(id)
)
ENGINE=INNODB;
```

Changement de la PK de la table concert

```
ALTER TABLE concert 
DROP PRIMARY KEY,
ADD PRIMARY KEY (singer_id,concert_hall_id,concert_date);
```