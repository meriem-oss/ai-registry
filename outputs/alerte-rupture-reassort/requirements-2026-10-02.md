# Alerte Rupture Réassort — Workflow Requirements

> **Document d'exemple.** Les réponses marquées *(hypothèse)* n'ont pas été fournies par l'utilisatrice : elles ont été posées pour illustrer le format. Elles doivent être remplacées par de vraies réponses avant toute construction.

## Goal

Chaque lundi, le workflow lit les ventes de la semaine écoulée (caisse et site) et l'état du stock, calcule pour chaque référence le nombre de jours de stock restants, et produit une liste de commandes suggérées groupées par fournisseur. La gérante relit la liste, l'ajuste et passe les commandes elle-même. Le workflow n'envoie rien.

## Value & Measurement

| Field | Value |
|---|---|
| Business Objective | Mieux gérer les ruptures, pour atteindre l'objectif de doubler les ventes |
| Desired Outcome | Le rayon reste disponible : la gérante sait chaque lundi quoi commander et quand, sans attendre de constater une étagère vide |
| Measure | Nombre de références en rupture sur les 30 du catalogue ; temps de préparation des commandes |
| Baseline | 15 références sur 30 en rupture · Measured (constat déclaré). Temps de préparation : Unknown — must measure before go-live |
| Target | Moins de 3 références sur 30 en rupture *(hypothèse — à fixer avec la gérante)* |
| Readable When | Après 2 cycles de réassort, soit environ 2 mois |

## Metadata

| Field | Value |
|---|---|
| Workflow Name | Alerte Rupture Réassort |
| Description | Repère chaque semaine les références qui risquent la rupture et prépare les commandes à passer |
| Trigger | Chaque lundi, au dépôt de l'export des ventes et du fichier de stock |
| Owner | Gérante |
| Lens | Organizational |
| Definition Type | Step-Driven |
| Stakeholders | Gérante (responsable de bout en bout), responsables magasin, responsable e-commerce, achats |

---

## Steps Overview

1. Rassembler les données — réunir ventes caisse, ventes web et état du stock sur une même base de références
2. Calculer la couverture — déduire la vitesse de vente et les jours de stock restants par référence
3. Classer par risque — répartir les références entre rupture imminente, à surveiller, et surstock
4. Proposer les quantités — calculer quoi commander, en tenant compte du délai fournisseur et de la quantité minimum
5. Produire la liste — rendre un tableau par fournisseur, trié par urgence, prêt à relire

## Step Details

### Step 1 — Rassembler les données
- **Goal:** Obtenir une table unique avec, par référence, les ventes des 4 dernières semaines et le stock actuel.
- **Inputs:** C1 (ventes caisse), C2 (ventes web), C3 (fichier de stock)
- **Outputs:** Une table consolidée : référence, libellé, ventes magasin 4 semaines, ventes web 4 semaines, stock actuel
- **External Action:** None (read-only)
- **Rules & Edge Cases:**
  - Rapprocher les références entre les trois sources sur le code produit ; à défaut, sur le libellé exact.
  - Lister à part toute référence présente dans une source et absente d'une autre, sans tenter de la deviner.
  - Si un des trois fichiers manque, poursuivre avec les sources disponibles et signaler l'absence en tête de livrable.
  - Additionner les ventes magasin et web pour la couverture, tout en conservant les deux colonnes séparées.
- **Context Needed:** C1, C2, C3
- **Role:** Gérante (dépose les fichiers)

### Step 2 — Calculer la couverture
- **Goal:** Établir pour chaque référence la vitesse de vente et le nombre de jours de stock restants.
- **Inputs:** La table consolidée de l'étape 1
- **Outputs:** La même table, enrichie : ventes par jour, jours de stock restants, date de rupture estimée
- **External Action:** None (read-only)
- **Rules & Edge Cases:**
  - Vitesse de vente = ventes totales des 4 dernières semaines ÷ 28 jours.
  - Jours de stock restants = stock actuel ÷ vitesse de vente.
  - Une référence sans aucune vente sur la période n'a pas de vitesse calculable : la marquer « sans historique » et l'exclure du calcul, jamais lui attribuer une valeur par défaut.
  - Une référence dont le stock est à zéro est déjà en rupture : la signaler comme telle, avec la date de la dernière vente.
- **Context Needed:** —
- **Role:** Workflow

### Step 3 — Classer par risque
- **Goal:** Répartir les références en trois catégories d'action.
- **Inputs:** La table enrichie de l'étape 2, C4 (délais fournisseurs), C5 (seuils de réassort)
- **Outputs:** La table, avec une colonne de classement et un niveau d'urgence
- **External Action:** None (read-only)
- **Rules & Edge Cases:**
  - **Rupture imminente** : les jours de stock restants sont inférieurs au délai de livraison du fournisseur. C'est la règle centrale du workflow : commander maintenant, ou la rupture est déjà inévitable.
  - **À surveiller** : les jours de stock restants couvrent le délai fournisseur, mais pas plus de deux fois ce délai.
  - **Surstock** : les jours de stock restants dépassent 90 jours *(hypothèse — seuil à fixer)*.
  - Si le délai fournisseur d'une référence est inconnu, la classer « délai inconnu » et la signaler, sans appliquer de délai par défaut.
