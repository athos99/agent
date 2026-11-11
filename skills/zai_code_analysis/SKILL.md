---
name: zai_code_analysis
description: Analyse et audit de code PHP/Symfony/Doctrine/JavaScript — cartographie d'architecture, complexité, couplage, code mort, dette technique, qualité et risques. Utiliser dès que l'utilisateur demande d'analyser, d'auditer, d'évaluer, de cartographier ou de comprendre un projet, un module ou des fichiers, même s'il ne dit pas explicitement « analyse » (par exemple « où est géré X », « comment ça marche », « qu'est-ce qui rame »).
---

# Analyse de code Symfony / Doctrine / JavaScript

Objectif : produire un diagnostic factuel, priorisé et actionnable d'un code existant, **sans le modifier**. Chaque constat doit pouvoir être vérifié par une personne qui ouvre le fichier cité.

## Méthode en 5 étapes

1. **Cartographier.** Lire `composer.json`, `package.json`, `config/packages/`, l'arborescence `src/` et `assets/`. Identifier : versions (Symfony, PHP, Node, DBAL), bundles installés, structure (Controller / Service / Repository / Entity), points d'entrée (routes, commandes console, handlers Messenger), base(s) de données (Oracle, PostgreSQL ou les deux).
2. **Mesurer.** Taille des classes et méthodes, nombre de dépendances injectées (plus de 5 est un signal), profondeur d'imbrication, duplication visible, couplage direct contrôleur-repository, requêtes ou logique métier dans les templates et le JS.
3. **Détecter** les odeurs spécifiques à ce stack :
   - PHP : `dump()`/`var_dump()` oubliés, `catch` vides, `@TODO`/`@FIXME`, logique métier dans les contrôleurs, accès direct à `EntityManager` hors services, DQL construit par concaténation, classes non `final` par défaut, `strict_types` absent.
   - Doctrine : `findAll()` non paginé sur des tables volumineuses, requêtes dans des boucles (N+1), écritures multiples sans transaction, migrations divergentes du schéma réel, entités sans repository dédié.
   - JS : promesses non catchées, `innerHTML` sur des données non sûres, variables globales, `console.log`, fonctions de plus de 50 lignes, duplication entre contrôleurs front.
4. **Vérifier** avec les outils, s'ils sont installés — sinon c'est déjà un constat de qualité à signaler :
   ```bash
   php vendor/bin/phpstan analyse -l 5 src
   php vendor/bin/php-cs-fixer fix --dry-run --diff
   php bin/console lint:container lint:twig lint:yaml
   php bin/console doctrine:schema:validate --skip-sync
   npx eslint assets --max-warnings 0
   ```
5. **Prioriser.** Classer chaque constat par sévérité (tableau ci-dessous), puis proposer un plan en 3 vagues.

## Sévérités

| Niveau | Critère | Exemple |
|---|---|---|
| Critique | Faille de sécurité, risque de perte de données, bug certain | SQL concaténé, `isGranted` absent sur une action admin |
| Majeur | Bug probable, performance dégradée, N+1 | `findAll()` en boucle, transaction absente |
| Modéré | Maintenabilité, lisibilité, duplication | Contrôleur de 400 lignes, DTO absent |
| Mineur | Style, nommage, commentaires | Convention PSR-12 non respectée |

## Rapport attendu — modèle imposé

```
# Analyse de <périmètre>
## 1. Synthèse exécutive (10 lignes max, chiffres clés)
## 2. Cartographie (arborescence annotée, tableau module | rôle | taille)
## 3. Constats par sévérité
   Pour chaque constat : [SÉVÉRITÉ] fichier:ligne — preuve (extrait court) — impact — correction recommandée
## 4. Métriques (nb classes, services, entités, routes, tests, ratio tests/code)
## 5. Plan d'action
   Vague 1 : quick wins (< 1 jour chacun)
   Vague 2 : moyen terme (refactorings structurants)
   Vague 3 : refondations éventuelles
```

## Règles de conduite

- Ne jamais modifier le code pendant une analyse pure ; produire le rapport seulement.
- Distinguer explicitement les **faits mesurés** des **hypothèses** (marquer « à vérifier »). Ne jamais présenter une supposition comme un constat.
- Un constat sans fichier ni ligne citée n'est pas un constat : c'est une opinion — l'éliminer ou le vérifier.
- Si le périmètre est trop large pour être analysé en profondeur (plus de quelques dizaines de fichiers), analyser en profondeur les zones à risque (sécurité, données, performance) et cartographier le reste de façon synthétique.
- Terminer par la question suivante utile : quel périmètre approfondir, quel plan d'action valider.
