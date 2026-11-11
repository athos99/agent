---
name: agent-md-refactor
description: >-
  Refactoriser les fichiers d'instructions agent volumineux (AGENTS.md, CLAUDE.md,
  ou similaires) pour suivre les principes de divulgation progressive.
  Divise les fichiers monolithiques en documentation organisée et reliée.
metadata:
  category: development
  source:
    repository: 'https://github.com/softaworks/agent-toolkit'
    path: skills/agent-md-refactor
    license_path: LICENSE
    commit: 3027f20f3181758385a1bb8c022d4041dfb4de84
---

# Agent MD Refactor

Refactoriser les fichiers d'instructions agent volumineux (AGENTS.md, CLAUDE.md, COPILOT.md, etc.) pour suivre les **principes de divulgation progressive** - conserver l'essentiel au niveau racine et organiser le reste dans des fichiers liés et catégorisés.

---

## Déclencheurs

Utiliser cette compétence lorsque :
- "refactorise mon AGENTS.md" / "refactorise mon CLAUDE.md"
- "divise mes instructions agent"
- "organise mon fichier CLAUDE.md"
- "mon AGENTS.md est trop long"
- "divulgation progressive pour mes instructions"
- "nettoie ma configuration agent"

---

## Référence Rapide

| Phase | Action | Sortie |
|-------|--------|--------|
| 1. Analyser | Trouver les contradictions | Liste des conflits à résoudre |
| 2. Extraire | Identifier l'essentiel | Instructions de base pour le fichier racine |
| 3. Catégoriser | Regrouper les instructions restantes | Catégories logiques |
| 4. Structurer | Créer l'arborescence des fichiers | Racine + fichiers liés |
| 5. Élaguer | Signaler pour suppression | Instructions redondantes/vagues |

---

## Processus

### Phase 1 : Trouver les Contradictions

Identifier les instructions qui entrent en conflit les unes avec les autres.

**Rechercher :**
- Des directives de style contradictoires (ex. "utiliser des points-virgules" vs "pas de points-virgules")
- Des instructions de workflow conflictuelles
- Des préférences d'outils incompatibles
- Des modèles mutuellement exclusifs

**Pour chaque contradiction trouvée :**
```markdown
## Contradiction Trouvée

**Instruction A :** [cite]
**Instruction B :** [cite]

**Question :** Laquelle doit primer, ou les deux doivent-elles être conditionnelles ?
```

Demander à l'utilisateur de résoudre avant de continuer.

---

### Phase 2 : Identifier l'Essentiel

Extraire UNIQUEMENT ce qui appartient au fichier agent racine. Le fichier racine doit être minimal - l'information qui s'applique à **chaque tâche sans exception**.

**Contenu essentiel (conserver dans la racine) :**
| Catégorie | Exemple |
|-----------|---------|
| Description du projet | Une phrase : "Un tableau de bord React pour l'analyse" |
| Gestionnaire de paquets | Uniquement si ce n'est pas npm (ex. "Utilise pnpm") |
| Commands non standards | Build/test/typecheck personnalisés |
| Overrides critiques | Ce qui DOIT remplacer les valeurs par défaut |
| Règles universelles | S'applique à 100% des tâches |

**NON essentiel (déplacer vers des fichiers liés) :**
- Conventions spécifiques aux langages
- Directives de test
- Détails du style de code
- Patterns de framework
- Normes de documentation
- Détails du workflow git

---

### Phase 3 : Regrouper le Reste

Organiser les instructions restantes en catégories logiques.

**Catégories courantes :**
| Catégorie | Contenu |
|-----------|---------|
| `typescript.md` | Conventions TS, patterns de type, règles du mode strict |
| `testing.md` | Frameworks de test, couverture, patterns de mocking |
| `code-style.md` | Formatage, nommage, commentaires, structure |
| `git-workflow.md` | Commits, branches, PRs, revues |
| `architecture.md` | Patterns, structure des dossiers, dépendances |
| `api-design.md` | Conventions REST/GraphQL, gestion d'erreurs |
| `security.md` | Patterns d'authentification, validation des entrées, secrets |
| `performance.md` | Règles d'optimisation, cache, chargement différé |

**Règles de regroupement :**
1. Chaque fichier doit être autonome pour son sujet
2. Visez 3-8 fichiers (ni trop granulaires, ni trop larges)
3. Nommer les fichiers clairement : `{sujet}.md`
4. Inclure uniquement les instructions actionnables

---

### Phase 4 : Créer la Structure de Fichiers

**Structure de sortie :**
```
racine-projet/
├── CLAUDE.md (ou AGENTS.md)     # Racine minimal avec liens
└── .claude/                      # Ou docs/agent-instructions/
    ├── typescript.md
    ├── testing.md
    ├── code-style.md
    ├── git-workflow.md
    └── architecture.md
```

