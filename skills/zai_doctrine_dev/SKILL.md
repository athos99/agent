---
name: zai_doctrine_dev
description: Développement et optimisation Doctrine ORM/DBAL — entités et associations, repositories et QueryBuilder, migrations, listeners, transactions, hydration et performance — avec les spécificités Oracle et PostgreSQL (stratégies d'identifiants, types, pièges). Utiliser pour toute tâche de modélisation de données, de requête, de migration ou d'optimisation base de données dans un projet Symfony, même si l'utilisateur dit simplement « ajoute un champ » ou « ça rame sur cette liste ».
---

# Doctrine ORM — entités, requêtes, migrations, Oracle & PostgreSQL

## Entités

Attributs de mapping (pas d'annotations ni YAML), constructeur propre, propriétés protégées, méthodes d'immersion métier quand la logique dépend du state :
```php
#[ORM\Entity(repositoryClass: OrderRepository::class)]
#[ORM\Table(name: 'orders')]
class Order
{
    #[ORM\Id, ORM\GeneratedValue, ORM\Column]
    private ?int $id = null;

    #[ORM\Column(length: 50, unique: true)]
    private string $reference;

    #[ORM\ManyToOne(targetEntity: Customer::class)]
    #[ORM\JoinColumn(nullable: false)]
    private Customer $customer;

    #[ORM\Column(type: 'datetime_immutable')]
    private DateTimeImmutable $createdAt;
}
```

## Identifiants : différence majeure Oracle / PostgreSQL

| Plateforme | Stratégie recommandée | Détail |
|---|---|---|
| PostgreSQL 10+ | `#[ORM\GeneratedValue(strategy: 'IDENTITY')]` | Colonne `GENERATED ... AS IDENTITY`, simple et sûre |
| Oracle | `#[ORM\GeneratedValue(strategy: 'SEQUENCE')]` | Doctrine crée la séquence ; aligner `allocationSize` sur `INCREMENT BY` (10 conseillé pour limiter les allers-retours) |

Ne jamais mélanger les plateformes sur une même migration sans la relire sur chacune : `doctrine:schema:validate --skip-sync` doit passer sur les deux connexions si le projet en a deux.

## Pièges Oracle / PostgreSQL à connaître

- **Oracle traite `''` comme `NULL`** : tout code testant une chaîne vide doit tester aussi `null` ; les colonnes `NOT NULL` refusent `''`. PostgreSQL les distingue.
- **Oracle `DATE` contient l'heure** : pour une date calendaire pure, tronquer à minuit côté applicatif et ne jamais comparer une `DATE` Oracle à une date naïve sans préciser l'heure.
- Casse des identifiants : Oracle uppercase sans guillemets, PostgreSQL lowercase — laisser Doctrine gérer, ne jamais quoter manuellement les identifiants sans nécessité.
- Types : `CLOB` (Oracle) ≈ `text` (PG) → type Doctrine `text` ; `NUMBER(p,s)` ≈ `numeric(p,s)` → `decimal` ; booléen Oracle via `boolean` Doctrine (representé NUMBER(1)).

## Repositories et requêtes

- QueryBuilder uniquement, paramètres liés :
  ```php
  public function findRecentByCustomer(Customer $c, int $limit = 20): array
  {
      return $this->createQueryBuilder('o')
          ->addSelect('i')                       // éviter le N+1 sur les lignes
          ->innerJoin('o.items', 'i')
          ->where('o.customer = :customer')
          ->setParameter('customer', $c)
          ->orderBy('o.createdAt', 'DESC')
          ->setMaxResults($limit)
          ->getQuery()->getResult();
  }
  ```
- `fetchAll`/`findAll` interdits sur des tables à fort volume : toujours paginer (`DoctrinePaginator` ou criteres).
- Native SQL (DBAL) seulement quand DQL ne suffit pas (fonctions spécifiques Oracle/PG, bulk) — paramètres liés obligatoires, et mapping explicite via `ResultSetMapping`.
- Aucune requête dans un contrôleur, un template ou un listener de vue.

## Transactions et écritures

- Écritures multiples = `$em->wrapInTransaction(fn () => ...)` : atomicité, pas de flush dispersé.
- Traitements volumineux par lots (`->iterate()` ou pagination par id) avec `$em->clear()` régulier pour libérer la mémoire.
- Conflits de concurrence : versionnage d'entité (`#[ORM\Version]`) quand deux utilisateurs peuvent écrire le même objet.

## Migrations

1. `bin/console make:migration` puis **relire et ajuster** la migration générée (elle ne voit pas l'intention métier).
2. `up()` et `down()` complets ; jamais de `DROP` sans sauvegarde confirmée.
3. Migration déployée = immuable ; correction = nouvelle migration.
4. Sur un projet dual Oracle/PostgreSQL : écrire du schéma portable via l'API de schéma DBAL, et valider les deux connexions ; les différences s'isolent dans des migrations séparées par connexion.
5. Commenter la migration en tête : ce qu'elle fait, quel ticket, quel impact données (voir `zai_code_comments`).

## Performance — les 5 réflexes

1. N+1 : détecter au profiler (`doctrine.dbal.default_connections` / panneau Doctrine), corriger par `addSelect` + `innerJoin`/`leftJoin` (fetch join).
2. Ne sélectionner que l'utile (`getScalarResult`, DTO hydration) pour les listes et exports.
3. Index : sur chaque colonne de jointure (FK) et de filtre fréquent ; vérifier via `EXPLAIN` (voir `zai_database_oracle_pg`).
4. Éviter les fonctions sur colonnes indexées dans les `WHERE` (tue l'index ; si besoin, index fonctionnel Oracle ou index expression PG).
5. Mesurer avant/après : chaque optimisation cite le temps constaté, pas une intuition.
