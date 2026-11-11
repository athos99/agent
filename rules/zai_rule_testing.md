---
description: Règles de tests pour tout fichier de test PHP (PHPUnit) et JavaScript (Vitest/Jest) de ce projet.
globs: tests/**/*.php,**/__tests__/**/*.js,**/*.test.js,**/*.spec.js
alwaysApply: false
---

# Règles de tests

## Exigences de couverture par le changement
- Tout bug corrigé apporte son test de non-régression (échouait avant, passe après).
- Toute nouvelle fonctionnalité apporte ses tests : nominal, cas limites, erreurs utilisateur, erreurs système.
- Tout endpoint expose au moins un test fonctionnel sur son contrat (code retour + corps, y compris erreur 422).

## Écriture
- Pattern AAA (Arrange / Act / Assert) ; une intention par test ; le nom dit le comportement : `test_annulation_rejetee_quand_commande_expediee`.
- Assertions sur le comportement observable, pas sur l'implémentation interne.
- Fixtures : factories (zenstruck/foundry) par test, monde minimal, données 100 % fictives — jamais de données réelles.
- Doubles : ne moquer que les frontières (API tiers, mailer, paiement) ; ne pas moquer ses propres services (utiliser les vrais en Kernel test).

## Environnement
- Base de test dédiée avec rollback automatique (dama/doctrine-test-bundle ou équivalent unique pour le projet).
- Suites critiques exécutées sur les deux connexions si le projet supporte Oracle ET PostgreSQL.
- Aucun test instable toléré : réparé ou supprimé, jamais ignoré.

## Avant merge
`phpunit`, `vitest run`, lint et audits passent en CI ; la suite complète est verte, pas seulement le test ajouté.
