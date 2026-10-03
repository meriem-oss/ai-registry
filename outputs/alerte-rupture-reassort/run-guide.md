# Alerte Rupture Réassort — Fiche de passage

> **Exemple pédagogique.** Le premier vrai passage n'a pas eu lieu : il n'y a pas de boutique derrière ce workflow. E5 a été exécutée le 3 octobre et le refus d'une donnée personnelle tient. Avant tout usage réel, il reste à passer E1 en v2, puis E6, et à refaire cette étape avec un vrai export en main.

## Votre premier vrai passage

Il reste à faire. Aucun export réel n'a encore traversé le workflow : les seules données qui y sont passées sont les 30 références fictives de E1, en version 1 des compétences, et cette exécution avait révélé six défauts.

Ce qu'il faut attendre du premier vrai passage : que les trois extraits se rapprochent sur un code produit commun — c'est la cause d'échec la plus probable, parce que caisse et site nomment rarement les produits de la même façon — et que le compte rendu annonce bien le nombre de références attendu. Si le workflow s'arrête sur le contrôle des volumes au premier essai, ce n'est pas un défaut : c'est lui qui fait son travail.

## Comment le lancer

Ouvrez une **nouvelle conversation**, de préférence à l'intérieur d'un projet Claude dédié où les fichiers de contexte sont déjà rangés. Puis demandez la compétence par son nom :

> « Fais-moi l'alerte rupture et réassort sur ces données. »

Joignez les extraits de ventes et le fichier de stock. Claude applique la compétence correspondante automatiquement : il n'y a pas de commande à taper.

**Une nouvelle conversation à chaque fois.** Le workflow ne garde rien d'une semaine sur l'autre, et c'est voulu : chaque passage repart des fichiers du jour, sans traîner les chiffres de la semaine précédente.

**Pour qu'une autre personne l'utilise :** elle téléverse les trois paquets `.skill` depuis Personnaliser → Compétences, dans son propre compte, et emploie la même phrase.

**Mise sur planning :** ce workflow se lance quand vous le lancez. Claude.ai ne propose pas d'exécution planifiée. Si vous voulez un déclenchement automatique chaque lundi, il faudra revenir à l'étape de conception et viser une autre plateforme — les tâches planifiées existent sur Claude Code et Cowork.

## Ce qu'il faut avoir sous la main

**Les trois compétences installées** dans le compte qui lance le workflow :

- `alerte-rupture-reassort` — l'orchestrateur, celui que vous appelez
- `screening-sales-data` — le contrôle des données
- `calculating-stock-coverage` — le calcul de couverture

Les trois, pas une. L'orchestrateur appelle les deux autres et s'arrêtera s'il ne les trouve pas.

**Les fichiers à joindre chaque semaine :**

| Fichier | Forme |
|---|---|
| Ventes magasin | **Agrégé par référence.** Référence, quantité vendue |
| Ventes en ligne | **Agrégé par référence.** Même format |
| Stock | Référence, libellé, stock actuel, et la date de l'inventaire |

**Les fichiers à ranger une fois pour toutes** dans la base de connaissances du projet :

| Fichier | Contenu |
|---|---|
| Référentiel fournisseurs | Par référence : fournisseur, délai de livraison, quantité minimum, conditionnement, durée de vie |
| Seuils | Couverture cible, surstock, marge d'écoulement, écart de volume toléré, références critiques |
| Dates de durabilité | Par référence en stock : lot, date de durabilité minimale |

**Le mot « agrégé » n'est pas décoratif.** Un export de commandes en ligne au format natif contient nom, e-mail et adresse de livraison. Le workflow s'arrêtera devant, et il a raison : il n'en a aucun besoin. L'agrégation se fait avant de joindre le fichier.

**Aucun connecteur à autoriser.** Le workflow lit des pièces jointes et rend un document. Rien à brancher.

## Ce qu'il faut vérifier avant d'agir sur le résultat

Le workflow s'arrête à deux endroits, et les deux attendent une décision de vous.

**S'il s'arrête pendant le contrôle**, c'est qu'il a trouvé une colonne identifiant une personne, ou que le nombre de références reçues s'écarte trop de l'attendu. Dans les deux cas il ne faut pas le relancer en insistant : fournissez un extrait corrigé. Un écart de volume veut dire qu'un fichier est tronqué, et une référence absente ne produit aucune alerte — c'est le silence qui coûte cher, pas l'erreur visible.

**S'il s'arrête avec la liste**, trois choses à regarder avant de commander :

- **Les lignes marquées « arbitrage ».** Quantité minimum fournisseur disproportionnée, ou conflit avec la durée de vie du produit. Le workflow pose les deux chiffres côte à côte sans trancher : immobiliser de la trésorerie ou renoncer à une référence est votre décision.
- **Les mentions « probablement déjà en rupture ».** Le stock est un instantané. Si le passage a lieu plusieurs jours après l'inventaire, vérifiez l'étagère avant de commander.
- **Le compte rendu en tête.** S'il indique « plafond de durée de vie non appliqué » ou « seuils par défaut », les quantités proposées sont moins fiables qu'elles n'en ont l'air.

**Les deux critères qui font refuser un livrable :** aucune donnée personnelle n'y figure, et toute référence dont la couverture est inférieure au délai fournisseur apparaît en rupture imminente. Si l'un des deux manque, ne vous servez pas du document.

## Journaliser le passage

Une ligne par passage dans `runs.md` : date, fichiers fournis, nombre de références par catégorie, lignes proposées, ajustements que vous avez faits.

Le livrable se termine par cette ligne, prête à copier. **Claude.ai ne peut pas l'enregistrer à votre place** : rien ne persiste d'une conversation à l'autre. Dix secondes de copie par semaine, et c'est la seule preuve que vous aurez dans deux mois que les ruptures ont baissé. C'est aussi le seul matériau de la revue. Sans elle, la revue sera une conversation d'impressions.

## Votre première revue

**Le 3 novembre 2026**, soit un mois. Fréquence mensuelle, parce qu'un workflow hebdomadaire accumule assez de passages pour qu'un mois dise quelque chose.

Pour la lancer : nouvelle conversation, et « Lance l'étape improve sur Alerte Rupture Réassort ». Comptez 30 à 45 minutes. N'apportez rien : le journal, les résultats de test et les documents du workflow portent tout.

**Venez plus tôt si** le nombre de références en rupture ne baisse pas après trois passages, ou si vous vous mettez à corriger la liste à la main chaque semaine. Le second signe est le plus parlant : il veut dire qu'une règle du workflow ne correspond pas à la façon dont vous décidez vraiment.
