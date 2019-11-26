---
title: Explorer des données avec l’explorateur – Azure Time Series Insights | Microsoft Docs
description: Découvrez comment utiliser l’explorateur Azure Time Series Insights pour afficher vos données IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: df895f99b64d30506bc9457fb5bdc54a2182b5fe
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012628"
---
# <a name="azure-time-series-insights-explorer"></a>Explorateur Azure Time Series Insights

Cet article décrit les fonctionnalités et options en disponibilité générale pour l’[application web Explorateur](https://insights.timeseries.azure.com/) Azure Time Series Insights. L’Explorateur Time Series Insights fait preuve de puissantes capacités de visualisation de données offertes par le service et vous pouvez y accéder au sein de votre propre environnement.

Azure Time Series Insights est un service entièrement managé d’analyse, de stockage et de visualisation qui simplifie la découverte et l’analyse simultanées de milliards d’événements IoT. Cette solution vous donne une vue globale de vos données, ce qui vous permet de valider rapidement votre solution IoT et d’éviter des temps morts coûteux d’appareils stratégiques. Vous pouvez découvrir des tendances masquées, détecter les anomalies et effectuer des analyses de cause première quasiment en temps réel. L’Explorateur Time Series Insights est actuellement en version préliminaire publique.

> [!TIP]
> Pour une visite guidée de l’environnement de démonstration, consultez le [Guide de démarrage rapide Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Vidéo

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Apprenez-en plus sur l’interrogation des données avec l’explorateur Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Regardez la vidéo précédente <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">Getting started with Time Series Insights using an Azure IoT Solution Accelerator</a> (Prise en main de Time Series Insights avec un accélérateur de solution Azure IoT).

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir utiliser l’Explorateur Time Series Insights, vous devez :

- Créez un environnement Time Series Insights. Pour plus d’informations, consultez [Comment prendre en main Time Series Insights](./time-series-insights-get-started.md).
- [Fournissez l’accès](time-series-insights-data-access.md) à votre compte dans l’environnement.
- Ajoutez-y une source [hub IoT](time-series-insights-how-to-add-an-event-source-iothub.md) ou un [hub d’événement](time-series-insights-how-to-add-an-event-source-eventhub.md).

## <a name="explore-and-query-data"></a>Explorer et interroger les données

Après quelques minutes suite à la connexion de votre source d’événements à votre environnement Time Series Insights, vous pouvez explorer et interroger vos données de série chronologique.

1. Pour démarrer, ouvrez l’[Explorateur Time Series Insights](https://insights.timeseries.azure.com/) dans votre navigateur web. Sur le côté gauche de la fenêtre, sélectionnez un environnement. Tous les environnements auxquels vous avez accès sont répertoriés par ordre alphabétique.

1. Une fois que vous sélectionnez un environnement, utilisez les configurations **From** (À partir de) et **To** (Vers) en haut, ou cliquez et faites glisser l’intervalle de temps souhaité. Sélectionnez la loupe en haut à droite, ou cliquez avec le bouton droit sur l’intervalle de temps sélectionné et sélectionnez **Rechercher**.

1. Vous pouvez également actualiser la disponibilité automatiquement toutes les minutes, en sélectionnant le bouton **Activer automatiquement**. Le bouton **Activer automatiquement** s’applique uniquement au graphique de disponibilité, pas au contenu de la visualisation principale.

1. L’icône Azure Cloud vous permet d’accéder à votre environnement dans le portail Azure.

   [![Environnement Time Series Insights](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Ensuite, vous voyez un graphique qui affiche le nombre total d’événements pendant la période sélectionnée. Vous avez plusieurs commandes disponibles :

    - **Panneau de l’éditeur de termes** : L’espace de terme correspond à l’endroit où vous interrogez votre environnement. Vous le trouverez sur le côté gauche de l’écran :
      - **Mesure** : Cette liste déroulante affiche toutes les colonnes numériques (**doubles**).
      - **Diviser par** : Cette liste déroulante affiche les colonnes catégorielles (**chaînes**).
      - Vous pouvez activer une interpolation par étape, afficher les valeurs minimale et maximale et ajuster l’axe des ordonnées à partir du panneau suivant pour **mesurer**. Vous pouvez aussi choisir si les données indiquées sont un nombre, une moyenne ou une somme des données.
      - Vous pouvez ajouter jusqu’à cinq conditions à afficher sur l’axe des abscisses. Utilisez le bouton **Copier** bouton pour ajouter un terme supplémentaire, ou sélectionnez **Ajouter** pour ajouter un nouveau terme.

        [![Panneau de l’éditeur de termes](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Prédicat** : Utilisez le prédicat pour filtrer rapidement les événements à l’aide de l’ensemble d’opérandes répertoriés dans le tableau ci-dessous. Si vous effectuez une recherche en la sélectionnant ou en cliquant dessus, le prédicat est automatiquement mis à jour selon cette recherche. Les types d’opérandes pris en charge comprennent les suivants :

         |Opération  |Types pris en charge  |Notes  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | Chaîne, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | Chaîne, Bool, Double, DateTime, TimeSpan, NULL        |  Tous les opérandes doivent être du même type ou être la constante NULL.        |
         |HAS     | Chaîne        |  Seuls les littéraux de chaîne constante sont autorisés à droite. Les chaînes vides et NULL ne sont pas autorisés.       |

      - **Exemples de requêtes**

         [![Exemples de requêtes](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Vous pouvez utiliser l’outil curseur **Taille de l’intervalle** pour effectuer un zoom/zoom arrière sur les intervalles pour le même intervalle. Ce curseur fournit un contrôle plus précis du déplacement entre les tranches de temps volumineuses qui montrent les tendances lissées, jusqu’à tranches aussi petites que la milliseconde, ce qui vous permet de voir des morceaux granulaires, haute résolution de vos données. Le point de départ par défaut du curseur est défini comme la vue optimale des données à partir de votre sélection afin d’équilibrer la résolution, la vitesse de la requête et la granularité.

1. L’outil **Balayage temporel** simplifie la navigation entre intervalles.

1. Utilisez la commande **Enregistrer** pour enregistrer votre requête actuelle et la partager avec d’autres utilisateurs de l’environnement. En utilisant **Ouvrir**, vous pouvez voir toutes vos requêtes enregistrées et toutes les requêtes partagées des autres utilisateurs dans les environnements auxquels vous avez accès.

   [![Requêtes](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Visualiser les données

1. Utilisez l’outil **Vue en perspective** pour obtenir une vue simultanée de jusqu’à quatre requêtes uniques. Le bouton **Vue en perspective** se situe dans le coin supérieur droit du graphique.

   [![Vue en perspective](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Affichez un graphique pour explorer visuellement vos données, et utilisez les outils du **Graphique** :

    - **Sélectionnez** ou **cliquez sur** un intervalle de temps spécifique ou une série de données unique.
    - Dans une sélection d’intervalle, vous pouvez effectuer un zoom ou explorer les événements.
    - Au sein d’une série de données, vous pouvez fractionner la série par une autre colonne, ajouter la série en tant que nouveau terme, afficher uniquement la série sélectionnée, exclure les séries sélectionnées, effectuer un test ping sur cette série ou explorer les événements de la série sélectionnée.
    - Dans la zone de filtre à gauche du graphique, vous pouvez voir toutes les séries de données affichées et les réorganiser par valeur ou par nom. Vous pouvez aussi afficher toutes les séries de données ou des séries épinglées ou non épinglées. Vous pouvez sélectionner une seule série de données et fractionner la série par une autre colonne, ajouter la série en tant que nouveau terme, afficher uniquement la série sélectionnée, exclure les séries sélectionnées, effectuer un test ping sur cette série ou explorer les événements de la série sélectionnée.
    - Lorsque vous affichez plusieurs termes simultanément, vous pouvez empiler, désempiler et voir des données supplémentaires sur une série de données et utiliser le même axe des ordonnées sur tous les termes du contrat. Utilisez les boutons dans le coin supérieur droit du graphique.

    [![Outil de graphique](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Utilisez la **carte thermique** pour identifier rapidement les séries de données uniques ou anormales dans une requête donnée. Un seul terme de recherche peut être visualisé comme une carte thermique.

    [![Carte thermique](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Lorsque vous explorez des événements en les sélectionnant ou en cliquant dessus, le panneau d’**événements** est affiché. Ici, vous pouvez voir tous les événements bruts et exporter vos événements sous forme de fichiers JSON ou CSV. Time Series Insights stocke toutes les données brutes.

    [![Événements](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Sélectionnez l’onglet **Statistiques** après avoir exploré les événements pour exposer des modèles et les statistiques de colonne.

    - **Modèles** : cette fonctionnalité fait ressortir de façon proactive les modèles statistiquement les plus significatifs dans une région de données sélectionnée. Vous n’avez pas à examiner plusieurs milliers d’événements pour comprendre les modèles qui nécessitent le plus de temps et d’énergie. Avec Time Series Insights, vous pouvez accéder directement à ces modèles statistiquement significatifs pour continuer la réalisation d’une analyse. Cette fonctionnalité est également utile pour les enquêtes post mortem des données historiques.
    - **Statistiques de colonne** : Les statistiques de colonne fournissent des graphiques et des tables qui décomposent les données de chaque colonne de la série de données sélectionnée sur l’intervalle de temps sélectionné.

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Vous avez maintenant vu les différentes fonctionnalités et options disponibles dans l’application web de l’Explorateur Time Series Insights.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Diagnostiquer et résoudre les problèmes](time-series-insights-diagnose-and-solve-problems.md) dans votre environnement Time Series Insights.

- Suivez la visite guidée du [démarrage rapide d’Azure Time Series Insights](time-series-quickstart.md).
