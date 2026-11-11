---
description: Règles de codage Symfony (PHP) pour tout fichier PHP, YAML de configuration et template Twig d'un projet Symfony 6/7.
globs: **/*.php,config/**/*.yaml,templates/**/*.twig
alwaysApply: false
---

# Règles Symfony (PHP, configuration, Twig)

## PHP
- `declare(strict_types=1)` en tête de tout fichier ; types sur toutes les propriétés, paramètres et retours.
- Classes `final` par défaut ; propriétés `private` par défaut ; constructeur avec promotion de propriétés.
- PSR-12 via php-cs-fixer (ruleset `@Symfony` conseillé) ; zéro dépréciation tolérée.
- Pas d'accès au conteneur de services hors compilation : tout passe par l'injection de constructeur.

## Contrôleurs
- Minces : mapping requête → service → réponse. Aucune règle métier, aucun QueryBuilder, aucun accès repository direct pour la logique.
- `#[IsGranted(...)]` (ou vérification Voter) sur chaque action sensible.
- Entrée validée par DTO + `#[MapRequestPayload]` / `#[MapQueryString]` ou formulaire dédié — jamais de validation manuelle dans l'action.

## Services et configuration
- Un service = un cas métier, `final readonly`, nommé par son rôle (OrderProcessor, pas Service1).
- Secrets et paramètres d'environnement uniquement via `%env(...)` — jamais de valeur d'environnement en dur.
- Événements via listeners `#[AsEventListener]`, commandes via `#[AsCommand]`, asynchrone via Messenger au-delà d'une seconde de traitement.

## Twig
- Auto-échappement jamais désactivé sans justification écrite (`|raw` audit obligatoire).
- Aucune requête ni logique métier dans les templates ; tout libellé passe par `|trans`.

## Avant livraison
`php vendor/bin/php-cs-fixer fix --dry-run`, `phpstan analyse`, `bin/console lint:container lint:twig lint:yaml` doivent passer.
