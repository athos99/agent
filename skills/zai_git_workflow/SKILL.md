---
name: zai_git_workflow
description: Discipline Git pour les projets Symfony/JavaScript — Conventional Commits, nommage de branches, préparation de pull requests, changelog, hooks de pré-commit. Utiliser dès qu'il faut créer des commits, préparer une PR/MR, écrire un message de commit ou organiser une livraison, et systématiquement avant de committer du travail d'agent.
---

# Git — commits, branches, pull requests

## Conventional Commits (obligatoire)

```
<type>(<périmètre>): <description courte à l'indicatif>

[corps optionnel : pourquoi, pas comment]

[BREAKING CHANGE: description] ou ! après le type
```

| Type | Usage |
|---|---|
| `feat` | Nouvelle fonctionnalité (visible utilisateur ou API) |
| `fix` | Correction de bug |
| `refactor` | Restructuration sans changement de comportement |
| `test` | Ajout ou correction de tests |
| `docs` | Documentation |
| `perf` | Optimisation mesurée |
| `chore` | Outiling, dépendances, CI |

Exemples :
```
feat(orders): ajoute l'annulation d'une commande non expédiée
fix(doctrine): corrige le N+1 sur la liste des factures (ZA-142)
refactor(services)!: renomme OrderService en OrderProcessor

BREAKING CHANGE: le service OrderService n'existe plus, utiliser OrderProcessor
```
Description en français dans ce projet (à ajuster si l'équipe exige l'anglais), à l'indicatif, sans point final, 72 caractères max sur la première ligne.

## Commits atomiques

Un commit = une intention réversible. Jamais « feat + fix + refactor » dans le même commit. Fichiers de debug (`dump()`, `console.log`) exclus : relecture systématique de `git diff --staged` avant commit. Les secrets (`.env.local`, clés, dumps) ne sont jamais commités — même « temporairement ».

## Branches

```
feat/za-123-annulation-commande
fix/za-142-n-plus-un-factures
chore/phpstan-niveau-6
```
Préfixe du type + ticket + libellé court. Durée de vie courte (quelques jours) : intégrer souvent, rebase/régulariser plutôt que laisser dériver. Selon l'organisation du projet : trunk-based (recommandé avec feature flags) ou flux par branches de release — suivre la convention installée, ne pas en inventer une troisième.

## Pull request / merge request

Chaque PR contient, dans la description :

```
## Contexte et objectif (ticket ZA-xxx)
## Changements (liste courte, par intention)
## Risques et points d'attention (zones sensibles touchées, migrations, config)
## Tests (nouveaux tests, suites exécutées, résultats)
## Vérifications
- [ ] lint et analyse statique verts
- [ ] migration réversible
- [ ] docs à jour (README, OpenAPI, doc de rôle si impactée)
- [ ] aucun secret ni dump dans les fichiers modifiés
```

Taille cible : moins de ~400 lignes de diff — au-delà, découper ; une grosse PR est plus lente à relire et donc moins relue.

## Hooks et garde-fous

Pré-commit recommandé : `php-cs-fixer`, `eslint --fix`, `prettier`. Pre-push : suite de tests rapide. Les hooks locaux sont un confort, la CI reste la vérité : ne jamais compter sur les hooks pour bloquer une erreur de sécurité.

## Changelog

Généré depuis les Conventional Commits (Keep a Changelog / git-cliff ou équivalent selon le projet) : `feat` → Added, `fix` → Fixed, `perf` → Performance, les `BREAKING CHANGE` en tête de version. Les entrées sont rédigées pour un lecteur métier, pas pour les développeurs.

## Règles de conduite pour l'agent

1. Toujours vérifier la branche courante et `git status` avant tout commit — jamais de commit sur `main` sans instruction explicite de l'utilisateur.
2. Un commit d'agent cite la provenance : corps contenant « Généré avec l'aide de Z.ai » quand la politique du projet l'accepte.
3. Ne jamais pousser avec `--force` sur une branche partagée ; `--force-with-lease` uniquement sur sa propre branche, après vérification.
4. En cas de conflit : rebaser en relisant chaque résolution — une résolution de conflit aveugle est un bug silencieux.
