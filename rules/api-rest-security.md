# API REST et sécurité des ressources

- Respecter les conventions de routes, méthodes HTTP, statuts et formats déjà adoptées.
- Utiliser GET pour lire, POST pour créer, PUT/PATCH pour modifier, DELETE pour supprimer,
  en respectant l'idempotence et la sémantique HTTP.
- Utiliser des DTO d'entrée/sortie pour maîtriser les données acceptées et exposées.
- Valider les entrées côté serveur ; ne jamais faire confiance au frontend.
- Contrôler l'autorisation sur chaque ressource et chaque opération, pas seulement à l'accès
  à l'endpoint. Utiliser les Voters si c'est le mécanisme du projet.
- Vérifier les risques IDOR/BOLA : un identifiant connu ne donne aucun droit d'accès.
- Appliquer le moindre privilège et refuser par défaut en cas d'autorisation indéterminée.
- Ne pas exposer les entités Doctrine directement si cela révèle des champs internes,
  relations ou données non destinées au consommateur.
- Documenter les endpoints, paramètres, réponses, erreurs et mécanismes de sécurité
  avec OpenAPI/NelmioApiDocBundle si installé.
- Prévoir pagination, tri et filtres bornés ; imposer une taille maximale de page.
- Ne pas révéler de stack trace, SQL, chemin serveur ou détail interne dans les erreurs.
- Pour les API keys : stockage sous forme non réversible si applicable, rotation, révocation,
  comparaison sûre et transmission exclusivement via TLS ; ne jamais les journaliser.
- Pour SAML2/OIDC : s'appuyer sur le fournisseur et les bibliothèques validés par le projet.
  Vérifier issuer, audience, signature, expiration, nonce/state et redirect URI selon le flux.
  Ne pas coder soi-même la validation cryptographique des assertions/tokens.
- Protéger les opérations sensibles contre CSRF lorsque le mode d'authentification l'exige.
- Limiter les uploads : taille, type réel, extension, nom généré, stockage non exécutable,
  contrôle d'accès et analyse antivirus si disponible.
