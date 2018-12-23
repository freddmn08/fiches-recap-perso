# Dictionnaire de données

## Entité Liste (`list`)

|Champ|Type|Spécificités|Description|
|-|-|-|-|
|id|INT|PRIMARY KEY, NOT NULL, UNSIGNED, AUTO_INCREMENT|L'identifiant|
|name|VARCHAR(64)|NOT NULL|Le nom de la liste|
|page_order|TINYINT|UNSIGNED, NOT NULL, DEFAULT 0|L'ordre de la liste dans la page|
|created_at|TIMESTAMP|NOT NULL, DEFAULT CURRENT_TIMESTAMP|La date de création de la liste|
|updated_at|TIMESTAMP|NULL|La date de la dernière mise à jour de la liste|

## Entité Post-it (`card`)

|Champ|Type|Spécificités|Description|
|-|-|-|-|
|id|INT|PRIMARY KEY, NOT NULL, UNSIGNED, AUTO_INCREMENT|L'identifiant|
|title|VARCHAR(255)|NOT NULL|Le titre/texte du post-it|
|list_order|TINYINT|UNSIGNED, NOT NULL, DEFAULT 0|L'ordre du post-it dans la liste|
|created_at|TIMESTAMP|NOT NULL, DEFAULT CURRENT_TIMESTAMP|La date de création du post-it|
|updated_at|TIMESTAMP|NULL|La date de la dernière mise à jour du post-it|
|list|entity||La liste dans laquelle le post-it est placé|
|label|entity||Le(s) label(s) affectés au post-it|

## Entité Label (`label`)

|Champ|Type|Spécificités|Description|
|-|-|-|-|
|id|INT|PRIMARY KEY, NOT NULL, UNSIGNED, AUTO_INCREMENT|L'identifiant|
|name|VARCHAR(32)|NOT NULL|Nom du label|
|created_at|TIMESTAMP|NOT NULL, DEFAULT CURRENT_TIMESTAMP|La date de création du label|
|updated_at|TIMESTAMP|NULL|La date de la dernière mise à jour du label|