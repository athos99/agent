---
name: zai_api_design
description: Conception et implémentation d'API REST avec Symfony — nommage des routes, méthodes et codes HTTP, gestion d'erreurs RFC 7807, pagination et filtrage, versionnement, documentation OpenAPI, sécurité API (JWT, rate limiting, CORS), support API Platform. Utiliser pour créer, modifier ou documenter un endpoint, ou dès qu'une question de contrat d'interface entre front et back apparaît.
---

# Conception d'API REST avec Symfony

## Contrat de base

- **Ressources en noms pluriels kebab-case** : `/api/v1/purchase-orders/42/cancellation-requests` — jamais de verbes dans les routes (`/getOrders` banni).
- **Méthodes** : `GET` lecture, `POST` création, `PUT` remplacement complet, `PATCH` modification partielle, `DELETE` suppression. Une action métier sans CRUD naturel (annuler, valider) = sous-ressource ou `POST` sur ressource d'état : `POST /orders/42/cancellations`.
- **Versionnement** dans l'URL, majeur uniquement (`/api/v1/...`) : une rupture de contrat = `v2`, pas une exception dans `v1`.

## Codes de réponse

| Code | Quand | Détail |
|---|---|---|
| 200 | GET/PATCH/PUT réussis | Corps : la ressource ou la page |
| 201 | POST création | En-tête `Location` vers la ressource créée |
| 204 | DELETE réussi | Corps vide |
| 400 | Requête malformée | Erreur de syntaxe |
| 401 | Non authentifié | Pas de jeton / jeton expiré |
| 403 | Authentifié mais non autorisé | Ne pas expliquer davantage (sécurité) |
| 404 | Ressource absente | Uniformiser avec 403 pour ne pas sonder |
| 409 | Conflit d'état | Annulation d'une commande déjà expédiée |
| 422 | Validation métier échouée | RFC 7807 avec détail des champs |
| 429 | Trop de requêtes | Rate limiter, en-têtes de quota |
| 500 | Erreur serveur | Jamais de trace technique dans la réponse |

## Erreurs : RFC 7807 (`application/problem+json`)

Symfony 6+ produit nativement ce format via le serializer :
```json
{
  "type": "validation_error",
  "title": "Requête invalide",
  "status": 422,
  "detail": "Certaines données sont incorrectes.",
  "violations": [
    { "propertyPath": "email", "title": "Cette adresse n'est pas valide." }
  ]
}
```
Le front (`zai_js_dev`) consomme ce contrat : `type` pour brancher la logique, `violations` pour afficher sous les champs. Ne jamais inventer un format maison sans raison forte.

## Pagination et filtrage

- Pagination par page avec plafond : `?page=2&pageSize=50` (plafond `pageSize` à 100 max côté serveur). Réponse enveloppée : `{ "data": [...], "meta": { "page", "pageSize", "totalPages", "totalItems" } }`.
- Pagination par curseur (`?cursor=eyJpZCI6...`) si la liste dépasse quelques dizaines de milliers de lignes ou si l'insertion est continue.
- Filtres nommés et validés (DTO `#[MapQueryString]`) ; tri contrôlé par liste blanche de colonnes (`sort=-createdAt,name`), jamais de tri libre injecté dans le QueryBuilder.

## Validation d'entrée

DTO immuable + contraintes + `#[MapRequestPayload]` / `#[MapQueryString]` (voir `zai_symfony_dev`). La validation côté front est un confort, jamais une protection : le back revalide tout.

## Sécurité API

- Authentification stateless : JWT (`lexik/jwt-authentication-bundle`) ou OAuth2 ; jamais de session pour une API publique, jamais de token dans l'URL.
- Autorisation : `#[IsGranted]` + Voters sur chaque endpoint ; les Voters restent la seule source de vérité métier.
- Rate limiter sur login et endpoints coûteux ; CORS strict (`nelmio_cors.yaml`, origines exactes, pas de `*` en production) ; logs sans secrets ni tokens.
- Voir `zai_security` pour la checklist complète avant livraison.

## Documentation OpenAPI

- Chaque endpoint exposé est documenté (attributs `#[OA\...]` avec `swagger-php`, ou API Platform qui génère automatiquement).
- La doc décrit : rôle, paramètres, corps de requête, codes de retour avec exemples, erreurs possibles, authentification requise.
- Une API sans doc OpenAPI à jour est considérée comme non livrée.

## API Platform (si présent dans composer.json)

Utiliser ses mécanismes plutôt que de réinventer : providers/processors pour la logique personnalisée, groupes de sérialisation pour les champs exposés, filtres déclarés. Ne pas mélanger contrôleurs custom et API Platform sur la même ressource sans raison documentée.

## Avant de livrer un endpoint

1. Contrat cohérent avec les endpoints voisins (noms, enveloppe, erreurs).
2. Validation complète, autorisation vérifiée côté serveur.
3. Tests fonctionnels sur le contrat (codes retour, corps d'erreur) — voir `zai_testing`.
4. Documentation OpenAPI à jour.
5. Changements de contrat = note de version + version d'API impactée.
