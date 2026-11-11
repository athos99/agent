---
name: zai_security
description: Audit de sécurité et codage sécurisé pour Symfony, Doctrine, JavaScript avec Oracle/PostgreSQL — OWASP Top 10, injection SQL, XSS, CSRF, authentification et autorisations, secrets, uploads, dépendances, données personnelles. Utiliser dès qu'une question de sécurité, de faille, de vulnérabilité, d'authentification, d'autorisation, de protection de données ou de conformité apparaît, et systématiquement avant de valider du code touchant à l'auth, aux paiements, aux fichiers ou aux données personnelles.
---

# Sécurité Symfony / JavaScript / Oracle / PostgreSQL

## Réflexe systématique

Toute entrée est hostile (utilisateur, API tierce, fichier, paramètre d'URL, en-tête). Toute sortie doit être échappée ou paramétrée. Le contrôle d'accès se vérifie **côté serveur** : ce que le front masque n'est jamais une protection.

## Grille OWASP appliquée à ce stack

### A01 — Contrôle d'accès défaillant
- `#[IsGranted('ROLE_X')]` sur les contrôleurs + **Voter** pour les règles métier (`OrderVoter` : peut-il annuler CETTE commande ?). Pour les opérations sensibles, revérifier `isGranted` dans le service, pas seulement dans le contrôleur.
- Ne jamais construire de requête `WHERE user_id = $request->get('user_id')` : l'identité vient du token ou de la session, jamais d'un paramètre client.
- Tester chaque endpoint avec l'identifiant d'un autre utilisateur (IDOR) et avec des rôles plus faibles.

### A02 — Défaillances cryptographiques
- Mots de passe : `UserPasswordHasherInterface` avec `argon2id` (ou `bcrypt`). Jamais md5, sha1, base64, ni « hash maison ».
- Secrets : `APP_SECRET`, identifiants BD, clés API uniquement via variables d'environnement ou `bin/console secrets:*`. Jamais dans Git, les logs, les templates, les messages d'erreur.
- TLS partout ; cookies `secure`, `httponly`, `samesite: lax` dans `framework.yaml`.

### A03 — Injection
- Doctrine : paramètres liés uniquement (`setParameter`, `bindValue` avec `ParameterType`). Toute requête native (`createNativeQuery`, DBAL `executeQuery`) doit être relue : c'est là que les injections arrivent.
- Oracle : pas de `EXECUTE IMMEDIATE` ni `DBMS_SQL` construits par concaténation. PostgreSQL : pas de `format()` / `||` sur des entrées utilisateur dans du plpgsql — utiliser les paramètres de requête.
- OS : jamais `shell_exec`/`proc_open` avec de l'entrée utilisateur.
- JS : pas d'`eval`, pas de `new Function`, pas de `setTimeout("chaîne")`.

### XSS / CSRF
- Twig échappe par défaut : auditer chaque `|raw` — justification obligatoire + sanitisation en amont (DOMPurify côté rédaction, par exemple).
- JS : `textContent` plutôt que `innerHTML` ; tout rendu de HTML riche (markdown, éditeur) passe par une lib de sanitisation.
- Formulaires Symfony : CSRF natif, ne pas le désactiver ; API stateless : protection par token (JWT/OAuth) et cookies `SameSite`, pas par session.
- En-têtes de sécurité : CSP, `X-Content-Type-Options`, `X-Frame-Options`, `Referrer-Policy` (bundle nelmio_security ou configuration web serveur).

### A05 — Mauvaise configuration
- Production : `APP_ENV=prod`, `APP_DEBUG=0`, profiler désactivé, pages d'erreur génériques, `/api/doc` protégé ou désactivé.
- Limiteur de débit (rate limiter Symfony) sur login, mot de passe oublié et endpoints coûteux.
- Audits de dépendances en CI : `composer audit`, `npm audit --omit=dev`.

### Uploads et fichiers
- Contraintes `#[Assert\File]`/`#[Assert\Image]` + vérification du mime réel (contenu binaire, pas l'extension), taille maximale, nom de fichier régénéré, stockage hors webroot, URLs signées si accès privé.

### Journalisation et données personnelles (RGPD)
- Ne jamais logger mots de passe, tokens, données de carte ; minimisation, durée de rétention définie, purge automatisée ; anonymisation ou suppression à la demande.

## Bases de données (Oracle / PostgreSQL)

- Compte applicatif à privilèges minimaux : `CREATE SESSION` + objets nécessaires — jamais de rôle DBA ou SUPERUSER.
- PostgreSQL : `pg_hba.conf` restreint par réseau, TLS, rôles sans SUPERUSER. Oracle : rôles granulaires, pas de `GRANT ANY`.
- Sauvegardes testées par une restauration réelle, pas seulement par l'existence d'un dump. Chiffrement au repos si exigé (TDE Oracle, disque chiffré PG).

## Rapport d'audit — modèle imposé

```
# Audit de sécurité — <périmètre>
## Synthèse (3-5 lignes : niveau de risque global, nb de constats par sévérité)
## Constats
[P-SÉVÉRITÉ] Titre — fichier:ligne
  Constat : ce qui est observé (extrait de code)
  Risque : scénario d'exploitation concret
  Remédiation : correction recommandée, effort estimé
## Points positifs (ce qui est bien fait — toujours les lister)
## Plan de remédiation priorisé
```

Sévérités : **Critique** (exploitable à distance, données compromises) / **Majeur** (exploitation conditionnée, impact fort) / **Modéré** (durcissement) / **Info** (bonne pratique).

## Règles de conduite

- Ne jamais créer ni exécuter de code d'exploitation réel ; la démonstration de risque reste descriptive.
- Chaque constat cite fichier et ligne ; une suspicion non vérifiable est marquée « à confirmer ».
- Remédiation toujours proposée : un constat sans correctif est incomplet.
