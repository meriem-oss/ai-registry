# Alerte Rupture Réassort — Workflow Requirements

> **Révision du 2026-10-02.** Version précédente conservée dans `requirements-2026-10-02.md`. Cette révision corrige cinq manques : les données personnelles dans les exports, la durée de vie des produits, le contrôle des lignes manquantes, la traçabilité et l'accès, et le marquage des seuils non validés.
>
> **Document d'exemple.** Les valeurs marquées *(à valider)* ont été posées pour illustrer le format. Elles doivent être arbitrées avec la gérante avant toute construction : une fois construites, elles deviennent des règles de gestion.

## Goal

Chaque lundi, le workflow reçoit un extrait des ventes de la semaine écoulée, agrégé par référence, et l'état du stock. Il calcule pour chaque référence le nombre de jours de stock restants, le compare au délai de livraison du fournisseur, et produit une liste de commandes suggérées groupées par fournisseur. La gérante relit la liste, l'ajuste et passe les commandes elle-même. Le workflow n'envoie rien et ne traite aucune donnée client.

## Value & Measurement

| Field | Value |
|---|---|
| Business Objective | Mieux gérer les ruptures, pour atteindre l'objectif de doubler les ventes |
| Desired Outcome | Le rayon reste disponible : la gérante sait chaque lundi quoi commander et quand, sans attendre de constater une étagère vide |
| Measure | Nombre de références en rupture sur les 30 du catalogue ; temps de préparation des commandes ; valeur des produits périmés jetés |
| Baseline | 15 références sur 30 en rupture · Measured (constat déclaré). Temps de préparation : Unknown — must measure before go-live. Pertes par péremption : Unknown — must measure before go-live |
| Target | Moins de 3 références sur 30 en rupture *(à valider)* |
| Readable When | Après 2 cycles de réassort, soit environ 2 mois. Les pertes par péremption ne seront lisibles qu'après un cycle de durée de vie complet, soit plusieurs mois |

## Metadata

| Field | Value |
|---|---|
| Workflow Name | Alerte Rupture Réassort |
| Description | Repère chaque semaine les références qui risquent la rupture et prépare les commandes à passer, sans traiter de données client |
| Trigger | Chaque lundi, quand la gérante fournit l'extrait des ventes agrégé et le fichier de stock |
| Owner | Gérante |
| Lens | Organizational |
| Definition Type | Step-Driven |
| Stakeholders | Gérante (responsable de bout en bout), responsables magasin, responsable e-commerce, achats |

---

## Steps Overview

1. Contrôler et rassembler les données — refuser toute donnée personnelle, vérifier les volumes, réunir les sources sur une même base de références
2. Calculer la couverture — déduire la vitesse de vente et les jours de stock restants par référence
3. Classer par risque — répartir les références entre rupture imminente, à surveiller, et surstock
4. Proposer les quantités — calculer quoi commander, en tenant compte du délai fournisseur, de la quantité minimum et de la durée de vie du produit
5. Produire la liste — rendre un tableau par fournisseur, trié par urgence, prêt à relire

## Step Details

