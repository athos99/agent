# Template — Documentation technique (rôle : développeur)

En-tête obligatoire : date, périmètre, version/branche source, auteur (agent Z.ai).
Niveau EXPRESS : garder sections 1, 2, 4. Niveau COMPLET : tout + annexes.

# <Nom du module / fonctionnalité>

## 1. En une phrase
Ce que fait ce module et où il se situe dans l'application (une phrase, sans marketing).

## 2. Cartographie du code
Arborescence annotée ou tableau :

| Fichier / dossier | Rôle |
|---|---|
| `src/Controller/OrderController.php` | Endpoints HTTP du module commande |
| `src/Service/OrderProcessor.php` | Règles métier de création |
| `src/Repository/OrderRepository.php` | Requêtes Doctrine |

Dépendances consommées (bundles, services externes) et consommateurs du module (qui l'appelle).

## 3. Modèle de données
Pour chaque entité : nom, table, champs clés, relations (avec cardinalités), contraintes. Sources : `src/Entity/*`, migrations. Référence croisée vers le diagramme ERD de la doc architecte si elle existe.

## 4. Endpoints et commandes
| Méthode + route | Action contrôleur | Entrée | Sortie | Sécurité |
|---|---|---|---|---|
| `POST /api/v1/orders` | `OrderController::__invoke` | `CreateOrderInput` | 201 + `OrderOutput` | `ROLE_CUSTOMER` + voter |

Commandes console, handlers Messenger et événements écoutés, dans le même format.

## 5. Flux métier principal
Diagramme de séquence Mermaid du scénario le plus important, puis description pas à pas (service appelé, transaction, effets de bord). Un seul flux par diagramme — scinder si nécessaire.

## 6. Configuration et environnement
Variables d'environnement utilisées (nom, rôle, exemple de valeur **non sensible**), paramètres `config/packages/*` pertinents, particularités Oracle/PostgreSQL si le module touche la base directement.

## 7. Tests
Où sont les tests du module, ce qu'ils couvrent, ce qu'ils ne couvrent pas. Commandes pour les lancer en local.

## 8. Points d'attention
Dette connue, pièges (ex. : Oracle traite `''` comme `NULL`), zones sensibles, liens vers tickets. Être honnête : c'est la section que les nouveaux développeurs lisent en premier.

## 9. Pour démarrer en local
Étapes numérotées de zéro à « la fonctionnalité tourne » : prérequis, variables, migrations, fixtures, commande de lancement.
