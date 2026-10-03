# Journal du registre

Migrations et changements de schéma uniquement.

## 2026-10-02 — Fondation

Registre créé par `scaffolding-registry` dans le dépôt `meriem-oss/ai-registry`.

Écrit : le schéma, une entreprise (Ascensionniste), deux lignes de métier (Formation, Conseil IA), trois fonctions (Pédagogie, Technique, Commercial) et six processus.

Aucun workflow à la fondation — c'est l'étape Analyze qui les inscrit. Un workflow existant, Alerte Rupture Réassort, a été rattrapé juste après dans un passage séparé.

## 2026-10-03 — Retrait de la branche Conseil IA

Le registre est ramené au seul périmètre Formation. Retirés : la ligne de métier Conseil IA, ses deux processus (Prospection et qualification, Cadrage et proposition), la fonction Commercial qui ne portait que ceux-là, le workflow Prospection Sur Signaux (statut backlog) et son opportunity report.

Rien n'était en production dans cette branche. Les fichiers restent dans l'historique git si la branche doit revenir.
