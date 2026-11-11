# Template — Documentation de test (rôle : QA / testeur)

En-tête obligatoire : date, périmètre, version/branche source, auteur (agent Z.ai).
Niveau EXPRESS : 1, 2, 4. Niveau COMPLET : tout.

# <Module / fonctionnalité> — Dossier de test

## 1. Périmètre testé
Ce qui est couvert par ce dossier, et explicitement ce qui en est exclu (modules connexes, intégrations hors périmètre). Un périmètre sans exclusions est incomplet.

## 2. Matrice de couverture
| Module / capacité | Tests unitaires | Tests intégration (Kernel) | Tests E2E | Statut |
|---|---|---|---|---|
| Création commande | Oui (12) | Oui (3) | Non | Partiel |

Sources : dossiers `tests/`, fichiers `*.test.js`, rapport de couverture s'il existe.

## 3. Environnements et données
Environnements cibles, base de données utilisée pour les tests (particularités Oracle/PostgreSQL : séquences, schémas, privilèges du compte de test), fixtures utilisées (zenstruck/foundry, fichiers de données), données personnelles fictives obligatoires (jamais de données réelles dans les tests).

## 4. Scénarios de test clés
Format Gherkin, ordonnés par risque. Couvrir au minimum : nominal, cas limites, erreurs utilisateur, erreurs système, sécurité (accès non autorisé), volumétrie si pertinent.

```
Scénario : refus d'annulation après expédition
Étant donné une commande au statut « expédiée »
Quand le client demande l'annulation
Alors l'API répond 409 avec un message métier explicite
Et la commande reste au statut « expédiée »
```

## 5. Jeux de données de référence
Tableaux de données d'entrée attendues/sorties attendues pour les cas non triviaux. Ces jeux doivent être réutilisables directement dans des tests automatisés (data providers PHPUnit, tests Vitest paramétrés).

## 6. Risques non couverts
Ce que les tests actuels ne protègent pas et pourquoi (outillage, temps, accès manquant). Prioriser : quels 3 tests ajoutés réduiraient le plus de risque ? Cette section alimente directement le backlog qualité.

## 7. Commandes d'exécution
```bash
php bin/phpunit --testsuite unit
php bin/phpunit --testsuite functional
npx vitest run --coverage
```
Adapter aux outils réellement présents dans le projet ; si l'outillage est absent, le signaler comme risque.
