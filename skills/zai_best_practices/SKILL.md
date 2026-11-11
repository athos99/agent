---
name: zai_best_practices
description: Vérification et application des bonnes pratiques Symfony 6/7, Doctrine ORM et JavaScript moderne — PSR-12, architecture en couches, SOLID, conventions de nommage, patterns officiels du framework. Utiliser pour écrire du nouveau code, corriger ou refactoriser du code existant, ou vérifier la conformité d'un développement, même si l'utilisateur dit seulement « améliore ce code » ou « c'est bien écrit comme ça ».
---

# Bonnes pratiques Symfony / Doctrine / JavaScript

Rôle : garantir que tout code produit ou modifié respecte les standards de l'écosystème et du projet. La cohérence du projet existant l'emporte sur les préférences personnelles : si le projet a un pattern correct, le réutiliser plutôt que d'en introduire un autre.

## Principes transverses (PHP + JS)

- **Responsabilité unique** : une classe ou un module = une raison de changer. Le contrôleur n'orchestre que la requête HTTP.
- **Dépendances explicites** : injection par constructeur uniquement. Accès direct au conteneur (`$this->container->get()`) interdit hors cas de compilation.
- **Immutabilité par défaut** : services et DTO `final readonly` en PHP, `const` en JS. La mutabilité doit se justifier.
- **Typage strict** : `declare(strict_types=1)` dans tout fichier PHP, types de propriétés et de retour systématiques ; JSDoc ou TypeScript côté JS.
- **Pas de magie** : pas de réflexion inutile, pas de `extract()`, pas de variables variables, pas d'appels dynamiques obscurs.

## Symfony — règles d'or

| Sujet | Règle |
|---|---|
| Contrôleurs | Minces : mapper la requête vers un service, retourner une réponse. Préférer `__invoke`. Aucune logique métier, aucun QueryBuilder. |
| Services | Un service par cas métier (`OrderProcessor`, `InvoiceGenerator`), `final`, constructeur promu, autowire + autoconfigure. |
| Configuration | Déclarative (yaml/php) ; secrets uniquement en variables d'environnement `%env(...)` ; jamais de valeur d'environnement en dur. |
| Événements | Listeners découplés via `#[AsEventListener]` / `#[AsEventListener]` plutôt qu'héritage. |
| Formulaires | Types dédiés + contraintes de validation sur DTO/entités ; jamais de validation manuelle dans le contrôleur. |
| Validation | Attributs PHP (`#[Assert\NotBlank]`, ...) sur les DTO ; groupes de validation si contextes multiples. |
| Commandes | `#[AsCommand]`, dépendances injectées, sortie stylée (`$io->success()`), code retour correct. |
| Twig | Auto-échappement jamais désactivé sans justification écrite ; aucune requête ni logique dans les templates ; `|trans` pour tout libellé. |
| Dépréciations | Zéro dépréciation tolérée : `lint:container` et logs propres, jamais de code sur API dépréciée. |

## Doctrine — règles d'or

- Toute requête passe par un Repository ou un service dédié — jamais dans un contrôleur, un template ou un listener de vue.
- QueryBuilder avec paramètres liés (`setParameter`) ; aucune concaténation de chaîne SQL/DQL.
- Écritures multiples = transaction explicite (`$em->wrapInTransaction(fn () => ...)`).
- Relations : côté propriétaire explicite, cascade réfléchie (persist/remove uniquement si le cycle de vie l'exige), `indexBy` quand cela simplifie la lecture.
- Migrations générées puis relues et ajustées ; réversibles ; une migration déployée ne se modifie plus jamais (on en crée une nouvelle).

## JavaScript — règles d'or

- Modules ES (`import`/`export`), pas de variable globale ; `const` par défaut, `let` si réassignation nécessaire.
- `async/await` avec gestion d'erreur systématique (`try/catch` ou `.catch`) ; jamais de promesse flottante dont l'erreur serait perdue.
- Pas d'`eval`, pas d'`innerHTML` avec des données non sûres (préférer `textContent`), pas de `document.write`.
- Fonctions courtes, un seul niveau d'abstraction par fonction ; extraire plutôt qu'empiler.
- Stimulus (Symfony UX) ou framework détecté (Vue, React) : suivre les conventions du framework présent, ne jamais mélanger les paradigmes dans le même écran.

## Contrôle avant livraison

```bash
php vendor/bin/php-cs-fixer fix --dry-run --diff
php vendor/bin/phpstan analyse
php bin/console lint:container lint:twig lint:yaml
php bin/console doctrine:schema:validate --skip-sync
npx eslint assets && npx prettier --check assets
```

Tout écart doit être corrigé ou explicitement justifié dans la réponse. Un écart « acceptable » sans justification est un écart non traité.

## Mode d'emploi pour l'agent

1. Identifier la couche du fichier concerné (contrôleur, service, entité, repository, vue JS) et appliquer ses règles.
2. Lire 2-3 fichiers voisins du même type pour capter les conventions locales avant d'écrire.
3. Signaler les écarts constatés dans du code voisin sans les corriger hors périmètre : les proposer.
4. Si une règle du projet contredit ce qui précède, appliquer la règle du projet et le signaler pour arbitrage.
