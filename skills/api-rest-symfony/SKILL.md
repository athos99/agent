---
name: api-rest-symfony
description: Concevoir, documenter, sécuriser ou faire évoluer une API REST Symfony DTO, validation, statuts HTTP, pagination, erreurs, OpenAPI/Nelmio, droits par ressource et opérations CRUD.
---

# Mission
Concevoir des API REST robustes et cohérentes avec le projet.

## Contrat
- Définir ressources, routes, méthodes HTTP, statuts, DTO d'entrée/sortie et erreurs.
- Distinguer création, lecture, modification partielle/complète et suppression.
- Définir validation, pagination bornée, filtres et tri autorisés.
- Documenter avec NelmioApiDocBundle/OpenAPI uniquement si présent.
- Prévoir tests fonctionnels des statuts, validations et permissions.

## Sécurité
- Contrôle d'accès à chaque ressource et opération (Voters si utilisés).
- Prévenir IDOR/BOLA et mass assignment.
- Ne pas exposer directement les entités sans analyse des champs.
- Ne jamais faire confiance aux identifiants ou rôles envoyés par le frontend.

## Livrables
Avant de coder : proposer le contrat API et les impacts.
Après : lister fichiers modifiés, tests exécutés et points restant à valider.
