# Git, Merge Requests et CI/CD

- Travailler sur une branche adaptée et garder les changements ciblés.
- Produire des commits compréhensibles et une Merge Request décrivant contexte, solution, impacts et tests.
- Le pipeline doit exécuter progressivement les contrôles rapides puis les contrôles plus coûteux.
- Séparer les étapes de qualité, tests, sécurité, packaging et déploiement.
- Ne jamais exposer de secret dans les variables affichées, artefacts ou logs.
- Protéger les branches et environnements selon leur criticité.
- Rendre les déploiements traçables et, lorsque possible, reproductibles.
- Prévoir des mécanismes de retour arrière ou de correction pour les déploiements à risque.
- Ne pas déclarer une livraison prête si les contrôles requis sont en échec ou inconnus.

**Contrôles :** statut pipeline, artefacts, provenance des dépendances, secrets, approbations, traçabilité et stratégie de rollback.
