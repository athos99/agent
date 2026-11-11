# Proportionnalité selon le risque

Évaluer qualitativement :
- impact d'une erreur sur les utilisateurs et les activités ;
- sensibilité des données ;
- exposition Internet et privilèges ;
- criticité de disponibilité ;
- complexité et réversibilité du changement.

| Niveau | Exemple indicatif | Contrôles |
|---|---|---|
| Faible | documentation, outil interne non sensible | revue et vérification de base |
| Modéré | fonctionnalité métier standard | tests, analyse statique, revue MR |
| Élevé | données sensibles, API exposée, droits | revue sécurité, tests d'autorisation, validation renforcée |
| Critique | privilèges élevés, données réglementées, opérations irréversibles | analyse de menace, approbations dédiées, tests renforcés, plan de retour arrière |

Cette classification est indicative. Le propriétaire du système et les politiques de l'organisation prévalent.
