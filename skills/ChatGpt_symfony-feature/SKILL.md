---
name: ChatGpt_symfony-feature
description: À utiliser pour concevoir ou implémenter une fonctionnalité dans une application Symfony existante, en respectant son architecture, ses conventions, Doctrine, les DTO, la validation, les Voters et les tests.
---

# Développement d'une fonctionnalité Symfony

## Procédure
1. Analyser le projet et ses versions réelles avant de proposer du code.
2. Clarifier le besoin, les acteurs, les droits, les données et les critères d'acceptation.
3. Identifier les couches et composants existants à réutiliser.
4. Proposer une conception concise : route/contrôleur, DTO, validation, service métier, repository, Voter et réponse.
5. Implémenter par étapes limitées, sans réécriture hors périmètre.
6. Ajouter les tests unitaires et fonctionnels pertinents, y compris les refus d'accès.
7. Mettre à jour la documentation API ou technique si nécessaire.
8. Exécuter les contrôles disponibles et rapporter les résultats réels.

## Contraintes
- Respecter le style Symfony du projet.
- Ne pas introduire API Platform si le projet utilise Symfony classique.
- Ne pas mettre la logique métier dans le contrôleur.
- Vérifier les autorisations côté serveur pour chaque opération.
- Pour Doctrine/Oracle, respecter les connexions et EntityManagers configurés.
- Ne pas inventer des classes, services ou méthodes sans vérifier leur existence ou proposer explicitement leur création.

## Livrable
Résumé de conception, fichiers créés/modifiés, explication des choix, tests ajoutés/exécutés, risques et points restant à valider.
