# Règles Doctrine et Oracle

- Vérifier la version Oracle, le pilote PHP, DBAL, ORM et les capacités réellement disponibles.
- Utiliser les paramètres Doctrine pour les valeurs ; ne jamais concaténer directement des entrées utilisateur dans SQL.
- Distinguer les tests unitaires des tests d'intégration exécutés sur Oracle.
- Ne pas présenter SQLite comme équivalent à Oracle : types, séquences, fonctions, contraintes, transactions et SQL peuvent différer.
- Pour plusieurs bases, expliciter la connexion et l'EntityManager concernés ; éviter les ambiguïtés de routage.
- Encadrer les transactions et comprendre les limites transactionnelles entre bases distinctes.
- Prévoir les contraintes, index, séquences, clés et stratégies de génération propres au schéma Oracle.
- Traduire les exceptions techniques en erreurs applicatives appropriées sans divulguer le SQL, les identifiants ou les détails internes.
- Tester les erreurs pertinentes avec des doubles pour les tests unitaires et avec des scénarios contrôlés sur une base d'intégration pour valider le comportement réel.
- Les migrations de schéma doivent être revues, testées et accompagnées d'une stratégie de déploiement/retour arrière.

**Contrôles :** connexion ciblée, SQL généré, paramètres, transactions, gestion des erreurs, performances et tests sur Oracle.
