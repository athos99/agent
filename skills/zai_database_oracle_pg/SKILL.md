---
name: zai_database_oracle_pg
description: Expertise SQL et bases de données pour Oracle et PostgreSQL — types et leur correspondance, requêtes portables et spécifiques, upsert, fenêtrage, plan d'exécution (EXPLAIN), index, PL/SQL vs PL/pgSQL, connexion depuis Doctrine, migration Oracle vers PostgreSQL (orafce, ora2pg). Utiliser dès qu'une question SQL, de schéma, de performance base de données, de portabilité Oracle/PostgreSQL ou de migration de données apparaît, même sans mentionner explicitement les bases.
---

# Oracle et PostgreSQL — le guide double plateforme

Contexte de départ à vérifier : le projet utilise-t-il les deux bases (dualité réelle) ou une seule ? La réponse change tout conseil : on écrit portable ou on écrit natif assumé.

## Correspondance des types

| Concept | Oracle | PostgreSQL | Type Doctrine |
|---|---|---|---|
| Chaîne bornée | `VARCHAR2(n)` | `varchar(n)` | `string` |
| Texte long | `CLOB` | `text` | `text` |
| Entier | `NUMBER(10)` | `integer` / `bigint` | `integer` |
| Décimal exact | `NUMBER(p,s)` | `numeric(p,s)` | `decimal` |
| Booléen | pas de bool natif (`NUMBER(1)`) | `boolean` | `boolean` |
| Date + heure | `DATE` (contient l'heure !) / `TIMESTAMP` | `timestamp` | `datetime_immutable` |
| Date seule | piège : `DATE` Oracle garde l'heure | `date` | `date_immutable` |
| JSON | `CLOB` (+ contrainte is json, 21c) | `jsonb` | `json` |
| UUID | `RAW(16)` ou `VARCHAR2(36)` | `uuid` | `uuid` |

## Différences de sémantique qui produisent des bugs

| Sujet | Oracle | PostgreSQL |
|---|---|---|
| Chaîne vide | `''` **est** `NULL` | `''` ≠ `NULL` |
| Limite de résultats | `FETCH FIRST n ROWS ONLY` (12c+) | `LIMIT n OFFSET m` |
| Upsert | `MERGE INTO` | `INSERT ... ON CONFLICT DO UPDATE` |
| ID retourné après insert | `RETURNING id INTO` (PL/SQL, 23c : `RETURNING`) | `RETURNING id` |
| Date courante | `SYSDATE` / `SYSTIMESTAMP` | `now()` / `CURRENT_DATE` |
| Concaténation null-safe | `NVL`, `COALESCE` | `COALESCE` |
| Casse identifiants | uppercase sans guillemets | lowercase sans guillemets |
| Hiérarchies | `CONNECT BY` | `WITH RECURSIVE` (standard, recommandé partout) |
| Jointures externes anciennes | `(+)` à bannir | jamais utilisé |

Doctrine QueryBuilder (`setMaxResults`, `setParameter`) neutralise la plupart de ces écarts : toute requête native qui contourne Doctrine doit être soit portable, soit doublée par plateforme avec un test sur chacune.

## Connexions Doctrine

```yaml
doctrine:
    dbal:
        connections:
            main:                                   # PostgreSQL
                url: '%env(resolve:DATABASE_URL)%'  # postgresql://app:***@host:5432/db
            legacy:                                 # Oracle
                driver: oci8
                url: '%env(resolve:ORACLE_URL)%'    # oci8://app:***@tns_alias
```
Compte applicatif = privilèges minimaux (session + objets nécessaires). Jamais de DBA/SUPERUSER pour l'application. Voir `zai_security`.

## Plan d'exécution et index

- PostgreSQL : `EXPLAIN (ANALYZE, BUFFERS) SELECT ...` — regarder les jointures à boucle sur gros volumes, les Seq Scan évitables, l'écart estimé/réel (statistiques à rafraîchir avec `ANALYZE`).
- Oracle : `EXPLAIN PLAN FOR <requête>;` puis `SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);` — attention aux prédicats sur colonnes indexées avec fonction (index fonctionnel nécessaire).
- Règles d'index : FK systématiques, colonnes de filtre fréquent, index composites ordonnés par sélectivité ; un index s'ajoute après mesure, pas par peur.
- Fenêtrage disponible et recommandé sur les deux (`ROW_NUMBER() OVER (PARTITION BY ... ORDER BY ...)`) : souvent plus lisible et plus rapide qu'une sous-requête corrélée.

## PL/SQL vs PL/pgSQL (si du code SQL procedural existe)

| Sujet | PL/SQL (Oracle) | PL/pgSQL (PostgreSQL) |
|---|---|---|
| Regroupement | Packages | Pas de packages : schémas + conventions de nommage |
| Types ancrés | `%TYPE`, `%ROWTYPE` | `%TYPE`, `%ROWTYPE` (identiques) |
| Erreurs | `EXCEPTION WHEN ... THEN` | `EXCEPTION WHEN ... THEN` + `RAISE EXCEPTION` |
| Sorties | `DBMS_OUTPUT.PUT_LINE` | `RAISE NOTICE` |
| Transactions autonomes | `PRAGMA AUTONOMOUS_TRANSACTION` | Pas d'équivalent direct — refondre le flux |

## Migration Oracle vers PostgreSQL (si demandée)

1. **Audit** : inventaire des objets (tables, vues, packages, triggers) — outil de référence : `ora2pg` pour l'extraction et la conversion.
2. **Types** : `VARCHAR2`→`varchar`, `NUMBER`→`numeric`/`integer` selon usage, `CLOB`→`text`, `DATE` Oracle (avec heure)→`timestamp` (piège n°1).
3. **Comportements** : `''`=NULL, `ROWNUM`, `CONNECT BY`, `DECODE`, `NVL2` à traduire ; extension **orafce** pour rapprocher les fonctions Oracle.
4. **PL/SQL** : réécriture PL/pgSQL, décomposition des packages.
5. **Validation** : exécution en parallèle sur données copiées, comparaison de comptes et de checksums par table, tests applicatifs complets sur la cible.
6. **Bascule** : double écriture ou fenêtre d'arrêt, rollback écrit et testé avant la bascule réelle.

## Règles de conduite

- Écrire du SQL en dernière intention : d'abord l'API Doctrine, puis requête native portables, puis SQL spécifique par plateforme justifié et commenté.
- Toute requête native cite son plan d'exécution si elle touche des gros volumes.
- Ne jamais modifier des données de production en direct depuis un agent : proposer le script + la procédure de validation et de rollback.