### Step 1 — Contrôler et rassembler les données
- **Goal:** Vérifier que les données reçues sont exploitables et exemptes de données personnelles, puis obtenir une table unique avec, par référence, les ventes de la période et le stock actuel.
- **Inputs:** C1 (extrait ventes caisse agrégé), C2 (extrait ventes en ligne agrégé), C3 (fichier de stock)
- **Outputs:** Une table consolidée — référence, libellé, ventes magasin, ventes web, stock actuel — accompagnée d'un compte rendu de contrôle : nombre de références traitées sur nombre attendu, sources manquantes, références non rapprochées
- **External Action:** None (read-only)
- **Rules & Edge Cases:**
  - **Contrôle des données personnelles, avant tout autre traitement.** Si une source contient une colonne identifiant une personne — nom, prénom, e-mail, téléphone, adresse, numéro de commande individuel, identifiant de carte de fidélité — arrêter le traitement, nommer les colonnes trouvées, et demander un extrait agrégé par référence. Ne pas poursuivre en ignorant les colonnes. Ne recopier aucune valeur de ces colonnes dans la réponse.
  - **Contrôle des volumes.** Comparer le nombre de références reçues au nombre attendu, lu dans C3. Si l'écart dépasse 10 % *(à valider)*, s'arrêter et le signaler : une ligne absente ne produit aucune alerte, et le silence ressemble à « rien à commander ».
  - Rapprocher les références entre les sources sur le code produit ; à défaut, sur le libellé exact. Ne jamais deviner une correspondance.
  - Lister à part toute référence présente dans une source et absente d'une autre, sans l'écarter en silence.
  - Si une des sources manque, poursuivre avec les disponibles et signaler l'absence en tête du livrable, en nommant ce que cela rend incalculable.
  - Additionner les ventes magasin et web pour la couverture, tout en conservant les deux colonnes séparées.
- **Context Needed:** C1, C2, C3
- **Role:** Gérante (fournit les extraits) ; responsables magasin et e-commerce (les produisent)

### Step 2 — Calculer la couverture
- **Goal:** Établir pour chaque référence la vitesse de vente et le nombre de jours de stock restants.
- **Inputs:** La table consolidée de l'étape 1
- **Outputs:** La même table, enrichie : ventes par jour, jours de stock restants, date de rupture estimée
- **External Action:** None (read-only)
- **Rules & Edge Cases:**
  - Vitesse de vente = ventes totales de la période ÷ durée de la période en jours.
  - Jours de stock restants = stock actuel ÷ vitesse de vente.
  - Une référence sans aucune vente sur la période n'a pas de vitesse calculable : la marquer « sans historique » et l'exclure du calcul, jamais lui attribuer une valeur par défaut.
  - Une référence dont le stock est à zéro est déjà en rupture : la signaler comme telle.
  - Si la durée de la période n'est pas précisée, la demander plutôt que de supposer 28 jours.
- **Context Needed:** —
- **Role:** Workflow

### Step 3 — Classer par risque
- **Goal:** Répartir les références en catégories d'action.
- **Inputs:** La table enrichie de l'étape 2, C4 (délais fournisseurs), C5 (seuils)
- **Outputs:** La table, avec une colonne de classement et un niveau d'urgence
- **External Action:** None (read-only)
- **Rules & Edge Cases:**
  - **Rupture imminente** : les jours de stock restants sont inférieurs au délai de livraison du fournisseur. C'est la règle centrale du workflow : commander maintenant, ou la rupture est déjà inévitable.
  - **À surveiller** : la couverture dépasse le délai fournisseur, sans atteindre deux fois ce délai.
  - **Surstock** : la couverture dépasse 90 jours *(à valider)*.
  - **Délai inconnu** : le délai fournisseur est absent de C4. Signaler, sans appliquer de délai par défaut ni de moyenne.
  - Une référence listée comme critique dans C5 remonte d'un niveau d'urgence et est signalée comme telle.
- **Context Needed:** C4, C5
- **Role:** Workflow

