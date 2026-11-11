---
name: ChatGpt_project-analysis
description: À utiliser lorsqu'il faut comprendre un dépôt ou une application existante avant de coder, documenter, migrer, auditer ou refactorer. Produit un état des lieux factuel et identifie les risques et conventions du projet.
---

# Analyse d'un projet existant

## Objectif
Construire une compréhension vérifiable du projet avant toute modification.

## Procédure
1. Identifier le langage, les frameworks, les versions et les gestionnaires de dépendances à partir des fichiers présents.
2. Examiner l'arborescence et repérer les points d'entrée, la configuration, les tests, les pipelines et la documentation.
3. Décrire l'architecture observée : couches, modules, flux principaux, accès aux données et intégrations.
4. Repérer les conventions réellement utilisées ; distinguer celles qui sont documentées de celles déduites du code.
5. Identifier les commandes de test, analyse statique, formatage et build.
6. Relever les risques : secrets exposés, dépendances obsolètes visibles, contrôles d'accès, dette technique, tests absents.
7. Ne pas lancer de commande destructive ni modifier de fichier pendant la phase d'analyse.

## Format de sortie
- Résumé du projet et versions vérifiées.
- Architecture et flux principaux.
- Conventions existantes.
- Commandes de vérification disponibles.
- Risques classés par impact et confiance.
- Questions ouvertes et recommandations priorisées.

## Règles
Ne jamais inventer une version ou une architecture. Pour chaque constat important, indiquer le fichier ou l'élément qui le justifie. Signaler explicitement ce qui n'a pas pu être inspecté.
