Voici le rôle de chaque répertoire dans l'organisation d'un agent IA :

**`skill/`** : le **savoir-faire**. Chaque skill est une capacité réutilisable, décrite dans un fichier (typiquement `SKILL.md`) avec une description, des instructions pas à pas et éventuellement des scripts ou des modèles. L'agent ne le charge que lorsque la tâche correspond (par exemple « analyser une entité Doctrine », « générer de la documentation pour un product owner », « faire une revue de sécurité Symfony »). C'est la réponse à la question « comment faire X ? ».

**`rule/`** : les **règles permanentes**. Ce sont les contraintes et conventions que l'agent doit toujours respecter, quelle que soit la tâche : standards de code (PSR-12, typage strict), conventions de nommage, interdits (pas de requête SQL brute, pas de secret en dur), exigences de sécurité, format des commentaires. Elles sont chargées en permanence ou selon des motifs de fichiers (par exemple `*.php`, `*.js`). C'est la réponse à « qu'est-ce qui est obligatoire ou interdit ? ».

**`context/`** : la **connaissance du projet**. Ce répertoire contient les informations dont l'agent a besoin pour comprendre l'environnement dans lequel il travaille : architecture, stack technique (versions de PHP/Symfony, bases de données), glossaire métier, décisions d'architecture, structure des dépôts, liens vers la documentation. C'est la réponse à « dans quel projet suis-je, et qu'est-ce qui existe déjà ? ».

**`agent/`** : les **profils d'agents**. Chaque fichier définit un agent spécialisé, avec un rôle, un périmètre, un ton, les outils auxquels il a accès, et les skills et rules qu'il mobilise (par exemple un agent « reviewer sécurité », un agent « architecte », un agent « rédacteur de doc »). Il orchestre les trois autres répertoires. C'est la réponse à « qui suis-je et quelle mission ai-je ? ».

**En résumé**

| Répertoire | Question à laquelle il répond | Chargement |
|---|---|---|
| `skill/` | Comment faire telle tâche ? | À la demande |
| `rule/` | Que dois-je toujours respecter ? | Permanent ou par motif de fichier |
| `context/` | Où suis-je et que sais-je du projet ? | Permanent ou selon la tâche |
| `agent/` | Qui suis-je, avec quel rôle et quels outils ? | À l'activation de l'agent |

Un agent s'appuie donc sur ses **skills** pour savoir faire, ses **rules** pour rester dans le cadre, son **context** pour comprendre le projet, et sa propre définition dans **agent/** pour savoir quel rôle tenir.


# KILO CODE

Kilo Code a changé de structure : l'extension actuelle (basée sur le CLI Kilo) utilise `.kilo/`, et l'ancienne utilisait `.kilocode/`. Les deux sont encore lues, donc je te donne la structure actuelle et les équivalents legacy.

## Correspondance avec tes 4 répertoires

| Ton répertoire | Équivalent Kilo | Remarque |
|---|---|---|
| `skill/` | `skills/<nom>/SKILL.md` | Un dossier par skill |
| `rule/` | `rules/*.md` | Branché via la clé `instructions` de `kilo.jsonc` |
| `context/` | `AGENTS.md` (+ `CONTEXT.md`) | Kilo n'a pas de dossier `context/` dédié |
| `agent/` | `agents/<nom>.md` | Le nom du fichier est le nom de l'agent, le corps est le prompt système |

## Au niveau du projet

```
mon-projet/
├── AGENTS.md                  ← context : stack, architecture, conventions
├── kilo.jsonc                 ← config : "instructions": [".kilo/rules/*.md"]
└── .kilo/
    ├── skills/
    │   └── mon-skill/SKILL.md
    ├── rules/
    │   ├── securite.md
    │   └── style-php.md
    └── agents/
        └── reviewer-securite.md
```

- `AGENTS.md` à la racine est chargé automatiquement. Tu peux en mettre dans des sous-dossiers (par exemple `src/backend/AGENTS.md`), ils sont lus quand l'agent travaille dans ces fichiers.
- Les règles ne sont prises en compte que si elles sont listées dans `instructions` de `kilo.jsonc`. Seul l'ancien `.kilocode/rules/` est inclus automatiquement.

## Au niveau global (tous tes projets)

```
~/.kilo/
└── skills/
    └── mon-skill/SKILL.md

~/.config/kilo/
├── kilo.jsonc                 ← "instructions": ["~/.config/kilo/rules/*.md"]
├── AGENTS.md                  ← context personnel, valable partout
├── rules/
│   └── mes-regles.md
└── agents/
    └── architecte.md
```

Les skills globaux sont donc dans `~/.kilo/`, alors que la config, les règles et les agents sont dans `~/.config/kilo/`. Les deux emplacements sont différents. Le projet prend le dessus sur le global en cas de conflit.

## Legacy (si ton extension est ancienne)

- Projet : `.kilocode/rules/`, `.kilocode/skills/`, `.kilocode/agents/`
- Global : `~/.kilocode/rules/`, `~/.kilocode/skills/`
- Il existe aussi des variantes par mode, par exemple `.kilocode/rules-code/` ou `skills-architect/`, pour limiter une règle ou un skill à un mode précis.
- L'ancien « Memory Bank » (`.kilocode/rules/memory-bank/`) est déprécié au profit de `AGENTS.md`.

## Points d'attention

- **Nom des skills** : le champ `name` du `SKILL.md` doit être identique au nom du dossier. Si tu préfixes tes skills par `Claude_`, vérifie que Kilo les accepte : à ma connaissance, la spécification Agent Skills n'autorise que minuscules, chiffres et tirets, donc `claude-symfony-review` serait plus sûr que `Claude_...`.
- **Rechargement** : après l'ajout d'un skill, redémarre la session ou recharge VS Code. La commande `kilo debug skill` liste les skills détectés.
- **Chemins Windows** : les skills globaux sont dans `%USERPROFILE%\.kilo\skills\`. Je n'ai pas vérifié l'emplacement exact du dossier de config sous Windows, à contrôler dans les réglages de Kilo.
- **Équipe** : mets dans le dépôt (`.kilo/` et `AGENTS.md`) ce qui est partagé, et garde tes préférences personnelles en global.

Si tu veux, je te génère le kit complet (skills, rules, agents, `AGENTS.md` et `kilo.jsonc`) pour Symfony, Doctrine et JavaScript, en ZIP avec cette arborescence.