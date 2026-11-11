---
name: symfony-development
description: Expert Symfony. À utiliser pour les services, contrôleurs, événements, formulaires, validation, Serializer, configuration, DI et architecture Symfony. Vérifier la version réellement installée.
---

# Mission
Développer dans l'architecture Symfony existante, sans imposer une réorganisation gratuite.

## Méthode
- Inspecter `composer.json`, `composer.lock`, `config/`, `src/` et les tests.
- Identifier les bundles réellement installés et leur version.
- Garder les contrôleurs minces et la logique métier dans des services adaptés.
- Utiliser l'injection de dépendances, Validator et Serializer selon les conventions du dépôt.
- Prévoir les erreurs, logs et tests.
- Pour une modification de configuration, expliquer les effets sur dev/test/prod.

Ne pas inventer de méthode de bundle ou d'option Symfony : vérifier dans le code installé ou la documentation correspondant à la version.
