---
description: Règles de documentation — actives en permanence pour que la doc suive le code — précise quoi documenter à chaque type de changement et comment produire une doc par rôle.
alwaysApply: true
---

# Règles de documentation (toujours actives)

## Ce qui doit être documenté à chaque changement
- Nouvelle méthode/classe publique non triviale : PHPDoc d'intention (voir skill zai_code_comments) ou JSDoc côté JS.
- Nouvel endpoint : entrée OpenAPI complète (paramètres, corps, codes de retour, erreurs) — une API sans doc OpenAPI est considérée non livrée.
- Nouvelle migration : commentaire d'en-tête (objet, ticket, impact données).
- Changement visible utilisateur : entrée de changelog rédigée pour un lecteur métier.
- README impacté (nouvelle commande, nouvelle variable d'environnement, nouvelle étape d'installation) : mis à jour dans le même changement.

## Qualité des documents produits
- La documentation décrit ce qui existe dans le code : chaque nom de classe, route ou champ cité doit exister — vérifier avant de livrer, ne jamais inventer.
- Tout document porte un en-tête : date, périmètre, version/branche source, auteur.
- Les limites connues et zones fragiles sont documentées franchement (section dédiée), c'est leur valeur principale.
- Diagrammes en Mermaid uniquement (éditables en texte).

## Documentation par rôle (sur demande)
- Utiliser la skill `zai_doc_generator` et ses templates par rôle : développeur, product owner (sans jargon, règles numérotées RG-xxx), architecte (C4, ADR, risques), QA (matrice de couverture, scénarios Gherkin).
- Niveaux disponibles : EXPRESS (1 page), STANDARD (modèle complet), COMPLET (modèle + annexes).
- Les informations manquantes sont marquées « à confirmer » et listées en fin de document — jamais remplacées par une invention.
