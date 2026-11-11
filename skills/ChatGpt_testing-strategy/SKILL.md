---
name: ChatGpt_testing-strategy
description: À utiliser pour définir ou améliorer une stratégie de tests unitaires, d'intégration, fonctionnels, API ou end-to-end, notamment pour une application PHP/Symfony avec Doctrine et Oracle.
---

# Conception d'une stratégie de tests

## Procédure
1. Identifier les comportements métier, risques et frontières du système.
2. Choisir le niveau de test adapté : unitaire, intégration, fonctionnel HTTP, API ou end-to-end.
3. Définir les scénarios nominaux, limites, erreurs, droits et régressions.
4. Déterminer les dépendances à isoler et celles qui doivent être réelles.
5. Pour Oracle, distinguer tests isolés avec doubles et tests d'intégration sur Oracle.
6. Prévoir des données déterministes et une remise en état fiable.
7. Définir les commandes et critères d'acceptation.
8. Mesurer la couverture utile sans optimiser uniquement le pourcentage.

## Pour chaque test proposé
Préciser le comportement vérifié, les données, les dépendances, les assertions et le niveau de test.

## Sortie
Matrice de tests priorisée, exemples de tests, commandes d'exécution et limites de l'environnement. Ne jamais déclarer un test passé sans exécution effective.
