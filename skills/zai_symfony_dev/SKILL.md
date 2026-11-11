---
name: zai_symfony_dev
description: Développement applicatif Symfony 6/7 — contrôleurs, services et injection de dépendances, formulaires, validation, sécurité applicative, événements, commandes console, Messenger, cache, Twig, configuration. Utiliser pour implémenter toute fonctionnalité backend Symfony, corriger un bug PHP, créer un endpoint, un service, une commande ou un formulaire — même si l'utilisateur demande simplement « ajoute X » sans mentionner Symfony.
---

# Développement Symfony 6/7

Cible : Symfony 6.4 LTS / 7.x, PHP 8.2+. Adapter si `composer.json` du projet indique autre chose — toujours le vérifier avant d'écrire.

## Structure cible d'une fonctionnalité

```
src/
  Controller/OrderController.php      # HTTP uniquement : mapping requête/réponse
  Entity/Order.php                    # modèle persisté Doctrine
  Repository/OrderRepository.php      # requêtes
  Service/OrderProcessor.php          # règles métier (final readonly)
  Dto/CreateOrderInput.php            # entrée immuable, contraintes de validation
  EventSubscriber/OrderSubscriber.php # réactions aux événements
  Command/ReindexOrdersCommand.php    # opérations console
```

Chaque classe fait une chose ; les dépendances viennent par constructeur ; aucune classe n'accède au conteneur.

## Recettes

### Contrôleur moderne (validation automatique du payload)
```php
#[Route('/api/v1/orders', name: 'api_order_create', methods: ['POST'])]
#[IsGranted('ROLE_CUSTOMER')]
public function __invoke(
    #[MapRequestPayload] CreateOrderInput $input,
    OrderProcessor $processor,
): JsonResponse {
    $order = $processor->create($input);
    return $this->json(OrderOutput::from($order), Response::HTTP_CREATED);
}
```
L'entrée est un DTO avec contraintes `#[Assert\...]` ; la validation échoue automatiquement en 422 (RFC 7807). Le contrôleur ne contient aucune règle métier.

### Service métier
```php
final readonly class OrderProcessor
{
    public function __construct(
        private OrderRepository $orders,
        private EventDispatcherInterface $events,
    ) {}

    public function create(CreateOrderInput $input): Order
    {
        // règles métier ici, pas dans le contrôleur ni dans le template
    }
}
```
Interface uniquement si un second usage prévisible existe (tests avec double, implémentation alternative) — sinon pas d'interface par cérémonie.

### Commande console
```php
#[AsCommand(name: 'app:orders:reindex', description: 'Réindexe les commandes du jour')]
final class ReindexOrdersCommand extends Command
{
    protected function execute(InputInterface $input, OutputInterface $output): int
    {
        // injection par constructeur, sortie via $io, retour self::SUCCESS/FAILURE
    }
}
```

### Messenger (traitement asynchrone)
Message = DTO sérialisable dans `src/Message/` ; handler = `#[AsMessageHandler]` dans `src/MessageHandler/` ; routage vers la queue dans `config/packages/messenger.yaml`. Toute tâche au-delà de ~1 seconde (génération PDF, envois massifs, appels tiers lents) doit être asynchrone.

### Événements
Préférer les événements du domaine (créés par les services via `EventDispatcherInterface`) écoutés par `#[AsEventListener]` : découplage fort. Les listeners de Doctrine (`AsEntityListener`) uniquement pour des préoccupations de persistance, pas de règles métier.

### Sécurité applicative
Rôles dans `config/packages/security.yaml`, règles objet par **Voters** (`OrderVoter::canCancel()`). `#[IsGranted]` sur chaque contrôleur, et dans le service pour les opérations sensibles. Voir la skill `zai_security` pour la checklist complète.

### Cache et performance
Cache applicatif via `CacheInterface` (adaptateur pool configuré), cache HTTP si trajectoire pertinente, `Messenger` pour décharger. Toujours mesurer avec le profiler avant d'optimiser (voir `zai_performance`).

## Règles de configuration

- Secrets et différences d'environnement uniquement en variables d'env (`%env(resolve:...)%`), fichiers `.env` committés sans secret réel, `.env.local` jamais commité.
- Paramètres applicatifs dans `config/services.yaml` (`bind:` pour les scalaires), pas de constantes magiques dispersées.
- `bin/console lint:container lint:twig lint:yaml` et zéro dépréciation avant livraison.

## Avant de dire « terminé »

1. Le endpoint/fonctionnalité est-il testé (voir `zai_testing`) ?
2. Les validations d'entrée couvrent-elles tous les champs ?
3. L'autorisation est-elle vérifiée côté serveur ?
4. La migration est-elle créée et réversible (voir `zai_doctrine_dev`) ?
5. Les commentaires PHPDoc sont-ils posés (voir `zai_code_comments`) ?
