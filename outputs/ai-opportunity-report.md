# Rapport d'opportunités IA

| | |
|---|---|
| **Rôle** | Entreprise de retail de cosmétiques (magasins physiques et vente en ligne) |
| **Date** | 2026-10-01 |
| **Vue** | Organisationnelle |
| **Opportunités identifiées** | 4 |
| **Recommandation n°1** | Alerte rupture et réassort : les ventes manquées à cause des ruptures freinent directement l'objectif de doubler les ventes, et les données nécessaires (tickets de caisse, ventes en ligne, fichiers Excel) existent déjà. |

## Tableau récapitulatif

| # | Opportunité | Autonomie | Implication | Impact |
|---|------------|----------|-------------|--------|
| 1 | Alerte rupture et réassort | Déterministe | Augmentée | Élevé |
| 2 | Bilan des ventes hebdomadaire | Déterministe | Automatisée | Moyen |
| 3 | Fichier et segmentation clientes | Guidée | Augmentée | Élevé |
| 4 | Contenus réseaux sociaux et promotions | Guidée | Augmentée | Élevé |

## Recommandations prioritaires

1. **Alerte rupture et réassort** : chaque rupture est une vente perdue, et aujourd'hui les commandes se décident au feeling sur des fichiers Excel.
2. **Fichier et segmentation clientes** : sans données clientes, l'objectif « cibler la bonne cliente » n'est pas atteignable. C'est la fondation de tout le marketing ciblé.
3. **Contenus réseaux sociaux et promotions** : c'est votre canal d'acquisition actuel. L'IA peut produire plus de contenu, plus régulièrement, aligné sur le stock réel.

## Fiches détaillées

### Déterministe

---

**1. Alerte rupture et réassort**

**Autonomie :** Déterministe
**Implication :** Augmentée

**Pourquoi c'est un bon candidat :**
Tâche répétitive, basée sur des règles claires (vitesse de vente, stock restant, délai fournisseur) et sur des données qui existent déjà : tickets de caisse, flux de ventes en ligne, fichiers Excel de stock.

**Problème actuel :**
Le stock est suivi « à la volée » dans des fichiers Excel. Les ruptures sont découvertes trop tard, les commandes sont décidées au feeling, ce qui crée à la fois des ruptures (ventes perdues) et du surstock (argent immobilisé).

**Comment l'IA aide :**
Chaque semaine, l'IA lit l'export des ventes (caisse et web) et le fichier de stock, calcule pour chaque produit le nombre de jours de stock restant, signale les produits qui vont manquer avant la prochaine livraison, et prépare une liste de commandes suggérées (produit, quantité, fournisseur). Vous validez avant d'envoyer.

**Pour commencer cette semaine :**
Exportez les ventes du dernier mois et votre fichier de stock actuel, et demandez à Claude de repérer les 10 produits les plus à risque de rupture.

**Objectif business :** Mieux gérer les ruptures, et donc doubler les ventes
**Parties prenantes :** Gérante (responsable), responsables magasin, responsable e-commerce, personne en charge des achats
**Indicateurs de succès :** Nombre de ruptures par mois, ventes perdues estimées, temps passé à préparer les commandes, valeur du surstock

---

**2. Bilan des ventes hebdomadaire**

**Autonomie :** Déterministe
**Implication :** Automatisée

**Pourquoi c'est un bon candidat :**
Même calcul chaque semaine, mêmes sources de données, format fixe. Idéal pour démarrer.

**Problème actuel :**
Les ventes magasin et en ligne sont dans des sources séparées. Il n'y a pas de vue simple de ce qui se vend, où, et ce qui progresse ou recule.

**Comment l'IA aide :**
Chaque lundi, l'IA rassemble les ventes de la semaine (caisse et web) et produit un bilan d'une page : top 10 des produits, produits en baisse, comparaison magasin et web, évolution par rapport à la semaine précédente, et 3 produits à mettre en avant.

**Pour commencer cette semaine :**
Donnez à Claude les exports de ventes de deux semaines et demandez un bilan comparatif d'une page.

**Objectif business :** Doubler les ventes
**Parties prenantes :** Gérante (responsable), responsables magasin, responsable e-commerce
**Indicateurs de succès :** Bilan livré chaque lundi, temps de préparation économisé, décisions prises à partir du bilan

---

### Guidée

---

**3. Fichier et segmentation clientes**

**Autonomie :** Guidée
**Implication :** Augmentée

**Pourquoi c'est un bon candidat :**
Classer des clientes par profil et rédiger des messages adaptés à chaque profil est un travail de tri et de rédaction, où l'IA est très efficace.

**Problème actuel :**
Il n'existe aucun fichier clientes : pas de carte fidélité, pas de liste e-mail. Impossible de savoir qui achète quoi, de relancer une cliente inactive ou de cibler une offre.

