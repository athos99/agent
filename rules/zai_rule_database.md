---
description: Règles base de données pour tout SQL, script de migration ou requête native visant Oracle ou PostgreSQL dans ce projet.
globs: **/*.sql,src/Migrations/**/*.php,migrations/**/*.php,src/Entity/**/*.php
alwaysApply: false
---

# Règles base de données (Oracle / PostgreSQL)

## SQL
- D'abord l'API Doctrine ; ensuite SQL natif portable ; le SQL spécifique à une plateforme est un dernier recours, commenté et justifié (pourquoi DQL ne suffit pas).
- `SELECT *` interdit : colonnes explicites ; pas de fonction sur colonne indexée dans un `WHERE` sans index fonctionnel dédié.
- Tout traitement de données volumineuses passe par des lots (batch) et une transaction assumée ; le rollback est prévu avant l'exécution.
- Paramètres liés partout (voir règle sécurité) ; `EXECUTE IMMEDIATE` / `format()` dynamiques sur entrée utilisateur interdits.

## Compatibilité des deux plateformes
- Oracle : `''` est `NULL` — tester les deux ; `DATE` contient l'heure ; `FETCH FIRST n ROWS ONLY` ; `MERGE` pour l'upsert.
- PostgreSQL : `LIMIT/OFFSET` ; `INSERT ... ON CONFLICT` pour l'upsert ; `RETURNING` pour l'identifiant créé.
- Tout code qui contourne Doctrine doit soit être portable, soit exister en deux variantes testées chacune sur sa plateforme.

## Schéma et migrations
- Identifiants : `IDENTITY` sur PostgreSQL, `SEQUENCE` sur Oracle.
- Index sur toutes les FK et colonnes de filtre fréquent ; un index s'ajoute après `EXPLAIN`, jamais par intuition.
- Migration déployée immuable ; `down()` toujours écrit ; pas de `DROP` sans sauvegarde confirmée.

## Comptes et privilèges
- Compte applicatif à privilèges minimaux (session + objets nécessaires), jamais DBA/SUPERUSER.
- Aucun accès en écriture directe à une base de production depuis un agent : proposer le script + procédure de validation et de rollback, l'exécution reste humaine.
