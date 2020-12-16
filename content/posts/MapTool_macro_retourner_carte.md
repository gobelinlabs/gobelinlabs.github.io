---
author: "Etienne Safa"
date: 2020-12-16
menu:
  main:
    parent: tutorials
tags: [
    "RPTools",
    "macro",
    "MapTool",
    "VTT",
    "cards",
]
categories: [
    "Development",
]
title: "Une macro MapTool pour les cartes d'affrontement (P. 2)"
description: "Conception d'une macro pour utiliser le système de cartes d'affrontement dans MapTool (partie 2)."
weight: 10
---


>Voir le [serveur Discord](https://discord.com/invite/hbn2bfn) et le [Wiki](https://wiki.rptools.info/index.php/Main_Page) de [RPTools](https://www.rptools.net/) pour plus d'informations et de discussions.

## Contexte

Le système de tirage de cartes inclue trois actions :

1. Tirer une carte face cachée,
1. Tirer une carte face visible,
1. Retourner une carte de sa face cachée vers sa face visible.

Le tirage des cartes a été couvert [dans le post correspondant]({{<relref "MapTool_macro_cartes_BP.md">}}).

Ici, l'objectif est le suivant : **Retirer une carte de la pile des faces cachées, la retourner et la placer dans la pile des faces visibles**.


## Dev Log

```
TODO

[ ] Installer un bouton radio pour supprimer l'une des deux macros (donner un choix à faire au départ pour cibler la pile Blessures ou la pile Perturbations)
```

___________


## Code (pour retourner les cartes Blessures)

<!--Empêche la macro s'il n'y a pas de cartes à retourner-->
[if(cartesBlessuresCachees == '""'):abort(0)]

<!-- Crée un array json et le mélange à partir de la liste contenue dans la propriété du token -->
[h: CartesCachees = json.shuffle(json.fromList(cartesBlessuresCachees))]

<!-- Prélève la première carte et l'enlève de la liste -->
[h: CarteRetournee = json.get(CartesCachees,0)]
[h: CartesCachees = json.remove(CartesCachees,0)]

<!-- Affiche la carte ainsi retournée -->
<br>
<img src='[r:CarteRetournee]'></img>

<!--Redéfinit cartesBlessuresCachees avec la nouvelle liste-->
[h: CartesCachees = json.toList(CartesCachees)]
[h,if(CartesCachees == ""): cartesBlessuresCachees = '""']
[h,if(CartesCachees != ""): cartesBlessuresCachees = CartesCachees]

<!--Insère l'asset de la carte retournée dans la propriété du token-->
[h: cartesBlessuresVisibles = listAppend(cartesBlessuresVisibles,CarteRetournee)]


## Code (pour retourner les cartes Perturbations)

<!--Empêche la macro s'il n'y a pas de cartes à retourner-->
[if(cartesPerturbationsCachees == '""'):abort(0)]

<!-- Crée un array json et le mélange à partir de la liste contenue dans la propriété du token -->
[h: CartesCachees = json.shuffle(json.fromList(cartesPerturbationsCachees))]

<!-- Prélève la première carte et l'enlève de la liste -->
[h: CarteRetournee = json.get(CartesCachees,0)]
[h: CartesCachees = json.remove(CartesCachees,0)]

<!-- Affiche la carte ainsi retournée -->
<br>
<img src='[r:CarteRetournee]'></img>

<!--Redéfinit cartesPerturbationsCachees avec la nouvelle liste-->
[h: CartesCachees = json.toList(CartesCachees)]
[h,if(CartesCachees == ""): cartesPerturbationsCachees = '""']
[h,if(CartesCachees != ""): cartesPerturbationsCachees = CartesCachees]

<!--Insère l'asset de la carte retournée dans la propriété du token-->
[h: cartesPerturbationsVisibles = listAppend(cartesPerturbationsVisibles,CarteRetournee)]