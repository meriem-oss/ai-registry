---
workflow: alerte-rupture-reassort
requirements_file: outputs/alerte-rupture-reassort/requirements.md
spec_version: 3.0
approved: false
definition_type: Step-Driven
mechanism: Skill
involvement: Augmented
platform: Claude Code
platform_mode: code
packaging: Standalone Skill
counts:
  steps: 5
  skills: 3
  agents: 0
  integrations: 0
---

# Alerte Rupture Réassort — Design Spec

> **Document d'exemple.** Le cahier des charges dont ce document découle contient des hypothèses non validées, et les données fournisseurs sont fictives. À relire avant toute construction réelle.

## Source

**Workflow Requirements:** `outputs/alerte-rupture-reassort/requirements.md`

Ce Design Spec consomme le cahier des charges comme source canonique. Le but, la valeur, les métadonnées, l'inventaire de contexte, la sécurité, les critères d'acceptation, les scénarios de test, les points de validation humaine et le détail des étapes y sont définis — ils ne sont pas répétés ici. Lire les deux documents ensemble au moment de construire.

## Value & Measurement

| Field | Value |
|---|---|
| Business Objective | Mieux gérer les ruptures, pour atteindre l'objectif de doubler les ventes |
| Desired Outcome | Le rayon reste disponible : la gérante sait chaque lundi quoi commander et quand, sans attendre de constater une étagère vide |
| Measure | Nombre de références en rupture sur les 30 du catalogue ; temps de préparation des commandes |
| Baseline | 15 références sur 30 en rupture · Measured. Temps de préparation : Unknown — must measure before go-live |
| Target | Moins de 3 références sur 30 en rupture (hypothèse à valider) |

Le temps de préparation reste `Unknown` : il devra être chronométré lors du premier passage, avant mise en service. Ce n'est pas un blanc à combler par une estimation, c'est une mesure à faire.

---

## Layer 1 — Architecture

## Execution Pattern

**Skill** — Les cinq étapes s'exécutent toujours dans le même ordre, sur les mêmes sources, avec des règles de calcul fixes. Rien ne dépend de ce que le workflow découvre en route. Une compétence lancée par son nom suffit ; un agent ajouterait de la machinerie et un besoin d'accès automatique aux fichiers, sans rien résoudre de plus.

## Architecture Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Lens | Organizational | Le processus traverse plusieurs rôles — magasin, e-commerce, achats — sous la responsabilité de la gérante |
| Platform | Claude Code | Choix de l'utilisatrice. Lecture directe des fichiers locaux, sans connecteur à configurer |
| Platform Mode | code | Claude Code est une plateforme à système de fichiers : les compétences s'installent comme des fichiers |
| Orchestration | Skill | Séquence fixe, déclenchement manuel, aucune décision de parcours à l'exécution |
| Involvement | Augmented | La gérante lance le workflow et valide la liste produite |
| Packaging | Standalone Skill | Une compétence d'orchestration et deux compétences de calcul, installées dans `.claude/skills/`. Pas de plugin à distribuer : l'usage est interne |
| Trigger | Chaque lundi, au dépôt de l'export des ventes et du fichier de stock | Déclenchement manuel : pas d'infrastructure de planification, pas d'exécution sans surveillance |

## Autonomy Spectrum Summary

**Niveau du workflow : Deterministic.**

Les cinq étapes sont déterministes : mêmes entrées, mêmes sorties. Aucune n'exige de jugement.

- **Étapes 1, 2, 4, 5** — arithmétique et mise en forme. La vitesse de vente est une division, les jours de stock une autre, la quantité une formule. Il n'y a pas de place pour l'interprétation.
- **Étape 3** — classement par seuils. Les trois catégories sont définies par des comparaisons chiffrées : la couverture est inférieure au délai fournisseur, ou elle ne l'est pas. Les seuils viennent de C5, ils ne sont pas inventés à l'exécution.

Un seul point du workflow demande un jugement, et il a été délibérément confié à un humain : décider de commander malgré une quantité minimum disproportionnée. C'est le rôle de G1.

