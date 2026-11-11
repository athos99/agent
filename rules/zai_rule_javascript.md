---
description: Règles JavaScript (et TypeScript/Vue) pour tout fichier front d'un projet Symfony — ES modules, async, sécurité DOM, intégration Stimulus/Encore/AssetMapper.
globs: assets/**/*.js,assets/**/*.ts,assets/**/*.vue,src/**/*.js,public/**/*.js
alwaysApply: false
---

# Règles JavaScript

## Langage
- Modules ES uniquement (`import`/`export`) ; aucune variable globale ; `const` par défaut.
- `async/await` avec gestion d'erreur systématique : aucune promesse flottante ; les erreurs atteignent toujours l'utilisateur (message visible), jamais une console silencieuse.
- Fonctions de moins de 50 lignes ; un niveau d'abstraction par fonction ; logique métier pure dans des modules séparés, testables sans DOM.

## Intégration Symfony
- Suivre le mode d'intégration présent dans le projet (AssetMapper, Encore, ou SPA Vue/React derrière `/api/*`) et ne pas en introduire un second.
- Contrôleurs Stimulus minces : écoute, délégation, rendu ; la logique va dans des modules.
- Chaînes affichées passées par le catalogue de traduction (i18n), jamais en dur.

## Sécurité DOM
- `textContent` par défaut ; `innerHTML` uniquement sur du HTML de confiance et sanitisé (DOMPurify) ; jamais d'`eval`, `new Function`, `document.write` ni `setTimeout("chaîne")`.
- `AbortController` sur les requêtes annulables (autocomplete, changement de vue) ; listeners ajoutés = listeners retirés (`disconnect()`).

## Qualité
- ESLint + Prettier verts avant commit ; `console.log` proscrit dans le code livré.
- Chaque module exporté non trivial possède son test Vitest (voir règle tests).
