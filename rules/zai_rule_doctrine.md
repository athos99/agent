---
description: Règles Doctrine ORM pour les entités, repositories, migrations et tout code accédant à la base (Oracle ou PostgreSQL) dans un projet Symfony.
globs: src/Entity/**/*.php,src/Repository/**/*.php,src/Migrations/**/*.php,migrations/**/*.php
alwaysApply: false
---

# Règles Doctrine (entités, repositories, migrations)

## Requêtes
- Toute requête dans un Repository ou un service dédié — interdite dans un contrôleur, un template ou un listener de vue.
- QueryBuilder avec paramètres liés (`setParameter`) uniquement ; aucune concaténation SQL/DQL ; toute requête native est relue pour l'injection (voir règle sécurité).
- `findAll()` sur une table à fort volume interdit sans pagination ; listes : fetch join sur les relations affichées pour éviter le N+1.

## Écritures
- Écritures multiples = `$em->wrapInTransaction(...)` ; jamais de flush dispersé sans transaction.
- Traitements volumineux par lots avec `$em->clear()` régulier.
- Concurrence : `#[ORM\Version]` quand deux utilisateurs peuvent écrire le même objet.

## Modélisation
- Mapping par attributs PHP ; types stricts (`datetime_immutable` quand la date ne change pas) ; côté propriétaire explicite sur chaque relation.
- Identifiants : `IDENTITY` sur PostgreSQL, `SEQUENCE` sur Oracle (allocationSize alignée sur l'INCREMENT).
- Compatibilité Oracle/PostgreSQL : pas de `''` en lieu et place de `NULL` (Oracle les confond) ; pas de mots réservés des deux plateformes comme nom de colonne.

## Migrations
- Générées puis relues et ajustées ; `up()` et `down()` complets ; jamais de `DROP` sans sauvegarde confirmée.
- Une migration déployée est immuable : correction = nouvelle migration.
- Commentaire d'en-tête obligatoire : objet de la migration, ticket, impact sur les données.
- Sur projet dual-base : schéma portable via l'API de schéma DBAL ; `doctrine:schema:validate` doit passer sur chaque connexion.
