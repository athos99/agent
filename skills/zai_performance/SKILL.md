---
name: zai_performance
description: Optimisation de performance pour Symfony, Doctrine, JavaScript et bases Oracle/PostgreSQL — méthode mesurer-optimiser-re-mesurer, profiler Symfony, détection N+1, cache, asynchrone Messenger, plans d'exécution, taille de bundle front. Utiliser dès qu'une question de lenteur, de charge, de mémoire, de temps de réponse ou de volumétrie apparaît (« ça rame », « timeout », « ça plante en prod mais pas en dev »).
---

# Performance — mesurer, optimiser, re-mesurer

Aucune optimisation sans mesure avant/après : une intuition n'est pas une preuve, et la moitié des « optimisations » intuitives dégradent la performance réelle. Chaque intervention suit le cycle : **mesurer → isoler le goulot → optimiser → re-mesurer → chiffrer le gain**.

## Étape 1 — Mesurer

| Zone | Outil | Ce qu'on cherche |
|---|---|---|
| Requêtes HTTP | Profiler Symfony (barre de debug), timeline | Temps total, répartition BD/services/Twig, nb de requêtes SQL |
| SQL PostgreSQL | `EXPLAIN (ANALYZE, BUFFERS)` | Seq Scan sur grosse table, jointures à boucle, écart estimé/réel |
| SQL Oracle | `EXPLAIN PLAN` + `DBMS_XPLAN.DISPLAY` | Prédicats non indexés, jointures dégradées, waits |
| Front | Lighthouse, onglet Réseau | Taille de bundle, requêtes en cascade, images |
| Traits de fond | Logs + Monolog chronométrés, APM si présent | Requêtes lentes récurrentes, timeouts |

La question d'abord : quel est l'objectif chiffré ? (ex. « liste commandes : p95 < 300 ms avec 50 lignes »). Sans objectif, tout est optimisable et rien n'est validé.

## Étape 2 — Les goulots classiques de ce stack, par ordre de fréquence

### Doctrine (cause n°1)
- **N+1** : une requête par ligne affichée. Détection : profiler (compteur SQL élevé). Correction : `addSelect` + `join` (fetch join) dans le repository.
- **Listes non paginées** : `findAll()` sur une table qui a grossi. Correction : pagination + tri indexé.
- **Hydratation lourde** : listes qui n'ont besoin que de 4 colonnes hydratées en objets entiers. Correction : hydration DTO/scalaire.
- **Écritures en boucle** : N flush au lieu d'un. Correction : transaction + batch + `clear()`.

### Base de données
- Index manquant sur FK et filtres fréquents (ajouté après `EXPLAIN`, pas par intuition).
- Fonctions sur colonnes indexées dans le `WHERE` (proscrit ; index fonctionnel si indispensable).
- Requêtes natives non portables optimisées pour une seule plateforme (Oracle ≠ PostgreSQL) — valider sur les deux (voir `zai_database_oracle_pg`).
- Statistiques périmées : `ANALYZE` (PG) / stats optimizer (Oracle) dans la maintenance.

### Application Symfony
- Traitements synchrones qui devraient être en `Messenger` (PDF, envois en masse, appels tiers).
- Cache absent là où le calcul est déterministe et coûteux (pools de cache Symfony, TTL assumés, invalidation documentée).
- OPcache activé et preloading configuré en production.

### JavaScript
- Bundle monolithique : découpage dynamique (`import()`), analyse de bundle avant couper.
- Requêtes en cascade (chained fetch) : paralléliser avec `Promise.all` quand indépendantes, abandonner les requêtes périmées (`AbortController`).
- Images : dimensions adaptées, lazy loading, formats modernes.

## Étape 3 — Re-mesurer et prouver

- Même outil, même scénario, même volumétrie que la mesure initiale.
- Rapport : avant / après / gain / coût (complexité ajoutée ?). Une optimisation qui ne démontre pas de gain mesurable se retire.
- Si le gain est réel mais fragile (dépend d'une volumétrie), ajouter le test de garde correspondant (voir `zai_testing`).

## Limites à ne pas franchir

- Ne pas micro-optimiser du code exécuté rarement : lisibilité d'abord hors point chaud.
- Ne pas empiler des couches de cache pour compenser une requête mal écrite : corriger la requête.
- Un changement d'architecture (async, cache distribué, dénormalisation) se propose avec chiffres et risques, et se décide avec l'utilisateur — pas en silence.
