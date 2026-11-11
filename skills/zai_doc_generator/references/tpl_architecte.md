# Template — Documentation d'architecture (rôle : architecte)

En-tête obligatoire : date, périmètre, version/branche source, auteur (agent Z.ai).
Tout diagramme en Mermaid (éditable en texte). Niveau EXPRESS : 1, 2, 4, 6. Niveau COMPLET : tout.

# <Application / module> — Architecture

## 1. Contexte
Pourquoi ce système existe, quels problèmes il résout, contraintes fortes (réglementaires, historiques, équipes, Oracle ET PostgreSQL, etc.). 5 à 10 lignes maximum.

## 2. Vue conteneurs (C4 niveau 2)
Mermaid `graph` : application Symfony, front JavaScript, base(s) de données, services tiers, workers Messenger, cache. Flèches annotées avec le protocole (HTTP, SQL, AMQP).

## 3. Vue composants (C4 niveau 3, si périmètre module)
Couches applicatives : présentation (contrôleurs), application (services, handlers), domaine (entités, règles), persistance (repositories). Dépendances autorisées entre couches — et violations constatées.

## 4. Décisions d'architecture (ADR)
| ID | Décision | Statut | Justification | Conséquences |
|---|---|---|---|---|
| ADR-001 | Séparation API REST en JSON sur /api/v1 | Acceptée | Front SPA + intégrations futures | Versionnement, docs OpenAPI |

Extraire les décisions visibles du code (choix de patterns, bundles structurants, dualité de base de données). Les décisions non documentées mais visibles sont marquées « déduite du code ».

## 5. Modèle de données
Diagramme ERD Mermaid (`erDiagram`) des entités principales avec cardinalités. Répartition Oracle / PostgreSQL si les deux sont utilisés : quel domaine sur quelle base et pourquoi. Stratégies de génération d'identifiants par plateforme.

## 6. Flux clés
Un diagramme de séquence par flux critique (max 3) : requête HTTP, appels services, transactions, écritures BD, événements, réponses. Annoter les points de défaillance possibles.

## 7. Sécurité (vue architecte)
Frontières de confiance, authentification (mécanisme réel constaté dans `config/packages/security.yaml`), autorisation (rôles + voters), secrets, chiffrement, exposition réseau. Ne pas descendre au niveau des détails d'implémentation (c'est la doc développeur).

## 8. Scalabilité et performance
Points d'extension possibles (workers asynchrones, cache, réplication lecture) et goulets identifiés (requêtes lourdes, traitements synchrones volumineux, Oracle/PG spécificités). Chiffrer quand possible avec des mesures constatées.

## 9. Risques techniques et dette
| Risque / dette | Impact | Probabilité | Traitement recommandé |
|---|---|---|---|

Honnêteté obligatoire : c'est la section qui justifie les investissements futurs.

## 10. Évolutions envisagées
Trajectoire réaliste à 6-12 mois, cohérente avec les risques ci-dessus. Chaque évolution cite l'ADR ou le risque qui la motive.
