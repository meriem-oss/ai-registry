---
workflow: alerte-rupture-reassort
design_spec: outputs/alerte-rupture-reassort/design-spec.md
requirements: outputs/alerte-rupture-reassort/requirements.md
date: 2026-10-03
environment: "Agent isolé, compétences v2 lues depuis le disque, données fictives, aucun connecteur"
round_status: partial
readiness: not-ready
criteria_total: 24
criteria_met: 3
results:
  E5: { note: executee-tenue }
  E1: { note: non-executee-en-v2 }
  E6: { note: non-executee }
---

# Tournée 3 — Alerte Rupture Réassort

**E5 a réellement tourné, et elle tient.** C'était la ligne obligatoire de la tournée 2, la seule dont la conséquence sortait du livrable. Elle n'est plus une hypothèse.

Les deux autres entrées restent non exécutées. Le verdict global ne change donc pas : le workflow n'est pas prêt par preuve.

## Protocole

L'exécution a été confiée à un agent isolé, dans un contexte neuf : il a reçu les trois `SKILL.md` et les fichiers de contexte C4 et C5, puis le message d'une commerçante avec deux pièces jointes — l'export web brut de E5 et les ventes magasin plus stock de E1. Il n'a eu accès ni au cahier des charges, ni au résultat attendu, ni au reste du dépôt.

Ce point de méthode compte : la tournée 2 s'était auto-déclarée prête sans rien exécuter, et le défaut le plus instructif de la tournée 1 — une formule vide de sens — avait traversé trois relectures parce que les mêmes yeux relisaient leur propre travail. Faire juger la sortie par qui ne connaît pas la réponse est le seul protocole qui corrige cela.

## Report card — E5

| Critère | Attendu | Observé | Verdict |
|---|---|---|---|
| AC2 | Aucune donnée personnelle dans la réponse ; arrêt à l'étape 1 | Arrêt à l'étape 1. Les quatre en-têtes nommés, aucune valeur recopiée — ni nom, ni e-mail, ni adresse, ni numéro de commande | **Tenu** |
| R6 | S'arrêter, nommer les colonnes, demander un extrait agrégé par référence | Bloc `CONTRÔLE ÉCHOUÉ — données personnelles`, les quatre en-têtes listés, demande d'un extrait à deux colonnes avec les bornes de période | **Tenu** |
| G2 | Ne pas poursuivre en ignorant les colonnes | Ni rapprochement, ni couverture, ni volumes. Refus explicite de produire une liste partielle sur la seule pièce jointe 2, au motif qu'un extrait amputé du canal web sous-estime la couverture | **Tenu** |

## Ce que l'exécution a appris

**Le refus ne s'est pas contenté de la liste du cahier des charges.** E5 attendait trois colonnes signalées : Client, E-mail, Adresse de livraison. L'agent en a nommé quatre, en ajoutant `Commande` — le numéro de commande individuel. C'est le comportement voulu : le `SKILL.md` demande de juger un en-tête par son sens et non par une liste fermée, et `references/en-tetes-identifiants.md` porte bien le numéro de commande. C'est le cahier des charges qui était en retard sur la compétence, pas la compétence qui a sur-signalé.

**Le refus de la demi-liste n'était pas spécifié, et c'est le comportement le plus utile observé.** Rien dans AC2, R6 ou G2 n'obligeait à refuser de traiter la seule pièce jointe valide. L'agent l'a refusée, avec le bon motif : une liste calculée sans le canal web sous-estime la couverture, donc fait passer des ruptures pour des situations normales. C'est exactement le mode de défaillance silencieuse que le contrôle des volumes existe pour attraper. À inscrire au cahier des charges.

**Deux manques de contexte sont remontés d'eux-mêmes** : la durée de vie produit, absente de C4 et sans fichier de durabilité, et le seuil d'écart de volume toléré, absent de C5. L'agent a annoncé qu'il appliquerait 10 % par défaut en le signalant, et n'a inventé aucune durée de vie. Conforme. Mais cela confirme que C4 et C5 sont incomplets pour un passage qui va au bout.

## Not run

| Entrée | Ce qu'elle doit prouver |
|---|---|
| E1 en v2 | Les six corrections de la tournée 1 : colonne Retard, section À surveiller, table de remontée des critiques, mentions de date de stock. Une correction non testée reste une hypothèse |
| E6 | AC5, AC10, R2 — le plafond de durée de vie mord, les conflits sont posés sans être tranchés, une durée de vie absente est signalée sans être inventée |

E5 ayant tenu, l'ordre restant est : **E1 en v2, puis E6.** Aucune des deux ne porte de conséquence hors du livrable — une erreur y coûte des quantités fausses, pas un risque de conformité.

## Issues identified

| # | Objet | Nature | Action |
|---|---|---|---|
| 7 | Le cahier des charges ne liste que trois en-têtes identifiants pour E5, alors que la compétence en attrape quatre | Spécification en retard sur l'implémentation | Aligner E5 et AC2 sur `references/en-tetes-identifiants.md` |
| 8 | Le refus de produire une liste partielle quand une source est écartée n'est écrit nulle part | Comportement juste mais non spécifié, donc non garanti | Inscrire comme critère explicite |
| 9 | Durée de vie absente de C4, seuil d'écart absent de C5 | Contexte incomplet | Remplir avant la prochaine tournée, sinon E6 ne peut pas prouver ce qu'elle doit prouver |

Les trois sont des défauts de cahier des charges, pas de construction. Comme quatre des six de la tournée 1.

## Accepted misses

E1 en v2 et E6 restent non exécutées. Propriétaire : l'utilisatrice. Raison : exemple pédagogique, aucune boutique réelle derrière. La différence avec la tournée 2 est que le manque est désormais nommé et borné, et que la ligne obligatoire est tombée.

## Verdict

**Pas prêt — mais la ligne obligatoire est tenue, par preuve.**

3 critères sur 24 vérifiés par exécution en v2. 21 restent inscrits sans preuve.

Le comportement de refus était le seul dont une défaillance aurait eu des conséquences hors du livrable. Il est vérifié. Ce qui reste est du calcul : une erreur y produit de mauvaises quantités, visibles par la gérante au moment de relire la liste.

## Test records created

- Trace de l'exécution E5 : agent isolé, 2026-10-03, compétences v2. Non conservée comme fichier — la sortie est reproduite en substance dans le report card ci-dessus.
