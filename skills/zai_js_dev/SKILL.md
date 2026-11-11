---
name: zai_js_dev
description: Développement JavaScript moderne dans l'écosystème Symfony — ES modules, async/await, fetch et AbortController, DOM sécurisé, contrôleurs Stimulus et Symfony UX, AssetMapper ou Webpack Encore, tests Vitest. Utiliser pour toute fonctionnalité front ou script JS d'un projet Symfony, et adapter automatiquement si Vue ou React est détecté dans package.json.
---

# JavaScript moderne dans un projet Symfony

Cible : ES2023+, navigateurs modernes. Vérifier d'abord l'intégration réelle du projet : `package.json` (dépendances), `assets/` (structure), `webpack.config.js` (Encore) ou `importmap.php` (AssetMapper), présence de Stimulus (`@symfony/stimulus-bridge`), Vue ou React.

## Intégration à Symfony — trois modes possibles

| Mode | Quand | Signal dans le projet |
|---|---|---|
| AssetMapper (recommandé Symfony 6.4+) | Apps classiques rendues par Twig + JS ciblé | `assets/app.js`, `importmap.php` |
| Webpack Encore | Build complexe, code splitting lourd, legacy | `webpack.config.js`, `assets/js/...` |
| SPA (Vue/React) derrière une API | Écran très interactif, team front dédiée | `vue`/`react` dans package.json, routes `/api/*` |

Ne jamais mélanger deux modes dans le même écran. Si le projet utilise Stimulus, écrire des contrôleurs Stimulus plutôt que du JS adhésif DOM.

## Contrôleur Stimulus — modèle
```js
// assets/controllers/order-form_controller.js
import { Controller } from '@hotwired/stimulus';

export default class extends Controller {
    static targets = ['total', 'lines'];
    static values = { vatRate: Number };

    recalculate() {
        // une responsabilité par contrôleur ; la logique réutilisable
        // part dans assets/js/ en module pur testable par Vitest
        this.totalTarget.textContent = formatMoney(this.computeTotal());
    }
}
```
La logique métier pure (formatage, calculs) vit dans des modules ES purs, testables sans DOM. Les contrôleurs Stimulus restent minces : écoute, délégation, rendu.

## Requêtes HTTP — modèle de wrapper

```js
// assets/js/api.js
export async function apiFetch(url, { signal, ...options } = {}) {
    const response = await fetch(url, {
        signal,
        headers: { Accept: 'application/json', ...(options.body ? { 'Content-Type': 'application/json' } : {}) },
        ...options,
    });
    if (!response.ok) {
        // RFC 7807 côté Symfony : typer l'erreur pour l'UI
        const problem = await response.json().catch(() => null);
        throw new ApiError(problem?.title ?? 'Erreur réseau', response.status, problem);
    }
    return response.status === 204 ? null : response.json();
}
```
Règles : toujours un `AbortController` pour les vues annulables (changement d'onglet, saisie autocomplete) ; jamais de promesse sans `catch` ; les erreurs atteignent toujours l'utilisateur (message visible, pas une console silencieuse).

## DOM et sécurité

- Écriture de texte : `textContent` ; `innerHTML` réservé au HTML de confiance et déjà sanitisé (DOMPurify si contenu riche). Voir `zai_security`.
- Jamais de `eval`, `document.write`, ni de `setTimeout("chaîne")`.
- Écouteurs ajoutés = écouteurs retirés (`AbortSignal` sur les listeners ou `disconnect()` Stimulus) : les fuites de listeners sont la fuite mémoire n°1 des apps Twig + JS.

## Conventions

- Modules ES uniquement ; `const` par défaut ; fonctions fléchées pour les callbacks, déclarations nommées pour la lisibilité.
- Nommage : `camelCase` pour variables/fonctions, `PascalCase` pour classes/composants, `kebab-case` pour fichiers et noms de contrôleurs Stimulus.
- Fonctions de moins de 50 lignes ; extraire plutôt qu'empiler ; un niveau d'abstraction par fonction.
- `console.log` interdit dans le code livré ; ESLint + Prettier doivent passer avant commit.
- Sélecteurs : `data-*` + API `closest()` plutôt que chaînes de classes fragiles.

## Tests

- Modules purs → Vitest (voir `zai_testing`) ; contrôleurs Stimulus → testables en montant un DOM minimal.
- E2E navigation → Panther (PHP) ou Playwright si le projet l'a adopté.

## Avant de dire « terminé »

1. Aucune promesse flottante, tous les états d'erreur affichés à l'utilisateur.
2. Aucun accès DOM hors du scope du contrôleur/module.
3. Build OK (Encore/AssetMapper) et ESLint propre.
4. Les chaînes affichées passent par la traduction (`trans` côté Twig, catalogue côté JS si i18n).
