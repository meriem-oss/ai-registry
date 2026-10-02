# E6 — Plafond de durée de vie et conflit avec le minimum fournisseur

Entrée à coller telle quelle. Entrée nouvelle, créée après la tournée 1 : AC5 n'avait pu être éprouvée, faute de données de durabilité dans E1.

## Ventes et stock (extrait de 6 références)

| Référence | Libellé | Ventes magasin (4 sem.) | Ventes web (4 sem.) | Stock actuel |
|---|---|---|---|---|
| PR-060 | Protection solaire visage SPF50 | 31 | 24 | 6 |
| SE-010 | Sérum vitamine C 30ml | 62 | 51 | 8 |
| MA-030 | Masque argile purifiant 75ml | 18 | 7 | 4 |
| GO-080 | Gommage visage grain fin 100ml | 12 | 6 | 3 |
| CR-003 | Crème mains réparatrice 75ml | 33 | 11 | 9 |
| NE-022 | Huile nettoyante 150ml | 27 | 20 | 14 |

Période de ventes : 28 jours. Date du stock : lundi 05/10/2026.

## Référentiel fournisseurs, avec durée de vie

| Référence | Fournisseur | Délai (jours) | Quantité minimum | Conditionnement | Durée de vie (jours) |
|---|---|---|---|---|---|
| PR-060 | Atelier Bellerive | 30 | 48 | 12 | 540 |
| SE-010 | Laboratoire Vallon | 21 | 12 | 6 | 365 |
| MA-030 | Cosmétique du Sud | 14 | 24 | 12 | 730 |
| GO-080 | Cosmétique du Sud | 14 | 24 | 12 | 730 |
| CR-003 | Laboratoire Vallon | 21 | 24 | 12 | 540 |
| NE-022 | Cosmétique du Sud | 14 | 36 | 12 | (vide) |

## Dates de durabilité du stock présent

| Référence | Lot | Date de durabilité minimale | Quantité du lot |
|---|---|---|---|
| PR-060 | B-2411 | 15/02/2027 | 6 |
| SE-010 | V-2502 | 30/11/2027 | 8 |
| MA-030 | S-2403 | 10/08/2028 | 4 |
| GO-080 | S-2401 | 20/12/2026 | 3 |
| CR-003 | V-2409 | 01/06/2028 | 9 |
| NE-022 | S-2412 | 05/04/2028 | 14 |

## Seuils

- Couverture cible : 30 jours au-delà du délai
- Marge d'écoulement avant péremption : 90 jours
- Surstock : plus de 90 jours de couverture
- Signalement quantité minimum : 50 % au-dessus du besoin
- Références critiques : SE-010, PR-060

## Ce qu'on attend

**PR-060 — conflit à poser, pas à trancher.** Besoin calculé modeste, mais le minimum fournisseur est de 48 et la durabilité du lot en stock court jusqu'au 15/02/2027. Les deux chiffres doivent apparaître côte à côte, avec le nombre d'unités qui risquent de périmer, et aucune quantité tranchée d'office.

**GO-080 — durabilité très courte.** Lot périmant le 20/12/2026, soit moins que la marge d'écoulement de 90 jours à compter de la date du stock. Le plafond doit mordre : la quantité proposée doit être réduite, ou le cas signalé comme ne permettant aucun réassort sain.

**NE-022 — durée de vie absente.** Le workflow doit calculer la quantité quand même, et écrire « plafond de durée de vie non appliqué » dans le contrôle et dans les points d'attention de la pause. Il ne doit pas inventer une durée de vie, même en s'appuyant sur les autres références du même fournisseur.

**SE-010 et PR-060 — critiques.** Elles doivent remonter d'un niveau selon la table, rester dans leur catégorie si elles sont déjà en rupture imminente, et apparaître en tête de leur groupe.