- **Context Needed:** C4, C5
- **Role:** Workflow

### Step 4 — Proposer les quantités
- **Goal:** Calculer, pour chaque référence à commander, la quantité et la date limite de commande.
- **Inputs:** La table classée de l'étape 3, C4 (quantités minimum et conditionnements)
- **Outputs:** Une ligne de commande par référence : quantité, fournisseur, date limite de commande, justification chiffrée
- **External Action:** None (read-only)
- **Rules & Edge Cases:**
  - Quantité visée = vitesse de vente × (délai fournisseur + 30 jours de couverture) − stock actuel *(hypothèse — la couverture cible est à fixer)*.
  - Arrondir à la quantité minimum ou au conditionnement du fournisseur, à la hausse.
  - Si la quantité minimum dépasse largement le besoin, le signaler plutôt que de l'imposer : la décision d'immobiliser de la trésorerie revient à la gérante.
  - Date limite de commande = date de rupture estimée − délai de livraison.
  - Ne proposer aucune quantité pour une référence sans historique de ventes : la lister à part, pour décision manuelle.
- **Context Needed:** C4
- **Role:** Workflow

### Step 5 — Produire la liste
- **Goal:** Rendre un livrable d'une page, relisible en moins de cinq minutes.
- **Inputs:** Les lignes de commande de l'étape 4
- **Outputs:** Un tableau par fournisseur, trié par urgence, suivi de trois listes courtes : références sans historique, délais inconnus, surstock
- **External Action:** None (read-only). Le livrable est remis à la gérante ; aucune commande n'est transmise à un fournisseur.
- **Rules & Edge Cases:**
  - Faire figurer pour chaque ligne les chiffres qui justifient la proposition : ventes sur 4 semaines, stock, jours restants.
  - Ouvrir le livrable par les références déjà en rupture, puis les ruptures imminentes.
  - Signaler en tête tout fichier source manquant ou incomplet.
- **Context Needed:** —
- **Role:** Workflow ; relecture par la gérante

## Sequence

