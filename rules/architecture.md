# Architecture et conception

- Identifier les responsabilités, frontières métier et dépendances avant de modifier une architecture.
- Garder les contrôleurs, vues et composants d'interface minces ; placer les règles métier dans des composants adaptés.
- Respecter l'inversion des dépendances lorsque cela apporte une valeur réelle.
- Éviter les services « fourre-tout », les objets trop couplés et les abstractions sans cas d'usage concret.
- Décrire les décisions structurantes dans un ADR : contexte, décision, alternatives, conséquences.
- Distinguer les contraintes existantes des choix recommandés.
- Pour une migration ou un refactoring, définir les étapes, la stratégie de retour arrière et les critères de réussite.
- Ne pas introduire une architecture distribuée ou une complexité opérationnelle sans besoin démontré.

**Contrôles :** dépendances, responsabilités, testabilité, observabilité, évolutivité et coût opérationnel.
