# E1 — Semaine typique

Entrée à coller telle quelle. Données fictives, générées pour l'exemple : 30 références, ventes des 4 dernières semaines, stock au lundi matin.

## Ventes et stock

| Référence | Libellé | Ventes magasin (4 sem.) | Ventes web (4 sem.) | Stock actuel |
|---|---|---|---|---|
| CR-001 | Crème hydratante jour 50ml | 48 | 22 | 12 |
| CR-002 | Crème riche nuit 50ml | 21 | 9 | 40 |
| CR-003 | Crème mains réparatrice 75ml | 33 | 11 | 9 |
| SE-010 | Sérum vitamine C 30ml | 62 | 51 | 8 |
| SE-011 | Sérum acide hyaluronique 30ml | 35 | 28 | 25 |
| SE-012 | Sérum rétinol 30ml | 0 | 0 | 36 |
| SE-013 | Sérum apaisant niacinamide 30ml | 44 | 39 | 0 |
| NE-020 | Nettoyant moussant 150ml | 55 | 18 | 60 |
| NE-021 | Eau micellaire 400ml | 40 | 15 | 95 |
| NE-022 | Huile nettoyante 150ml | 27 | 20 | 14 |
| NE-023 | Gel nettoyant peaux mixtes 200ml | 61 | 24 | 18 |
| MA-030 | Masque argile purifiant 75ml | 18 | 7 | 4 |
| MA-031 | Masque tissu hydratant | 72 | 44 | 30 |
| MA-032 | Masque nuit repulpant 50ml | 16 | 13 | 70 |
| GO-080 | Gommage visage grain fin 100ml | 12 | 6 | 3 |
| GO-081 | Gommage corps sucre 200ml | 9 | 5 | 88 |
| TO-090 | Tonique apaisant 200ml | 29 | 13 | 47 |
| TO-091 | Brume hydratante 100ml | 22 | 17 | 11 |
| CO-040 | Contour des yeux liftant 15ml | 26 | 19 | 18 |
| CO-041 | Patchs contour des yeux | 38 | 31 | 22 |
| HU-050 | Huile démaquillante 200ml | 14 | 11 | 52 |
| HU-051 | Huile sèche corps 100ml | 10 | 8 | 64 |
| PR-060 | Protection solaire visage SPF50 | 31 | 24 | 6 |
| PR-061 | Protection solaire corps SPF30 | 19 | 12 | 58 |
| BA-070 | Baume à lèvres nourrissant | 88 | 12 | 110 |
| BA-071 | Baume à lèvres teinté | 64 | 29 | 31 |
| FO-100 | Fond de teint fluide | 44 | 33 | 15 |
| FO-101 | Poudre libre matifiante | 23 | 14 | 47 |
| MA-110 | Mascara volume | 57 | 41 | 0 |
| RO-120 | Rouge à lèvres satiné | 36 | 28 | 19 |

## Référentiel fournisseurs (C4)

| Référence | Fournisseur | Délai (jours) | Quantité minimum | Conditionnement |
|---|---|---|---|---|
| CR-001 | Laboratoire Vallon | 21 | 24 | 6 |
| CR-002 | Laboratoire Vallon | 21 | 24 | 6 |
| CR-003 | Laboratoire Vallon | 21 | 24 | 12 |
| SE-010 | Laboratoire Vallon | 21 | 12 | 6 |
| SE-011 | Laboratoire Vallon | 21 | 12 | 6 |
| SE-012 | Laboratoire Vallon | 21 | 12 | 6 |
| SE-013 | Laboratoire Vallon | 21 | 12 | 6 |
| NE-020 | Cosmétique du Sud | 14 | 36 | 12 |
| NE-021 | Cosmétique du Sud | 14 | 36 | 12 |
| NE-022 | Cosmétique du Sud | 14 | 36 | 12 |
| NE-023 | Cosmétique du Sud | 14 | 36 | 12 |
| MA-030 | Cosmétique du Sud | 14 | 24 | 12 |
| MA-031 | Cosmétique du Sud | 14 | 24 | 24 |
| MA-032 | Cosmétique du Sud | 14 | 24 | 12 |
| GO-080 | Cosmétique du Sud | 14 | 24 | 12 |
| GO-081 | Cosmétique du Sud | 14 | 24 | 12 |
| TO-090 | Cosmétique du Sud | 14 | 36 | 12 |
| TO-091 | Cosmétique du Sud | 14 | 36 | 12 |
| CO-040 | Atelier Bellerive | 30 | 12 | 6 |
| CO-041 | Atelier Bellerive | 30 | 12 | 12 |
| HU-050 | Atelier Bellerive | 30 | 12 | 6 |
| HU-051 | Atelier Bellerive | 30 | 12 | 6 |
| PR-060 | Atelier Bellerive | 30 | 48 | 12 |
| PR-061 | Atelier Bellerive | 30 | 48 | 12 |
| BA-070 | Maison Arpège | 10 | 50 | 25 |
| BA-071 | Maison Arpège | 10 | 50 | 25 |
| FO-100 | Maison Arpège | 10 | 50 | 25 |
| FO-101 | Maison Arpège | 10 | 50 | 25 |
| MA-110 | Maison Arpège | 10 | 50 | 25 |
| RO-120 | Maison Arpège | 10 | 50 | 25 |

## Seuils (C5)

- Couverture cible : 30 jours au-delà du délai de livraison
- Seuil de surstock : plus de 90 jours de couverture
- Références à ne jamais laisser en rupture : SE-010, PR-060
