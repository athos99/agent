# Règles Symfony

**Profil initial :** Symfony 8.x ; vérifier la version réellement installée dans `composer.lock`.

- Respecter les conventions Symfony et l'architecture déjà en place.
- Utiliser l'injection de dépendances et l'autowiring plutôt que récupérer le conteneur dans le code métier.
- Garder les contrôleurs minces ; déléguer les traitements métier à des services.
- Utiliser Validator pour la validation des données et Serializer/DTO selon les besoins du contrat.
- Utiliser les Voters pour les autorisations métier fines ; vérifier l'autorisation côté serveur sur chaque opération concernée.
- Ne pas confondre authentification et autorisation. Un utilisateur anonyme peut être soumis à une décision de Voter si le modèle de sécurité le prévoit.
- Ne pas ajouter API Platform si le projet demande explicitement une API Symfony classique.
- Documenter les endpoints avec l'outil adopté par le projet, par exemple NelmioApiDocBundle.
- Utiliser les composants Symfony adaptés : HttpFoundation, HttpKernel, Console, Messenger, Cache, EventDispatcher, selon le besoin.
- Respecter les conventions de configuration par environnement et ne pas committer les secrets.

**Contrôles :** services, contrôleurs, DTO, validation, Voters, configuration, logs, tests Kernel/HTTP et documentation API.
