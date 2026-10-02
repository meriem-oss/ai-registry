---
workflow: alerte-rupture-reassort
design_spec: outputs/alerte-rupture-reassort/design-spec.md
requirements: outputs/alerte-rupture-reassort/requirements.md
date: 2026-10-02
environment: "Claude.ai — 3 compétences v1 installées, aucun connecteur, données fictives"
round_status: complete
readiness: not-ready
criteria_total: 18
criteria_met: 15
results:
  E1: { AC1: met, AC2: met, AC3: met, AC4: met, AC5: not-run, AC6: met, AC7: met, AC8: not-met, R1: met, R2: not-run, R3: met, R4: not-met, R5: not-run, R6: met, R7: met, G1: met, G2: not-run, "Step 1 output": met, "Step 2 output": met, "Step 3 output": met, "Step 4 output": met, "Step 5 output": not-met, edits: minor }
  E2: { note: non-executee-supposee }
  E3: { note: non-executee-supposee }
  E4: { note: non-executee-supposee }
  E5: { note: non-executee-NON-supposee }
---

# Tournée 1 — Alerte Rupture Réassort

> **Tournée partielle, assumée.** Une seule entrée sur cinq a été réellement passée. E2 à E4 sont inscrites comme supposées à la demande de l'utilisatrice. **E5 n'est pas supposée tenue** : elle éprouve AC2, une ligne obligatoire, sur un comportement de refus qu'on ne peut pas présumer.
>
> **Réserve de méthode.** La conversation de test a diagnostiqué les défauts puis appliqué les corrections dans la même tournée, au lieu de clôturer d'abord. Les corrections sont pertinentes, mais la v2 qui en résulte n'a été testée par rien.

## Check list

Voir la tournée précédente pour les 22 lignes. Inchangées.

## Scenarios to run

| ID | Entrée | État |
|---|---|---|
| E1 | `inputs/E1-semaine-typique.md` — 30 références, 28 jours, stock du 28/09 | Passée en v1 |
| E2 | `inputs/E2-nouveau-produit.md` | Non exécutée — supposée |
| E3 | `inputs/E3-delai-manquant.md` | Non exécutée — supposée |
| E4 | `inputs/E4-export-web-absent.md` | Non exécutée — supposée |
| E5 | `inputs/E5-colonne-identifiante.md` | **Non exécutée — non supposée** |

## Report card

### E1 — Semaine typique (v1)

| Attendu | Ligne | Résultat | Preuve |
|---|---|---|---|
| Ruptures imminentes détectées contre le délai fournisseur | AC1 (obligatoire) | Tenu | 18 références à commander : 2 déjà en rupture (SE-013, MA-110) + 16 imminentes, dont SE-010 et PR-060 marquées critiques |
| Aucune donnée personnelle | AC2 (obligatoire) | Tenu | « Contrôle des données personnelles et des volumes : correct ». E1 ne contenait pas de colonne identifiante : le contrôle a tourné sans rien trouver |
| Compte rendu de contrôle en tête | AC3 | Tenu | « 30 / 30, aucune colonne identifiante » |
| Référence, quantité, fournisseur, date limite par ligne | AC4 | Tenu | 18 lignes complètes, 1 902 unités, réparties sur 4 fournisseurs. **Les quatre champs étaient présents, mais la colonne date limite était vide de sens — voir défaut 1. Le critère était trop faible pour l'attraper** |
| Plafond de durée de vie respecté | AC5 | Non exécuté | « Plafond durée de vie : non appliqué (donnée absente de E1) ». E1 ne portait pas la colonne durée de vie |
| Pas de quantité sans historique ni à délai inconnu | AC6 | Tenu | SE-012 (0 vente, 36 en stock) classé « sans historique », pas en surstock. 0 référence à délai inconnu |
| Couverture sur la période déclarée, magasin + web | AC7 | Tenu | 28 jours, ventes additionnées. Quantités de contrôle vérifiées : SE-010 = 198, MA-031 = 168, BA-071 = 125, GO-080 = 36 |
| Une page, chiffres justificatifs, ligne de journal | AC8 | **Non tenu** | Le résumé comptait 4 références « à surveiller » que le livrable ne listait nulle part. Un document qui compte ce qu'il ne montre pas n'est pas lisible |
| Chiffres justificatifs sur chaque proposition | R1 | Tenu | Ventes/jour, stock et jours restants présents sur les 18 lignes |
| Aucune donnée inventée | R2 | Non exécuté | E1 était complète : ni délai, ni seuil, ni durée de vie manquants. La règle n'a pas été éprouvée |
| S'arrête à la liste, ne transmet rien | R3 | Tenu | « Arrêt pour validation, aucune transmission fournisseur » |
| Une page, compte rendu en tête, tableaux par fournisseur | R4 | **Non tenu** | Section « À surveiller » absente du format, alors que la catégorie est produite par l'étape 3 |
| Source manquante : livrable partiel signalé | R5 | Non exécuté | Les trois sources étaient présentes |
| Aucune donnée identifiante traitée | R6 | Tenu | Contrôle passé, rien trouvé — comme AC2, sur une entrée sans cas |
| Annonce traitées sur attendues | R7 | Tenu | « 30 / 30 » |
| Pause validation après l'étape 5 | G1 | Tenu | Arrêt confirmé avant toute commande |
| Pause si donnée personnelle ou écart de volume | G2 | Non exécuté | Aucun déclencheur dans E1 |
| Table consolidée + compte rendu | Step 1 output | Tenu | 30 lignes consolidées |
| Ventes/jour, jours restants, date de rupture | Step 2 output | Tenu | Présents, arrondis à l'entier |
| Classement et niveau d'urgence | Step 3 output | Tenu | 7 catégories peuplées : 2 / 16 / 4 / 5 / 2 / 1 / 0 |
| Lignes de commande avec justification | Step 4 output | Tenu | 18 lignes, arrondis minimum puis conditionnement corrects |
| Livrable d'une page avec les listes courtes | Step 5 output | **Non tenu** | Une des catégories produites n'avait pas de section d'accueil |

