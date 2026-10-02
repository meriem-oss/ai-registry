---
name: calculating-stock-coverage
description: This skill should be used when the user needs to know which product references risk running out of stock. Use it whenever someone asks about stock coverage, days of stock remaining, stockout risk, "rupture de stock", "combien de jours de stock", "qu'est-ce qui va manquer", or supplies a sales extract together with stock levels and supplier lead times — even if they do not use the word coverage. It computes sales velocity over the stated period, derives days of stock remaining per reference, estimates the stockout date, and classifies every reference against its own supplier lead time as already out of stock, imminent stockout, to watch, overstock, unknown lead time, or no sales history. It never invents a missing lead time or threshold. Do not use it to decide order quantities.
---

# Calculer la couverture de stock

La question « combien de jours de stock reste-t-il ? » n'a aucune valeur seule. Trente jours de stock, c'est confortable chez un fournisseur qui livre en une semaine, et c'est une rupture déjà jouée chez un fournisseur qui livre en six.

C'est pourquoi cette compétence ne rend pas un nombre de jours : elle rend un **classement par rapport au délai de chaque fournisseur**. C'est le délai qui transforme un chiffre en décision.

## Les calculs

Trois formules, dans cet ordre.

**Vitesse de vente** = ventes totales de la période ÷ durée de la période en jours

Additionner magasin et en ligne : c'est le même stock qui sert les deux. Conserver néanmoins les deux colonnes séparées dans le résultat, parce que la répartition entre les canaux intéresse le commerçant même si elle n'entre pas dans le calcul.

Si la durée de la période n'est pas précisée, la demander. Supposer 28 jours est tentant et faux une fois sur deux : un extrait peut couvrir un mois calendaire, une semaine, ou un trimestre.

**Jours de stock restants** = stock actuel ÷ vitesse de vente

**Date de rupture estimée** = date du jour + jours de stock restants

## Le classement

Appliquer dans cet ordre de priorité, et s'arrêter au premier cas qui correspond. L'ordre n'est pas décoratif : un produit sans vente et à stock nul relève du premier cas, pas du second.

| Ordre | Catégorie | Condition |
|---|---|---|
| 1 | **Déjà en rupture** | stock à zéro |
| 2 | **Sans historique** | aucune vente sur la période — couverture non calculable |
| 3 | **Délai inconnu** | le délai fournisseur est absent du référentiel |
| 4 | **Rupture imminente** | jours restants < délai fournisseur |
| 5 | **À surveiller** | jours restants ≤ 2 × délai fournisseur |
| 6 | **Surstock** | jours restants > seuil de surstock |
| 7 | **Normal** | aucun des cas ci-dessus |

Une référence désignée comme critique dans les seuils porte la mention « critique » et remonte d'un niveau d'urgence, selon cette table. L'idée : certains produits ne doivent jamais manquer, soit parce qu'ils font venir les clients, soit parce qu'ils sont saisonniers et qu'une rupture en pleine saison ne se rattrape pas.

| Catégorie calculée | Catégorie d'une référence critique |
|---|---|
| Normal | À surveiller |
| Surstock | Surstock (la remontée ne s'applique pas : le stock est là) |
| À surveiller | Rupture imminente |
| Rupture imminente | Rupture imminente, **placée en tête de sa catégorie** |
| Déjà en rupture, Sans historique, Délai inconnu | Inchangée |

Une référence critique ne passe jamais en « déjà en rupture » tant que son stock n'est pas à zéro : cette catégorie décrit un fait, pas un niveau d'inquiétude. Une catégorie fausse se lit comme une donnée fausse.

## Décalage entre la date du stock et la date du passage

Le stock est un instantané, souvent pris le lundi matin, et le passage peut avoir lieu plus tard dans la semaine. Calculer les dates à partir de la **date du stock**, l'indiquer dans le résultat, et si le passage a lieu après :

- marquer « **probablement déjà en rupture** » toute référence dont la date de rupture estimée est antérieure ou égale à la date du passage ;
- ne pas changer sa catégorie pour autant : on ne sait pas, on le soupçonne. Demander une vérification du stock réel.

Si la date du stock n'est pas donnée, la demander.

## Ce qu'il ne faut pas combler

Trois absences se signalent au lieu de se remplir.

**Un délai fournisseur manquant** ne devient jamais une moyenne des autres délais. Un délai moyen de 19 jours appliqué à un fournisseur qui livre en 45 produit une catégorie « à surveiller » rassurante sur un produit déjà perdu. Classer « délai inconnu » et le dire.

**Une référence sans vente** n'a pas de vitesse, donc pas de couverture. Elle ne doit surtout pas tomber en surstock parce que son stock est élevé : un produit lancé la semaine dernière avec 36 unités n'est pas en surstock, il est en attente de premières ventes. Deux situations très différentes, une seule mauvaise réponse possible.

**Un seuil de surstock absent** supprime la catégorie surstock. Ne pas en inventer un : ce seuil dépend de la trésorerie et de la durée de vie des produits, pas d'un usage général.

## Données aberrantes

Un stock négatif, un stock non numérique, des ventes négatives : signaler la ligne comme suspecte et l'exclure du calcul. Un stock négatif signale presque toujours un décalage d'inventaire, et le calculer donne une couverture négative qui n'a aucun sens. Mieux vaut une ligne écartée et visible qu'une ligne fausse et silencieuse.

## Ce que la compétence rend

La table d'entrée enrichie de quatre colonnes : **ventes par jour**, **jours de stock restants**, **date de rupture estimée**, **catégorie**. Les lignes critiques portent la mention.

Puis le décompte, qui est souvent l'information la plus parlante :

```
Déjà en rupture : [n]
Rupture imminente : [n]
À surveiller : [n]
Normal : [n]
Surstock : [n]
Sans historique : [n]
Délai inconnu : [n]
Total : [n] références
```

Arrondir les jours de stock à l'entier. La fausse précision d'un « 11,4 jours » suggère une exactitude que des ventes sur quatre semaines ne permettent pas.
