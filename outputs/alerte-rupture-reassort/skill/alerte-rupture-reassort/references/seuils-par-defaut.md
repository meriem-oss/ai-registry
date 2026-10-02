# Seuils par défaut

**Ces valeurs sont des points de départ, pas des recommandations.** Elles ont été posées lors de la conception du workflow sur un exemple, et aucune n'a été arbitrée avec un commerçant.

Dès qu'un fichier de seuils est fourni, il les remplace. À défaut, les appliquer **et le signaler en tête du livrable**, pour que le lecteur sache que les décisions reposent sur des réglages génériques.

| Seuil | Valeur par défaut | Ce qu'il décide |
|---|---|---|
| Couverture cible | 30 jours au-delà du délai de livraison | Combien de stock la commande vise à constituer. Plus haut : moins de ruptures, plus de trésorerie immobilisée |
| Seuil de surstock | plus de 90 jours de couverture | À partir de quand un stock est signalé comme dormant |
| Marge d'écoulement | 90 jours avant la date de durabilité | Le temps qu'on se laisse pour vendre avant péremption. Plafonne les quantités |
| Écart de volume toléré | 10 % | À partir de quel écart entre références reçues et attendues le workflow s'arrête |
| Signalement quantité minimum | 50 % au-dessus du besoin | À partir de quand une quantité minimum fournisseur est signalée comme disproportionnée |
| Références critiques | aucune par défaut | Celles qui ne doivent jamais manquer. Remontent d'un niveau d'urgence |

## Comment les régler

**Couverture cible** : regarder à quelle fréquence on veut commander. Une cible de 30 jours avec un délai de 21 jours signifie une commande par mois et demi environ.

**Marge d'écoulement** : la durée réelle qu'il faut pour écouler un produit en fin de vie, pas une durée théorique. Si les clients regardent les dates, elle doit être plus large.

**Références critiques** : les deux ou trois produits dont la rupture fait partir un client chez un concurrent, et les saisonniers dont une rupture en pleine saison ne se rattrape pas.
