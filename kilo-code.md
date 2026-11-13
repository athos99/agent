# Organisation des répertoires d'un agent IA et configuration de Kilo Code

Pour bien organiser un agent IA de développement, notamment pour PHP, Symfony, Doctrine et JavaScript, je te conseille de distinguer les connaissances, les règles de comportement, le contexte du projet et les capacités opérationnelles de l'agent.

Les quatre répertoires que tu mentionnes correspondent à quatre responsabilités différentes. Ils sont complémentaires et ne doivent pas être utilisés indistinctement.

## 1. À quoi correspondent les quatre répertoires ?

skill — Les compétences

Explique à l'agent comment réaliser une tâche particulière. C'est une procédure ou une expertise réutilisable.

Exemples :

- Analyser la sécurité d'une application Symfony.
- Générer des tests PHPUnit.
- Réaliser une revue de code.
- Documenter une API REST.

rule — Les règles

Définit les obligations, interdictions et conventions que l'agent doit respecter.

Exemples :

- Respecter PSR-12.
- Ne jamais exposer de secrets.
- Utiliser les repositories Doctrine.
- Vérifier les autorisations avec des Voters.
- Ne pas modifier une API publique sans justification.

context — Le contexte

Fournit les informations nécessaires pour comprendre l'environnement dans lequel l'agent travaille.

Exemples :

- Architecture de l'application.
- Versions PHP et Symfony.
- Organisation des bases Oracle.
- Architecture des API.
- Choix techniques et décisions d'architecture.
- Glossaire métier.

agent — Les agents spécialisés

Définit des assistants spécialisés, avec leur rôle, leurs responsabilités, leurs objectifs et éventuellement leurs outils.

Exemples :

- Agent architecte Symfony.
- Agent développeur PHP.
- Agent expert sécurité AppSec.
- Agent QA / tests.
- Agent documentaliste technique.

### La différence fondamentale

Prenons un exemple : tu demandes à l'IA d'analyser une application Symfony.

| Répertoire | Son rôle                                            |
| ---------- | --------------------------------------------------- |
| `skill`    | Comment réaliser un audit de sécurité               |
| `rule`     | Les exigences de sécurité à respecter               |
| `context`  | Comment est construite cette application            |
| `agent`    | Qui réalise l'audit et avec quelles responsabilités |

Un bon agent utilise les quatre ensemble. Il ne faut pas mettre toute la connaissance dans les instructions de l'agent lui-même.

## 2. Existe-t-il d'autres répertoires utiles ?

Oui. Pour une bibliothèque sérieuse d'agents de développement, je recommande de connaître également les éléments suivants.

| Élément                 | Fonction                                           | Exemple                           |
| ----------------------- | -------------------------------------------------- | --------------------------------- |
| `workflow` / `commands` | Enchaîner plusieurs opérations                     | Audit complet d'une application   |
| `templates`             | Modèles de documents à produire                    | Documentation technique, ADR      |
| `references`            | Documentation technique consultable par les skills | Documentation Symfony, OWASP      |
| `scripts`               | Scripts exécutables par l'agent                    | Analyse PHPStan, contrôle qualité |
| `tests`                 | Vérifications et exemples de validation            | Tests d'un skill ou d'un script   |
| `memory`                | Informations persistantes et décisions antérieures | Historique des décisions          |
| `config`                | Configuration de l'environnement                   | Modèles, permissions, paramètres  |
| `docs`                  | Documentation produite par l'agent                 | Audit, architecture, guides       |

Attention : ces répertoires ne sont pas tous des répertoires officiellement reconnus par chaque outil. Certains sont des conventions d'organisation que tu peux créer librement.

### Architecture logique recommandée

Voici comment je structurerais une bibliothèque indépendante de l'outil utilisé :

```
AI-Development/
│
├── agents/
│   ├── php-developer.md
│   ├── symfony-architect.md
│   ├── appsec-expert.md
│   ├── qa-engineer.md
│   └── technical-writer.md
│
├── skills/
│   ├── php/
│   ├── symfony/
│   ├── doctrine/
│   ├── javascript/
│   ├── security/
│   ├── testing/
│   └── documentation/
│
├── rules/
│   ├── general/
│   ├── php/
│   ├── symfony/
│   ├── security/
│   └── documentation/
│
├── context/
│   ├── architecture/
│   ├── technical/
│   ├── security/
│   ├── business/
│   └── glossary/
│
├── workflows/
│   ├── code-review.md
│   ├── security-audit.md
│   └── generate-documentation.md
│
├── templates/
│   ├── architecture/
│   ├── audit/
│   └── documentation/
│
├── references/
│   ├── php/
│   ├── symfony/
│   └── owasp/
│
└── scripts/
    ├── php/
    └── security/
```

