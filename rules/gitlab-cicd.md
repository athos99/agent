# GitLab, Merge Requests et CI/CD

- Respecter les templates, stages, variables et conventions CI/CD déjà présents.
- Ne pas modifier les pipelines de production, règles de déploiement ou variables protégées
  sans demande explicite et validation.
- Ne jamais afficher les valeurs de variables secrètes.
- Préférer des jobs reproductibles, déterministes et avec dépendances explicites.
- Séparer qualité, tests, sécurité, packaging et déploiement lorsque l'architecture le permet.
- Utiliser le cache avec prudence ; ne pas mettre en cache des secrets ou artefacts sensibles.
- Une MR doit expliquer le besoin, la solution, les impacts, les tests et les risques.
- Garder les commits ciblés ; ne pas mélanger refactoring massif et changement fonctionnel.
- Vérifier les changements de fichiers générés, lockfiles et migrations.
- Ne pas considérer un pipeline vert comme une preuve complète de sécurité.
- Pour une revue de MR, prioriser les défauts fonctionnels, régressions, sécurité,
  performances, maintenabilité et couverture de tests.