**Modèle de fichier racine :**
```markdown
# Nom du Projet

Description en une phrase du projet.

## Référence Rapide

- **Gestionnaire de Paquets :** pnpm
- **Build :** `pnpm build`
- **Test :** `pnpm test`
- **Typecheck :** `pnpm typecheck`

## Instructions Détaillées

Pour des directives spécifiques, voir :
- [Conventions TypeScript](.claude/typescript.md)
- [Directives de Test](.claude/testing.md)
- [Style de Code](.claude/code-style.md)
- [Workflow Git](.claude/git-workflow.md)
- [Patterns d'Architecture](.claude/architecture.md)
```

**Modèle de fichier lié :**
```markdown
# Directives {Sujet}

## Aperçu
Contexte bref pour quand ces directives s'appliquent.

## Règles

### Catégorie de Règle 1
- Instruction spécifique et actionnable
- Autre instruction spécifique

### Catégorie de Règle 2
- Instruction spécifique et actionnable

## Exemples

### Correct
\`\`\`typescript
// Exemple de pattern correct
\`\`\`

### À Éviter
\`\`\`typescript
// Exemple de ce qu'il ne faut pas faire
\`\`\`
```

---

### Phase 5 : Signaler pour Suppression

Identifier les instructions qui devraient être supprimées entièrement.

**Supprimer si :**
| Critère | Exemple | Pourquoi Supprimer |
|---------|---------|-------------------|
| Redondant | "Utiliser TypeScript" (dans un projet .ts) | L'agent le sait déjà |
| Trop vague | "Écrire du code propre" | Non actionnable |
| Trop évident | "Ne pas introduire de bugs" | Perd du contexte |
| Comportement par défaut | "Utiliser des noms de variables descriptifs" | Pratique standard |
| Obsolète | Fait référence à des API dépréciées | Ne s'applique plus |

**Format de sortie :**
```markdown
## Signalé pour Suppression

| Instruction | Raison |
|-------------|--------|
| "Écrire du code propre et maintenable" | Trop vague pour être actionnable |
| "Utiliser TypeScript" | Redondant - le projet est déjà en TS |
| "Ne pas committer de secrets" | L'agent le sait déjà |
| "Suivre les bonnes pratiques" | Sans signification sans spécificités |
```

---

## Liste de Vérification d'Exécution

```
[ ] Phase 1 : Toutes les contradictions identifiées et résolues
[ ] Phase 2 : Le fichier racine contient UNIQUEMENT l'essentiel
[ ] Phase 3 : Toutes les instructions restantes catégorisées
[ ] Phase 4 : Structure de fichiers créée avec les liens appropriés
[ ] Phase 5 : Instructions redondantes/vagues supprimées
[ ] Vérifier : Chaque fichier lié est autonome
[ ] Vérifier : Le fichier racine fait moins de 50 lignes
[ ] Vérifier : Tous les liens fonctionnent correctement
```

---

## Anti-Patterns

| À Éviter | Pourquoi | Au lieu de cela |
|----------|----------|-----------------|
| Tout conserver dans la racine | Volumineux, dur à maintenir | Diviser en fichiers liés |
| Trop de catégories | Fragmentation | Consulider les sujets similaires |
| Instructions vagues | Perd des tokens, aucune valeur | Être spécifique ou supprimer |
| Dupliquer les valeurs par défaut | L'agent le sait déjà | Ne remplacer que si nécessaire |
| Nesting profond | Dur à naviguer | Structure plate avec liens |

---

## Exemples

### Avant (Racine Volumineuse)
```markdown
# CLAUDE.md

Ceci est un projet React.

## Style de Code
- Utiliser 2 espaces
- Utiliser des points-virgules
- Préférer const à let
- Utiliser les flèches
... (200 lignes de plus)

## Test
- Utiliser Jest
- Couverture > 80%
... (100 lignes de plus)

## TypeScript
- Activer le mode strict
... (150 lignes de plus)
```

### Après (Divulgation Progressive)
```markdown
# CLAUDE.md

Tableau de bord React pour la visualisation d'analyse en temps réel.

## Commands
- `pnpm dev` - Démarrer le serveur de développement
- `pnpm test` - Lancer les tests avec couverture
- `pnpm build` - Build de production

## Directives
- [Style de Code](.claude/code-style.md)
- [Test](.claude/testing.md)
- [TypeScript](.claude/typescript.md)
```

---

## Vérification

Après le refactoring, vérifier :

1. **Le fichier racine est minimal** - Moins de 50 lignes, uniquement l'information universelle
2. **Les liens fonctionnent** - Tous les fichiers référencés existent
3. **Pas de contradictions** - Les instructions sont cohérentes
4. **Contenu actionnable** - Chaque instruction est spécifique
5. **Couverture complète** - Aucune instruction n'a été perdue (sauf si signalée pour suppression)
6. **Fichiers autonomes** - Chaque fichier lié fonctionne de manière autonome

---