Cette organisation sépare les ressources réutilisables de la configuration propre à un outil comme Kilo Code.

## 3. Les particularités de Kilo Code

Kilo Code possède ses propres mécanismes. Il ne suffit donc pas de créer les quatre répertoires précédents pour que tout soit automatiquement reconnu.

Il faut distinguer plusieurs notions.

### A. Les Skills

Chargement à la demande

Un skill est généralement un répertoire contenant un fichier `SKILL.md`.

Exemple :

```
.kilo/
└── skills/
    ├── ChatGpt_Symfony_Architecture/
    │   ├── SKILL.md
    │   ├── references/
    │   │   ├── doctrine.md
    │   │   └── architecture.md
    │   ├── templates/
    │   └── scripts/
    │
    └── ChatGpt_PHP_Security/
        └── SKILL.md
```

Le fichier `SKILL.md` possède des métadonnées, notamment :

```
---
name: ChatGpt_Symfony_Architecture
description: Analyse et conception d'architectures Symfony
---

# Objectif

...

# Méthodologie

...

# Exemples

...
```

Particularité importante : Kilo ne charge pas nécessairement l'intégralité de tous les skills dans le contexte de l'IA.

Il découvre leurs descriptions, puis décide quel skill utiliser selon la demande. Le contenu détaillé est chargé lorsque le skill est invoqué.

Cela permet de disposer d'une bibliothèque importante sans encombrer systématiquement le contexte du modèle.

Dans la nouvelle version de Kilo Code, les skills sont partagés entre les agents : on ne crée plus un répertoire de skills distinct pour chaque mode.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://kilo.ai\&sz=32)

Kilo+1



### B. Les Rules

Instructions permanentes

Les règles sont des instructions qui encadrent le comportement de l'agent.

Exemple :

```
.kilo/
└── rules/
    ├── 01-general.md
    ├── 02-php.md
    ├── 03-symfony.md
    ├── 04-doctrine.md
    └── 05-security.md
```

Attention : avec la version actuelle de Kilo Code, il faut déclarer les fichiers de règles dans `kilo.jsonc`, via la propriété `instructions`.

Exemple :

```

{
  "instructions": [
    ".kilo/rules/*.md",
    ".kilo/rules/**/*.md"
  ]
}

```

Tu peux également utiliser des règles globales dans la configuration utilisateur.

Il faut retenir que les règles ne fonctionnent pas exactement comme les skills : elles sont destinées à être appliquées de façon permanente dans leur périmètre, alors que les skills sont sélectionnés selon les besoins.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://kilo.ai\&sz=32)

Kilo+1



### C. Les Agents

Spécialisation et permissions

Dans Kilo Code, les agents personnalisés sont appelés Custom Agents ou Custom Modes selon la version et l'interface.

Dans la nouvelle extension VS Code, ils sont définis sous forme de fichiers Markdown avec des métadonnées YAML.

Exemple :

```
.kilo/
└── agents/
    ├── symfony-architect.md
    ├── php-developer.md
    ├── appsec-expert.md
    └── documentation-writer.md
```

Un agent peut notamment définir :

- Son rôle et ses objectifs.
- Son modèle IA.
- Ses instructions spécifiques.
- Ses outils autorisés.
- Ses permissions de lecture et d'écriture.
- Son droit d'appeler des sous-agents.

Exemple simplifié :

```

---
name: symfony-architect
description: Expert en architecture Symfony et Doctrine
mode: primary
---

Tu es un architecte logiciel spécialisé
dans PHP et Symfony.

Tu analyses les architectures existantes,
identifies les problèmes et proposes
des solutions maintenables et sécurisées.

Tu privilégies Symfony classique,
Doctrine ORM et une architecture claire.

```

Une particularité intéressante : Kilo permet de créer des agents secondaires (subagents). Tu peux par exemple avoir un agent architecte qui délègue l'analyse de sécurité à un agent AppSec et les tests à un agent QA.

Les permissions sont également importantes : un agent de revue de code peut être limité à la lecture seule, tandis qu'un agent développeur peut avoir le droit de modifier les fichiers.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://kilo.ai\&sz=32)

Kilo+1



### D. Les Workflows / Commands

C'est un élément particulièrement intéressant pour ton utilisation.

Un workflow permet de définir une procédure reproductible, déclenchée par une commande `/`.

Exemple :

```
.kilo/
└── commands/
    ├── audit-security.md
    ├── review-code.md
    ├── generate-api-doc.md
    └── analyze-project.md
```

