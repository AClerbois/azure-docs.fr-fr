---
title: Ajouter une barre d’outils de dessin à Azure Maps | Microsoft Docs
description: Comment ajouter une barre d’outils de dessin à une carte à l’aide du kit de développement logiciel (SDK) web Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6bc754c9a4f333da85e57c5ad9780da8df93e895
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309718"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Ajouter une barre d’outils de dessin à une carte

Cet article explique comment utiliser le module Outils de dessin et afficher la barre d’outils de dessin sur la carte. Le contrôle [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) ajoute la barre d’outils de dessin sur la carte. Vous allez apprendre à créer des cartes avec un seul et tous les outils de dessin et à personnaliser le rendu des formes de dessin dans le gestionnaire de dessins.

## <a name="add-drawing-toolbar"></a>Ajouter une barre d’outils de dessin

Le code suivant crée une instance du gestionnaire de dessins et affiche la barre d’outils sur la carte.

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus :

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ajouter une barre d’outils de dessin" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>Add drawing toolbar</a> (Ajouter une barre d’outils de dessin) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Limiter les options de barre d’outils affichées

Le code suivant crée une instance du gestionnaire de dessins avec seulement un outil de dessin de polygones et affiche la barre d’outils sur la carte. 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus :

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ajouter un outil de dessin de polygone" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Add a polygon drawing tool</a> (Ajouter un outil de dessin de polygone) d’Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Modifier le style de rendu de dessin

Le code suivant obtient les couches de rendu à partir du gestionnaire de dessins et modifie leurs options pour modifier le style de rendu du dessin. Dans ce cas, les points sont affichés avec une icône de marque bleue, les lignes sont en rouge et de quatre pixels de largeur, les polygones ont une couleur de remplissage verte et un contour orange.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

Vous trouverez ci-dessous l’exemple de code d’exécution complet de la fonctionnalité ci-dessus :

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Modifier le style de rendu de dessin" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez la page <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>Modifier le style de rendu de dessin</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Barre d’outils de dessin](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Gestionnaire de dessins](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)