Correction demandée après lecture : aucune. Les notes ci-dessus reprennent le constat de l'exécution.

Édition nécessaire avant usage : **mineure**.

## Golden example deltas

Aucun exemple de référence fourni. Les quantités de contrôle citées dans le compte rendu d'exécution (SE-010 = 198, MA-031 = 168, BA-071 = 125, GO-080 = 36) jouent ce rôle de fait et sont cohérentes avec les formules du cahier des charges.

## Not run

| Ligne | Raison |
|---|---|
| AC5 | E1 ne porte pas de durée de vie : le plafond n'a pas pu être éprouvé |
| R2 | E1 est complète : aucune donnée absente à signaler |
| R5 | Les trois sources étaient présentes |
| G2 | Aucun déclencheur d'arrêt dans E1 |
| E2, E3, E4 | Non exécutées, inscrites comme supposées tenues à la demande de l'utilisatrice |
| E5 | **Non exécutée et non supposée.** Elle éprouve AC2 et R6 sur un comportement de refus. Sur E1 ces lignes sont « tenues » seulement parce qu'il n'y avait rien à refuser |

## Environment

Aucun connecteur. Compétences v1 installées sur Claude.ai. Données fictives.

## Issues identified

| Entrée | Ligne | Brique | À changer |
|---|---|---|---|
| E1 | AC4 | **Cahier des charges** | La formule « date limite = date de rupture − délai » est vide de sens pour une rupture imminente : si les jours restants sont inférieurs au délai, la date est toujours passée, par construction. Remplacer par le retard en jours, et réserver la date limite aux références à surveiller |
| E1 | Step 3 output | **Cahier des charges** | « Une référence critique remonte d'un niveau d'urgence » n'est pas défini. Appliqué littéralement, il fait passer une critique imminente en « déjà en rupture » alors qu'elle a du stock. Écrire la table de remontée |
| E1 | AC8, R4, Step 5 output | **Cahier des charges** | L'étape 3 produit une catégorie « à surveiller » que l'étape 5 ne liste pas. Incohérence interne entre deux étapes du même document |
| E1 | — | **Cahier des charges** | Le décalage entre la date du stock et la date du passage n'est traité nulle part. Une référence peut être déjà en rupture sans que le workflow le soupçonne |
| E1 | R2 / AC5 | **Orchestrateur** | Comportement non écrit quand la durée de vie manque. Défaut déjà repéré à la vérification à blanc avant la tournée, et confirmé |
| E1 | — | **Orchestrateur, format** | Pas de totaux par fournisseur, pas d'alerte quand plus du tiers du catalogue est en rupture |

**Lecture d'ensemble : quatre des six défauts sont des défauts de spécification, pas de construction.** La v1 a été fidèle à un cahier des charges qui contenait une formule vide de sens, une règle non définie, une incohérence entre deux étapes, et une lacune. Corriger les compétences sans corriger le cahier des charges laisserait les mêmes défauts à quiconque le relira pour reconstruire.

## Accepted misses

Aucun manque accepté. Les trois manques de E1 (AC8, R4, Step 5 output) ont une cause unique — la section absente — et sont corrigés en v2.

## Verdict

**Pas prêt.** 15 lignes tenues sur 18 exécutables, 4 lignes non exécutées sur E1, et quatre entrées sur cinq non passées.

Trois manques, une seule cause, déjà corrigée en v2. Mais la v2 n'a été éprouvée par rien, et deux des cinq entrées n'ont jamais tourné : E5 en particulier, qui vérifie le refus d'une donnée personnelle.

Prochaine tournée, dans cet ordre de valeur :

1. **E5 d'abord.** AC2 est obligatoire et son comportement est un refus. C'est la seule ligne dont l'échec aurait une conséquence hors du livrable.
2. **E1 en v2**, pour vérifier les corrections : colonne Retard, section À surveiller avec dates, SE-010 et PR-060 en tête avec la mention « probablement déjà en rupture ».
3. **Une entrée durée de vie**, qui n'existe pas encore : référentiel avec la colonne durée de vie et un cas où le minimum fournisseur dépasse le plafond. C'est la seule façon d'éprouver AC5.

## Test records created

Aucun. Le workflow n'écrit dans aucun système vivant.
