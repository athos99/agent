# Sécurité applicative

**Portée :** toutes les applications, API, traitements et pipelines.

- Appliquer le principe du moindre privilège.
- Vérifier l'autorisation côté serveur pour chaque opération sensible ; masquer un bouton côté client n'est jamais un contrôle d'accès.
- Valider les entrées et encoder les sorties selon leur contexte.
- Utiliser des requêtes paramétrées et les mécanismes sûrs du framework.
- Ne jamais journaliser de secrets, mots de passe, jetons, données personnelles sensibles ou contenus confidentiels non nécessaires.
- Ne jamais placer de secrets dans le dépôt, les exemples, les logs ou les prompts.
- Protéger les opérations contre CSRF, XSS, injection, SSRF, désérialisation dangereuse et contrôle d'accès défaillant selon le contexte.
- Vérifier les dépendances et leur provenance ; limiter les permissions des comptes techniques.
- Les erreurs destinées à l'utilisateur ne doivent pas exposer les détails internes.
- Les contrôles AppSec et les tests de sécurité complètent la revue humaine ; ils ne la remplacent pas.

**Contrôles :** authentification et autorisation, validation, secrets, dépendances, journaux, gestion d'erreurs, configuration et exposition réseau.
