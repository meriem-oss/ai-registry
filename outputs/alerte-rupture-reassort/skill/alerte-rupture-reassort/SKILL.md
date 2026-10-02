---
name: alerte-rupture-reassort
description: This skill should be used when the user asks to run the weekly stockout and reorder check for a retail catalogue. Use it whenever someone says "alerte rupture", "réassort", "qu'est-ce qu'on commande cette semaine", "la liste de commandes", or attaches a weekly sales extract together with a stock file — even if they do not name the skill. It screens the inputs for personal data and missing rows, computes stock coverage against each supplier's lead time, proposes order quantities bounded by lead time, minimum order quantity and product shelf life, and produces a one-page order list grouped by supplier with the control report first and references already out of stock next, then stops for the manager's validation. It never contacts a supplier, never writes to the source files, and never processes customer data. Do not use it for a single ad-hoc coverage question on one product — calculating-stock-coverage answers that directly.
---

# Alerte rupture et réassort hebdomadaire

Produit chaque semaine la liste de ce qu'il faut commander, et pourquoi. Le point de départ : une rupture n'est pas un incident de stock, c'est une vente perdue qui ne se rattrape pas. Et sur un petit catalogue, elle se joue en amont, au moment où il reste moins de jours de stock que le fournisseur n'en met à livrer.

Ce workflow s'arrête deux fois, et c'est voulu. Une fois si les données ne sont pas saines, une fois avant que la moindre commande ne soit décidée.

## Ce qu'il faut avoir sous la main

| Élément | Forme attendue |
|---|---|
| Extrait des ventes magasin | **Agrégé par référence.** Référence, quantité vendue sur la période |
| Extrait des ventes en ligne | **Agrégé par référence.** Même format |
| Fichier de stock | Référence, libellé, stock actuel. Fait foi sur l'étendue du catalogue |
| Référentiel fournisseurs | Par référence : fournisseur, délai de livraison en jours, quantité minimum, conditionnement, durée de vie du produit. Gabarit : `references/gabarit-fournisseurs.md` |
| Dates de durabilité du stock présent | Par référence : lot, date de durabilité minimale. Gabarit : `references/gabarit-durabilite.md` |
| Seuils | Facultatif. À défaut, ceux de `references/seuils-par-defaut.md` s'appliquent, et le livrable le signale |

Le mot **agrégé** porte tout le poids : un export de commandes en ligne au format natif contient nom, e-mail et adresse de livraison, dont ce workflow n'a aucun besoin. L'agrégation se fait avant de fournir le fichier.

## Le déroulé

### 1. Contrôler et rassembler

Invoquer `screening-sales-data` sur les trois sources, avec le nombre de références attendues (celui du fichier de stock) et le seuil d'écart toléré.

**Si le contrôle échoue, s'arrêter là.** Reprendre tel quel ce que la compétence a renvoyé — les en-têtes en cause, ou les deux comptes — et demander un extrait corrigé. Ne pas poursuivre sur les colonnes restantes, ne pas produire de liste partielle « en attendant ». Un contrôle qui s'arrête a fait son travail.

Si une source est simplement absente, c'est différent : poursuivre sur celles disponibles, et le signaler en tête du livrable en nommant ce que cela rend incalculable. Sans les ventes en ligne, la couverture est sous-estimée, donc certaines ruptures passeront inaperçues — le lecteur doit le savoir.

### 2. Calculer la couverture et classer

Invoquer `calculating-stock-coverage` avec la table consolidée, la durée de la période, les délais fournisseurs et les seuils.

### 3. Proposer les quantités

Pour les seules catégories **déjà en rupture** et **rupture imminente** :

**Quantité visée** = vitesse de vente × (délai fournisseur + couverture cible) − stock actuel

Puis trois contraintes, dans cet ordre.

**Plafond de durée de vie.** La quantité ne doit pas représenter plus de jours de couverture que la durée de vie restante du produit, moins la marge d'écoulement. Un cosmétique a une date de durabilité : commander au-delà, c'est acheter une perte. C'est la contrainte que l'on oublie quand on raisonne en pure rotation.

**Quantité minimum et conditionnement.** Arrondir à la hausse, d'abord à la quantité minimum du fournisseur, puis au conditionnement.

