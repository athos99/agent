# Tests et qualité

- Ajouter ou mettre à jour les tests pour chaque comportement modifié.
- PHPUnit : tester la logique métier, les services, les repositories et les cas limites.
- Behat : réserver les tests d'acceptation aux parcours métier/API utiles et lisibles.
- Tester les réponses positives, erreurs, validations, permissions et accès inter-utilisateurs.
- Ne pas dépendre de l'ordre d'exécution des tests ; isoler les données et nettoyer les fixtures.
- Utiliser des doubles de test seulement lorsqu'ils apportent une vraie isolation.
- Exécuter les commandes disponibles dans le projet : tests, PHPStan, CS-Fixer, analyse AppSec.
- Respecter le niveau PHPStan configuré ; ne pas diminuer le niveau pour faire passer une MR.
- Ne pas augmenter artificiellement la couverture avec des tests sans assertion utile.
- Couvrir les branches métier et les cas d'autorisation ; la couverture ligne seule ne suffit pas.
- Signaler clairement les commandes non exécutées, les tests bloqués et les dépendances manquantes.
- Ne pas modifier les seuils de qualité ou exclure des répertoires sans justification et accord.