## Safety & Permissions

| Question | Finding | Mitigation |
|---|---|---|
| **Write access** — quels outils le workflow peut-il créer, modifier ou envoyer ? | Aucun. Le workflow lit trois fichiers et écrit un document nouveau dans `outputs/alerte-rupture-reassort/`. Aucun connecteur, aucune messagerie, aucun système fournisseur | Aucun accès en écriture n'est demandé en dehors du dossier de sortie du workflow. Les fichiers sources sont ouverts en lecture seule |
| **Untrusted input** — une étape traite-t-elle du contenu que l'équipe n'a pas écrit ? | Aucun. Les trois sources sont produites par l'entreprise : export de caisse, export e-commerce, fichier de stock tenu en interne | Sans objet. Aucun contenu externe n'entre dans le workflow, donc aucun risque d'instruction dissimulée dans les données |
| **Unattended runs** — tourne-t-il sans surveillance ? | Non. Déclenchement manuel, chaque lundi, par la gérante | Sans objet. Si une planification est ajoutée plus tard, G1 devra être conservé et chaque exécution journalisée |
| **Blast radius** — pire conséquence réaliste d'une mauvaise exécution ? | Une quantité surévaluée est proposée, et la gérante commande trop : de la trésorerie immobilisée. Aucune commande ne peut partir toute seule | G1 place une validation humaine devant la seule conséquence coûteuse. R1 impose d'afficher les chiffres qui justifient chaque ligne, pour que l'erreur soit visible à la relecture plutôt que cachée dans un total |

### Constraint Conformance

| Constraint | From | Met by | State |
|---|---|---|---|
| Ne transmet aucune commande à un fournisseur, par aucun canal, quelle que soit la demande | Prohibited actions · Self | Aucun connecteur de messagerie ni d'EDI n'est prévu dans le design. Le livrable est un document remis à la gérante ; la transmission reste un geste manuel | Satisfied |
| N'écrit pas dans le fichier de stock ni dans les exports de ventes | Prohibited actions · Self | Les trois sources sont lues et jamais réécrites. Le livrable est un fichier nouveau, dans un dossier distinct | Satisfied |

Aucune contrainte n'est `Open` ni `Accepted`.

## Integration Options

*No integrations — the workflow is text-only.*

Les trois sources sont des fichiers locaux, lus nativement par Claude Code. Aucun connecteur MCP, API, CLI ou SDK n'est nécessaire. C'est l'intérêt du choix de plateforme : rien à configurer, rien à autoriser.

## Model Recommendation

**Default capability:** fast — Les étapes 1, 2, 3 et 5 sont de l'arithmétique et de la mise en forme sur trente lignes. Un modèle rapide suffit, et coûte moins cher à l'exécution hebdomadaire.

*(En clair : **reasoning-heavy** = plus lent, pour les jugements complexes ; **fast** = plus rapide, pour les tâches simples et répétitives ; **vision** = sait lire des images.)*

**Per-step overrides:**
- Étape 4 : reasoning-heavy — c'est la seule étape qui demande un arbitrage. Quand la quantité minimum du fournisseur dépasse largement le besoin calculé, le workflow doit le signaler avec un commentaire utile plutôt que de l'imposer en silence. C'est du jugement, pas du calcul.

**Per-platform mapping:** résolu par l'étape Build au moment de la génération, qui vérifiera les noms de modèles en vigueur. Aucun identifiant de modèle n'est inscrit ici : ils vieillissent mal.

---

## Layer 2 — Decomposition

## Step-by-Step Decomposition