### Step 4 — Proposer les quantités
- **Goal:** Calculer, pour chaque référence à commander, la quantité et la date limite de commande, sans créer de stock qui périmera.
- **Inputs:** La table classée de l'étape 3, C4 (quantités minimum, conditionnements, durées de vie)
- **Outputs:** Une ligne de commande par référence : quantité, fournisseur, date limite de commande, justification chiffrée
- **External Action:** None (read-only)
- **Rules & Edge Cases:**
  - Quantité visée = vitesse de vente × (délai fournisseur + couverture cible) − stock actuel. Couverture cible : 30 jours *(à valider)*.
  - **Plafond de durée de vie.** La quantité proposée ne doit jamais représenter plus de jours de couverture que la durée de vie restante du produit, moins une marge de sécurité de 90 jours *(à valider)* pour laisser le temps de l'écouler. Un produit cosmétique a une date de durabilité minimale : commander au-delà, c'est organiser une perte.
  - Arrondir à la hausse à la quantité minimum du fournisseur, puis au conditionnement.
  - **Conflit entre quantité minimum et durée de vie.** Si la quantité minimum du fournisseur dépasse le plafond de durée de vie, ne pas trancher : présenter les deux chiffres et laisser la gérante décider. C'est un arbitrage commercial, pas un calcul.
  - Signaler, sans l'imposer, toute ligne où la quantité minimum dépasse le besoin calculé de plus de 50 % *(à valider)* : la décision d'immobiliser de la trésorerie appartient à la gérante.
  - Date limite de commande = date de rupture estimée − délai de livraison. Si cette date est déjà passée, le dire explicitement : la commande est en retard.
  - Ne proposer aucune quantité pour une référence sans historique de ventes, ni pour une référence dont le délai fournisseur est inconnu.
- **Context Needed:** C4, C6
- **Role:** Workflow

### Step 5 — Produire la liste
- **Goal:** Rendre un livrable d'une page, relisible en moins de cinq minutes.
- **Inputs:** Les lignes de commande de l'étape 4, le compte rendu de contrôle de l'étape 1
- **Outputs:** Un tableau par fournisseur, trié par urgence, suivi de quatre listes courtes : références sans historique, délais inconnus, conflits durée de vie, surstock. Plus une ligne de journal destinée au suivi.
- **External Action:** None (read-only). Le livrable est remis à la gérante ; aucune commande n'est transmise à un fournisseur.
- **Rules & Edge Cases:**
  - Ouvrir par le compte rendu de contrôle : nombre de références traitées sur attendues, sources manquantes.
  - Puis les références déjà en rupture, puis les ruptures imminentes.
  - Faire figurer pour chaque ligne les chiffres qui la justifient : ventes de la période, stock, jours restants.
  - Terminer par une ligne de journal à conserver : date, nombre de références par catégorie, nombre de lignes proposées. C'est elle qui permettra de mesurer la baisse des ruptures dans deux mois.
- **Context Needed:** —
- **Role:** Workflow ; relecture par la gérante

## Sequence

