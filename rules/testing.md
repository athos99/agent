# Stratégie de tests

- Choisir le niveau de test en fonction du risque et du comportement à vérifier.
- Tester les comportements observables, pas uniquement les détails d'implémentation.
- Couvrir les cas nominaux, limites, erreurs, autorisations et régressions pertinentes.
- Préférer des tests déterministes, isolés et répétables.
- Les mocks ne doivent pas remplacer systématiquement les composants réels lorsque leur intégration constitue précisément le risque.
- Pour les bases de données, distinguer tests unitaires avec doubles, tests d'intégration sur le moteur réel et tests de contrat.
- Ne pas prétendre qu'une base Oracle est simulée fidèlement par SQLite ou un mock.
- Mesurer la couverture, mais ne pas confondre pourcentage de couverture et qualité des tests.
- Ne jamais supprimer ou assouplir un test uniquement pour faire passer le pipeline sans comprendre la cause.

**Sortie attendue :** tests ajoutés/modifiés, commande exacte, résultat réel, limites et éventuels tests non exécutés.