| Step | Name (from Requirements) | Autonomy | Orchestration | Integration (use/build) | Intelligence | Build Output | Human Gate? |
|------|------|----------|---------------|------------------------|--------------|--------------|-------------|
| Step 1 | Rassembler les données | Deterministic | Skill | — | Model: fast; Context: C1, C2, C3 | Inline prompt → Workflow Requirements Step 1 | No |
| Step 2 | Calculer la couverture | Deterministic | Skill | — | Model: fast | New skill: S2 | No |
| Step 3 | Classer par risque | Deterministic | Skill | — | Model: fast; Context: C4, C5 | New skill: S2 | No |
| Step 4 | Proposer les quantités | Deterministic | Skill | — | Model: reasoning; Context: C4 | New skill: S3 | No |
| Step 5 | Produire la liste | Deterministic | Skill | — | Model: fast | Inline prompt → Workflow Requirements Step 5 | Yes |

Les étapes 2 et 3 sont couvertes par une seule compétence : calculer la couverture et la comparer au délai fournisseur sont deux moments du même geste, et les séparer obligerait à faire circuler une table intermédiaire sans valeur propre. Les étapes 1 et 5 restent dans l'orchestrateur : consolider trois fichiers et mettre en forme une page ne sont pas des capacités réutilisables ailleurs.

## Orchestrator Prompt Outline

```
[Intro : cette compétence produit la liste hebdomadaire de réassort.
 Se lance le lundi, après dépôt de l'export des ventes et du fichier de stock.]

[Étape 1 — Rassembler les données]
  - Source : Workflow Requirements Step 1
  - Build Output : Inline prompt → Workflow Requirements Step 1
  - L'utilisatrice fournit : export caisse (C1), export web (C2), fichier de stock (C3)
  - Produit : une table consolidée — référence, libellé, ventes magasin, ventes web, stock
  - Si une source manque : poursuivre et le signaler en tête du livrable (R5)

[Étapes 2 et 3 — Calculer la couverture et classer]
  - Source : Workflow Requirements Steps 2, 3
  - Build Output : New skill: S2 (calculating-stock-coverage)
  - Reçoit : la table consolidée, C4 (délais fournisseurs), C5 (seuils)
  - Produit : la table enrichie — vitesse de vente, jours de stock restants,
    date de rupture estimée, catégorie de risque

[Étape 4 — Proposer les quantités]
  - Source : Workflow Requirements Step 4
  - Build Output : New skill: S3 (proposing-reorder-quantities)
  - Reçoit : la table classée, C4 (quantités minimum et conditionnements)
  - Produit : une ligne de commande par référence — quantité, fournisseur,
    date limite de commande, justification chiffrée

[Étape 5 — Produire la liste]
  - Source : Workflow Requirements Step 5
  - Build Output : Inline prompt → Workflow Requirements Step 5
  - Produit : une page — un tableau par fournisseur trié par urgence,
    puis trois listes courtes (sans historique, délai inconnu, surstock)

[PAUSE — validation humaine, G1]
  - Ce que la gérante relit : les lignes de commande et leurs quantités
  - Ce qu'elle décide : commander, ajuster, ou écarter. En particulier
    les lignes où la quantité minimum du fournisseur dépasse le besoin
  - Rien ne part sans elle : le workflow ne transmet aucune commande

[Livrable final : outputs/alerte-rupture-reassort/liste-YYYY-MM-DD.md]

[Résumé de fin — « Ce que j'ai fait » :
  - les étapes parcourues, dans l'ordre
  - la pause G1 et la décision prise
  - les actions sur fichiers, sous la forme système : action
  - l'emplacement du livrable]
```

Le résumé de fin est obligatoire. L'étape 5 du cadre, Test, le cite comme preuve pour chaque règle et chaque point de validation : ce qui n'y figure pas ne peut pas être vérifié.

## Data Readiness Summary

