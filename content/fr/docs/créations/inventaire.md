---
title: "Inventaire et prix"
weight: 1
---

# Inventaire et prix des biens et marchandises

L'inventaire est en cours de traduction et de réglage (notamment au niveau de l'harmonie des prix).

Il est issu d'un travail précédent dont j'ai malheureusement perdu la référence (si jamais vous la connaissez, n'hésitez pas [à mettre un mémo sur le repo](https://github.com/gobelinlabs/gobelinlabs.github.io/issues)).
Entre temps, la fiche a relativement évolué mais utilise toujours les mêmes mécanismes légèrement réadaptés et/ou réarrangés :

1. Utilisez le premier onglet `POI setup` pour définir le lieu
1. Rendez-vous sur le second onglet `Inventaire global` pour trouver la liste complète des items
    - La colonne `Qtt` indique le nombre d'items disponibles (si vide : la boutique ne les possède pas)
    - La colonne `Prix` indique le prix actuel de l'item. Elle est basée sur la valeur de `Base Prix` en appliquant une variation selon le lieu.
    - À vous, Maîtres du Jeu, d'ajuster ensuite selon la rareté (pour l'instant)
1. Pour faire le premier setup d'un personnage, vous pouvez utiliser l'onglet `Inventaire personnage` qui vous indique la richesse du personnage en fonction de ce que vous lui achetez, tout en intégrant les variantes en fonction de [l'historique "Fortune"]({{<ref "../regles/personnage/historiques_personnage/#fortune">}}).

{{<hint info>}}
► Vous pouvez utiliser les filtres pour n'afficher que les valeurs non-nulles dans la colonne `Qtt` !
{{</hint>}}

{{<hint danger>}}
**ATTENTION !**

La fiche fonctionne sur une formule qui réactualise les prix dès qu'un changement est opéré sur une cellule.

► Si vous changez quoi que ce soit dans le fichier, tous les prix seront recalculés !
{{</hint>}}

{{<details "TODO-list" open>}}

- [ ] Traduction FR
- [ ] Estimation coût moyen de la vie
    - [ ] Prix définitif bâtiments et transports en rapport avec le coût moyen de la vie
- [ ] Vérifier que les armures sont bien définitives
    - [ ] Supprimer l'onglet Armures

{{</details>}}

----------

## Utilisation

Sentez-vous libre de copier la fiche pour pouvoir la modifier 😊

{{< button href="https://docs.google.com/spreadsheets/d/1S9k09au1V9tzfg4xTF9HDqkc8MmD3oTnPSTC4VLC1lY/edit?usp=sharing" >}}Voir l'Inventaire en tant que **"commentateur"** ► Google-Sheet{{< /button >}}