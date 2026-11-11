---
description: Règles API pour les contrôleurs HTTP, endpoints REST et tout code définissant le contrat entre front et back dans ce projet Symfony.
globs: src/Controller/**/*.php,src/Api/**/*.php,src/ApiResource/**/*.php
alwaysApply: false
---

# Règles API REST

## Contrat
- Ressources plurielles kebab-case, versionnées dans l'URL (`/api/v1/...`) ; pas de verbe dans la route ; action métier = sous-ressource (`POST /orders/42/cancellations`).
- Codes HTTP exacts : 201 + `Location` pour une création, 204 pour un delete, 422 pour une validation échouée, 409 pour un conflit d'état, 403 vs 404 cohérents (ne pas aider au sondage).
- Erreurs en RFC 7807 (`application/problem+json`) avec `violations` détaillées — jamais de format maison sans raison forte documentée.

## Validation et autorisation
- Entrée systématiquement un DTO validé (contraintes + `#[MapRequestPayload]`/`#[MapQueryString]`) — la validation front n'est jamais une protection.
- `#[IsGranted]` + Voters sur chaque endpoint ; l'autorisation métier reste dans les Voters, source unique de vérité.

## Pagination et tri
- Pagination avec plafond serveur (`pageSize` max 100) et métadonnées complètes (`page`, `totalPages`, `totalItems`) ; curseur si volumétrie continue.
- Tri par liste blanche de colonnes uniquement, jamais injecté librement dans le QueryBuilder.

## Documentation et évolution
- OpenAPI à jour pour chaque endpoint exposé (attributs ou API Platform) ; une API sans doc est non livrée.
- Toute rupture de contrat = nouvelle version majeure ; les changements de contrat sont annoncés au front avant merge.
