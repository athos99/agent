# Contextes de cycle de vie

Les exigences doivent être adaptées au contexte, sans supprimer les exigences de sécurité essentielles.

| Contexte | Priorités | Vigilance |
|---|---|---|
| Nouveau projet | architecture, conventions, sécurité dès la conception, CI, tests | éviter le surdimensionnement |
| Maintenance | stabilité, compréhension de l'existant, régression | limiter le périmètre |
| Migration | compatibilité, inventaire, stratégie par étapes | rollback, données, dépendances |
| Refactoring majeur | objectifs mesurables, tests de caractérisation | éviter le changement fonctionnel involontaire |
| Incident/correctif urgent | diagnostic, réduction du risque, correction minimale | tests ciblés puis complément après incident |

Avant de coder, identifier le contexte. Si celui-ci est inconnu et change la stratégie, poser une question courte ou annoncer l'hypothèse retenue.
