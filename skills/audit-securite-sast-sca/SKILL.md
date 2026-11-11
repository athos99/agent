---
name: rapport-securite-sast-sca
description: Génère un rapport de sécurité complet et détaillé (SAST + SCA) pour un projet PHP/Symfony (Composer) avec JavaScript (Yarn). À utiliser quand l'utilisateur demande une analyse de sécurité, un audit de code, un audit de dépendances, un rapport SAST/SCA, une revue de vulnérabilités ou la mise à jour de docs/analyse-securite-complet.md. Le rapport est écrit en Markdown dans docs/analyse-securite-complet.md avec la date/heure de génération et le moteur IA en en-tête.
---

# Rapport de sécurité SAST + SCA (PHP/Symfony + JS/Yarn)

## Objectif

Produire un rapport d'analyse de sécurité **complet, détaillé et actionnable** couvrant :

- **SAST** (analyse statique du code) : PHP/Symfony, Twig, JavaScript, configuration, secrets ;
- **SCA** (analyse de composition logicielle) : dépendances Composer et Yarn, vulnérabilités connues, paquets abandonnés, licences.

Le rapport est un fichier Markdown enregistré sous **`docs/analyse-securite-complet.md`** (à la racine du projet).

## Règles impératives

1. **Lecture seule** : ne jamais modifier le code, `composer.lock`, `yarn.lock`, ni la configuration du projet. Si une installation est nécessaire pour lancer un outil, la faire dans une copie temporaire.
2. **Aucune exploitation active** : pas de requêtes contre des environnements réels, pas de payloads d'attaque exécutés. L'analyse est statique.
3. **Ne jamais écrire de secret en clair** dans le rapport (mots de passe, tokens, clés privées, `APP_SECRET`, DSN avec identifiants). Les masquer : `AKIA****************`, ou indiquer uniquement le fichier, la ligne et le type de secret.
4. **Ne rien inventer** : chaque constat doit citer un fichier et une ligne (SAST) ou un paquet et une version (SCA), avec la source de l'information (outil, avisory, CVE). Ce qui n'a pas pu être vérifié est marqué « À vérifier ».
5. **Signaler ce qui n'a pas été exécuté** : si un outil est absent ou échoue, le dire dans la section « Périmètre et limites » et utiliser la méthode de repli (recherche par motifs + lecture du code).
6. **Pas de faux sentiment de sécurité** : l'absence de constat ne prouve pas l'absence de vulnérabilité. Le rapport le rappelle dans la section « Limites ».
7. **Réduire le bruit** : relire chaque résultat d'outil avant de le reporter. Écarter les faux positifs évidents (code de test, fixtures, dépendances `dev` non déployées) ou les classer en « Faible confiance ».
8. **Ignore les fichiers htdocs/.env.local et htdocs/.env.test**. Ne les lis JAMAIS.
9. **Exclus tous les fichiers CI de GitLab (.gitlab-ci-*.yml)**

10. lit le fichier docs/justificatifs.md qui contient des justificatifs concernant certains problème que tu vas détecter. Donc affiche le justificatif si tu signale une vulnérabilité ou problème.

# important
Pour avoir le bash et un environement de developpement, avec PHP et Node, on utiliser un container docker
Pour appeler la console il faut lanacer la commande
````bash
cmd/term
````



## Étape 0 — En-tête : date, heure et moteur IA

Récupérer la date et l'heure **réelles** avec la commande, sans les deviner :

````bash
TZ=Europe/Zurich date '+%d.%m.%Y %H:%M:%S %Z (%z)'
````

Le moteur IA est **le modèle qui réalise réellement l'analyse** (nom, version et identifiant s'ils sont connus). Si l'identité exacte n'est pas connue, écrire `non déterminé` : ne jamais inventer un nom de modèle.

Récupérer aussi le contexte du dépôt (si Git est disponible) :

````bash
git rev-parse --show-toplevel
git rev-parse --abbrev-ref HEAD
git rev-parse --short HEAD
git status --porcelain | head -20
````

Le début du fichier doit être strictement de cette forme :

````markdown
# Analyse de sécurité — <nom du projet>

| Champ                         | Valeur |
|-------------------------------|--------|
| Date et heure de génération   | 10.11.2026 14:32:07 CET (+0100) |
| Moteur IA                     | <Nom du modèle> — <version / identifiant> (<éditeur>) |
| Type d'analyse                | SAST + SCA |
| Dépôt / branche / commit      | <nom> / <branche> / <hash court> (<propre ou modifié>) |
| Périmètre                     | PHP/Symfony (Composer), JavaScript (Yarn), configuration, secrets |
| Outils utilisés               | <liste avec versions> |