| Context ID | Current State | Required Action | Affects Steps |
|---|---|---|---|
| C1 — Export des ventes caisse | Partial | Export manuel hebdomadaire, déposé dans un dossier connu du workflow. Format et noms de colonnes stables d'une semaine à l'autre | Step 1 |
| C2 — Export des ventes en ligne | Partial | Idem C1. Les deux exports doivent partager le même code produit que le fichier de stock, sinon le rapprochement échoue | Step 1 |
| C4 — Référentiel fournisseurs | No | **Prérequis bloquant.** À créer avant la première exécution : une ligne par référence, avec le délai de livraison, la quantité minimum et le conditionnement. Trente lignes. Un gabarit existe à `context/C4-fournisseurs.md`, actuellement rempli de données fictives | Steps 3, 4 |
| C5 — Seuils de réassort | No | À créer : couverture cible, seuil de surstock, références à ne jamais laisser en rupture. Gabarit à `context/C5-seuils.md` | Step 3 |

C3, le fichier de stock, est le seul contexte directement exploitable aujourd'hui.

**C4 est le vrai chemin critique de ce workflow.** Sans délai fournisseur, la règle centrale de l'étape 3 ne se calcule pas, et le workflow ne peut pas distinguer une couverture confortable d'une rupture déjà jouée. Aucune construction n'a de sens avant que ces trente lignes existent.

## Recommended Implementation Order

### Quick Wins (implement first)
1. **S2 — `calculating-stock-coverage`** — Utilisable seule, dès qu'un export et le référentiel existent. Elle répond à la question « qu'est-ce qui va manquer ? », qui est déjà la moitié de la valeur du workflow. Elle permet aussi de valider les seuils de C5 sur des données réelles avant d'aller plus loin.

### Core (implement second)
1. **S3 — `proposing-reorder-quantities`** — Dépend de la sortie de S2. Ajoute la réponse à « combien commander ? ».
2. **S1 — `alerte-rupture-reassort`** — L'orchestrateur, construit en dernier : il enchaîne S2 et S3 et ajoute la consolidation des sources et la mise en page. Le construire après les deux autres évite de réécrire l'enchaînement à chaque ajustement.

### Future Enhancement (optional)
1. **Lecture directe des exports** — Si le logiciel de caisse ou la plateforme e-commerce expose une connexion, supprimer l'export manuel hebdomadaire. Fait passer C1 et C2 de `Partial` à `Yes`.
2. **Planification hebdomadaire** — Déclencher le workflow automatiquement le lundi matin. Suppose de conserver G1 et de journaliser chaque exécution. À ne considérer qu'après plusieurs semaines de confiance.
3. **Historique et apprentissage des délais réels** — Comparer les délais annoncés par les fournisseurs aux délais constatés, et corriger C4. C'est ce qui rendrait le workflow plus juste que le référentiel lui-même.

---

## Layer 3 — Component Blueprints

## Skill Candidates

### S1 — alerte-rupture-reassort

| Field | Detail |
|---|---|
| **ID** | S1 |
| **Name** | alerte-rupture-reassort |
| **Description** | This skill should be used when the user asks to run the weekly stockout and reorder check, says "alerte rupture", "réassort", "qu'est-ce qu'on commande cette semaine", or supplies a weekly sales export together with a stock file. It consolidates the till export, the online sales export and the stock file into one table, invokes calculating-stock-coverage and proposing-reorder-quantities, and produces a one-page order list grouped by supplier, references already out of stock first, then pauses for the manager's validation. It never contacts a supplier and never writes to the stock file or the sales exports. Do not use it for a single ad-hoc coverage question on one product — calculating-stock-coverage answers that directly. |
| **Purpose** | Orchestrateur du workflow hebdomadaire : enchaîne les cinq étapes et s'arrête à la validation humaine. Spécifique à ce workflow, non réutilisable ailleurs. |
| **Covers Steps / Domains** | Toutes — Steps 1 à 5 |
| **Inputs** | Export des ventes caisse (C1) — chemin de fichier ou table collée ; ventes par référence sur 4 semaines<br>Export des ventes en ligne (C2) — idem<br>Fichier de stock (C3) — chemin de fichier ; référence, libellé, stock actuel<br>Référentiel fournisseurs (C4) — chemin de fichier<br>Seuils (C5) — chemin de fichier |
| **Outputs** | Un fichier `outputs/alerte-rupture-reassort/liste-YYYY-MM-DD.md` : un tableau de commandes par fournisseur trié par urgence, suivi des listes « sans historique », « délai inconnu » et « surstock ». Plus une ligne ajoutée à `runs.md`. |
| **Decision Logic** | Suivre l'Orchestrator Prompt Outline de ce spec.<br>Rapprocher les références entre sources sur le code produit, à défaut sur le libellé exact ; ne jamais deviner une correspondance.<br>Si une source manque, produire le livrable partiel et le signaler en tête (R5).<br>Ouvrir le livrable par les références déjà en rupture, puis les ruptures imminentes.<br>S'arrêter après l'étape 5 et présenter la liste pour validation (G1). |
| **Failure Modes** | Une source manque → produire le livrable sur les sources disponibles, signaler l'absence en tête et nommer ce que cela rend incalculable<br>C4 absent ou vide → s'arrêter et le dire : sans délai fournisseur le workflow n'a pas d'objet<br>Aucune référence commune aux trois sources → s'arrêter et montrer un extrait de chaque fichier, pour que l'utilisatrice voie où le rapprochement échoue<br>Une référence présente dans une source et absente d'une autre → la lister à part, ne pas l'écarter en silence |
| **Required Tools** | Aucun. Lecture et écriture de fichiers locaux, nativement |
| **Depends On** | S2, S3 |
| **Stateful?** | No — chaque exécution repart des fichiers du jour. `runs.md` est un journal, pas un état lu par le workflow. |

