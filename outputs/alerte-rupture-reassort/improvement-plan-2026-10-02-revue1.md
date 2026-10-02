# Alerte Rupture Réassort — Revue

Date : 2026-10-02. Revue menée le jour même de la mise en service, non à l'échéance du 3 novembre.

## Ce que cette revue a pu établir, et ce qu'elle n'a pas pu

**La revue ne peut pas être conduite comme prévu.** Elle s'appuie sur trois sources, et les trois sont vides ou non valides :

| Source | État | Conséquence |
|---|---|---|
| Journal des passages | Une ligne, issue d'un test, pas d'un passage réel | Aucune fréquence d'usage, aucune tendance d'édition, aucune dérive observable |
| Base de référence (tournée 2) | Verdict « prêt » par convention, zéro entrée exécutée | Rien à comparer. Un diff contre une ligne vide ne dit rien |
| Contexte métier | Inchangé depuis la conception, le même jour | Aucun signal de changement de besoin |

C'est le constat principal de cette revue, et il vaut mieux qu'un faux bilan : **l'étape 7 est celle qui révèle si les précédentes ont été faites sérieusement.** Sans journal tenu et sans tournée de test exécutée, elle n'a pas de matière. Un workflow qui tourne depuis un mois avec quatre lignes de journal produit une revue utile ; celui-ci n'en produit aucune.

## Résumé de performance

Aucune donnée d'usage. Un seul passage enregistré, le 02/10, sur 30 références fictives en version 1 des compétences. Il avait révélé six défauts, dont quatre de spécification.

## Régression

**Non mesurable.** La base de référence de la tournée 2 ne contient aucun résultat exécuté : il n'existe pas de ligne à comparer. Le tableau des lignes retournées est donc vide, non parce que rien n'a bougé, mais parce que rien n'a été mesuré.

La seule comparaison disponible est historique : la tournée 1 avait donné 15 lignes tenues sur 18 exécutables, avec trois manques ayant une cause unique — une section absente du livrable. Corrigée en v2, sans vérification.

**Tendance d'édition :** une seule observation, « mineure ». Une observation n'est pas une tendance.

## Problèmes identifiés

| # | Problème | Cause diagnostiquée | Brique |
|---|---|---|---|
| 1 | La compétence installée ne correspond pas à la spécification | `calculating-stock-coverage` tourne en v1 : 4 960 octets, sans la table de remontée des références critiques ni le traitement du décalage de date de stock. Le cahier des charges révision 3 et le plan décrivent la v2. Deux téléversements ont eu lieu, les deux ont pris le mauvais fichier — même nom, deux cartes différentes dans la conversation | **S3** |
| 2 | Deux critères obligatoires ne sont pas éprouvés | AC2 et R6 — le refus d'une donnée personnelle — n'ont été « tenus » que sur une entrée qui ne contenait rien à refuser. Le scénario dédié, E5, n'a jamais tourné | **Test**, pas une brique |
| 3 | Le plafond de durée de vie n'a jamais été exercé | AC5 et AC10. L'entrée E6 a été créée pour cela et n'a pas tourné | **Test**, pas une brique |
| 4 | Le journal ne se tiendra probablement pas | Claude.ai ne peut pas écrire de fichier : la ligne de journal dépend d'un geste humain hebdomadaire. C'est la contrainte acceptée du design, et c'est déjà elle qui rend cette revue muette, le jour même | **Conception** — à rouvrir si le journal reste vide à la prochaine revue |

Le problème 1 est le seul qui soit un vrai écart entre l'installé et le spécifié. Les trois autres sont des lacunes de vérification ou une limite assumée de la plateforme.

## Évaluation de graduation

**Aucune graduation justifiée.** Le workflow reste déterministe : même ordre d'étapes, mêmes formules, aucune décision de séquencement à prendre à l'exécution. Rien n'indique qu'il ait besoin de devenir un agent.

Le seul motif qui pourrait faire évoluer le mécanisme n'est pas la complexité mais la **plateforme** : si le journal reste vide et que la mesure de l'amélioration devient impossible, le bon arbitrage serait de porter le workflow sur une plateforme capable d'écrire un fichier et de se planifier. Ce serait un retour à la conception, pas une graduation.

## Recommandation

**Régler.** Une seule brique nommée.

**S3 — `calculating-stock-coverage`** : la v1 installée doit être remplacée par la v2. Ce n'est pas une régénération : le fichier correct existe déjà, à la fois dans l'arborescence du workflow et sur l'ordinateur de l'utilisatrice, à `C:\Users\meragu.GIFI\Downloads\calculating-stock-coverage.skill`. C'est un problème d'installation, pas de construction. Supprimer d'abord l'entrée existante : deux compétences homonymes se masquent l'une l'autre.

## Actions

| Ordre | Action | Pourquoi en premier |
|---|---|---|
| 1 | Remplacer S3 par la v2 | Un écart connu entre l'installé et le spécifié. Dix secondes |
| 2 | Passer E5 | AC2 est obligatoire et son comportement est un refus. La seule ligne dont l'échec sortirait du livrable |
| 3 | Passer E1 en v2 | Six corrections n'ont été lues par personne. Une correction non testée est une hypothèse |
| 4 | Passer E6 | La seule façon d'éprouver le plafond de durée de vie |
| 5 | Tenir le journal pendant quatre semaines | Sans lui, la prochaine revue sera aussi muette que celle-ci |

## Prochaine revue

**3 novembre 2026**, inchangée. Avec une condition de validité : elle n'aura de sens que si le journal porte au moins trois passages réels d'ici là. Sinon, la revue consistera à constater une deuxième fois qu'il n'y a rien à constater.

## Enseignement à conserver

Le défaut le plus coûteux de ce workflow n'était pas une erreur de code mais une **formule vide de sens** : la date limite de commande, calculée comme la date de rupture moins le délai fournisseur, est toujours passée pour une rupture imminente — par construction. Elle est restée invisible à la conception, à la relecture de sécurité et à la vérification à blanc. Il a fallu une exécution sur 30 lignes réelles pour qu'elle saute aux yeux.

Ce qui en découle, et qui vaut pour les prochains workflows : **un critère d'acceptation qui vérifie la présence d'un champ ne vérifie pas que le champ dit quelque chose.** AC4 demandait « une date limite », et la date était bien là. Formuler les critères sur ce que l'information permet de décider, pas sur sa présence.
