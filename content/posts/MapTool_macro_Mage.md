---
author: "Etienne Safa"
date: 2020-12-01
menu:
  main:
    parent: tutorials
tags: [
    "RPTools",
    "macro",
    "MapTool",
    "VTT",
]
categories: [
    "Development",
]
title: "Une macro MapTool pour le système de Mage : l'Ascension"
description: "Conception d'une macro pour augmenter les fonctionnalités de base de MapTool dans un jeu de Mage : l'Ascension"
weight: 10
---

>Voir le [serveur Discord](https://discord.com/invite/hbn2bfn) et le [Wiki](https://wiki.rptools.info/index.php/Main_Page) de [RPTools](https://www.rptools.net/) pour plus d'informations et de discussions.

## Contexte

MapTool est un logiciel open-source issu de la suite RPTools et conçu pour masteriser des parties de jeu de rôles en ligne via un système de table de jeu virtuelle, ou VTT (Virtual Table-Top).
Il s'agit d'une alternative très satisfaisante à d'autres logiciels et applications freemium ou payants tels que Roll20, Astral ou FantasyGround.

Comme tous ces logiciels, MapTool propose une liste assez exhaustive de fonctions pour effectuer des lancers de dés via la tchat-box, mais malheureusement aucune n'est exactement adaptée au système de *Mage : l'Ascension* (Le Monde des Ténèbres) et c'est pourquoi nous vous proposons ici les macros conçues pour pouvoir jouer avec ce système sur MapTool en toute simplicité !

## Postulat de départ

### Objectif
Le but est de reproduire le mécanisme de lancers de dés suivant :

1. Les jets prennent la forme d'un groupement de 1 à 10d10,
1. On compte 1 succès pour chaque dé supérieur à la difficulté du jet (de 2 à 10),
1. Les 1 comptent pour 1 échec et retirent 1 succès au résultat du jet,
1. Le cas échéant, les 10 explosent : on compte 1 succès et on relance le jet, renouvelable à l'infini
1. La valeur du résultat nous indique le degré de réussite de l'action :
  * Résultat < 0 : échec critique plus ou moins grave selon la valeur négative
  * Résultat = 0 : échec de l'action
  * Résultat > 1 : succès plus ou moins important selon la valeur positive
  * Résultat >= 5 : succès phénoménal, le personnage regagne 1 point de Volonté

### Incompatibilité
L'incompatibilité réside dans la fonction explosive de MapTool : la commande `3d10es6` additionne les dés explosifs plutôt que de les comptabiliser indépendamment des autres dés, réduisant par là-même le nombre de succès obtenus.

`3d10es6` signifie : "lance 3d10, les dés explosent sur un 10, compte 1 succès pour chaque résultat égal ou supérieur à 6.

**Résultat :** [4,17,6] = 3 succès (10 a explosé et a donné un 7)

**Résultat souhaité :** [4,10,7,6] = 4 succès

## Code

```
[h:input("NombreDeDes", "Difficulte")]
[h:expr = NombreDeDes+"d10e"]
[h: eval(expr)]
[h: bigRoll=getRolled()]
[r: sorted=json.sort(bigRoll, "d")] >>> 

[h,switch(Difficulte),code:
    case 2: {[fail='[1]'] };
    case 3: {[fail='[1,2]'] };
    case 4: {[fail='[1,2,3]'] };
    case 5: {[fail='[1,2,3,4]'] };
    case 6: {[fail='[1,2,3,4,5]'] };
    case 7: {[fail='[1,2,3,4,5,6]'] };
    case 8: {[fail='[1,2,3,4,5,6,7]'] };
    case 9: {[fail='[1,2,3,4,5,6,7,8]'] };
    case 10: {[fail='[1,2,3,4,5,6,7,8,9]'] };
    default: {[fail='[1,2,3,4,5]'] };
]

[h: successRolls = json.removeAll(bigRoll,fail)]
[h: successes = json.length(successRolls)]
[h: critFail= json.intersection(bigRoll,'[1]')]
[r: results = successes -json.length(critFail) ] succès
```

## Développement

Nouvelles pistes à explorer :

**Remplacer switch par :**

```
[H: fail = '[1]']
[H, c(Difficulte-2): fail = json.append(fail,roll.count+2)]
```

**Error checking :**

```
[H, if(isNumber(Difficulte)): slice = max(0,min(10,Difficulte)-2); slice = 4]
[H: fail = json.get('[1,2,3,4,5,6,7,8,9]',0,slice)]
```