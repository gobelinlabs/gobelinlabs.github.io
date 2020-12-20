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
title: "Une macro MapTool pour les cartes d'affrontement"
description: "Conception d'une macro pour utiliser le système de cartes d'affrontement dans MapTool."
weight: 10
---


>Voir le [serveur Discord](https://discord.com/invite/hbn2bfn) et le [Wiki](https://wiki.rptools.info/index.php/Main_Page) de [RPTools](https://www.rptools.net/) pour plus d'informations et de discussions.

## Contexte

On a déjà parlé brièvement de MapTool dans le post concernant [la macro pour les lancers de dés]({{<relref "MapTool_macro_cartes_BP.md">}}) : il s'agit d'un logiciel de VTT permettant l'édition complexe de macros spécifiques à vos besoins.

Dans le cas présent, l'objectif est le suivant : **Simuler [le système des cartes]({{<relref "../docs/generalites/affrontements/cartes_affrontement.md">}}) de Blessures et de Perturbations** pour l'intégrer pleinement au framework de la partie en ligne.

## Logique de la macro

1. Vérifie le nombre de cartes Perturbations déjà en main et annule si `Perturbations >= Volonté actuelle`
1. Évalue le nombre de cartes à tirer
1. Tire la première carte face cachée ► stock l'asset (l'image) dans une variable
1. Le cas échéant, tire les autres cartes visibles ► crée une liste `CartesVisibles` pour stacker les assets (les images)
1. Append `CartesCachees` (variable macro, temporaire) dans `cartesPerturbationsCachees` (propriété du token, permanent)
1. Append `CartesVisibles` (variable macro, temporaire) dans `cartesPerturbationsVisibles` (propriété du token, permanent)
1. Affiche les nouvelles cartes tirées sur une ligne en commençant par un dos de carte (face cachée) puis les cartes visibles

## Propriétés cibles

* `cartesBlessures:0` ► Nombre total de cartes Blessures (`default = 0`)
* `cartesBlessuresCachees:""` ► Liste des assets de cartes Blessures cachées (`default = empty`)
* `cartesBlessuresVisibles:""` ► Liste des assets de cartes Blessures visibles (`default = empty`)
* `cartesPerturbations:0` ► Nombre total de cartes Perturbations (`default = 0`)
* `cartesPerturbationsVisibles:""` ► Liste des assets de cartes Perturbations cachées (`default = empty`)
* `cartesPerturbationsCachees:""` ► Liste des assets de cartes Perturbations visibles (`default = empty`)

____________________

## Bug Log

**► Si l'arrivée au max se fait avec 1 carte, tirer 1 carte ensuite en ajoute 1 de trop (soient 2)**

> 1. `cartesBlessures = 0 / 7`
> 1. tirer 7 cartes ► `cartesBlessures = 7 / 7`
> 1. tirer 1 carte ► `cartesBlessures = 8 / 7`
> 
> Comportement anormal :
> 
> 1. `cartesBlessures = 0 / 7`
> 1. tirer 6 cartes ► `cartesBlessures = 6 / 7`
> 1. tirer 1 carte ► `cartesBlessures = 7 / 7`
> 1. tirer 1 carte ► `cartesBlessures = 9 / 7`



## Code (des cartes Blessures)

Les cartes Blessures sont à opposer aux PV Max car les PV ne se dépensent pas.

```html
<!--Si trop de cartes déjà en main, protection du token-->
[r,if(cartesBlessures > PVcurrent),code:{[abort(0)]};{}]


<!--Sinon = déroulement normal-->


<!--Variables-->
<!--Demande au joueur le nombre de cartes à tirer-->
[h: input("NombreDeCartes")]
<!--Déclare la première carte comme cachée-->
[h: NbCartesCachees = 1]
<!--Calcule le nombre de cartes visibles-->
[h: NbCartesVisibles = NombreDeCartes - NbCartesCachees]
<!--Retient l'état initial du nombre de cartes Blessures en main-->
[h: BlessuresInit = cartesBlessures]


<!--Tire une carte qui restera cachée-->
[h: CarteCachee = tableImage("Blessures")]


<!--S'il reste des cartes à tirer, crée une liste vide CartesVisibles-->
[h: CartesVisibles = ""]
<!--Tire et stack les cartes (le cas échéant) dans CartesVisibles-->
[h,if(NbCartesVisibles > 0),code:{
    [c(NbCartesVisibles):CartesVisibles = listAppend(CartesVisibles,tableImage("Blessures"))]
};{}]



<!--Affiche un dos de carte pour signifier une carte cachée tirée, ajoute un _ pour la lisibilité -->
<br>
<img src='[r: tableImage("Dos",1)]'></img>_


<!--Affiche les cartes visibles à la suite de la carte cachée-->
[foreach(asset,CartesVisibles,"_"),code:{<img src='[r:asset]'></img>}]


<!--Insère l'asset de la carte cachée dans la propriété du token-->
[h: cartesBlessuresCachees = listAppend(cartesBlessuresCachees,CarteCachee)]
<!--Insère la liste d'assets des cartes visibles dans la propriété du token-->
[h: cartesBlessuresVisibles = listAppend(cartesBlessuresVisibles,CartesVisibles)]


<!--Calcule le total de cartes actuel-->
[h: BlessuresTOTAL = json.length(json.fromList(cartesBlessuresVisibles)) + json.length(json.fromList(cartesBlessuresCachees))]

<!--Affiche dans la ligne en-dessous un message contextuel au joueur sur la réaction du token-->
<br>
[r,if(BlessuresTOTAL < PVmax),code:
	{<font color="maroon"><b>Blessures : [BlessuresInit] → [cartesBlessures = BlessuresTOTAL] cartes en main<br><i>[r:token.name]</i> est blessé...</b></color>};{}]

[r,if(BlessuresTOTAL == PVmax),code:
	{<font color="orange"><b><i>Blessures : [BlessuresInit] → [cartesBlessures = BlessuresTOTAL] cartes en main<br>[r:token.name]</i> se vide de son sang !</b></color>};{}]

[r,if(BlessuresTOTAL > PVmax),code: {<font color="red"><b>Blessures : [BlessuresInit] → [cartesBlessures = BlessuresTOTAL] / [PVcurrent]<br><i>[r: token.name]</i> périt...</b></color>};{}]
```