> Ce rapport a été généré automatiquement par un moteur d'IA à partir d'une analyse statique.
> Il doit être relu et validé par un humain avant toute décision.
````

## Étape 1 — Reconnaissance du projet

Identifier et consigner dans la section « Inventaire » :

- version de PHP (`composer.json` → `require.php`, `.php-version`, Dockerfile, CI) ;
- version de Symfony (`symfony/framework-bundle` dans `composer.lock`) et si elle est **LTS** ou **en fin de vie** ;
- bundles tiers installés, en particulier ceux liés à la sécurité (authentification, SAML, OAuth, CORS, upload, purification HTML) ;
- gestionnaire JS : **Yarn classique (v1)** si `yarn.lock` commence par `# yarn lockfile v1`, **Yarn Berry (v2+)** si présence de `.yarnrc.yml` ;
- outil de build front (Webpack Encore, Vite, etc.) et frameworks JS (Stimulus, React, Vue, jQuery, etc.) ;
- présence de Docker, de CI/CD (`.gitlab-ci.yml`, GitHub Actions), de `.env*`, de `config/packages/*`, de `public/`.
- nombre de dépendances directes et transitives (PHP et JS), code de production vs `dev`.

Définir le périmètre : exclure `vendor/`, `node_modules/`, `var/`, `public/build/`, `public/bundles/` du SAST (mais pas du SCA).

## Étape 2 — SCA : dépendances PHP (Composer)

````bash
composer validate --strict
composer audit --locked --format=json          # CVE / avis de sécurité connus + paquets abandonnés
composer outdated --direct --format=json       # retards de version
composer licenses --format=json                # licences
````

À analyser :

- vulnérabilités connues (identifiant CVE / GHSA / avis Symfony, version affectée, version corrigée) ;
- **paquets abandonnés** et leur remplaçant suggéré ;
- dépendances directes très en retard (versions majeures de retard, branches non maintenues) ;
- contraintes de version trop larges (`*`, `dev-master`) et dépôts Composer non officiels (`repositories` dans `composer.json`) ;
- scripts Composer (`scripts`, `post-install-cmd`) exécutant du code externe ;
- `composer.lock` absent ou non versionné (build non reproductible).

Si `composer audit` n'est pas disponible (Composer < 2.4), utiliser en repli `osv-scanner --lockfile=composer.lock` ou l'API publique des avis de sécurité de Packagist, et le noter dans les limites.

## Étape 3 — SCA : dépendances JavaScript (Yarn)

Yarn v1 :

````bash
yarn audit --json
yarn audit --groups dependencies --json        # vulnérabilités de production uniquement
yarn outdated
````

Yarn Berry (v2+) :

````bash
yarn npm audit --all --recursive --json
````

Complément recommandé (si installé) :

````bash
osv-scanner --lockfile=yarn.lock --lockfile=composer.lock --format=json
trivy fs --scanners vuln,secret,misconfig --format json .
npx --no-install license-checker --json        # licences JS, si disponible
````

À analyser :

- vulnérabilités par paquet, **chemin de dépendance** (dépendance directe ou transitive) et distinction `dependencies` / `devDependencies` ;
- vulnérabilités **atteignables en production** (code embarqué dans le bundle) vs outillage de build uniquement ;
- bibliothèques front obsolètes connues pour être vulnérables (jQuery, Bootstrap, lodash, moment, handlebars, etc.) ;
- paquets abandonnés ou non maintenus depuis longtemps ;
- risques de chaîne d'approvisionnement : scripts `preinstall`/`postinstall`, dépendances par URL Git ou tarball, **typosquatting** possible, `yarn.lock` non versionné ou incohérent avec `package.json` ;
- `resolutions` (Yarn) servant à forcer des versions : vérifier qu'elles corrigent bien la faille visée.

## Étape 4 — SAST : PHP / Symfony

Si les outils sont présents, les lancer (sinon repli par lecture du code et `grep`) :

````bash
vendor/bin/phpstan analyse --level=max --error-format=json --no-progress   # si phpstan.neon présent
vendor/bin/psalm --taint-analysis --output-format=json                      # si psalm.xml présent
semgrep scan --config p/php --config p/symfony --config p/owasp-top-ten --config p/secrets --json \
  --exclude vendor --exclude node_modules --exclude var
````

Revue manuelle ciblée (OWASP Top 10, CWE associés) :