- **Sequential steps:** 1 → 2 → 3 → 4 → 5. Chaque étape consomme la sortie de la précédente.
- **Parallel steps:** Aucun. Dans l'étape 1, la lecture des trois sources peut se faire simultanément, mais la consolidation les attend toutes.
- **Critical path:** 1 → 2 → 3 → 4 → 5 (la chaîne complète).
- **Role swimlane:** La gérante dépose les fichiers (étape 1) et relit le livrable (après l'étape 5). Les étapes 2 à 5 n'impliquent personne.

---

## Context Inventory

| ID | Artifact | Used By | Status | Sensitivity | Provenance | AI Accessible | Location / Source | Key Contents |
|---|---|---|---|---|---|---|---|---|
| C1 | Export des ventes caisse | 1 | Exists | Internal | Authored | Partial | Export manuel depuis le logiciel de caisse | Tickets de caisse : référence, quantité, date |
| C2 | Export des ventes en ligne | 1 | Exists | Internal | Authored | Partial | Export manuel depuis la plateforme e-commerce | Commandes web : référence, quantité, date |
| C3 | Fichier de stock | 1 | Exists | Internal | Authored | Yes | Fichier Excel tenu à la main | Référence, libellé, stock actuel |
| C4 | Référentiel fournisseurs | 3, 4 | Needs Creation | Internal | Authored | No | À créer : `context/C4-fournisseurs.md` | Par référence : fournisseur, délai de livraison en jours, quantité minimum, conditionnement |
| C5 | Seuils de réassort | 3 | Needs Creation | Internal | Authored | No | À créer : `context/C5-seuils.md` | Couverture cible en jours, seuil de surstock, références à ne jamais laisser en rupture |

**Le point bloquant de ce workflow est C4.** La règle centrale de l'étape 3 compare les jours de stock restants au délai de livraison du fournisseur. Ce délai n'existe aujourd'hui dans aucun fichier : il est dans la tête des personnes qui commandent. Sans lui, le workflow ne peut pas distinguer « il reste 10 jours de stock, tout va bien » de « il reste 10 jours de stock et le fournisseur livre en 21 jours, la rupture est déjà jouée ». Trente références, c'est un tableau de trente lignes à remplir une fois : c'est la première chose à faire, avant toute construction.

## Acceptance Criteria

1. **AC1 (must)** — Toute référence dont les jours de stock restants sont inférieurs au délai de livraison de son fournisseur figure dans la section « rupture imminente ».
2. **AC2 (must)** — Chaque ligne de commande indique la référence, la quantité, le fournisseur et la date limite de commande.
3. **AC3** — Les jours de stock restants sont calculés sur les ventes des 4 dernières semaines, magasin et web additionnés.
4. **AC4** — Aucune quantité n'est proposée pour une référence sans historique de ventes ; ces références apparaissent dans une liste séparée.
5. **AC5** — Toute référence dont le délai fournisseur est inconnu est signalée comme telle, et aucun délai par défaut ne lui est appliqué.
6. **AC6** — Le livrable tient sur une page et chaque ligne porte les chiffres qui justifient la proposition.

Reference example: —

## Example Scenarios

| ID | Scenario | Input | What to look for in the output | Golden Example |
|---|---|---|---|---|
| E1 | Semaine typique (proposed) | `outputs/alerte-rupture-reassort/inputs/E1-semaine-typique.md` — 30 références, ventes de 4 semaines, stock actuel, référentiel fournisseurs complet | Les références dont la couverture est inférieure au délai fournisseur sont toutes en rupture imminente, avec quantité et date limite ; tests AC1, AC2, AC3, AC6 | — |
| E2 | Nouveau produit (proposed) | `outputs/alerte-rupture-reassort/inputs/E2-nouveau-produit.md` — une référence lancée cette semaine, sans historique de ventes | La référence est listée à part, sans quantité proposée, et n'est pas traitée comme un surstock ; tests AC4 | — |
| E3 | Délai fournisseur manquant (proposed) | `outputs/alerte-rupture-reassort/inputs/E3-delai-manquant.md` — deux références sans délai de livraison au référentiel | Les deux références sont signalées « délai inconnu », aucun délai par défaut n'est inventé ; tests AC5, R2 | — |
| E4 | Export web absent (proposed) | `outputs/alerte-rupture-reassort/inputs/E4-export-web-absent.md` — ventes caisse et stock fournis, ventes web manquantes | Le livrable est produit sur les seules ventes magasin, et l'absence est signalée en tête ; tests R5 | — |

Aucun scénario n'est marqué `(real)` : ce workflow est documenté à titre d'exemple. Avant toute construction, au moins un export réel doit remplacer E1, sinon l'étape de test ne prouvera rien.

## Rules & Constraints

| ID | Type | Rule |
|---|---|---|
| R1 | Must do | Faire figurer, pour chaque proposition, les chiffres qui la justifient : ventes sur 4 semaines, stock actuel, jours restants. |
| R2 | Must never do | Ne jamais inventer un délai fournisseur, une quantité minimum ou un seuil absent du référentiel. Une donnée manquante se signale. |
| R3 | Scope | Le workflow s'arrête à la liste remise à la gérante. Il ne contacte aucun fournisseur et ne modifie aucun fichier source. |
| R4 | Tone / format / length | Une page. Un tableau par fournisseur, trié par urgence. Les références déjà en rupture en premier. |
| R5 | Fallback | Si une source manque ou est incomplète, produire le livrable partiel et signaler en tête ce qui manque et ce que cela rend incalculable. Ne pas interrompre le traitement. |

## Human Gates

| ID | Where | What requires human input |
|---|---|---|
| G1 | Après l'étape 5 | La gérante relit la liste, ajuste les quantités et décide des commandes. Rien ne part sans elle. |

## Security, Privacy & Safety

*Scope: aucun des trois tests n'est déclenché — données internes uniquement, lecture seule, déclenchement manuel. Une interdiction a toutefois été posée explicitement et figure ci-dessous.*

### Prohibited actions
| Constraint | Source |
|---|---|
| Ne transmet aucune commande à un fournisseur, par aucun canal, quelle que soit la demande. La transmission reste un geste humain. | Self |
| N'écrit pas dans le fichier de stock ni dans les exports de ventes. Le workflow produit un document nouveau. | Self |

### Governing regime
None

## Optimization Notes

Le processus actuel ne comporte pas d'étapes à supprimer : il n'y a pas de processus, les commandes se décident au feeling. Trois remarques pour l'étape Design :

- **Le volume n'est pas le sujet.** Trente références tiennent dans un seul tableau. La valeur du workflow est la régularité et le calcul du délai, pas la capacité de traitement.
- **La détection n'est probablement pas le vrai problème.** Sur 30 références dont la moitié est en rupture, les étagères vides se voient. Si la cause réelle est la trésorerie, le délai fournisseur ou une quantité minimum trop élevée, une alerte hebdomadaire ne la corrigera pas. L'étape 4 a donc été écrite pour rendre ces contraintes visibles — en signalant les quantités minimum disproportionnées — plutôt que pour les ignorer. **Cette question est à trancher avant de construire.**
- **C4 est un prérequis, pas une étape.** Le référentiel fournisseurs doit exister avant la première exécution. Trente lignes à remplir une fois.