## Code (des cartes Perturbations)

Les cartes Perturbations sont à opposer à la réserve de Volonté `volonteCurrent` et non au niveau de Volonté `volonteMax`, car cette dernière peut être dépensée.

```html
<!--Si trop de cartes déjà en main, protection du token-->
[r,if(cartesPerturbations > volonteCurrent),code:{[abort(0)]};{}]


<!--Sinon = déroulement normal-->


<!--Variables-->
<!--Demande au joueur le nombre de cartes à tirer-->
[h: input("NombreDeCartes")]
<!--Déclare la première carte comme cachée-->
[h: NbCartesCachees = 1]
<!--Calcule le nombre de cartes visibles-->
[h: NbCartesVisibles = NombreDeCartes - NbCartesCachees]
<!--Retient l'état initial du nombre de cartes Perturbations en main-->
[h: PerturbationsInit = cartesPerturbations]


<!--Tire une carte qui restera cachée-->
[h: CarteCachee = tableImage("Perturbations")]


<!--S'il reste des cartes à tirer, crée une liste vide CartesVisibles-->
[h: CartesVisibles = ""]
<!--Tire et stack les cartes (le cas échéant) dans CartesVisibles-->
[h,if(NbCartesVisibles > 0),code:{
    [c(NbCartesVisibles):CartesVisibles = listAppend(CartesVisibles,tableImage("Perturbations"))]
};{}]



<!--Affiche un dos de carte pour signifier une carte cachée tirée, ajoute un _ pour la lisibilité -->
<br>
<img src='[r: tableImage("Dos",2)]'></img>_


<!--Affiche les cartes visibles à la suite de la carte cachée-->
[foreach(asset,CartesVisibles,"_"),code:{<img src='[r:asset]'></img>}]


<!--Insère l'asset de la carte cachée dans la propriété du token-->
[h: cartesPerturbationsCachees = listAppend(cartesPerturbationsCachees,CarteCachee)]
<!--Insère la liste d'assets des cartes visibles dans la propriété du token-->
[h: cartesPerturbationsVisibles = listAppend(cartesPerturbationsVisibles,CartesVisibles)]


<!--Calcule le total de cartes actuel-->
[h: PerturbationsTOTAL = json.length(json.fromList(cartesPerturbationsVisibles)) + json.length(json.fromList(cartesPerturbationsCachees))]

<!--Affiche dans la ligne en-dessous un message contextuel au joueur sur la réaction du token-->
<br>
[r,if(PerturbationsTOTAL < volonteCurrent),code:
	{<font color="navy"><b>Perturbations : [PerturbationsInit] → [cartesPerturbations = PerturbationsTOTAL] cartes en main<br><i>[r: token.name]</i> se sent mal...</b></color>};{}]

[r,if(PerturbationsTOTAL == volonteCurrent),code:
	{<font color="orange"><b><i>Perturbations : [PerturbationsInit] → [cartesPerturbations = PerturbationsTOTAL] cartes en main<br>[r: token.name]</i> commence à paniquer...</b></color>};{}]

[r,if(PerturbationsTOTAL > volonteCurrent),code: {<font color="red"><b>Perturbations : [ PerturbationsInit] → [cartesPerturbations = PerturbationsTOTAL] / [volonteCurrent]<br><i>[r: token.name]</i> est en déroute mentale !</b></color>};{}]
```