**Injection (A03)**
- SQL/DQL : concaténation ou interpolation dans `createQuery`, `createQueryBuilder`, `->where("... $x")`, `Connection::executeQuery/executeStatement` sans paramètres liés (CWE-89) ;
- exécution de commandes : `exec`, `shell_exec`, `system`, `passthru`, `proc_open`, backticks, `Process::fromShellCommandline` avec entrée utilisateur (CWE-78) ;
- `eval`, `assert` avec chaîne, `create_function`, `preg_replace` avec `/e`, `include`/`require` dynamiques (CWE-94, CWE-98) ;
- injection de **template** (Twig `createTemplate` avec entrée utilisateur, SSTI), injection LDAP, XPath, **injection d'en-têtes HTTP** et CRLF (`header()`, `Response::headers->set` avec entrée non filtrée) (CWE-113).

**XSS (A03)**
- Twig : `|raw`, `autoescape` désactivé, `{% autoescape false %}`, `|striptags` utilisé comme protection, sortie dans un contexte JS/URL/attribut sans `|e('js')`/`|e('url')` ;
- HTML « purifié » : vérifier la configuration de HTMLPurifier (liste blanche, `URI.AllowedSchemes`, cache) et que le filtre est appliqué **avant** `|raw` ;
- réponses `Response` construites à la main avec du HTML non échappé.

**Authentification et contrôle d'accès (A01, A07)**
- `config/packages/security.yaml` : pare-feux (`firewalls`), `access_control` (ordre et regex, routes oubliées), `role_hierarchy`, `password_hashers` (algorithme `auto`), `remember_me` (durée, `secure`, `samesite`), `logout`, throttling de connexion (`login_throttling`) ;
- contrôleurs sans `#[IsGranted]` / `denyAccessUnlessGranted`, **IDOR** (accès à un objet par identifiant sans voter ni vérification de propriété) (CWE-639), absence de Voters pour les ressources sensibles ;
- **SAML / OAuth / JWT** : validation de la signature, de l'audience, de l'émetteur, de l'expiration et du `state`/`nonce` ; certificats SP/IdP ; `strict` activé ; `wantAssertionsSigned` ; paramètres `RelayState` / redirections non validés ;
- comparaison de secrets non constante (`==` au lieu de `hash_equals`).

**CSRF (A01)**
- formulaires et actions modifiant l'état sans jeton (`csrf_protection`), routes `GET` à effet de bord, configuration des jetons stateless vs classiques, `SameSite` des cookies de session.

