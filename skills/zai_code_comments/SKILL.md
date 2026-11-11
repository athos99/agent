---
name: zai_code_comments
description: Génère et normalise les commentaires de code — PHPDoc pour PHP/Symfony, JSDoc pour JavaScript, commentaires d'intention, TODO tracés avec tickets — pour tout code écrit ou modifié. Utiliser dès que l'utilisateur demande d'ajouter des commentaires, de documenter des classes/méthodes/fonctions, d'harmoniser la documentation en ligne, ou par défaut sur tout bloc de code livré sans explication suffisante.
---

# Commentaires de code — PHPDoc et JSDoc

Principe fondateur : le meilleur code se documente lui-même (noms clairs, types, petites fonctions). Le commentaire explique alors **pourquoi**, jamais **quoi**. Un commentaire qui paraphrase le code est du bruit qui se périme ; un commentaire qui explique une contrainte invisible est un actif.

## Ce qu'on commente, ce qu'on ne commente pas

| On commente | On ne commente pas |
|---|---|
| Le but d'une classe et son périmètre | Ce que dit déjà la signature |
| Les contraintes non visibles (délai tiers, règle légale, ticket) | Les setters/getters évidents |
| Les effets de bord et conditions limites | L'historique des modifications (c'est le rôle de Git) |
| Les workarounds : pourquoi le code « étrange » est nécessaire | Le code mort commenté (on le supprime) |
| Les pièges métier (ex. : Oracle `''` = `NULL`) | Les `// incrément de i` |

## PHPDoc (PHP / Symfony)

- Toute classe publique : 1 à 3 lignes décrivant le **rôle métier**, pas la mécanique.
- Toute méthode publique ou complexe : `@param` avec types réels (y compris `?Type` nullable), `@return`, `@throws` avec le **quand** de l'exception.
- Les types sont déjà dans les signatures PHP modernes : le PHPDoc ajoute la sémantique (unités, formats, invariants), pas la répétition.
- Modèle :
  ```php
  /**
   * Calcule le montant de caution exigé à la réservation.
   *
   * La règle est asymétrique : gratuit => aucune caution, sinon caution standard
   * doublée pour les locataires sans historique (règle RG-007, ticket ZA-231).
   *
   * @return Money Montant positif, zéro si exonéré
   * @throws UnavailableResourceException si le bien est déjà réservé sur la période
   */
  ```

## JSDoc (JavaScript)

- Fonctions exportées et contrôleurs Stimulus : `@param`, `@returns`, `@throws` quand pertinent ; types en JSDoc (`@typedef`) si le projet n'est pas en TypeScript.
- `@async` implicite : documenter ce que la promesse résout et les erreurs possibles, car les erreurs perdues sont la faute n°1 du JS.
- Modèle :
  ```js
  /**
   * Recherche les dossiers pour la vue liste.
   * @param {Object} filters Filtres actifs (statut, période) — null = tous
   * @param {AbortSignal} [signal] Annulation du changement d'onglet
   * @returns {Promise<Folder[]>} Triés par date décroissante (côté serveur)
   * @throws {ApiError} 403 si le dossier n'appartient pas à l'utilisateur
   */
  ```

## Commentaires en ligne

- Forme « pourquoi » : `// Retry : l'API Oracle renvoie parfois ORA-02049 sous charge (ticket ZA-88)`.
- Les TODO sont tracés et assignés : `// TODO(zai): à remplacer par Messenger quand ZA-345 sera livré` — jamais de TODO sans périmètre ni auteur.
- Les FIXME signalent un défaut connu actif, les NOTE un invariant fragile.
- Un commentaire précède la ligne qu'il éclaire, jamais en fin de ligne au milieu d'un bloc dense.

## Langue et style

- Langue des commentaires : suivre la convention existante du projet ; à défaut, **français** avec identifiants de code en anglais (la langue des réponses à l'utilisateur s'applique).
- Phrases complètes terminées par un point. Pas d'abréviations obscures, pas d'ASCII art, pas de bannières décoratives.
- Une information = un seul endroit : ne pas dupliquer la même explication dans la méthode appelée et la méthode appelante.

## Comment commenter du code existant à la demande

1. Lire la méthode, puis ses **appelants** et ses **dépendances** : beaucoup de « pourquoi » vivent chez les appelants.
2. Chercher le contexte : tickets cités dans le voisinage, tests (ils révèlent les cas limites attendus), git log si disponible.
3. Documenter d'abord les entrées publiques de la classe, puis les méthodes complexes, puis les invariants. S'arrêter là : noircir tout est contre-productif.
4. Si le code semble faux ou contradictoire avec son nom, ne pas « documenter le doute » : signaler la suspicion à l'utilisateur — commenter n'est pas légitimer.
5. Proposer le diff final en montrant les commentaires ajoutés, avec un résumé des zones volontairement laissées sans commentaire et pourquoi.
