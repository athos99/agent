---
name: zai_code_review
description: Revue de code structurée pour PHP/Symfony/Doctrine/JavaScript — lecture contextualisée du diff, checklist par dimension (fonctionnel, sécurité, performance, lisibilité, tests), commentaires constructifs avec sévérités et verdict final. Utiliser dès que l'utilisateur demande de relire, de valider, de critiquer du code ou une pull request, ou « qu'en penses-tu » sur un bout de code.
---

# Revue de code

Objectif : protéger le projet (bugs, failles, dette) et faire progresser l'auteur, dans cet ordre. Une revue qui n'est pas relue contre le code réel est une fiction : toujours ouvrir les fichiers.

## Processus

1. **Comprendre avant de juger.** Lire la description du changement et son ticket. Sans contexte, le demander : une revue sans intention connue produit des remarques hors sujet.
2. **Lire le diff, puis le contexte.** Le diff montre ce qui change ; les fichiers appelants et appelés montrent ce que ça casse. Vérifier au moins les usages de chaque signature modifiée.
3. **Passer la checklist dimension par dimension** (ci-dessous), du plus risqué au plus cosmétique : sécurité, fonctionnel, données, performance, tests, lisibilité.
4. **Vérifier les tests** : présents, pertinents, verts. Un correctif sans test de non-régression est un constat bloquant.
5. **Formuler** en commentaires typés puis produire le verdict.

## Checklist par dimension

- **Sécurité** : entrées validées, autorisation vérifiée côté serveur (Voter/`isGranted`), paramètres SQL liés, secrets hors code, échappement (`|raw` justifié, `innerHTML` absent) — voir `zai_security`.
- **Fonctionnel** : le changement fait ce que le ticket demande ; cas limites traités (vide, nul, volumétrie, concurrence) ; erreurs utilisateurs compréhensibles.
- **Données** : migration présente et réversible ; requêtes paginées ; transactions sur écritures multiples ; comportement cohérent Oracle/PostgreSQL (`''` vs NULL).
- **Performance** : pas de N+1, pas de requête en boucle, asynchrone si au-delà d'une seconde, cache justifié.
- **Tests** : unitaires/Kernel/fonctionnels au bon niveau, AAA, assertions sur le comportement (pas sur l'implémentation).
- **Lisibilité** : nommage, taille des méthodes, couche respectée (pas de logique métier dans le contrôleur), PHPDoc/JSDoc utiles — voir `zai_code_comments`.
- **Cohérence projet** : patterns existants réutilisés plutôt que doublés.

## Format des commentaires de revue

`[SÉVÉRITÉ] fichier:ligne — constat — pourquoi c'est un problème — proposition concrète`

| Balise | Signification | Bloque le merge |
|---|---|---|
| `[BLOQUANT]` | Bug, faille, perte de données, non-conformité sécurité | Oui |
| `[MAJEUR]` | Dette immédiate, performance, test manquant | Oui, sauf arbitrage explicite |
| `[SUGGESTION]` | Meilleure approche possible, lisible | Non |
| `[NIT]` | Style, micro-nommage | Non, jamais discuté longuement |

Ton : factuel, adressé au code, jamais à la personne. Une remarque positive par revue au minimum (ce qui est bien fait se dit aussi, sinon les bons réflexes disparaissent). Une question honnête (« Pourquoi ce retry ici ? ») vaut mieux qu'un verdict abrupt.

## Verdict — modèle de rapport final

```
# Revue — <titre du changement>
**Verdict** : Approuvé / Approuvé avec remarques / À reprendre
## Synthèse (3 lignes : ce que fait le changement, risque global)
## Commentaires ([BLOQUANT] → [NIT], ordonnés par fichier)
## Points positifs
## Tests constatés (présents/absents, pertinence)
```

## Règles de conduite

- Ne jamais modifier le code revué : la revue propose, l'auteur applique (sauf demande explicite de correctif direct).
- Distinguer « doit changer avant merge » de « pourrait changer un jour » : tout mettre au même niveau noie les BLOQUANTS.
- Si le changement est trop gros pour être relu sérieusement (plus de ~400 lignes), le dire et demander un découpage — c'est une réponse de revue légitime.
- Relire sa propre proposition de correctif suggéré : une suggestion fausse coûte plus de crédibilité qu'un silence.
