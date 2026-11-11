# Refactor d'Agent MD

Une fonctionnalité qui transforme les fichiers d'instructions d'agents volumineux en documentation claire et organisée selon les principes de divulgation progressive.

Basé sur https://x.com/mattpocockuk/status/2012906065856270504 (L'idée de Prompt de Matt Pocock)

## Purpose

Avec le temps, les fichiers d'instructions d'agents comme `CLAUDE.md`, `AGENTS.md` ou `COPILOT.md` ont tendance à devenir des documents encombrés contenant des centaines de lignes d'instructions mélangées. Cela pose plusieurs problèmes :

- **Gaspillage de contexte** : Chaque tâche charge le fichier entier, même lorsque la plupart des instructions sont irrelevantes
- **Charge de maintenance** : Trouver et mettre à jour des instructions spécifiques devient difficile
- **Contradictions** : Les directives conflictuelles s'accumulent sans être remarquées
- **Rapport signal/bruit** : Les règles importantes se retrouvent enfouies parmi des déclarations évidentes ou imprécises

Cette fonctionnalité résout ces problèmes en appliquant la **divulgation progressive** - en ne conservant que les instructions essentielles et universelles dans le fichier racine tout en organisant tout le reste dans des fichiers de documentation liés et ciblés.

## Quand l'utiliser

Utilisez cette fonctionnalité lorsque vous devez nettoyer les fichiers d'instructions d'agents. Les déclencheurs courants incluent :

- "refactor my AGENTS.md" / "refactor my CLAUDE.md"
- "split my agent instructions"
- "organize my CLAUDE.md file"
- "my AGENTS.md is too long"
- "progressive disclosure for my instructions"
- "clean up my agent config"

**Bonnes candidat pour le refactor :**

- Fichiers racine dépassant 50-100 lignes
- Fichiers mélangeant plusieurs sujets non liés (testing, style de code, architecture, etc.)
- Documents qui ont grandi organiquement sans structure
- Fichiers contenant des instructions contradictoires ou redondantes

## Fonctionnement

La fonctionnalité suit un processus systématique en 5 phases :

### Phase 1 : Trouver les Contradictions

Avant le restructuring, la fonctionnalité identifie les instructions conflictuelles qui doivent être résolues. Les exemples incluent des directives de style contradictoires ("utiliser des points-virgules" vs "pas de points-virgules") ou des instructions de workflow incompatibles. Chaque contradiction est affichée avec une question pour que l'utilisateur la résolve.

### Phase 2 : Identifier l'Essentiel

Extrait uniquement ce qui appartient vraiment au fichier racine - les informations qui s'appliquent à chaque seule tâche :

| Garder dans le Racine | Déplacer Ailleurs |
|-------------|----------|
| Description du projet en une phrase | Conventions spécifiques au langage |
| Gestionnaire de paquets non-standard | Directives de test |
| Commandes de build/test personnalisées | Détails du style de code |
| Overrides critiques | Patterns de framework |
| Règles universelles (100% des tâches) | Normes de documentation |

### Phase 3 : Regrouper le Reste

Organise les instructions restantes en catégories logiques comme :

- `typescript.md` - Patterns de type, règles du mode strict
- `testing.md` - Frameworks de test, couverture, mocking
- `code-style.md` - Formatage, nommage, structure
- `git-workflow.md` - Commits, branches, PRs
- `architecture.md` - Patterns, structure des dossiers

### Phase 4 : Créer la Structure de Fichiers

Génère la nouvelle hiérarchie de fichiers avec la documentation correctement liée :

```
project-root/
 ├── CLAUDE.md              # Racine minimal avec liens
 └── .claude/               # Instructions catégorisées
     ├── typescript.md
     ├── testing.md
     ├── code-style.md
     └── architecture.md
```

### Phase 5 : Marquer pour Suppression

Identifie les instructions qui doivent être supprimées entièrement :

- **Redondant** : "Utiliser TypeScript" dans un projet TypeScript
- **Trop vague** : "Écrire du code propre" sans spécificités
- **L'évidence même** : "Ne pas introduire de bugs"
- **Comportement par défaut** : "Utiliser des noms de variables descriptifs"
- **Périmé** : Références à des API dépréciées

## Fonctionnalités Clés

