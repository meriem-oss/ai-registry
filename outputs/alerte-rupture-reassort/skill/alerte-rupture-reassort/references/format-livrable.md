# Format du livrable

Une page. L'ordre des sections est délibéré : d'abord sur quelles données on décide, puis ce qui est perdu, puis ce qui peut être sauvé, puis ce qui attend un arbitrage, puis ce qu'il faut anticiper.

```markdown
# Réassort — semaine du [date du stock]

## Contrôle
Références traitées : [n] sur [attendu]
Colonnes vérifiées : aucune donnée identifiante
Sources manquantes : [liste, ou aucune — avec ce que cela rend incalculable]
Plafond de durée de vie : [appliqué | non appliqué — durée de vie absente]
Seuils : [fournis par vous | valeurs par défaut de la compétence — préciser lesquels]
Dates calculées à partir du stock du [date]. Passage du [date] : [n] références probablement déjà en rupture.

## Déjà en rupture — [n] références
| Réf | Produit | Ventes/j | Stock | Fournisseur | Quantité | Retard |
|---|---|---|---|---|---|---|

## Rupture imminente — [n] références
Groupées par fournisseur, pour permettre une commande unique. Références critiques en tête.

### [Fournisseur] — délai [n] jours · total [n] unités
| Réf | Produit | Ventes/j | Stock | Jours restants | Quantité | Retard |
|---|---|---|---|---|---|---|

## Arbitrages à rendre — [n]
| Réf | Besoin calculé | Minimum fournisseur | Problème |
|---|---|---|---|
| [réf] | 12 | 48 | 30 unités risquent de périmer avant écoulement |

## À surveiller — [n]
Aucune quantité proposée. La date limite est celle à tenir pour ne pas basculer en rupture imminente.
| Réf | Produit | Ventes/j | Stock | Jours restants | Fournisseur | Commander avant |
|---|---|---|---|---|---|---|

## Sans historique de ventes — [n]
Aucune quantité proposée : pas de vitesse de vente calculable. Décision manuelle.

## Délai fournisseur inconnu — [n]
Aucune quantité proposée. À compléter dans le référentiel fournisseurs.

## Surstock — [n]
Pour information : trésorerie immobilisée, et candidats à une promotion.
```

## Règles de forme

**Les chiffres justifient, ils ne décorent pas.** Chaque ligne porte ventes par jour, stock et jours restants, pour qu'une proposition puisse être contestée sans rouvrir les fichiers.

**Arrondir les jours à l'entier.** « 11 jours » et non « 11,4 » : quatre semaines de ventes ne permettent pas cette précision.

**Une section vide se mentionne en une ligne**, elle ne disparaît pas. « Aucune référence sans historique cette semaine » est une information ; une section absente laisse croire à un oubli.

**Le retard s'écrit en jours, avec la date limite entre parenthèses** : « 20 j (08/09) ». Pour les ruptures imminentes et déjà en rupture, la date limite est toujours passée ; une date seule se lirait comme une coquille.

**Les mentions s'affichent dans la colonne Réf** : « critique », « probablement déjà en rupture ».

**Total par fournisseur dans l'en-tête de chaque groupe**, et total général dans le résumé de fin.