Tu pourrais lancer :

```
/audit-security
```

Et demander à Kilo de suivre une procédure structurée :

1. Analyser l'architecture.
2. Identifier les points sensibles.
3. Examiner les contrôleurs.
4. Examiner les Voters.
5. Contrôler les accès Doctrine.
6. Identifier les vulnérabilités.
7. Produire un rapport.

Les workflows sont particulièrement adaptés aux tâches complexes que tu souhaites reproduire régulièrement.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://kilo.ai\&sz=32)

Kilo



## 4. Où placer les répertoires dans Kilo Code ?

C'est probablement la partie la plus importante pour ton organisation.

Kilo distingue deux niveaux :

- Global (utilisateur) : ressources disponibles dans tous tes projets.
- Projet (workspace) : ressources spécifiques à une application.

### A. Installation globale

Sous Ubuntu/Linux, voici les emplacements à utiliser :

Configuration globale

Répertoire utilisateur : \~/.config/kilo/ et \~/.kilo/

```

~/.config/kilo/
│
├── AGENTS.md
├── kilo.jsonc
│
├── agent/
│   ├── symfony-architect.md
│   ├── php-developer.md
│   └── appsec-expert.md
│
└── commands/
    ├── audit-security.md
    └── review-code.md


~/.kilo/
└── skills/
    ├── ChatGpt_PHP_Standards/
    │   └── SKILL.md
    ├── ChatGpt_Symfony_Architecture/
    │   └── SKILL.md
    ├── ChatGpt_Doctrine/
    │   └── SKILL.md
    └── ChatGpt_AppSec/
        └── SKILL.md

```

Réutilisable dans tous les projets

Pour les règles globales, utilise les fichiers Markdown référencés par le `kilo.jsonc` global, via `instructions`.

Par exemple, tu peux conserver les règles dans `~/.config/kilo/rules/` et les déclarer dans cette configuration.

### B. Installation au niveau d'un projet

Voici l'organisation que je recommande pour un projet Symfony.

MonProjetSymfony/

```

MonProjetSymfony/
│
├── AGENTS.md
├── CONTEXT.md
├── kilo.jsonc
│
├── .kilo/
│   │
│   ├── agents/
│   │   ├── project-architect.md
│   │   └── project-developer.md
│   │
│   ├── rules/
│   │   ├── project.md
│   │   ├── doctrine.md
│   │   ├── oracle.md
│   │   └── security.md
│   │
│   ├── skills/
│   │   └── project-specific/
│   │       └── SKILL.md
│   │
│   └── commands/
│       ├── analyze-project.md
│       └── generate-documentation.md
│
├── docs/
│   ├── architecture/
│   ├── technical/
│   ├── api/
│   └── decisions/
│
└── src/

```

Spécifique au projet

Quelques précisions :

- `AGENTS.md` : instructions générales propres au projet.
- `CONTEXT.md` : informations complémentaires sur le projet.
- `kilo.jsonc` : configuration Kilo, notamment les règles et les chemins de ressources.
- `.kilo/agents/` : agents spécialisés dans ce projet.
- `.kilo/rules/` : règles spécifiques à cette application.
- `.kilo/skills/` : compétences propres à cette application.
- `.kilo/commands/` : procédures spécifiques.

Kilo reconnaît également les fichiers `AGENTS.md` dans les sous-répertoires. Cela permet d'avoir des instructions complémentaires dans certains modules sans surcharger les instructions générales.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://github.com\&sz=32)

GitHub+1



### C. Tableau récapitulatif des emplacements

| Ressource    | Global                                       | Projet                        |
| ------------ | -------------------------------------------- | ----------------------------- |
| Agents       | `~/.config/kilo/agent/`                      | `.kilo/agents/`               |
| Skills       | `~/.kilo/skills/`                            | `.kilo/skills/`               |
| Rules        | `~/.config/kilo/rules/` + `kilo.jsonc`       | `.kilo/rules/` + `kilo.jsonc` |
| Instructions | `~/.config/kilo/AGENTS.md`                   | `AGENTS.md`                   |
| Context      | Instructions globales et fichiers référencés | `CONTEXT.md` et `docs/`       |
| Workflows    | `~/.config/kilo/commands/`                   | `.kilo/commands/`             |
| Templates    | Bibliothèque personnelle                     | `docs/templates/`             |
| Scripts      | Bibliothèque personnelle                     | `scripts/`                    |

Attention : `CONTEXT.md`, `docs/`, `templates/` et `scripts/` sont des conventions d'organisation. Ils ne constituent pas tous des mécanismes de chargement automatique de Kilo. Pour les instructions de contexte, privilégie `AGENTS.md` et les références explicitement chargées.