### S2 — calculating-stock-coverage

| Field | Detail |
|---|---|
| **ID** | S2 |
| **Name** | calculating-stock-coverage |
| **Description** | This skill should be used when the user needs to know which product references risk running out of stock. It triggers on questions about stock coverage, days of stock remaining, stockout risk, "rupture de stock", "combien de jours de stock", or on a sales export supplied together with a stock level table. It computes sales velocity over the stated period, derives days of stock remaining per reference, estimates the stockout date, and classifies every reference against its own supplier lead time as already out of stock, imminent stockout, to watch, overstock, or no sales history. It never invents a missing lead time or threshold. Do not use it to decide order quantities — proposing-reorder-quantities does that. |
| **Purpose** | Capacité de diagnostic : transforme ventes et stock en couverture et en niveau de risque. Réutilisable par tout commerce tenant un stock, indépendamment de ce workflow. |
| **Covers Steps / Domains** | Steps 2, 3 |
| **Inputs** | Table de ventes et stock — une ligne par référence, avec ventes de la période et stock actuel<br>Durée de la période de ventes, en jours — par défaut 28<br>Référentiel de délais fournisseurs — une ligne par référence, avec le délai en jours<br>Seuils — couverture cible, seuil de surstock, références critiques |
| **Outputs** | La table d'entrée enrichie de quatre colonnes : ventes par jour, jours de stock restants, date de rupture estimée, catégorie de risque. Plus un décompte par catégorie. |
| **Decision Logic** | Vitesse de vente = ventes totales de la période ÷ durée de la période en jours.<br>Jours de stock restants = stock actuel ÷ vitesse de vente.<br>Date de rupture estimée = date du jour + jours de stock restants.<br>Classement, dans cet ordre de priorité :<br>— stock à zéro → « déjà en rupture »<br>— aucune vente sur la période → « sans historique », couverture non calculable<br>— jours restants < délai fournisseur → « rupture imminente »<br>— jours restants ≤ 2 × délai fournisseur → « à surveiller »<br>— jours restants > seuil de surstock → « surstock »<br>— sinon → « normal »<br>Une référence listée comme critique dans les seuils remonte d'un niveau d'urgence et est signalée comme telle. |
| **Failure Modes** | Délai fournisseur absent pour une référence → la classer « délai inconnu », ne jamais appliquer de délai moyen ni de valeur par défaut<br>Aucune vente sur la période → « sans historique », pas de couverture, pas de classement en surstock malgré un stock élevé<br>Stock négatif ou non numérique → signaler la ligne comme suspecte et l'exclure du calcul<br>Période de ventes non précisée → demander la durée plutôt que de supposer 28 jours |
| **Required Tools** | Aucun |
| **Depends On** | None |
| **Stateful?** | No |