**Les conflits ne se tranchent pas.** Si la quantité minimum dépasse le plafond de durée de vie, ou dépasse le besoin calculé de plus du seuil de signalement, poser les deux chiffres côte à côte et laisser décider. Exemple : « besoin 12, minimum fournisseur 48, dont 30 risquent de périmer avant écoulement ». Immobiliser de la trésorerie ou renoncer à une référence est un arbitrage commercial, et le trancher à la place du commerçant lui retire précisément l'information qu'il lui faut.

**Date limite de commande** = date de rupture estimée − délai de livraison.

Pour une rupture imminente, cette date est **toujours passée**, par construction : si les jours restants sont inférieurs au délai, la date limite tombe avant la date du stock. L'afficher ne dit donc rien de neuf. À la place, donner le **retard** en jours (date du stock − date limite) et le dire franchement : la commande est en retard, et l'ampleur du retard change la conversation avec le fournisseur (délai express, livraison partielle).

La date limite n'est utile que pour les références **à surveiller** : c'est là qu'elle est encore devant nous. Pour ces références, la calculer et l'afficher, sans proposer de quantité. C'est ce qui permet de commander à temps la semaine suivante au lieu de rattraper.

**Si la durée de vie est absente** — colonne manquante dans le référentiel ou fichier de durabilité non fourni — calculer les quantités quand même, mais l'écrire dans le contrôle (« plafond de durée de vie non appliqué ») et le rappeler dans les points d'attention de la pause validation. Ne pas inventer une durée de vie, même pour une famille de produits « connue ».

Aucune quantité n'est proposée pour une référence **sans historique** ou à **délai inconnu**. Elles vont dans leurs listes, pour décision manuelle.

### 4. Produire la liste

Suivre `references/format-livrable.md`. L'ordre y est délibéré : le compte rendu de contrôle d'abord, parce qu'il dit sur quelles données on décide ; puis ce qui est déjà perdu ; puis ce qui peut encore être sauvé.

Chaque ligne porte les chiffres qui la justifient — ventes de la période, stock, jours restants. Une quantité sans ses chiffres ne peut pas être contestée, et une proposition qu'on ne peut pas contester ne se relit pas vraiment.

### 5. S'arrêter pour validation

Présenter la liste et s'arrêter. Rien ne part sans décision humaine : ce workflow ne contacte aucun fournisseur, par aucun moyen.

Attirer l'attention sur les lignes où un arbitrage attend : quantités minimum disproportionnées, conflits de durée de vie, commandes en retard. Puis, dans cet ordre :

- les références critiques marquées « probablement déjà en rupture » (date de rupture estimée dépassée à la date du passage) : vérifier le stock réel avant de commander ;
- le volume total proposé, par fournisseur et au global, surtout si le plafond de durée de vie n'a pas pu être appliqué ;
- si plus du tiers du catalogue est en rupture ou imminente, le dire : ce n'est plus une semaine de réassort, c'est un rattrapage, et le réglage de la couverture cible est sans doute à revoir.

## Deux interdits

**Ne rien transmettre à un fournisseur.** Le livrable est un document interne. La transmission reste un geste humain, quelle que soit la demande.

**Ne pas écrire dans les sources.** Le fichier de stock et les extraits sont lus, jamais modifiés. Le livrable est un document nouveau.

## Clore la séance

Terminer par ce résumé. Il sert à deux choses : vérifier que rien n'a été sauté, et fournir la ligne de journal.

```
## Ce que j'ai fait

- Contrôle : [n] références traitées sur [attendu], aucune colonne identifiante
- Couverture calculée sur [n] jours de ventes, magasin et web
- Classement : [n] en rupture, [n] imminentes, [n] à surveiller
- [n] lignes de commande proposées ([n] unités au total), [n] arbitrages signalés
- Plafond de durée de vie : [appliqué | non appliqué]
- Pause validation : en attente de votre décision
- Livrable : ci-dessus

**Ligne de journal à conserver**
[date] · [n] en rupture · [n] imminentes · [n] lignes proposées · ajustements : ___
```

La ligne de journal se colle dans la feuille de suivi. Cette plateforme ne conserve rien d'une semaine sur l'autre : sans ce geste, il sera impossible dans deux mois de montrer que les ruptures ont baissé — et c'est pourtant la seule preuve que ce workflow sert à quelque chose. Le rappeler à chaque passage.
