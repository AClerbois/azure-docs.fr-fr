---
title: Fonctionnalités avancées d’Azure Metrics Explorer
description: En savoir plus sur les fonctionnalités avancées d’Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 67e4281b24a7489cf202d82bdddbe99992aac095
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271677"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Fonctionnalités avancées d’Azure Metrics Explorer

> [!NOTE]
> Cet article suppose que vous êtes familiarisé avec les fonctionnalités de base de Metrics Explorer. Si vous êtes un nouvel utilisateur et que vous souhaitez apprendre à créer votre premier graphique de métriques, consultez [prise en main Azure Metrics Explorer](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Métriques dans Azure

Les [métriques dans Azure Monitor](data-platform-metrics.md) sont les séries de valeurs et de comptes mesurés qui sont collectées et stockées au fil du temps. Il existe des métriques standard (ou de « plateforme ») et des métriques personnalisées. Les métriques standards vous sont fournies par la plateforme Azure elle-même. Les métriques standards reflètent les statistiques d’intégrité et d’utilisation de vos ressources Azure. Tandis que les mesures personnalisées sont envoyés à Azure par vos applications à l’aide de la [API Application Insights pour les événements et mesures personnalisés](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), [extension Windows Azure Diagnostics (WAD)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview), ou par [Azure Surveiller les API REST](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Créer des vues avec plusieurs mesures et graphiques

Vous pouvez créer des graphiques qui tracer plusieurs lignes de mesures ou d’affichent des graphiques de mesures plusieurs à la fois. Cette fonctionnalité vous permet de :

- mettre en corrélation les mesures associées sur le même graphique pour voir comment une valeur sont liée à un autre
- afficher les métriques avec différentes unités de mesure à proximité
- agréger visuellement et de comparer les mesures de plusieurs ressources

Par exemple, si vous avez 5 comptes de stockage et vous souhaitez connaître la quantité d’espace total est consommée entre eux, vous pouvez créer un graphique en aires (empilées) qui illustre la personne et la somme de toutes les valeurs à un point particulier dans le temps.

### <a name="multiple-metrics-on-the-same-chart"></a>Plusieurs métriques sur le même graphique

Tout d’abord, [créer un nouveau graphique](metrics-getting-started.md#create-your-first-metric-chart). Cliquez sur **ajouter une métrique** et répétez les étapes pour ajouter une autre mesure sur le même graphique.

   > [!NOTE]
   > Vous ne souhaiterez normalement pas avoir des métriques avec des unités de mesure différentes (par exemple, « millisecondes » et « Ko ») ou d’échelles très différentes sur un même graphique. Au lieu de cela, envisagez d’utiliser plusieurs graphiques. Pour créer plusieurs graphiques dans Metrics Explorer, cliquez sur le bouton Ajouter un graphique.

### <a name="multiple-charts"></a>Plusieurs graphiques

Cliquez sur le **ajouter un graphique** et créer un autre graphique avec une autre mesure.

### <a name="order-or-delete-multiple-charts"></a>Définir ou supprimer plusieurs graphiques

Pour définir ou supprimer plusieurs graphiques, cliquez sur le bouton de sélection ( **...**  ) symbole pour ouvrir le menu de graphique et choisissez l’élément de menu approprié de **déplacer vers le haut**, **Descendre**, ou **supprimer**.

## <a name="apply-filters-to-charts"></a>Appliquer des filtres aux graphiques

Vous pouvez appliquer des filtres à des graphiques qui montrent des métriques associées à des dimensions. Par exemple, si la métrique « Nombre de transactions » a une dimension « Type de réponse » indiquant si la réponse provient de transactions qui ont réussi ou échoué, un filtrage sur cette dimension trace une courbe de graphique uniquement pour les transactions qui ont réussi ou échoué. 

### <a name="to-add-a-filter"></a>Pour ajouter un filtre

1. Sélectionnez **Ajouter un filtre** au-dessus du graphique

2. Sélectionnez la dimension (propriété) que vous souhaitez filtrer.

   ![image de métrique](./media/metrics-charts/00006.png)

3. Sélectionnez les valeurs de dimension que vous souhaitez inclure lors du traçage du graphique (cet exemple illustre le filtrage des transactions de stockage qui ont réussi) :

   ![image de métrique](./media/metrics-charts/00007.png)

4. Après avoir sélectionné les valeurs de filtre, cliquez à côté du sélecteur de filtre pour fermer celui-ci. Le graphique montre à présent le nombre de transactions de stockage qui ont échoué :

   ![image de métrique](./media/metrics-charts/00008.png)

5. Vous pouvez répéter les étapes 1 à 4 pour appliquer plusieurs filtres aux mêmes graphiques.



## <a name="apply-splitting-to-a-chart"></a>Appliquer le fractionnement à un graphique

Vous pouvez fractionner une métrique par dimension afin de visualiser la façon dont les différents segments de la métrique se comparent entre eux, et d’identifier des segments éloignés d’une dimension.

### <a name="apply-splitting"></a>Appliquer la division

1. Cliquez sur **Appliquer un fractionnement** au-dessus du graphique.
 
   > [!NOTE]
   > Fractionnement ne peut pas être utilisé avec des graphiques qui ont plusieurs métriques. En outre, vous pouvez avoir plusieurs filtres mais qu’une seule dimension fractionnement appliqué à n’importe quel graphique unique.

2. Choisissez la dimension sur laquelle vous souhaitez segmenter votre graphique :

   ![image de métrique](./media/metrics-charts/00010.png)

   Le graphique montre à présent plusieurs lignes, une par segment de la dimension :

   ![image de métrique](./media/metrics-charts/00012.png)

3. Cliquez à côté du **sélecteur de regroupement** pour fermer celui-ci.

   > [!NOTE]
   > Pour masquer les segments non pertinents pour votre scénario et faciliter la lecture des graphiques, utilisez un Filtrage et un Fractionnement sur la même dimension.

## <a name="lock-boundaries-of-chart-y-axis"></a>Verrouiller les limites de l’axe y du graphique

Lorsque le graphique montre des fluctuations plus faibles sur de plus grandes valeurs, il est important de verrouiller la plage de l’axe y. 

Par exemple, imaginons que le volume de requêtes réussies passe de 99,99 % à 99,95 %. Ces chiffres peuvent correspondre à une baisse significative de la qualité de service. Toutefois, avec les paramètres du graphique par défaut, il est très difficile voire impossible de remarquer une faible fluctuation des valeurs numériques. Dans ce cas, vous pouvez verrouiller la limite inférieure du graphique à 99 %. Cette petite baisse apparaît alors plus évidente. 

Autre exemple avec la fluctuation de la mémoire disponible, pour laquelle la valeur ne peut pas atteindre zéro (techniquement). Si la plage est définie sur une valeur plus élevée, il se peut que les chutes de la mémoire disponible soient plus faciles à repérer. 

Pour contrôler la plage de l’axe des y, utilisez le menu du graphique « ... », puis sélectionnez **Modifier le graphique** pour accéder aux paramètres avancés du graphique. Modifiez les valeurs de la section de la plage de l’axe y, ou utilisez le bouton **Auto** pour rétablir les valeurs par défaut.

![image de métrique](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> En général, pour verrouiller les limites de l’axe y pour les graphiques qui effectuent le suivi de nombres ou de sommes différents sur une période (et par conséquent, utilisent les agrégations de compte, somme, minimum ou maximum), vous devez spécifier une granularité temporelle fixe plutôt que de conserver les valeurs définies automatiquement par défaut. En effet, les valeurs des graphiques changent lorsque la granularité temporelle est modifiée automatiquement par l’utilisateur qui redimensionne sa fenêtre de navigation ou qui change de résolution d’écran. La modification de la granularité temporelle qui en découle affecte l’apparence du graphique et invalide la sélection actuelle de la plage de l’axe y.

## <a name="pin-charts-to-dashboards"></a>Épingler des graphiques à des tableaux de bord

Après avoir configuré les graphiques, vous pouvez les ajouter aux tableaux de bord afin de pouvoir les réafficher, par exemple, dans le contexte d’une autre télémétrie de surveillance ou d’un partage avec votre équipe.

Pour épingler un graphique configuré à un tableau de bord :

Après avoir configuré votre graphique, cliquez sur le menu **Actions** dans l’angle supérieur droit du graphique, puis sur **Épingler au tableau de bord**.

![image de métrique](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Créer des règles d'alerte

Vous pouvez utiliser les critères que vous avez définis pour visualiser vos métriques en tant que base d’une règle d’alerte basée sur une métrique. La nouvelle règle d’alerte inclura les dimensions de la ressource, de la métrique, du fractionnement et du filtre cibles de votre graphique. Vous ne pourrez pas modifier ces paramètres ultérieurement dans le volet de création de règles d’alerte.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Pour créer une nouvelle règle d’alerte, cliquez sur **Nouvelle règle d’alerte**

![Bouton Nouvelle règle d’alerte surligné en rouge](./media/metrics-charts/015.png)

Vous êtes redirigé vers le volet de la création de règle d’alerte avec les dimensions de métriques sous-jacentes de votre graphique préremplies pour faciliter la génération de règles d’alerte personnalisées.

![Créer une règle d’alerte](./media/metrics-charts/016.png)

Lisez cet [article](alerts-metric.md) pour en savoir plus sur la configuration d’alertes de métrique.

## <a name="troubleshooting"></a>Résolution de problèmes

*Mon graphique ne contient aucune donnée.*

* Les filtres s’appliquent à tous les graphiques du volet. Lorsque vous vous concentrez sur un graphique, vérifiez que vous n’avez pas défini un filtre qui exclut toutes les données d’un autre graphique.

* Si vous souhaitez définir des filtres différents sur différents graphiques, créez-les dans des panneaux différents, enregistrez-les sous forme de favoris distincts. Si vous le souhaitez, vous pouvez les épingler au tableau de bord afin de les afficher parallèlement.

* Si vous segmentez un graphique en fonction d’une propriété qui n’est pas définie dans la mesure, alors il n’y aura rien à afficher sur le graphique. Essayez d’effacer la segmentation (fractionnement) ou sélectionnez une autre propriété.

## <a name="next-steps"></a>Étapes suivantes

  Pour découvrir les meilleures pratiques en matière de création de tableaux de bord actionnables avec des métriques, voir [Création de tableaux de bord d’indicateurs de performance clés personnalisés](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards).

