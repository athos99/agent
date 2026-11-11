---
name: ChatGpt_appsec-review
description: À utiliser pour une revue de sécurité applicative, une analyse AppSec ou une vérification OWASP d'un changement, d'une API ou d'une application web.
---

# Revue AppSec

## Périmètre
Identifier les actifs, données, utilisateurs, frontières de confiance, interfaces exposées et opérations sensibles.

## Procédure
1. Comprendre les flux de données et les rôles.
2. Examiner authentification, autorisation et contrôles au niveau ressource/objet.
3. Vérifier validation des entrées, encodage des sorties et accès aux données.
4. Examiner CSRF, XSS, injections, SSRF, désérialisation, téléversements et redirections selon le périmètre.
5. Contrôler secrets, configuration, dépendances, journaux et messages d'erreur.
6. Examiner les scénarios d'abus et les tests négatifs.
7. Classer les constats selon impact, exploitabilité et exposition ; expliquer les hypothèses.
8. Proposer des corrections concrètes et des tests de non-régression.

## Format de sortie
- Périmètre et limites de l'analyse.
- Résumé des risques.
- Constats avec gravité, scénario, impact, preuve et remédiation.
- Tests de sécurité à ajouter.
- Risques résiduels.

## Garde-fous
Ne pas effectuer de test intrusif sur un système réel sans autorisation explicite. Ne pas exposer de secrets. Une absence de vulnérabilité détectée ne constitue pas une certification de sécurité.
