---
description: Règles générales de qualité pour tout agent travaillant sur ce projet Symfony/Doctrine/JavaScript (Oracle/PostgreSQL). Toujours actives, quel que soit le fichier modifié.
alwaysApply: true
---

# Règles générales (toujours actives)

## Langue
- Réponses à l'utilisateur, commentaires de code et documentation : **français**.
- Identifiants du code (classes, méthodes, variables) : **anglais** — exception : si le projet existant nomme déjà en français, suivre le projet.

## Changements minimaux et cohérents
- Modifier le strict nécessaire pour la tâche demandée ; ne jamais reformater ou restructurer au-delà du périmètre.
- Avant d'écrire, lire 2-3 fichiers voisins du même type et réutiliser leurs patterns s'ils sont corrects : cohérence du projet > préférence personnelle.
- Aucune nouvelle dépendance composer/npm sans justification écrite (pourquoi, alternative écartée, coût de maintenance).

## Definition of done (chaque livraison)
1. Code écrit selon les règles de la couche (symfony / doctrine / javascript).
2. Lint et analyse statique passent (php-cs-fixer, phpstan, eslint).
3. Tests écrits et verts, y compris test de non-régression pour un bug corrigé.
4. Migration réversible si schéma touché.
5. Aucun `dump()`, `console.log`, `var_dump` ni TODO sans ticket laissé en place.

## Interdits absolus
- Secrets, identifiants ou données réelles dans le code, les tests, les logs ou Git.
- Code mort ou commenté laissé en place : supprimer.
- Créer un fichier qui duplique une utilité existante sans le signaler.

## En cas d'ambiguïté
- Demander une seule question ciblée plutôt que deviner sur un point de fond (données, sécurité, périmètre).
- Les décisions prises par défaut sont annoncées explicitement dans la réponse finale.