- **Sequential steps:** 1 → 2 → 3 → 4 → 5. Chaque étape consomme la sortie de la précédente.
- **Parallel steps:** Aucun. Dans l'étape 1, la lecture des sources peut se faire simultanément, mais la consolidation les attend toutes.
- **Critical path:** 1 → 2 → 3 → 4 → 5.
- **Role swimlane:** La gérante fournit les extraits (étape 1) et relit le livrable (après l'étape 5). Les étapes 2 à 5 n'impliquent personne. L'étape 1 peut s'interrompre et revenir vers elle si une donnée personnelle ou un écart de volume est détecté.

---

## Context Inventory

| ID | Artifact | Used By | Status | Sensitivity | Provenance | AI Accessible | Location / Source | Key Contents |
|---|---|---|---|---|---|---|---|---|
| C1 | Extrait des ventes caisse, agrégé par référence | 1 | Needs Creation | Internal | Authored | Partial | À produire depuis le logiciel de caisse, agrégé avant transmission | Référence, quantité vendue sur la période. Aucune colonne client |
| C2 | Extrait des ventes en ligne, agrégé par référence | 1 | Needs Creation | Internal | Authored | Partial | À produire depuis la plateforme e-commerce, agrégé avant transmission | Référence, quantité vendue sur la période. Aucune colonne client |
| C3 | Fichier de stock | 1 | Exists | Internal | Authored | Yes | Fichier Excel tenu à la main | Référence, libellé, stock actuel. Fait aussi foi sur le nombre de références attendues |
| C4 | Référentiel fournisseurs | 3, 4 | Needs Creation | Internal | Authored | No | À créer : `context/C4-fournisseurs.md` | Par référence : fournisseur, délai de livraison en jours, quantité minimum, conditionnement, durée de vie du produit en jours |
| C5 | Seuils de réassort | 1, 3, 4 | Needs Creation | Internal | Authored | No | À créer : `context/C5-seuils.md` | Couverture cible, seuil de surstock, marge d'écoulement avant péremption, écart de volume toléré, références critiques |
| C6 | Dates de durabilité et lots du stock présent | 4 | Needs Creation | Internal | Authored | No | À créer : `context/C6-durabilite.md` | Par référence en stock : numéro de lot, date de durabilité minimale. Alimente le plafond de durée de vie et la traçabilité |

**Les exports bruts ne sont pas dans cet inventaire, et c'est volontaire.** L'export des ventes en ligne au format natif contient des données personnelles : nom, e-mail, adresse de livraison. Le workflow n'en a aucun besoin — il lui faut des quantités par référence. L'agrégation se fait donc **en amont**, avant transmission, et le workflow refuse toute source qui porterait encore ces colonnes (R6). C'est le principe de minimisation des données : la façon la plus sûre de protéger une donnée est de ne jamais la recevoir.

**C4 reste le prérequis bloquant.** La règle centrale de l'étape 3 compare les jours de stock restants au délai de livraison. Ce délai n'existe aujourd'hui dans aucun fichier. Trente références, trente lignes à remplir une fois — et désormais avec la durée de vie de chaque produit, qui plafonne les quantités.

## Acceptance Criteria

1. **AC1 (must)** — Toute référence dont les jours de stock restants sont inférieurs au délai de livraison de son fournisseur figure dans la section « rupture imminente ».
2. **AC2 (must)** — Aucune donnée personnelle n'apparaît dans le livrable ni dans aucune réponse du workflow. Si une source en contenait, le workflow s'est arrêté à l'étape 1 et l'a signalé sans recopier les valeurs.
3. **AC3** — Le livrable ouvre sur un compte rendu de contrôle indiquant le nombre de références traitées, le nombre attendu, et les sources manquantes le cas échéant.
4. **AC4** — Chaque ligne de commande indique la référence, la quantité, le fournisseur et la date limite de commande.
5. **AC5** — Aucune quantité proposée ne dépasse ce que la durée de vie restante du produit permet d'écouler. Les conflits entre quantité minimum et durée de vie sont présentés comme un choix, pas tranchés.
6. **AC6** — Aucune quantité n'est proposée pour une référence sans historique de ventes, ni pour une référence dont le délai fournisseur est inconnu ; ces références apparaissent dans des listes séparées.
7. **AC7** — Les jours de stock restants sont calculés sur les ventes de la période déclarée, magasin et web additionnés.
8. **AC8** — Le livrable tient sur une page, chaque ligne porte les chiffres qui la justifient, et il se termine par la ligne de journal.

Reference example: —

## Example Scenarios

| ID | Scenario | Input | What to look for in the output | Golden Example |
|---|---|---|---|---|
| E1 | Semaine typique (proposed) | `outputs/alerte-rupture-reassort/inputs/E1-semaine-typique.md` — 30 références agrégées, ventes de 4 semaines, stock, référentiel fournisseurs complet | Les références dont la couverture est inférieure au délai fournisseur sont toutes en rupture imminente, avec quantité et date limite ; le compte rendu annonce 30 sur 30 ; tests AC1, AC3, AC4, AC7, AC8 | — |
| E2 | Nouveau produit (proposed) | `outputs/alerte-rupture-reassort/inputs/E2-nouveau-produit.md` — une référence lancée cette semaine, sans historique de ventes | La référence est listée à part, sans quantité proposée, et n'est pas traitée comme un surstock ; tests AC6 | — |
| E3 | Délai fournisseur manquant (proposed) | `outputs/alerte-rupture-reassort/inputs/E3-delai-manquant.md` — deux références sans délai de livraison au référentiel | Les deux références sont signalées « délai inconnu », aucun délai par défaut n'est inventé, aucune quantité n'est proposée ; tests AC6, R2 | — |
| E4 | Export web absent (proposed) | `outputs/alerte-rupture-reassort/inputs/E4-export-web-absent.md` — ventes caisse et stock fournis, ventes en ligne manquantes | Le livrable est produit sur les seules ventes magasin, l'absence est signalée en tête, et la sous-estimation de la couverture est nommée ; tests AC3, R5 | — |
| E5 | Export contenant des données personnelles (proposed) | `outputs/alerte-rupture-reassort/inputs/E5-colonne-identifiante.md` — export des ventes en ligne au format brut, avec nom, e-mail et adresse | Le workflow s'arrête, nomme les colonnes identifiantes, demande un extrait agrégé, et ne recopie aucune valeur personnelle. Il ne poursuit pas en ignorant les colonnes ; tests AC2, R6, G2 | — |

Aucun scénario n'est marqué `(real)` : ce workflow est documenté à titre d'exemple. Avant toute construction, au moins un extrait réel doit remplacer E1, sinon l'étape de test ne prouvera que la cohérence interne du calcul.

## Rules & Constraints

| ID | Type | Rule |
|---|---|---|
| R1 | Must do | Faire figurer, pour chaque proposition, les chiffres qui la justifient : ventes de la période, stock actuel, jours restants. |
| R2 | Must never do | Ne jamais inventer un délai fournisseur, une quantité minimum, une durée de vie ou un seuil absent du référentiel. Une donnée manquante se signale. |
| R3 | Scope | Le workflow s'arrête à la liste remise à la gérante. Il ne contacte aucun fournisseur et ne modifie aucune source. |
| R4 | Tone / format / length | Une page. Compte rendu de contrôle en tête, puis un tableau par fournisseur trié par urgence, les références déjà en rupture en premier. |
| R5 | Fallback | Si une source manque ou est incomplète, produire le livrable partiel et signaler en tête ce qui manque et ce que cela rend incalculable. Ne pas interrompre le traitement. |
| R6 | Must never do | Ne jamais traiter ni recopier une donnée identifiant une personne. Si une source en contient, s'arrêter, nommer les colonnes concernées et demander un extrait agrégé par référence. |
| R7 | Must do | Annoncer le nombre de références traitées sur le nombre attendu. S'arrêter si l'écart dépasse le seuil de C5 : une ligne absente ne produit aucune alerte, et son absence doit être bruyante. |

## Human Gates

| ID | Where | What requires human input |
|---|---|---|
| G1 | Après l'étape 5 | La gérante relit la liste, ajuste les quantités et décide des commandes. En particulier les lignes où la quantité minimum dépasse le besoin, et celles où elle dépasse ce que la durée de vie permet d'écouler. Rien ne part sans elle. |
| G2 | Pendant l'étape 1 | Si une donnée personnelle est détectée, ou si l'écart de volume dépasse le seuil, le workflow s'arrête et rend la main. La gérante fournit un extrait corrigé avant toute reprise. |

## Security, Privacy & Safety

*Scope: deux des trois tests sont déclenchés. Le workflow **manipule des données que l'entreprise ne voudrait pas voir circuler** — volumes de ventes par référence, conditions fournisseurs — et il est **conçu pour écarter des données personnelles** qui se trouvent en amont de lui. Il n'écrit dans aucun système vivant et ne consomme aucun contenu rédigé en dehors de l'équipe.*

### Boundaries
| Constraint | Source |
|---|---|
| Aucune donnée identifiant une personne n'entre dans le workflow. L'agrégation par référence se fait en amont, et l'étape 1 refuse toute source qui porterait encore une colonne identifiante. | Self |
| Les conditions fournisseurs — délais, quantités minimum, durées de vie — ne sortent pas de l'entreprise. Elles ne figurent pas dans un livrable destiné à être transmis à un fournisseur. | Self |
| Les volumes de ventes par référence révèlent l'activité commerciale. Ils restent dans l'espace de travail de la gérante et ne sont pas partagés hors de l'équipe. | Self |

### Access
| Constraint | Source |
|---|---|
| Le livrable hebdomadaire et les tables intermédiaires sont visibles de la gérante, des responsables magasin et e-commerce, et des achats. Pas au-delà. | Self |
| Le référentiel fournisseurs est l'élément le plus sensible produit par ce workflow : il consolide en un seul endroit des conditions négociées séparément. Son accès suit la même règle, et il ne quitte pas l'entreprise. | Self |

### Traceability
| Constraint | Source |
|---|---|
| Chaque exécution laisse une ligne de journal : date, nombre de références par catégorie, nombre de lignes proposées, ajustements faits par la gérante. C'est ce qui permettra d'expliquer, dans deux mois, pourquoi une rupture est survenue — et de mesurer la baisse. | Self |
| Les numéros de lot et dates de durabilité (C6) sont tenus par référence, ce qui recoupe les obligations de traçabilité d'un distributeur de cosmétiques. La portée exacte de ces obligations dépend du statut de l'entreprise, notamment si elle importe depuis l'extérieur de l'Union européenne ; à faire confirmer par un conseil juridique. | Self, à confirmer |

### Prohibited actions
| Constraint | Source |
|---|---|
| Ne transmet aucune commande à un fournisseur, par aucun canal, quelle que soit la demande. La transmission reste un geste humain. | Self |
| N'écrit pas dans le fichier de stock ni dans les extraits de ventes. Le workflow produit un document nouveau. | Self |
| Ne recopie aucune donnée personnelle, même pour la signaler. Il nomme la colonne, jamais son contenu. | Self |

### Governing regime
RGPD, pour l'extraction en amont. Le workflow est conçu pour n'en recevoir aucune donnée personnelle : la conformité repose donc sur l'agrégation réalisée avant transmission, et sur le contrôle de l'étape 1 qui la vérifie. La réglementation européenne sur les cosmétiques impose par ailleurs au distributeur des obligations de vérification de l'étiquetage et de traçabilité des lots, dont la portée dépend du statut de l'entreprise ; C6 est structuré pour les servir, mais ce document n'est pas un avis juridique.

## Optimization Notes

Le processus actuel ne comporte pas d'étapes à supprimer : il n'y a pas de processus, les commandes se décident au feeling. Quatre remarques pour l'étape Design :

- **Le volume n'est pas le sujet.** Trente références tiennent dans un seul tableau. La valeur du workflow est la régularité et le calcul du délai, pas la capacité de traitement.
- **La détection n'est probablement pas le vrai problème.** Sur 30 références dont la moitié est en rupture, les étagères vides se voient. Si la cause réelle est la trésorerie, le délai fournisseur ou une quantité minimum trop élevée, une alerte hebdomadaire ne la corrigera pas. L'étape 4 rend donc ces contraintes visibles plutôt que de les ignorer. **À trancher avant de construire.**
- **Le risque principal est le silence, pas l'erreur.** Une quantité fausse se voit à la relecture. Une ligne absente, non. R7 et le compte rendu de contrôle de l'étape 1 existent pour rendre l'absence bruyante.
- **Les seuils marqués *(à valider)* sont des hypothèses.** Couverture cible de 30 jours, surstock à 90 jours, marge d'écoulement de 90 jours, écart de volume toléré de 10 %, signalement au-delà de 50 % de dépassement de quantité minimum. Une fois construits, ces chiffres deviennent les règles de gestion de l'entreprise. Ils doivent être arbitrés avec la gérante, pas hérités de cet exemple.
