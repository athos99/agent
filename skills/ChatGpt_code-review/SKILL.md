---
name: ChatGpt_code-review
description: À utiliser pour examiner un diff, une Merge Request ou un ensemble de changements et détecter les défauts fonctionnels, régressions, problèmes de conception, qualité, performance et sécurité.
---

# Revue de code

## Entrées
Diff ou fichiers concernés, objectif métier, contraintes du projet, version de langage/framework et tests associés. Si l'objectif est absent, l'indiquer et limiter les conclusions.

## Procédure
1. Lire les conventions du dépôt et comprendre le changement demandé.
2. Examiner le diff complet et les appels/callers pertinents.
3. Rechercher les défauts concrets : logique, cas limites, erreurs, concurrence, transactions, compatibilité, performance.
4. Examiner validation, authentification, autorisation, exposition de données et secrets.
5. Vérifier les tests : comportement nominal, limites, erreurs, droits et régressions.
6. Écarter les remarques purement stylistiques déjà couvertes par un formateur, sauf impact réel.
7. Ne pas supposer qu'un test a été exécuté ; lancer uniquement les commandes autorisées et disponibles.

## Format des constats
Pour chaque constat :
- Gravité : bloqueur / élevé / moyen / faible.
- Fichier et ligne ou emplacement.
- Problème concret et scénario de reproduction.
- Impact.
- Correction recommandée.

## Conclusion
Lister les points bloquants, les améliorations non bloquantes, les contrôles exécutés et ceux restant à faire. Ne pas déclarer une MR approuvable si des éléments critiques restent non résolus.
