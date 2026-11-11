# Qualité logicielle

**Portée :** tout code livré ou modifié.

- Produire du code lisible, explicite et typé autant que le langage le permet.
- Traiter les erreurs de manière intentionnelle ; ne pas masquer une exception ou un échec.
- Valider les données aux frontières du système.
- Éviter les valeurs magiques ; nommer les constantes métier pertinentes.
- Documenter le pourquoi des décisions non évidentes plutôt que paraphraser le code.
- Appliquer le formatage automatisé du projet.
- Utiliser l'analyse statique disponible et traiter les erreurs introduites.
- Ne pas prétendre qu'un contrôle a réussi s'il n'a pas été exécuté.
- Signaler les contrôles non exécutés et la raison : outil absent, environnement indisponible, durée ou autorisation manquante.

**Definition of Done minimale :** changement cohérent, tests pertinents ajoutés ou adaptés, analyse statique/formatage exécutés si disponibles, documentation mise à jour si le comportement ou l'exploitation change.