**Comment l'IA aide :**
Première étape : mettre en place une collecte simple des contacts (e-mail ou téléphone en caisse, comptes sur le site, inscription newsletter). Ensuite, l'IA classe régulièrement les clientes en groupes (fidèles, nouvelles, inactives, par type de produit acheté) et propose pour chaque groupe une offre et un message.

**Pour commencer cette semaine :**
Ajoutez en caisse et sur le site une question simple : « Voulez-vous recevoir nos offres ? » avec une petite remise à la clé, et centralisez les contacts dans un seul fichier.

**Objectif business :** Cibler la bonne cliente
**Parties prenantes :** Gérante (responsable), équipe en magasin, responsable e-commerce, responsable marketing
**Indicateurs de succès :** Nombre de contacts collectés par mois, taux de retour en magasin ou sur le site, taux d'ouverture des messages

---

**4. Contenus réseaux sociaux et promotions**

**Autonomie :** Guidée
**Implication :** Augmentée

**Pourquoi c'est un bon candidat :**
Rédaction de contenus réguliers, sur un format connu, à partir d'informations disponibles (produits, stock, promotions). L'IA propose et vous validez.

**Problème actuel :**
Les promotions et les publications sur les réseaux sociaux sont votre principal levier de ventes, mais elles demandent du temps et ne tiennent pas toujours compte du stock (risque de promouvoir un produit en rupture).

**Comment l'IA aide :**
Chaque semaine, l'IA prépare un calendrier de publications (textes, idées de visuels pour Canva, hashtags) et des idées de promotions, en mettant en avant les produits qui se vendent bien et ceux qui sont en surstock, sans jamais pousser un produit en rupture.

**Pour commencer cette semaine :**
Demandez à Claude, avec `/marketing:draft-content`, une semaine de publications Instagram pour 3 produits que vous voulez pousser.

**Objectif business :** Doubler les ventes
**Parties prenantes :** Responsable marketing ou réseaux sociaux (responsable), gérante, responsables magasin
**Indicateurs de succès :** Nombre de publications par semaine, engagement, ventes des produits mis en avant, temps passé à créer le contenu

---

## Workflow Candidate Summary

| Champ | Contenu |
|-------|---------|
| **Workflow** | Alerte Rupture Réassort |
| **Description** | Repère chaque semaine les produits qui risquent la rupture et prépare la liste des commandes à passer. |
| **Déclencheur** | Chaque lundi, à partir de l'export des ventes (caisse et web) et du fichier de stock Excel |
| **Livrable** | Liste des produits à risque (jours de stock restants) et liste de commandes suggérées, à valider |
| **Autonomie** | Déterministe |
| **Implication** | Augmentée |
| **Problème actuel** | Stock suivi à la volée sur Excel, commandes décidées au feeling, ruptures découvertes trop tard |
| **Opportunité IA** | Calculer la vitesse de vente et les jours de stock restants par produit, signaler les risques, proposer les quantités à commander |
| **Fréquence** | Hebdomadaire |
| **Priorité** | Haute |
| **Justification** | Impact direct sur les ventes, données déjà disponibles, aucune connexion d'outil nécessaire pour démarrer |
| **Vue** | Organisationnelle |
| **Objectif business** | Mieux gérer les ruptures, pour doubler les ventes |
| **Parties prenantes** | Gérante (responsable), responsables magasin, responsable e-commerce, achats |
| **Indicateurs de succès** | Ruptures par mois, ventes perdues estimées, temps de préparation des commandes, valeur du surstock |

**À construire en premier : Alerte Rupture Réassort.** C'est aussi un bon premier projet : environ 5 étapes, lancé à la main, à partir de fichiers que vous avez déjà, sans connexion d'outil à configurer.

**Vue individuelle :** à faire lors d'une prochaine session (les tâches que vous faites personnellement dans votre rôle).

## Annexe : définitions

**Autonomie : quelle part de décision a l'IA ?**

- **Déterministe** : l'IA suit des règles fixes, sans jugement. Même entrée, même résultat. Exemples : calculer un stock, mettre en forme un rapport.
- **Guidée** : l'IA prend des décisions encadrées. Vous fixez la direction, l'IA choisit comment faire. Exemples : rédiger des publications, classer des clientes.
- **Autonome** : l'IA planifie, décide et s'adapte seule. Exemples : veille concurrentielle, tri et routage automatique de demandes.

**Implication humaine : êtes-vous présente pendant l'exécution ?**

- **Augmentée** : vous participez pendant le déroulement (vous relisez, validez, orientez).
- **Automatisée** : l'IA travaille seule du début à la fin, vous relisez seulement le résultat.