Les emplacements ci-dessus correspondent à la nouvelle génération de Kilo Code pour VS Code. Les anciennes installations utilisaient notamment `.kilocode/`, `.kilocodemodes` et `custom_modes.yaml`. Il vaut mieux ne pas mélanger les deux générations.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://kilo.ai\&sz=32)

Kilo+2



## 5. Ma recommandation pour ta bibliothèque de développement

Compte tenu de ton objectif de créer une bibliothèque réutilisable pour Symfony, PHP, Doctrine, JavaScript, AppSec et documentation technique, je te conseille une architecture en trois niveaux.

Niveau 1

Bibliothèque commune

`~/.kilo/skills/` et `~/.config/kilo/`

Toutes les compétences, les agents et les connaissances génériques réutilisables.

Niveau 2

Configuration du projet

`.kilo/` et `AGENTS.md`

Les règles, compétences et agents adaptés à l'application en cours.

Niveau 3

Documentation du projet

`docs/` et `CONTEXT.md`

Les spécifications métier, l'architecture, les décisions et les documentations produites.

### Comment répartir les responsabilités ?

Voici un exemple concret pour ton environnement Symfony :

| Besoin                                            | Ressource                                 |
| ------------------------------------------------- | ----------------------------------------- |
| Respecter les bonnes pratiques PHP                | Rule globale                              |
| Connaître les mécanismes internes de Symfony      | Skill global                              |
| Analyser une architecture Symfony                 | Skill global                              |
| Réaliser un audit AppSec                          | Skill global                              |
| Connaître les deux bases Oracle du projet         | Context projet                            |
| Appliquer les règles de sécurité de l'application | Rule projet                               |
| Concevoir une nouvelle API REST                   | Agent architecte                          |
| Effectuer une revue de code                       | Workflow                                  |
| Générer la documentation pour un développeur      | Skill documentation                       |
| Générer une documentation pour le Product Owner   | Skill documentation avec un profil métier |
| Produire un rapport d'architecture                | Template + skill                          |

### 6. Quelques particularités importantes à retenir

1. Ne multiplie pas les agents inutilement. Un agent est un profil de travail, pas une bibliothèque de connaissances.
2. Privilégie les skills réutilisables. Un skill bien construit peut être utilisé par plusieurs agents.
3. Les rules doivent rester concises et impératives. Évite d'y placer des cours complets.
4. Le context doit être documenté et maintenu. Il ne faut pas demander à l'agent de deviner l'architecture.
5. Les workflows doivent être reproductibles. Ils sont utiles pour les audits, les revues et les générations documentaires.
6. Les templates doivent séparer le fond de la forme. Un même skill peut produire plusieurs types de documents.

Documentation officielle :

- [Kilo Code – Customize](https://kilo.ai/docs/customize?utm_source=chatgpt.com)
- [Skills](https://kilo.ai/docs/customize/skills?utm_source=chatgpt.com)
- [Custom Rules](https://kilo.ai/docs/customize/custom-rules?utm_source=chatgpt.com)
- [Custom Agents](https://kilo.ai/docs/customize/custom-modes?utm_source=chatgpt.com)
- [Workflows](https://kilo.ai/docs/customize/workflows?utm_source=chatgpt.com)

## 7. Ce que je te propose comme architecture cible

Pour ta bibliothèque `ChatGpt_`, je conserverais une structure source indépendante de Kilo Code, puis je prévoirais une distribution vers les emplacements reconnus par Kilo.

Cela permettrait d'utiliser la même bibliothèque avec Kilo Code, Claude Code et d'autres agents compatibles avec les formats standards.

Je partirais sur cette organisation :

```
ChatGpt_AI_Library/
│
├── agents/
├── skills/
├── rules/
├── contexts/
├── workflows/
├── templates/
├── references/
├── scripts/
│
├── platforms/
│   ├── kilocode/
│   ├── claude/
│   └── generic/
│
├── documentation/
│   ├── architecture.md
│   ├── installation.md
│   └── conventions.md
│
└── install/
    ├── install-linux.sh
    └── install-windows.ps1
```

L'avantage : tu conserves une bibliothèque maîtresse, sans dépendre de l'organisation particulière d'un outil. Les adaptations Kilo Code sont gérées séparément.

À mon avis, c'est la meilleure approche pour constituer progressivement une véritable bibliothèque professionnelle d'agents de développement.

Tu peux ainsi avoir un agent architecte, un agent développeur, un agent sécurité et un agent documentaliste qui partagent les mêmes compétences et les mêmes standards.