### S3 — proposing-reorder-quantities

| Field | Detail |
|---|---|
| **ID** | S3 |
| **Name** | proposing-reorder-quantities |
| **Description** | This skill should be used when the user needs to decide how much of each product reference to reorder. It triggers on questions about reorder quantities, "combien commander", supplier order preparation, minimum order quantities, or pack sizes, and on a classified stock-coverage table supplied with a supplier reference. It computes a target quantity from sales velocity, supplier lead time and a coverage target, rounds up to the supplier's minimum order quantity and pack size, and returns one order line per reference with the latest safe order date and the figures that justify it. It flags a minimum order quantity that far exceeds the computed need rather than imposing it, and it never transmits an order to a supplier. Do not use it to assess stockout risk — calculating-stock-coverage does that. |
| **Purpose** | Capacité de prescription : transforme un diagnostic de couverture en lignes de commande chiffrées et justifiées. Réutilisable hors de ce workflow. |
| **Covers Steps / Domains** | Step 4 |
| **Inputs** | Table de couverture classée — sortie de calculating-stock-coverage<br>Référentiel fournisseurs — délai, quantité minimum, conditionnement par référence<br>Couverture cible, en jours au-delà du délai de livraison |
| **Outputs** | Une ligne de commande par référence à commander : référence, libellé, fournisseur, quantité proposée, date limite de commande, et les chiffres justificatifs (ventes de la période, stock, jours restants). Plus une liste des références écartées, avec le motif. |
| **Decision Logic** | Quantité visée = vitesse de vente × (délai fournisseur + couverture cible) − stock actuel.<br>Arrondir à la hausse, d'abord à la quantité minimum du fournisseur, puis au conditionnement.<br>Date limite de commande = date de rupture estimée − délai fournisseur.<br>Ne proposer de quantité que pour les catégories « déjà en rupture » et « rupture imminente ».<br>Signaler, sans l'imposer, toute ligne où la quantité minimum dépasse le besoin calculé de plus de 50 % : la décision d'immobiliser de la trésorerie appartient à la gérante.<br>Grouper les lignes par fournisseur, pour permettre une commande unique. |
| **Failure Modes** | Référence sans historique de ventes → ne proposer aucune quantité, la lister à part pour décision manuelle<br>Délai fournisseur inconnu → aucune quantité, aucune date limite ; la référence apparaît dans la liste « délai inconnu »<br>Quantité visée négative ou nulle → ne pas commander, et le dire : le stock couvre déjà la cible<br>Quantité minimum absente du référentiel → proposer la quantité visée telle quelle et signaler que l'arrondi n'a pas pu être appliqué<br>Date limite déjà dépassée → la commande est en retard : le signaler explicitement plutôt que d'afficher une date passée sans commentaire |
| **Required Tools** | Aucun |
| **Depends On** | S2 |
| **Stateful?** | No |

## Prerequisites

1. **Claude Code installé** et ouvert sur un dossier de travail dédié à ce workflow.
2. **C4, le référentiel fournisseurs, rempli avec les vraies conditions** : une ligne par référence du catalogue, avec le délai de livraison en jours, la quantité minimum et le conditionnement. C'est le prérequis bloquant : trente lignes, à remplir une fois, avant toute construction.
3. **C5, les seuils, validés avec la gérante** : couverture cible, seuil de surstock, et la liste des références à ne jamais laisser en rupture.
4. **Un emplacement stable pour les exports hebdomadaires**, avec des noms de colonnes constants d'une semaine à l'autre, et un code produit commun aux trois sources.
5. **Un export de ventes réel** pour remplacer le scénario E1 des tests. Les données actuelles sont fictives : tester dessus ne prouverait rien d'autre que la cohérence interne du calcul.

## Deployment Plan

