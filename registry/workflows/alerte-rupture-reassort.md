---
type: Workflow
title: "Alerte Rupture Réassort"
description: "Repère chaque semaine les références qui risquent la rupture et prépare les commandes à passer, sans traiter de données client."
generated: { by: process:scaffolding-registry, at: 2026-10-02 }
status: in-production
definition_type: step-driven
execution_mode: augmented
autonomy: deterministic
trigger: "Chaque lundi, quand la gérante fournit l'extrait des ventes agrégé et le fichier de stock"
stale_after: 2026-11-03
---
# Alerte Rupture Réassort

Cas d'usage de démonstration, construit sur un jeu de données fictif : une boutique de cosmétiques de trente références, dont la moitié en rupture. Le workflow lit les ventes agrégées et le stock, calcule les jours de couverture par référence, les compare au délai de chaque fournisseur, et produit une liste de commandes groupée par fournisseur. Il s'arrête deux fois : devant une donnée personnelle ou un écart de volume, et avant toute décision de commande.

Les sept étapes du cadre ont été parcourues. Sa valeur pédagogique tient autant à ses défauts qu'à son résultat : le test a révélé six problèmes, dont quatre venaient du cahier des charges et non de la construction. Le plus instructif était une formule vide de sens — la date limite de commande, toujours passée pour une rupture imminente, par construction — qui a traversé la conception, la relecture de sécurité et la vérification à blanc sans être vue.

Réserve : deux tournées ont réellement tourné. La tournée 1 sur la version 1 des compétences, et la tournée 3 sur la seule entrée E5 — le refus d'une donnée personnelle, vérifié en version 2 par un agent isolé qui ne connaissait pas le résultat attendu. Il tient. Restent non exécutées l'entrée E1 en version 2, qui porte les six corrections de la tournée 1, et l'entrée E6, qui porte le plafond de durée de vie. 3 critères sur 24 sont vérifiés par preuve.

# Artifacts

- [Opportunity report](outputs/ai-opportunity-report.md)
- [Requirements](outputs/alerte-rupture-reassort/requirements.md)
- [Design spec](outputs/alerte-rupture-reassort/design-spec.md)
- [Test results](outputs/alerte-rupture-reassort/test-results.md)
- [Run guide](outputs/alerte-rupture-reassort/run-guide.md)
- [Run log](outputs/alerte-rupture-reassort/runs.md)
- [Improvement plan](outputs/alerte-rupture-reassort/improvement-plan.md)
- [Raw source](outputs/alerte-rupture-reassort/)

# Skills

- alerte-rupture-reassort — l'orchestrateur des cinq étapes
- screening-sales-data — refuse les données personnelles et les extraits tronqués
- calculating-stock-coverage — vitesse de vente, jours de couverture, classement par risque

# Agents

Aucun.

# Insights

<!-- GENERATED:insights -->
<!-- /GENERATED -->
