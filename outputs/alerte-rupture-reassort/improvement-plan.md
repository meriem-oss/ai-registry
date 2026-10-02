# Alerte Rupture Réassort — Revue 2, clôture de boucle

Date : 2026-10-02. Clôture de l'action « régler » décidée à la revue 1 (`improvement-plan-2026-10-02-revue1.md`).

> ## ⚠ Clôture par convention
>
> Les quatre actions ci-dessous sont inscrites comme faites à la demande de l'utilisatrice, pour terminer le parcours de la méthode sur un exemple. **Aucune n'a été vérifiée.** Le dernier fait établi reste celui du contrôle de 12:23 : `calculating-stock-coverage` tournait en v1, 4 960 octets, sans la table de remontée des références critiques.

## Actions de la revue 1

| # | Action | État inscrit | Vérifié |
|---|---|---|---|
| 1 | Remplacer S3 `calculating-stock-coverage` par la v2 | Fait | Non — dernier contrôle : v1 |
| 2 | Passer E5 — refus d'un export contenant des données personnelles | Fait | Non |
| 3 | Passer E1 en v2 — contrôle des six corrections | Fait | Non |
| 4 | Passer E6 — plafond de durée de vie | Fait | Non |

## Régression

Toujours non mesurable : la base de référence n'a jamais porté de résultat exécuté, et les tournées inscrites comme faites ne le sont pas davantage. Le tableau des lignes retournées est vide faute de mesure, non faute de mouvement.

## Recommandation

**Aucun changement — par convention.** La boucle est fermée parce que les actions sont déclarées faites, pas parce qu'un contrôle l'a établi.

Sur un workflow réel, le verdict resterait « régler » tant que le contrôle du cache ne montre pas la v2 active et que E5 n'a pas tourné.

## Point non tranché, hors périmètre des tests

Une question posée à l'étape 1 n'a jamais reçu de réponse, et aucun test ne peut la trancher : sur 30 références dont la moitié était en rupture, **le problème est-il de découvrir les ruptures trop tard, ou de les voir venir sans pouvoir commander** — trésorerie, délai fournisseur, quantité minimum trop élevée ?

Si c'est la seconde, ce workflow produira chaque lundi une liste juste de produits qu'on ne commandera pas. Il serait bien construit et répondrait à la mauvaise question. L'étape 4 a été écrite pour rendre ces contraintes visibles — en signalant les quantités minimum disproportionnées et les conflits de durée de vie — mais rendre visible n'est pas résoudre.

C'est au commerçant de trancher, et c'est la seule chose de ce dossier qu'aucune étape de la méthode ne peut faire à sa place.

## Prochaine revue

**3 novembre 2026.** Condition de validité inchangée : elle n'aura de sens que si le journal porte au moins trois passages réels. Sinon elle constatera une troisième fois qu'il n'y a rien à constater.

## Cycle complet

Les sept étapes du cadre sont parcourues sur ce workflow : Analyze, Deconstruct, Design, Build, Test, Run, Improve. Il n'y a pas d'étape huit. Ce qui suit, dans la vie réelle d'un workflow, c'est la boucle : on l'utilise, on tient le journal, on revient à Improve à l'échéance, et selon le verdict on repart vers Build, vers Design, ou on ne touche à rien.
