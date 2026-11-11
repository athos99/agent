---
description: Règles de sécurité non négociables pour tout agent sur ce projet — actives en permanence, quel que soit le fichier (PHP, JS, SQL, YAML, Twig).
alwaysApply: true
---

# Règles de sécurité (toujours actives — non négociables)

## Entrées et sorties
- Toute entrée est hostile : validée côté serveur, toujours, même si le front valide déjà.
- Requêtes SQL/DQL : paramètres liés exclusivement ; concaténation interdite ; toute requête native est relue avant d'être livrée.
- Sorties échappées : Twig auto-échappement conservé, `|raw` justifié et audité ; JS : `textContent` plutôt qu'`innerHTML` sur données non sûres.

## Autorisation
- Contrôle d'accès vérifié côté serveur (`#[IsGranted]` + Voters) sur chaque action sensible, jamais uniquement côté interface.
- L'identité de l'utilisateur vient de la session/token, jamais d'un paramètre client (`user_id` dans la requête interdit pour l'autorisation).

## Secrets et données
- Aucun secret dans le code, les tests, les logs, Git ou les messages d'erreur : uniquement variables d'environnement ou coffre Symfony.
- Aucune donnée personnelle réelle dans les tests, les fixtures ou les commits ; mots de passe hachés avec argon2id/bcrypt via `UserPasswordHasherInterface`.
- Logs sans données sensibles (mots de passe, tokens, données de carte).

## Configuration et dépendances
- Production : `APP_ENV=prod`, `APP_DEBUG=0`, profiler désactivé, pages d'erreur génériques, `/api/doc` protégé.
- Rate limiter sur login, mot de passe oublié et endpoints coûteux ; CORS avec origines exactes (pas de `*` en production).
- `composer audit` et `npm audit` sans vulnérabilité critique/majeure avant livraison.
- CSRF : protection native des formulaires jamais désactivée ; API stateless protégée par token et cookies `SameSite`.

## Uploads et fichiers
- Validation du mime réel (contenu), taille maximale, nom régénéré, stockage hors webroot public, URLs signées si privé.