| Artifact | Target Location | Deployment Steps |
|---|---|---|
| S1 — `alerte-rupture-reassort` | `.claude/skills/alerte-rupture-reassort/SKILL.md` | Générer le fichier, le déposer dans le dossier, relancer la session. Lancement par `/alerte-rupture-reassort` |
| S2 — `calculating-stock-coverage` | `.claude/skills/calculating-stock-coverage/SKILL.md` | Idem. Invocable seule, ou appelée par S1 |
| S3 — `proposing-reorder-quantities` | `.claude/skills/proposing-reorder-quantities/SKILL.md` | Idem |
| C4, C5 | `outputs/alerte-rupture-reassort/context/` | Remplir les gabarits avant la première exécution |

**Packaging note:** Trois compétences installées séparément dans `.claude/skills/`, sans couche de distribution. Claude Code lit ce dossier directement. Si le workflow devait être partagé avec un tiers plus tard, les trois dossiers se regroupent en plugin sans rien réécrire.

**Orchestrator artifact:** S1 est le point d'entrée déclenché par l'utilisatrice. Il porte le nom du workflow ; S2 et S3 portent des noms de capacité, pour que le point d'entrée n'éclipse jamais une sous-compétence.

**Run Logging:** S1 ajoute une ligne à `outputs/alerte-rupture-reassort/runs.md` à la fin de chaque exécution — date, sources utilisées, nombre de références par catégorie, nombre de lignes de commande proposées, ajustements faits par la gérante. Le fichier est créé avec son en-tête s'il n'existe pas. C'est ce journal qui permettra, dans quelques semaines, de mesurer la baisse des ruptures.

**Recommended for frequent use:** Garder le dossier de travail ouvert dans Claude Code, avec les exports déposés au même endroit chaque lundi.

---

## Cross-Layer Sections

## Evaluation Inputs

Les critères d'acceptation (AC1 à AC6), les quatre scénarios de test (E1 à E4) et le point de validation humaine (G1) sont définis dans `outputs/alerte-rupture-reassort/requirements.md` et ne sont pas répétés ici. L'étape 5, Test, les lit directement dans ce fichier.

Réserve : les quatre scénarios sont marqués `(proposed)`. Aucun ne repose sur des données réelles. Avant la mise en service, au moins E1 doit être remplacé par un export réel.

## Deferred to Build

- [ ] Noms de modèles en vigueur sur Claude Code pour les capacités `fast` et `reasoning` — à vérifier au moment de la génération
- [ ] Format exact des exports de caisse et e-commerce — noms de colonnes, séparateur, encodage — à constater sur un export réel
- [ ] Nom du code produit commun aux trois sources, et comportement si les codes diffèrent entre caisse et site
- [ ] Partage éventuel des compétences avec un tiers, et donc regroupement en plugin

## Stakeholders

| Rôle | Intervention |
|---|---|
| Gérante | Responsable de bout en bout. Dépose les fichiers, lance le workflow, valide la liste à G1, passe les commandes |
| Responsable magasin | Fournit l'export de caisse et la mise à jour du stock |
| Responsable e-commerce | Fournit l'export des ventes en ligne |
| Achats | Reçoit la liste validée et transmet les commandes aux fournisseurs |

Les étapes 2 à 5 n'impliquent personne : le workflow travaille seul entre le dépôt des fichiers et la validation.

## Self-Test Summary

