---
title: Planifier la mise à l’échelle de votre environnement Azure Time Series Insights | Microsoft Docs
description: Cet article décrit comment suivre les meilleures pratiques lorsque vous planifiez un environnement Azure Time Series Insights. Les zones couvertes incluent la capacité de stockage, la conservation des données, la capacité d’entrée, la surveillance et la continuité d’activité et récupération d’urgence (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 659a6357736817f4a590b97e585230ec8c2b7dae
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332919"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planifier votre environnement Azure Time Series Insights en disponibilité générale

Cet article explique comment planifier votre environnement Azure Time Series Insights en disponibilité générale (GA) en fonction de votre taux d’entrée attendu et de vos besoins de conservation des données.

## <a name="video"></a>Vidéo

**Regardez cette vidéo pour en savoir plus sur la conservation des données dans Azure Time Series Insights et comment la planifier** :<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Bonnes pratiques

Pour commencer à utiliser Azure Time Series Insights, il est conseillé de connaître la quantité de données à transmettre à la minute et la durée de stockage de vos données.  

Pour plus d’informations sur la capacité et la rétention des deux références Time Series Insights, consultez [Tarifs de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Pour planifier au mieux l’environnement et assurer sa réussite à long terme, tenez compte des attributs suivants :

- [Capacité de stockage](#storage-capacity)
- [Période de rétention des données](#data-retention)
- [Capacité d’entrée](#ingress-capacity)
- [Mise en forme de vos événements](#shape-your-events)
- [Vérification de la mise en place des données de référence](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Capacité de stockage

Par défaut, Time Series Insights conserve les données en fonction de la quantité de stockage que vous avez approvisionnée (unités x quantité de stockage par unité) et de l’entrée.

## <a name="data-retention"></a>Conservation des données

Vous pouvez modifier le paramètre de **durée de conservation des données** dans votre environnement Azure Time Series Insights. Vous pouvez activer jusqu’à 400 jours de rétention. 

Azure Time Series Insights a deux modes :

* Un mode optimise les données les plus récentes. Il applique une stratégie pour **vider les anciennes données** laissant les données récentes disponibles avec l’instance. Ce mode est activé par défaut. 
* L’autre optimise les données pour qu’elles restent sous les limites de rétention configurées. L’option **Suspendre l’entrée**  empêche l’entrée des nouvelles données lorsqu’elles sont sélectionnées comme **Comportement de limite de stockage dépassée**. 

Vous pouvez ajuster la rétention et basculer entre les deux modes dans la page de configuration de l’environnement dans le portail Azure.

> [!IMPORTANT]
> Vous pouvez configurer une durée maximale de conservation des données de 400 jours dans votre environnement Azure Time Series Insights en disponibilité générale.

### <a name="configure-data-retention"></a>Configurer la rétention de données

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre environnement Time Series Insights.

1. Dans le volet de l’**environnement Time Series Insights**, sous **Paramètres**, sélectionnez **Configurer**.

1. Dans la zone **Durée de la conservation des données (en jours)** , entrez une valeur comprise entre 1 et 400.

   [![Configurer la rétention](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Pour en savoir plus sur l’implémentation d’une stratégie de rétention des données appropriée, consultez [Comment configurer la rétention](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacité d’entrée

Le deuxième domaine sur lequel se concentrer lors de la planification de votre environnement Time Series Insights est la *capacité d’entrée*. La capacité d’entrée est un dérivé de l’allocation par minute.

En termes de limitation, un paquet de données entré avec une taille de paquet de 32 Ko est considéré comme 32 événements, chacun faisant 1 Ko. La taille d’événement maximale autorisée est de 32 Ko. Les paquets de données de plus de 32 Ko sont tronqués.

Le tableau suivant récapitule la capacité d’entrée par unité pour chaque référence SKU Time Series Insights :

|SKU  |Nombre d’événements par mois  |Taille d’événement par mois  |Nombre d’événements par minute  |Taille d’événement par minute  |
|---------|---------|---------|---------|---------|
|S1     |   30 millions     |  30 Go     |  720    |  720 Ko   |
|S2     |   300 millions    |   300 Go   | 7 200   | 7 200 Ko  |

Vous pouvez augmenter la capacité d’une référence SKU S1 ou S2 à 10 unités dans un environnement unique. Vous ne pouvez pas migrer d’un environnement S1 vers S2. Vous ne pouvez pas migrer d’un environnement S2 vers S1.

Pour la capacité d’entrée, déterminez tout d’abord le total des entrées dont vous avez besoin chaque mois. Déterminez ensuite vos besoins par minute. 

La limitation et la latence jouent un rôle dans la capacité par minute. En cas de pic d’entrée de données d’une durée inférieure à 24 heures, Time Series Insights peut effectuer un « rattrapage » à un taux d’entrée égal à deux fois les taux indiqués dans le tableau précédent.

Par exemple, si vous avez une seule référence SKU S1, que des données sont entrées à un taux de 720 événements par minute, et un pic d’une durée inférieure à une heure à un taux de 1 440 événements maximum, aucune latence ne sera notable dans votre environnement. Toutefois, si vous dépassez les 1 440 événements par minute pendant plus d’une heure, vous constaterez probablement une latence des données qui sont visualisées et disponibles pour les requêtes dans votre environnement.

Vous ne savez peut-être pas à l’avance la quantité de données que vous allez transmettre en mode push. Dans ce cas, la télémétrie de données pour [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) et [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) est disponible dans votre abonnement Azure. La télémétrie peut vous aider à déterminer comment configurer votre environnement. Utilisez le volet **Indicateurs de performance** dans le portail Azure de la source d’événements correspondante pour afficher sa télémétrie. Comprendre les indicateurs de performance de votre source d’événement vous permet de planifier et configurer plus efficacement votre environnement Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calculer les besoins d’entrée

Pour calculer vos besoins d’entrée :

- Vérifiez que votre capacité d’entrée est supérieure à votre taux moyen par minute et que votre environnement est suffisamment grand pour gérer votre entrée attendue qui équivaut à deux fois votre capacité pendant moins d’une heure.

- Si des pics d’entrée durent plus de 1 heure, utilisez le taux de pic comme moyenne. Approvisionnez un environnement avec la capacité de gérer le taux de pic.

### <a name="mitigate-throttling-and-latency"></a>Résoudre la limitation et la latence

Pour plus d’informations sur la manière d’éviter la limitation et la latence, consultez [Résoudre la latence et la limitation](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Mettre en forme vos événements

Il est important de vérifier si votre méthode d’envoi d’événements à Time Series Insights prend en charge la taille de l’environnement que vous approvisionnez. (À l’inverse, vous pouvez mapper la taille de l’environnement sur le nombre d’événements que Time Series Insights lit et la taille de chaque événement.) Il est également important de songer aux attributs que vous souhaiterez peut-être utiliser pour le filtrage lors de l’interrogation de vos données.

> [!TIP]
> Passez en revue la documentation relative à la mise en forme du code JSON dans [Envoi des événements](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Vérification de la présence de données de référence

Un *jeu de données de référence* est une collection d’éléments qui augmente les événements issus de votre source d’événements. Le moteur d’entrée Time Series Insights associe chaque événement de votre source d’événements à la ligne de données correspondante dans votre jeu de données de référence. L’événement ajouté est ensuite disponible pour la requête. La jointure repose sur les colonnes de **clé privée** définies dans votre jeu de données de référence.

> [!NOTE]
> Les données de référence ne sont pas jointes rétroactivement. Seules les données d’entrée actuelles et futures sont mises en correspondance et jointes au jeu de données de référence lorsqu’il a été configuré et chargé. Si vous envisagez d’envoyer une grande quantité de données historiques à Time Series Insights et de ne pas charger ou de créer tout d’abord des données de référence dans Time Series Insights, vous devrez peut-être restaurer votre travail (conseil : pas très fun).  

Pour plus d’informations sur la création, le chargement et la gestion des données de référence dans Time Series Insights, accédez à notre [documentation sur les jeux de données de référence](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Étapes suivantes

- Commencez par créer un [nouvel environnement Time Series Insights dans le portail Azure](time-series-insights-get-started.md).

- Découvrez comment [ajouter une source d’événement Event Hubs](time-series-insights-how-to-add-an-event-source-eventhub.md) à Time Series Insights.

- Apprenez-en plus sur la [configuration d’une source d’événement IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
