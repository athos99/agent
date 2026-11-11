# Template — Documentation fonctionnelle (rôle : product owner)

En-tête obligatoire : date, périmètre, version, auteur (agent Z.ai).
Règle absolue : aucun terme technique (entité, API, migration, cache). Parler utilisateurs, capacités, règles. Niveau EXPRESS : sections 1, 2, 3, 5.

# <Nom de la fonctionnalité / du module>

## 1. En une phrase
Ce que l'utilisateur obtient grâce à cette fonctionnalité, formulé du point de vue de l'utilisateur.

## 2. Utilisateurs concernés
| Profil | Ce qu'il peut faire | Ce qu'il ne peut pas faire |
|---|---|---|
| Client | Passer et annuler une commande | Modifier une commande expédiée |

Utiliser les rôles réels de l'application (rôles Symfony traduits en langage métier).

## 3. Capacités
Liste formulée « L'utilisateur peut... », une capacité par ligne, ordonnée par importance. Chaque capacité renvoie vers la ou les règles de gestion qui la régissent.

## 4. Règles de gestion
| ID | Règle | Pourquoi |
|---|---|---|
| RG-001 | Une commande expédiée ne peut plus être annulée | Contrainte logistique, voir ticket ZA-123 |

Extraire ces règles du code (voters, validateurs, conditions métier des services). Une règle non écrite trouvée dans le code est précieuse : la documenter et la marquer « extraite du code, à confirmer ».

## 5. Parcours utilisateurs
Pour chaque parcours clé (2 à 5) : étapes numérotées du point de vue de l'utilisateur, avec à chaque étape ce que l'utilisateur voit et décide. Format Gherkin en COMPLET :
```
Scénario : annulation d'une commande expédiée
Étant donné que ma commande a été expédiée
Quand je tente de l'annuler depuis mon compte
Alors je vois un message m'expliquant que ce n'est plus possible
```

## 6. Données manipulées (vue métier)
Quelles informations l'application stocke sur les utilisateurs/objets, sans schéma technique. Mentionner les données personnelles et leur durée de conservation si connue (lien RGPD).

## 7. Limites connues
Ce que la fonctionnalité ne fait pas volontairement, ce qui est approximatif, ce qui dépend d'un tiers. Transparence totale : c'est ce que le PO doit savoir pour gérer les attentes.

## 8. Glossaire
Termes métier du domaine, 1 ligne chacun. Inclure la traduction métier ↔ technique quand utile (ex. « dossier » côté métier = entité `Case` côté code).

## 9. Questions ouvertes
Décisions à arbitrer, règles non confirmées, évolutions suggérées par l'analyse du code. Chaque question est adressable directement en backlog.
