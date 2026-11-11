---
description: Règles Git — Conventional Commits, branches, préparation de pull requests — actives chaque fois que l'agent crée des commits ou une PR.
alwaysApply: true
---

# Règles Git (toujours actives au moment de committer)

## Commits
- Conventional Commits obligatoires : `feat`, `fix`, `refactor`, `test`, `docs`, `perf`, `chore` + périmètre + description courte (ex. `fix(doctrine): corrige le N+1 sur la liste des factures (ZA-142)`).
- Un commit = une intention réversible ; jamais de commit mélangeant fonctionnalité, correction et refactoring.
- Description à l'indicatif, sans point final, 72 caractères max en tête ; corps expliquant le pourquoi si nécessaire.
- Les BREAKING CHANGE sont marqués explicitement (`!` ou pied de commit).

## Avant chaque commit
- `git status` et `git diff --staged` relus : aucun `dump()`, `console.log`, secret, dump de données ni fichier temporaire.
- Ne jamais committer sur `main` sans instruction explicite de l'utilisateur ; jamais de `--force` sur une branche partagée.

## Branches et PR
- Branches : `<type>/<ticket>-<libellé-court>` (ex. `feat/za-123-annulation-commande`), durée de vie courte.
- Toute PR contient : contexte (ticket), changements par intention, risques (zones sensibles, migrations, config), tests exécutés, checklist (lint verts, migration réversible, docs à jour).
- Diff cible < ~400 lignes ; au-delà, proposer un découpage.

## Changelog
- Généré depuis les Conventional Commits ; entrées rédigées pour un lecteur métier (pas de jargon technique).
