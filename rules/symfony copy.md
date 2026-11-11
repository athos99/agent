# AGENTS.md




- pour utiliser le shell on utilise docker, le script shell cmd/term permet de lancer la console. Ensuite va dans le réprtoire htdocs/ tu aura accès à toutes les utilitaires pour ce proje come php, node, composer, bin/console etc...


# Contexte du projet – à compléter

## Environement
- Symfony se trouve dans le répertoire htdocs/ ( /home/user/www/6206-gps/htdocs en absolu),
- Si tu utilise le terminal, assure toi que tu es bien dans le répertoire htdocs/
- Pour avoir une console linux on utilise une image docker. Utilise la commande qui permet d'accéder au terminal
  `docker exec -it php84 bash -c "cd 6206-gps/htdocs && bash"
- Un serveur apache est installé dans le conteneur docker php84, il est accessible à l'adresse http://localhost:1085/6206-gps/htdocs/public/ ce qui pointe sur le répertoire htdocs/public/
- Depuis la console, les applications suivantes sont installées.
  * composer
  * node
  * yarn
  * symfony
  * bin/console




## Stack cible indicative
- PHP : vérifier la version réellement utilisée dans `composer.json` et en CI.
- Symfony : cible indicative 8.1
- ORM : Doctrine ORM ; 4 bases de données Oracle
- API : REST, documentation OpenAPI via NelmioApiDocBundle si installé.
- Tests : PHPUnit et Behat si présents.
- CI/CD : GitLab CI si le dépôt utilise GitLab.


## Règles de priorité
1. Le code et les configurations existants du dépôt font foi.
2. Les versions verrouillées dans `composer.lock` font foi.
3. Ne pas introduire de dépendance ou de composant sans expliquer son utilité.
4. Ne pas supposer qu'un bundle, driver, outil ou service est installé.
5. En cas de contradiction entre ce fichier et l'implémentation existante, signaler
   la contradiction et demander confirmation avant une migration structurante.

## À renseigner par l'équipe
- Version Oracle / driver OCI : Oracle Database 19c Enterprise Edition Release 19.0.0.0.0
- Authentification (SAML2, OIDC, autre) : SAML2
- Convention d'API / format d'erreur :
- Commandes qualité et tests :


## Demander avant de générer

Si la tâche ne spécifie pas les choix d'architecture, demandez plutôt que de deviner :

- Persistance : Doctrine ORM, Doctrine ODM, ou aucune ?
- Interface : rendu serveur (Twig), API (Serializer, peut-être API Platform), ou les deux ?
- Authentification : SecurityBundle, et quel authenticator ?

Si vous ne pouvez pas demander (pas de canal interactif), indiquez l'hypothèse que vous faites et choisissez l'option la plus simple (par ex. aucune couche de persistance) plutôt que de générer une pile complète que personne n'a demandée.

## Ajouter des fonctionnalités : avec Flex, pas à la main

Ouvrir un terminal, travailler dans le répertoire htdocs
Installez les nouvelles fonctionnalités avec `composer require <package>` (par ex. `symfony/lock`, `symfony/messenger`, `orm-pack`) et laissez la recette Flex enregistrer le bundle et générer sa configuration. Ne modifiez pas manuellement `config/bundles.php` ni n'écrivez à la main la configuration de base d'un bundle : c'est le rôle de la recette. Ne passez pas à côté d'un Composant Symfony bien adapté simplement parce qu'il n'est pas encore installé ; l'installer prend une seule commande.

## Conventions

Suivez https://symfony.com/doc/current/best_practices.html pour écrire du code idiomatique Symfony :

- Utilisez les attributs PHP pour les métadonnées du framework, et pas uniquement dans les contrôleurs : `#[Route]`, `#[MapRequestPayload]`, `#[IsGranted]` sur les actions, `#[Assert\...]` sur les propriétés, `#[AsCommand]`, `#[AsEventListener]`, `#[AsMessageHandler]`, et `#[AsAlias]` / `#[AsTaggedItem]` / `#[Autoconfigure]` sur les services. Pas de routage YAML ou XML.
- CompteZ sur l'autowiring et l'autoconfiguration. Indiquez les types dans les arguments du constructeur et laissez le conteneur les résoudre. Là où un type ne peut pas les exprimer, restez dans la classe avec `#[Autowire]` (paramètres, variables d'environnement, expressions) ou `#[Target]` (l'une des plusieurs implémentations d'une interface). Une définition de service YAML n'est le premier choix que si rien d'autre ne fonctionne.
- Les contrôleurs héritent de `AbstractController`, restent légers, et déléguent aux services.
- Utilisez le framework pour ce qu'il fait déjà : Form pour les formulaires rendus serveur, Validator pour la validation, Serializer pour le JSON, Messenger pour le travail asynchrone, Security (voters, authenticators) pour le contrôle d'accès, Twig `path()`/`url()` au lieu d'URLs codées en dur.
- Avant d'écrire vous-même l'infrastructure (verrous, files d'attente, caches, clients HTTP, messagers, planification) ou de recourir à une bibliothèque tierce, vérifiez si un Composant Symfony le couvre. C'est généralement le cas.

Trois points spécifiques qu'il est facile de se procurer, car ils sont souvent mal compris :

- Liez les données de la requête avec `#[MapRequestPayload]` / `#[MapQueryString]` sur les arguments des actions, ce qui configure Serializer et Validator automatiquement, au lieu d'appeler `json_decode()` ou `SerializerInterface` à la main. Si aucun de ces paquets n'est encore installé, faites `composer require` plutôt que de recourir à un analyseur manuel.
- Utilisez la promotion de propriétés dans le constructeur, et `readonly` pour les DTO et les objets valeur.
- Utilisez `symfony/lock` (`LockFactory`) pour l'exclusion mutuelle. Un flag ou un fichier de verrou construit à la main fait bonne figure en revue de code, mais est généralement incorrect en concurrence.

## Travail au quotidien

- Si quelque chose échoue, lisez `var/log/dev.log` et le profileur web (`/_profiler`) avant de modifier le code.
- Si `maker-bundle` est installé, préférez `bin/console make:*` avec tous les arguments passés devant et `--no-interaction` là où c'est supporté : les makers bootent sur un terminal par défaut, ce qui bloquera un shell non interactif. Si un maker a encore besoin d'une entrée interactive, écrivez le code à la main.
- `.env` est commité et contient uniquement les valeurs par défaut. Les vrais secrets vont dans `.env.local` (ignoré par git) ou le vault de secrets (`bin/console secrets:set`), lus via `%env(...)%.
-


# Principes généraux d'ingénierie

- Comprendre l'architecture et les conventions existantes avant de modifier le code.
- Privilégier des changements petits, cohérents, réversibles et faciles à relire.
- Ne pas réécrire un fichier entier si une modification ciblée suffit.
- Ne pas ajouter d'abstraction, de dépendance ou de configuration sans besoin démontré.
- Respecter les principes SOLID sans sur-ingénierie.
- Séparer clairement logique métier, accès aux données, présentation et infrastructure.
- Éviter la duplication ; factoriser seulement lorsque cela améliore réellement la lisibilité.
- Ne jamais inventer une API, une méthode de bundle ou une option de configuration :
  vérifier les dépendances et versions du projet.
- Préserver la compatibilité existante, sauf demande explicite de changement.
- Signaler les hypothèses, impacts, migrations et risques.
- Ne jamais afficher, copier ou committer de secrets, tokens, mots de passe ou données
  personnelles.
- Ne jamais affirmer qu'une commande ou un test a réussi sans résultat d'exécution.


# Standards PHP

- Suivre la version PHP déclarée par le projet ; éviter toute syntaxe plus récente.
- Respecter PSR-12 et les conventions de nommage déjà établies.
- Utiliser les types de paramètres, retours et propriétés lorsque cela clarifie le contrat.
- Préférer les types stricts (`declare(strict_types=1);`) selon la convention du dépôt.
- Garder les méthodes courtes et focalisées ; éviter les effets de bord cachés.
- Gérer explicitement les erreurs ; ne pas masquer une exception avec un `catch` vide.
- Ne pas utiliser `mixed`, `@phpstan-ignore` ou des casts pour contourner une analyse
  sans justification documentée et ciblée.
- Éviter les fonctions obsolètes et vérifier les changements de compatibilité.
- Échapper les données au moment de leur sortie selon le contexte ; valider les entrées.
- Ne jamais construire une requête SQL par concaténation de données utilisateur.
- Respecter les standards de formatage du dépôt ; ne pas reformater des fichiers sans lien.


# Conventions Symfony

- Vérifier la version Symfony et les bundles réellement installés avant de proposer une API.
- Respecter l'architecture existante (controllers, services, repositories, DTO, événements).
- Garder les contrôleurs minces : orchestration HTTP, délégation à des services applicatifs.
- Placer les règles métier dans des services/domain objects adaptés, pas dans les contrôleurs.
- Utiliser l'injection de dépendances et l'autowiring conformément au projet.
- Utiliser Validator pour les contraintes d'entrée lorsque pertinent.
- Utiliser Serializer/DTO selon les conventions du projet ; contrôler explicitement les
  champs exposés et les groupes de sérialisation.
- Centraliser la traduction des exceptions métier en réponses HTTP.
- Ne pas injecter le container PSR-11 ou le container Symfony dans les services métier.
- Éviter les services trop génériques et les classes « Utils » fourre-tout.
- Pour toute modification de configuration, expliquer l'impact sur les environnements.


# Doctrine et Oracle

- Vérifier la version de Doctrine DBAL/ORM, le driver et la configuration Oracle du projet.
- Respecter les mappings et conventions de nommage déjà en place.
- Utiliser les repositories pour encapsuler les requêtes de lecture réutilisables.
- Utiliser les paramètres liés ; ne jamais concaténer une entrée utilisateur dans du SQL.
- Ne pas supposer que toutes les fonctionnalités DQL/SQL sont portables entre SGBD.
- Pour du SQL spécifique Oracle, documenter pourquoi DBAL/DQL ne suffit pas.
- Tenir compte des séquences, identifiants, types, dates, encodage et contraintes Oracle.
- Éviter les requêtes N+1 ; examiner les jointures, fetch strategy et volumes retournés.
- Définir clairement les frontières transactionnelles ; ne pas ouvrir de transaction
  sans stratégie de commit/rollback.
- Ne pas appeler `flush()` dans une boucle sans justification ; considérer le batching.
- Ne jamais lancer de migration destructive ou de modification de schéma sans validation.
- Ajouter des tests couvrant les cas Oracle spécifiques lorsque l'environnement le permet.

# AppSec – exigences de sécurité

- Intégrer la sécurité dès la conception et pendant la revue de code.
- Examiner au minimum : contrôle d'accès, injection, XSS, CSRF, SSRF, désérialisation,
  téléversement, secrets, dépendances vulnérables, configuration et journalisation.
- Appliquer les recommandations OWASP pertinentes (notamment API Security Top 10).
- Vérifier l'autorisation au niveau de l'objet, de la fonction et des propriétés.
- Valider les données selon leur contexte et encoder les sorties.
- Utiliser les API paramétrées pour SQL et les mécanismes sûrs du framework.
- Ne pas désactiver TLS, vérification de certificat, contrôles CSRF ou protections de sécurité
  pour contourner un problème.
- Ne pas ajouter de secret dans le dépôt, les logs, les fixtures ou les exemples.
- Vérifier les dépendances et versions ; signaler les CVE pertinentes avec leur source.
- Éviter les messages d'erreur détaillés en production ; journaliser sans données sensibles.
- Pour chaque constat, fournir : gravité, préconditions, impact, preuve, correction proposée
  et éventuelles limites de l'analyse.
- Distinguer vulnérabilité démontrée, risque potentiel et simple recommandation.
- Ne jamais tenter d'exploiter un système externe ou de scanner une cible sans autorisation.