**Structure**
- ✓ Frontmatter présent, avec workflow, requirements_file, spec_version 3.0, approved false, definition_type, mechanism, involvement, platform, platform_mode, packaging et counts
- ✓ Les `counts` correspondent au corps : 3 compétences, 0 agent, 0 intégration, 5 étapes
- ✓ La section Source nomme le chemin du cahier des charges
- ✓ Toutes les sections obligatoires sont présentes dans l'ordre du gabarit
- ✓ Le tableau Architecture Decisions comporte les lignes Lens, Platform, Platform Mode, Orchestration, Involvement, Packaging et Trigger
- ✓ Chaque étape du tableau de décomposition a ses colonnes Orchestration, Integration, Intelligence et Build Output distinctes
- ✓ Les identifiants d'étapes correspondent à ceux du cahier des charges (Step 1 à Step 5)
- ✓ Chaque étape utilise un terme d'autonomie canonique : ici Deterministic pour les cinq
- ✓ Colonnes Integration : aucune étape n'utilise d'outil externe, toutes portent `—`
- ✓ Chaque Build Output est une forme canonique : `Inline prompt → Workflow Requirements Step N` et `New skill: SN`
- ✓ Packaging vaut `Standalone Skill`, une forme canonique
- ✓ Mechanism vaut `Skill`, une forme canonique

**Skill Candidates**
- ✓ Chaque `New skill: SN` du tableau a son entrée : S2 et S3
- ✓ Les trois compétences ont leurs 12 champs
- ✓ Les noms sont en minuscules avec tirets, sans tirets consécutifs, sous 64 caractères. S2 et S3 portent des noms de capacité ; seul S1, l'orchestrateur, porte le nom du workflow
- ✓ Les trois descriptions commencent par « This skill should be used when », sont à la troisième personne, sous 1024 caractères, et nomment chacune plusieurs déclencheurs concrets
- ✓ Aucune compétence ne décrit la même capacité à deux étapes : S2 couvre les étapes 2 et 3 en une seule entrée
- ✓ S1 est l'orchestrateur, nommé d'après le workflow, Covers Steps : toutes
- ✓ Aucun `Extend existing` dans ce design : aucune compétence installée ne couvrait ces capacités

**Agent Configuration**
- ✓ Sans objet — aucun `New agent` dans le tableau de décomposition
- ✓ Sans objet — `agents: 0`
- ✓ Sans objet
- ✓ Sans objet
- ✓ Sans objet — un seul mécanisme, aucune configuration multi-agents requise

**Cross-references**
- ✓ Aucune étape ne nomme d'outil : Integration Options porte la ligne unique prévue
- ✓ Chaque `Depends On` pointe vers un identifiant défini : S1 dépend de S2 et S3, S3 dépend de S2

**Mechanism-specific**
- ✓ L'Orchestrator Prompt Outline est présent, le mécanisme étant `Skill`
- ✓ L'Orchestrator Prompt Outline nomme le résumé de fin « Ce que j'ai fait »
- ✓ `agents: 0` est posé et la logique d'orchestration est documentée dans l'Orchestrator Prompt Outline et le Deployment Plan

**Safety**
- ✓ La section Safety & Permissions répond aux quatre questions avec leurs mitigations. L'échappatoire « lecture seule » n'a pas été utilisée, le cahier des charges portant deux interdictions explicites
- ✓ Le tableau Constraint Conformance liste les deux contraintes du cahier des charges, toutes deux `Satisfied`. Aucune n'est `Accepted` ni `Open`
- ✓ Value & Measurement reprend objectif, résultat attendu, mesure, base de départ et cible. Le temps de préparation reste `Unknown`, porté tel quel
- ✓ Le cahier des charges ne précède pas ce format : les contraintes viennent bien de l'étape Deconstruct, avec leur source
- ✓ Sans objet — le workflow ne consomme aucun contenu externe et n'a aucun accès en écriture hors de son dossier de sortie

**Completeness**
- ✓ Model Recommendation présent, avec capacité par défaut, une dérogation justifiée à l'étape 4, et le renvoi à Build pour les noms de modèles
- ✓ Data Readiness Summary présent, référençant C1, C2, C4 et C5, avec C4 signalé comme prérequis bloquant
- ✓ Deployment Plan présent, avec emplacement et étapes pour chaque artefact, plus la note de packaging
- ✓ Evaluation Inputs présent, renvoyant au cahier des charges sans dupliquer les critères
- ✓ Deferred to Build liste quatre points à résoudre à la génération
- ✓ Self-Test Summary présent en fin de document, énumérant les 40 points du contrôle
