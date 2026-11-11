---
name: zai_testing
description: Écriture et organisation des tests automatisés pour Symfony et JavaScript — PHPUnit (unitaires, Kernel, fonctionnels), fixtures avec zenstruck/foundry, tests Vitest côté JS, tests E2E (Panther/Playwright), couverture et intégration continue. Utiliser dès qu'il faut tester du code, corriger un bug (test de non-régression obligatoire), créer des fixtures, ou répondre à « est-ce que ça marche » de façon vérifiable.
---

# Tests — PHPUnit et Vitest

Règle d'or : **tout bug corrigé ajoute son test de non-régression** (le test qui échouait avant le correctif et passe après). Toute nouvelle fonctionnalité arrive avec ses tests. Un développement sans test est un développement non terminé.

## Pyramide côté Symfony

| Niveau | Classe de base | Ce qu'il teste | Vitesse |
|---|---|---|---|
| Unitaires | `TestCase` | Services métier purs, DTO, utilitaires | Millisecondes, pas de base |
| Kernel | `KernelTestCase` | Services avec Doctrine, voters, handlers Messenger | Secondes, base de test |
| Fonctionnels | `WebTestCase` | Endpoints complets : requête HTTP → réponse, codes, JSON | Secondes, base de test |

## Structure et nommage

```
tests/
  Unit/Service/OrderProcessorTest.php
  Functional/Controller/OrderControllerTest.php
```
Nom de méthode en trois temps : `test_<sujet>_<comportement_attendu>_<condition>` (ex. `test_annulation_rejetee_quand_commande_expediee`). Pattern AAA systématique : Arrange (données/fixtures), Act (une seule action), Assert (une intention par test).

## Fixtures — zenstruck/foundry

Factories par entité (`OrderFactory::new()->create()`), états réutilisables (`shipped()`, `cancelled()`), pas de fixtures globales monolithiques : chaque test construit son monde minimal. Les données de test sont fictives — jamais de données réelles ni de données de production même anonymisées sans justification RGPD.

## Tests avec base de données (Oracle / PostgreSQL)

- Base de test dédiée, jamais la base de dev.
- `dama/doctrine-test-bundle` (rollback automatique par transaction) ou `liip/test-fixtures-bundle` : choisir un mécanisme unique pour tout le projet.
- Attention aux différences de plateforme dans les tests SQL natifs (voir `zai_database_oracle_pg`) : un test sur Oracle ne garantit pas PostgreSQL — si le projet supporte les deux, les suites critiques tournent sur les deux connexions.
- Séquences et identifiants : ne jamais supposer des valeurs d'id consécutives.

## Doubles de test (mocks)

- On ne moque **pas** ce qu'on possède (vos services) : on utilise les vrais en Kernel test. On moque uniquement les frontières (API tiers, mailer, paiement).
- PHPUnit `$this->createMock()` pour les interfaces ; préférer les fakes simples quand ils existent.
- Un test qui moque tout ne teste rien : si le mock reproduit le comportement attendu, le test est vide — le signaler.

## Côté JavaScript — Vitest

```js
// assets/js/__tests__/order-total.test.js
import { describe, it, expect, vi } from 'vitest';
import { computeTotal } from '../order-total';

describe('computeTotal', () => {
    it('applique la TVA et arrondit à 2 décimales', () => {
        expect(computeTotal([{ price: 10.005 }], { vat: 0.2 })).toBe(12.01);
    });
});
```
Modules purs testés sans DOM ; contrôleurs Stimulus testés avec un DOM minimal ; `vi.mock()` uniquement pour les frontières (`api.js`, horloge). Tests de composants selon le framework détecté (Vue Test Utils, React Testing Library).

## Couverture et CI

- Objectifs de couverture : logique métier critique proche de 100 %, moyenne projet 70-80 % — la couverture est un indicateur, pas un but ; un test trivial pour gonfler la couverture est une dette.
- CI obligatoire avant merge : `php vendor/bin/phpunit`, `npx vitest run`, lint (voir `zai_best_practices`), audits (`composer audit`, `npm audit`).
- Un test instable (flaky) est un bug de test : réparé ou supprimé, jamais ignoré.

## Gherkin pour les tests d'acceptation

Si le projet utilise Behat ou si le PO veut des scénarios lisibles : `Étant donné / Quand / Alors` (voir le template QA de `zai_doc_generator`). Les scénarios d'acceptation sont écrits avant l'implémentation, avec le PO.

## Avant de dire « testé »

1. Nominal + cas limites + erreurs utilisateur + erreurs système.
2. Sécurité : accès non autorisé à la ressource (403/404) testé.
3. Le test de non-régression du bug corrigé est en place.
4. Suite complète verte en local, pas seulement le nouveau test.
