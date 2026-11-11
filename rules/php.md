# Règles PHP

**Profil initial :** PHP 8.4+ ; adapter la version à `composer.json`, à l'environnement d'exécution et aux contraintes de déploiement.

- Déclarer les types des paramètres, retours et propriétés lorsque pertinent.
- Utiliser `declare(strict_types=1);` selon la convention du projet.
- Respecter PSR-4 et les standards PSR pertinents.
- Préférer les fonctionnalités natives du langage aux abstractions artisanales.
- Éviter les tableaux associatifs non documentés lorsqu'un DTO ou un objet typé clarifie le contrat.
- Gérer explicitement les exceptions ; ne pas intercepter `Throwable` sans stratégie claire.
- Respecter Composer et le verrouillage des dépendances (`composer.lock`).
- Appliquer PHP-CS-Fixer ou l'outil déjà retenu par le dépôt.
- Configurer PHPStan progressivement selon la maturité du projet ; ne pas masquer les erreurs avec des exclusions globales.
- Éviter les fonctions globales et l'état mutable partagé lorsqu'une conception orientée objet est déjà utilisée.

**Contrôles :** version PHP effective, Composer, PSR-4, typage, analyse statique, formatage et tests.