**Désérialisation et fichiers (A08, A04)**
- `unserialize()` sur des données non fiables, `Serializer` avec types polymorphes non restreints (CWE-502) ;
- **uploads** : validation du type MIME **réel** (pas seulement l'extension ou `Content-Type`), taille, renommage, stockage hors `public/`, exécution de scripts dans le dossier d'upload, **path traversal** (`../`) et `file_get_contents`/`readfile`/`fopen` avec chemin dynamique (CWE-22, CWE-434) ;
- génération de fichiers/archives (Zip Slip), traitement XML (**XXE** : `libxml_disable_entity_loader`, `LIBXML_NOENT`, `DOMDocument::loadXML` avec options dangereuses) (CWE-611).

**SSRF et réseau (A10)**
- `HttpClient`, `curl_*`, `file_get_contents('http://…')` avec URL contrôlée par l'utilisateur ; absence de liste blanche d'hôtes ;
- **validation SSL/TLS désactivée** : `verify_peer: false`, `verify_host: false`, `CURLOPT_SSL_VERIFYPEER => false` (CWE-295) ;
- **redirections ouvertes** : `redirect($request->get('url'))` sans validation (CWE-601).

**Configuration et divulgation d'information (A05)**
- `APP_ENV`/`APP_DEBUG` en production, profiler/`_wdt`/`_profiler` exposés, pages d'erreur détaillées ;
- `trusted_proxies` / `trusted_headers` mal configurés (usurpation d'IP/hôte) ;
- cookies de session : `cookie_secure`, `cookie_httponly`, `cookie_samesite` ;
- **CORS** (`nelmio_cors`) : `allow_origin: ['*']` combiné à `allow_credentials` ;
- en-têtes de sécurité absents (CSP, HSTS, X-Content-Type-Options, Referrer-Policy, Permissions-Policy) ;
- `phpinfo()`, scripts de debug ou fichiers sensibles accessibles dans `public/` ;
- messages d'erreur ou logs contenant des données personnelles ou des secrets (A09) ;
- **génération de nombres aléatoires** non sûrs pour des jetons : `rand`, `mt_rand`, `uniqid` au lieu de `random_bytes`/`random_int` (CWE-338) ; algorithmes faibles (`md5`, `sha1`, `crypt` pour les mots de passe, ECB, IV statique) (CWE-327).

**API (le cas échéant)**
- absence de pagination/limitation de débit (`rate_limiter`), exposition excessive de champs dans la sérialisation (groupes), validation des DTO (`Assert\*`), mass assignment via le formulaire ou le serializer.

## Étape 5 — SAST : JavaScript

````bash
semgrep scan --config p/javascript --config p/typescript --config p/xss --json \
  --exclude node_modules --exclude public/build --exclude vendor
npx --no-install eslint --no-eslintrc --plugin security --rule '{"security/detect-eval-with-expression":"error"}' assets/ # si disponible
````

Cibles de revue manuelle :

- **XSS DOM** : `innerHTML`, `outerHTML`, `insertAdjacentHTML`, `document.write`, `$(…).html()`, `v-html`, `dangerouslySetInnerHTML`, templates Handlebars/Mustache avec triple accolade `{{{ }}}` ou échappement désactivé (CWE-79) ;
- `eval`, `new Function`, `setTimeout`/`setInterval` avec chaîne, import dynamique d'URL contrôlée ;
- `postMessage` sans vérification de `event.origin`, `window.open` sans `noopener`, liens `target="_blank"` sans `rel="noopener noreferrer"` ;
- secrets ou jetons dans le code front, `localStorage`/`sessionStorage` utilisés pour des jetons d'authentification ;
- appels `fetch`/`XMLHttpRequest` sans gestion du jeton CSRF ; URL d'API construites avec de l'entrée non validée ;
- usage de bibliothèques de templates, de parsing d'URL ou de markdown sans assainissement ;
- configuration de build : **source maps** publiées en production, `devtool` non sûr, variables d'environnement injectées dans le bundle (`DefinePlugin`/`.env` exposé).

## Étape 6 — Secrets et configuration d'infrastructure

````bash
gitleaks detect --source . --no-banner --redact --report-format json --report-path /tmp/gitleaks.json
````

Repli (sans gitleaks), recherche par motifs : clés privées (`BEGIN .* PRIVATE KEY`), `password\s*[:=]`, `secret`, `token`, `api[_-]?key`, DSN avec identifiants (`://user:pass@`), `AKIA[0-9A-Z]{16}`, etc.

À vérifier :

- fichiers `.env`, `.env.local`, `.env.prod` versionnés et valeurs sensibles par défaut (`APP_SECRET`, `DATABASE_URL`, `MAILER_DSN`) ;
- clés/certificats (`*.pem`, `*.key`, `*.p12`, `*.pfx`, `*.crt`) présents dans le dépôt ;
- `Dockerfile` / `docker-compose.yml` : image de base non épinglée (`latest`), exécution en `root`, secrets en `ENV`/`ARG`, ports exposés inutilement, volumes sensibles ;
- CI/CD : secrets en clair, étapes exécutant du code distant (`curl | sh`), absence d'analyse de sécurité dans le pipeline ;
- `.gitignore` incomplet (`var/`, `.env.local`, `*.pem`).

## Étape 7 — Évaluation et priorisation

Échelle de sévérité (indiquer le score CVSS v3.1/v4 quand il est publié, sinon justifier la sévérité) :

| Sévérité   | Critère |
|------------|---------|
| **Critique** | Exécution de code à distance, injection SQL exploitable sans authentification, contournement d'authentification, secret de production exposé |
| **Élevée**   | Faille exploitable avec peu de privilèges (IDOR, XSS stocké, SSRF, désérialisation), CVE avec exploit public sur composant exposé |
| **Moyenne**  | Faille nécessitant des conditions particulières (XSS réfléchi, CSRF, mauvaise configuration de session), dépendance vulnérable non atteignable directement |
| **Faible**   | Durcissement manquant, divulgation d'information mineure, dette technique à impact sécurité limité |
| **Info**     | Bonne pratique, observation sans risque direct |

Pour chaque constat, estimer : **exploitabilité**, **impact** (confidentialité, intégrité, disponibilité), **exposition** (public, authentifié, interne) et **niveau de confiance** (Élevée / Moyenne / Faible).

## Étape 8 — Rédaction du rapport

Créer le dossier si nécessaire (`mkdir -p docs`) puis écrire **`docs/analyse-securite-complet.md`**. Si le fichier existe déjà, l'écraser, mais reprendre dans la section « Évolution » les chiffres de l'analyse précédente (date, nombre de constats par sévérité) pour permettre la comparaison.

### Structure obligatoire

````markdown
# Analyse de sécurité — <projet>
<tableau d'en-tête de l'étape 0>

## 1. Synthèse exécutive
- Niveau de risque global (Critique / Élevé / Moyen / Faible) avec justification en 3 à 5 lignes
- Tableau : nombre de constats par sévérité et par catégorie (SAST-PHP, SAST-JS, SCA-PHP, SCA-JS, Secrets, Config)
- Top 5 des actions prioritaires
- Évolution par rapport à l'analyse précédente (si disponible)

## 2. Périmètre, méthodologie et limites
- Ce qui a été analysé et exclu
- Outils exécutés (nom, version, commande) et outils non disponibles / en échec
- Méthodes de repli utilisées
- Limites : analyse statique uniquement, pas de test dynamique, faux négatifs possibles

## 3. Inventaire technique
- Versions (PHP, Symfony, Node, Yarn), état de support (LTS / fin de vie)
- Nombre de dépendances (directes / transitives, prod / dev)
- Bundles et bibliothèques sensibles

## 4. SCA — Dépendances PHP (Composer)
- Tableau : paquet | version installée | CVE/avis | sévérité | version corrigée | direct/transitif | prod/dev
- Paquets abandonnés
- Dépendances obsolètes
- Licences à risque

## 5. SCA — Dépendances JavaScript (Yarn)
- Même structure + chemin de dépendance et atteignabilité en production

## 6. SAST — PHP / Symfony
- Constats détaillés (format ci-dessous), regroupés par catégorie OWASP

## 7. SAST — JavaScript
- Constats détaillés, regroupés par catégorie

## 8. Secrets et configuration (Symfony, Docker, CI/CD)
- Constats détaillés (valeurs toujours masquées)

## 9. Bonnes pratiques constatées
- Points positifs (protections correctement en place), pour équilibrer le rapport

## 10. Plan de remédiation priorisé
- Tableau : priorité | constat(s) | action | effort estimé (S/M/L) | responsable suggéré
- Regroupé en : Immédiat (< 1 semaine), Court terme (< 1 mois), Moyen terme

## 11. Annexes
- A. Commandes exécutées
- B. Résultats bruts résumés des outils
- C. Faux positifs écartés et motif
- D. Glossaire (SAST, SCA, CVE, CWE, CVSS, OWASP)
````

### Format d'un constat

````markdown
### [SAST-PHP-003] Titre court et explicite

| Champ | Valeur |
|-------|--------|
| Sévérité | Élevée (CVSS 7.5 si applicable) |
| Confiance | Élevée / Moyenne / Faible |
| Statut | Confirmé / À vérifier |
| Catégorie | OWASP A03:2021 — Injection |
| CWE | CWE-89 |
| Localisation | `src/Repository/ArticleRepository.php:84` |

**Description** : ce qui est vulnérable et pourquoi.

**Preuve** :
```php
// extrait de 10 lignes maximum, secrets masqués
```

**Impact** : conséquence concrète pour l'application et les données.

**Remédiation** : correction précise, avec exemple de code corrigé.

**Références** : lien CVE / avis / documentation Symfony / OWASP.
````

Les identifiants suivent le schéma `SAST-PHP-NNN`, `SAST-JS-NNN`, `SCA-PHP-NNN`, `SCA-JS-NNN`, `SEC-NNN` (secrets), `CFG-NNN` (configuration).

## Étape 9 — Contrôle qualité avant de terminer

Vérifier que :

- [ ] l'en-tête contient la **date/heure réelle** (issue de `date`) et le **moteur IA** ;
- [ ] le fichier est bien `docs/analyse-securite-complet.md` ;
- [ ] aucun secret n'apparaît en clair ;
- [ ] chaque constat a une localisation (fichier:ligne ou paquet@version), une sévérité, une remédiation ;
- [ ] les totaux de la synthèse correspondent au détail ;
- [ ] les outils non exécutés et les limites sont mentionnés ;
- [ ] les faux positifs écartés sont listés en annexe.

## Réponse finale à l'utilisateur

Une fois le fichier écrit, répondre en quelques lignes : chemin du fichier, niveau de risque global, nombre de constats par sévérité, les 3 actions les plus urgentes, et les outils qui n'ont pas pu être exécutés. Ne pas recopier le rapport dans la conversation.