---
name: zai_doc_generator
description: Génère de la documentation adaptée au rôle et au niveau demandé — développeur, product owner, architecte, QA/ops — à partir du code réel d'un projet Symfony/Doctrine/JavaScript. Utiliser dès que l'utilisateur demande une documentation, un dossier technique, une synthèse fonctionnelle, une doc d'architecture, un document d'onboarding ou une explication « pour quelqu'un d'autre que moi », même sans préciser le rôle (le déduire de la demande).
---

# Génération de documentation par rôle et par niveau

Objectif : transformer le code réel en documents lisibles par la personne visée. La documentation décrit **ce qui existe**, jamais ce qu'on aimerait qu'il existe : toute divergence entre code et document est une erreur, pas une simplification.

## Paramètres à établir avant de générer

1. **Rôle destinataire** : développeur / product owner / architecte / QA. Si non précisé, le déduire : « expliquer comment ça marche » = développeur ; « ce que fait le produit » = product owner ; « la structure et les choix techniques » = architecte. En cas de doute, demander une seule question.
2. **Niveau de détail** :
   - `EXPRESS` — 1 page, l'essentiel pour agir (réunion, revue rapide).
   - `STANDARD` — le modèle complet du rôle (défaut).
   - `COMPLET` — modèle complet + annexes (métriques, diagrammes supplémentaires, historique).
3. **Périmètre** : projet entier, module, fonctionnalité, endpoint.

## Workflow

1. **Explorer le code avant d'écrire un mot** : routes (`bin/console debug:router` ou `config/routes.yaml`), entités, services, contrôleurs, templates, JS, configuration Doctrine, migrations. La source de vérité est le code, pas les vieux documents.
2. **Choisir le template du rôle** dans `references/` :
   - `references/tpl_developpeur.md` — doc technique (architecture fichier, entités, endpoints, flux, configuration, tests).
   - `references/tpl_product_owner.md` — doc fonctionnelle sans jargon (capacités, règles de gestion, parcours).
   - `references/tpl_architecte.md` — doc d'architecture (C4, diagrammes Mermaid, ADR, risques).
   - `references/tpl_qa.md` — doc de test (périmètre, matrice de couverture, scénarios, jeux de données).
3. **Extraire les faits** : noms réels des classes, routes réelles, valeurs de configuration réelles. Citer le fichier d'origine pour chaque affirmation technique.
4. **Rédiger dans le ton du rôle** (voir ci-dessous).
5. **Contrôler la cohérence** : chaque nom, chaque route, chaque champ cité dans le document doit exister dans le code. Vérifier par une recherche ; corriger le document, jamais inventer.

## Tonalité par rôle

| Rôle | Ton | Vocabulaire | Interdits |
|---|---|---|---|
| Développeur | Précis, orienté code | Classes, méthodes, routes réelles | Phrases vagues sans référence fichier |
| Product owner | Simple, orienté valeur | Utilisateurs, capacités, règles de gestion | Jargon technique (DTO, EntityManager, API REST) |
| Architecte | Structuré, justifié | Décisions, compromis, diagrammes | Détails d'implémentation mineurs |
| QA | Factuel, exhaustif | Périmètres, scénarios, jeux de données | « Ça marche » sans critère vérifiable |

## Règles de conduite

- Diagrammes en **Mermaid** uniquement (flowchart, sequence, erDiagram, C4 avec graph) pour rester éditables en texte.
- Un document sans date, sans périmètre et sans auteur est incomplet : ajouter un en-tête (date, périmètre, version, source = commit ou branche).
- Les limites connues et les zones fragiles font partie de la documentation : les dire franchement, c'est leur valeur principale.
- Si une information manque (règle de gestion non écrite, choix non documenté), la signaler dans le document comme « à confirmer » plutôt que d'inventer.
- Pour les product owners : chaque capacité doit se formuler « l'utilisateur peut... » ; chaque règle de gestion est numérotée (RG-001) pour être traçable vers les tests.
- Lister en fin de document les questions ouvertes à arbitrer avec le destinataire.