- **Détection de contradictions** : Affiche les instructions conflictuelles avant le restructuring
- **Catégorisation intelligente** : Regroupe les instructions liées dans des fichiers logiques
- **Minimisation du fichier racine** : Cible moins de 50 lignes pour le fichier principal
- **Recommandations de suppression** : Identifie les instructions gaspillant des tokens de contexte
- **Sortie basée sur des templates** : Structure cohérente dans tous les fichiers générés
- **Vérification des liens** : Assure que toutes les références entre fichiers sont valides

## Exemples d'Utilisation

### Refactor de Base

```
Utilisateur : refactor my CLAUDE.md

Kilo : Je vais analyser votre fichier CLAUDE.md et le refactoriser en utilisant les principes
de divulgation progressive...
```

### Fichier Spécifique

```
Utilisateur : my AGENTS.md is too long, can you split it up?

Kilo : Je vais examiner votre AGENTS.md et l'organiser en fichiers liés et ciblés...
```

### Après la Croissance d'un Projet

```
Utilisateur : organize my agent config - it's gotten out of control

Kilo : J'applique le processus de refactor en 5 phases pour nettoyer vos
instructions d'agent...
```

## Sortie

Après avoir exécuté la fonctionnalité, vous obtenez :

**Fichier racine minimal (~50 lignes ou moins) :**
```markdown
# Nom du Projet

Description en une phrase du projet.

## Référence Rapide

- **Gestionnaire de Paquets :** pnpm
- **Build :** `pnpm build`
- **Test :** `pnpm test`

## Instructions Détaillées

- [Conventions TypeScript](.claude/typescript.md)
- [Directives de Test](.claude/testing.md)
- [Style de Code](.claude/code-style.md)
```

**Fichiers liés organisés avec structure cohérente :**
```markdown
# Directives de Test

## Aperçu
Contexte briève pour quand ces directives s'appliquent.

## Règles

### Tests Unitaires
- Instruction spécifique et actionnable
- Autre instruction spécifique

## Exemples

### Bien
[exemple de code]

### À Éviter
[exemple de code]
```

**Rapport de Suppression :**
```markdown
## Marqué pour Suppression

| Instruction | Raison |
|-------------|--------|
| "Écrire du code propre et maintenable" | Trop vague pour être actionnable |
| "Utiliser TypeScript" | Redondant - le projet est déjà en TS |
```

## Meilleures Pratiques

### Avant le Refactor

1. **Commiter l'état actuel** - Avoir un état git propre pour pouvoir revoir les changements
2. **Identifier vos objectifs** - Savoir quels problèmes vous voulez résoudre
3. **Rassembler tous les fichiers d'instructions** - Certains projets ont des instructions dispersées dans plusieurs emplacements

### Pendant le Refactor

1. **Résoudre d'abord les contradictions** - Ne pas procéder tant que les conflits ne sont pas adressés
2. **Être agressif dans la minimisation du racine** - En doute, déplacez-le ailleurs
3. **Viser 3-8 fichiers liés** - Ni trop granulaire, ni trop large
4. **Supprimer généreusement** - Les instructions vagues gaspillent des tokens sans apporter de valeur

### Après le Refactor

1. **Vérifier que tous les liens fonctionnent** - Tester que les fichiers référencés existent
2. **Vérifier qu'aucune instruction n'a été perdue** - S'assurer que rien d'important n'a été oublié
3. **Tester avec de vraies tâches** - Exécuter quelques tâches typiques pour vérifier que l'agent peut trouver les instructions nécessaires

## Anti-Modèles à Éviter

| À Éviter | Pourquoi | À la Place |
|-------|-----|---------|
| Garder tout dans le racine | Volumineux, difficile à maintenir | Diviser en fichiers liés |
| Trop de catégories | Fragmentation, surcharge de navigation | Consolidation des sujets liés |
| Instructions vagues | Gaspille des tokens, sans valeur | Être spécifique ou supprimer |
| Duplication des défauts | L'agent le sait déjà | Remplacer seulement quand nécessaire |
| Nesting profond | Difficile à naviguer | Structure plate avec liens |

## Liste de Vérification de Vérification

Après le refactor, vérifier :

- [ ] Le fichier racine fait moins de 50 lignes
- [ ] Le racine contient UNIQUEMENT des informations universelles
- [ ] Tous les liens vers les sous-fichiers fonctionnent correctement
- [ ] Aucune contradiction ne subsiste entre les fichiers
- [ ] Chaque instruction est spécifique et actionnable
- [ ] Aucune instruction n'a été perdue (sauf suppression intentionnelle)
- [ ] Chaque fichier lié est autonome pour son sujet

## Licence

MIT