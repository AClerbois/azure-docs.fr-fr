---
title: Envoyer des événements à un environnement Azure Time Series Insights | Microsoft Docs
description: Apprenez à configurer un hub d'événements et à exécuter un exemple d'application pour envoyer (push) des événements consultables dans Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: ae59e8115ca2d1ba69c8a3a099216eb3d98e2658
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237692"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Envoyer des événements à un environnement Time Series Insights à l'aide d'un hub d'événements

Ce tutoriel explique comment créer et configurer un hub d’événements dans Azure Event Hubs. Il traite également de l’exécution d’un exemple d’application pour envoyer (push) des événements dans Azure Time Series Insights à partir d’Event Hubs. Si vous disposez d’un concentrateur d’événements existant qui a déjà des événements au format JSON, ignorez ce didacticiel et affichez votre environnement dans [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurer un concentrateur d’événements

1. Pour en savoir plus sur la création d'un hub d'événements, consultez la [documentation Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. Dans la zone de recherche, recherchez **Event Hubs**. Dans la liste retournée, sélectionnez **Event Hubs**.
1. Sélectionnez votre hub d'événements.
1. Lorsque vous créez un hub d’événements, vous créez un espace de noms de hub d’événements. Si vous n’avez pas encore créé de hub d’événements dans l’espace de noms, accédez au menu et, sous **Entités**, créez-en un.  

    [![Liste de hubs d’événements](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. Une fois votre hub d'événements créé, sélectionnez-le dans la liste.
1. Dans le menu, sous **Entités**, sélectionnez **Event Hubs**.
1. Sélectionnez le nom du hub d'événements pour le configurer.
1. Sous **Entités**, sélectionnez **Groupes de consommateurs**, puis **Groupe de consommateurs**.

    [![Créer un groupe de consommateurs](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. Veillez à créer un groupe de consommateurs qui sera utilisé exclusivement par votre source d’événement Time Series Insights.

    > [!IMPORTANT]
    > Assurez-vous que ce groupe de consommateurs n’est pas utilisé par un autre service, par exemple, une tâche Azure Stream Analytics ou un autre environnement Time Series Insights. Si le groupe de consommateurs est utilisé par les autres services, les opérations de lecture sont affectées pour cet environnement et pour les autres services. Si vous utilisez le groupe de consommateurs **$Default**, d'autres lecteurs risquent de pouvoir réutiliser votre groupe de consommateurs.

1. Dans le menu, sous **Paramètres**, sélectionnez **Stratégies d’accès partagé**, puis **Ajouter**.

    [![Sélectionnez Stratégies d’accès partagé, puis le bouton Ajouter](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. Dans le volet **Ajouter une nouvelle stratégie d'accès partagé**, créez un accès partagé intitulé **MySendPolicy**. Vous utilisez cette stratégie d’accès partagé pour envoyer des événements dans les exemples C# plus loin dans cet article.

    [![Dans la zone Nom de la stratégie, entrez MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. Sous **Revendication**, cochez la case **Envoyer**.

## <a name="add-a-time-series-insights-instance"></a>Ajouter une instance de Time Series Insights

La mise à jour de Time Series Insights utilise des instances pour ajouter des données contextuelles aux données de télémétrie entrantes. Les données sont jointes au moment de la requête à l'aide d'un **ID de série chronologique**. L’**ID de série chronologique** de l’exemple de projet d’éoliennes que nous utiliserons plus loin dans cet article est `id`. Pour en savoir plus sur les instances de Time Series Insight et sur l'**ID de série chronologique**, consultez [Modèles de séries chronologiques](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Créer une source d'événement Time Series Insights

1. Si vous n'avez créé aucune source d'événement, suivez la procédure de [création d'une source d'événement](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Définissez une valeur pour `timeSeriesId`. Pour en savoir plus sur l'**ID de série chronologique**, consultez [Modèles de séries chronologiques](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Envoyer (push) des événements (exemple de projet d'éoliennes)

1. Sur la barre de recherche, recherchez **Event Hubs**. Dans la liste retournée, sélectionnez **Event Hubs**.

1. Sélectionnez votre hub d'événements.

1. Accédez à **Stratégies d'accès partagé** > **RootManageSharedAccessKey**. Copiez la valeur de la **Chaîne de connexion-clé primaire**.

    [![Copier la valeur de la chaîne de connexion de la clé primaire](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. Accédez à https://tsiclientsample.azurewebsites.net/windFarmGen.html L'URL exécute des éoliennes simulées.
1. Dans la zone **Chaîne de connexion Event Hub** de la page web, collez la chaîne de connexion que vous avez copiée dans [Envoyer (push) des événements](#push-events).
  
    [![Coller la chaîne de connexion de la clé primaire dans la zone Chaîne de connexion Event Hub](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. Sélectionnez **Cliquez pour démarrer**. Le simulateur génère une instance JSON que vous pouvez utiliser directement.

1. Revenez à votre hub d'événements sur le portail Azure. La page **Aperçu** affiche les nouveaux événements reçus par le hub d’événements.

    [![Page d’aperçu du hub d’événements affichant les métriques de celui-ci](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>Structures JSON prises en charge

### <a name="example-one"></a>Premier exemple

* **Entrée**: Un objet JSON simple.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Sortie**: Un événement.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Deuxième exemple

* **Entrée**: Un tableau JSON avec deux objets JSON. Chaque objet JSON est converti en événement.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Sortie**: Deux événements.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Troisième exemple

* **Entrée**: Objet JSON avec tableau JSON imbriqué contenant deux objets JSON.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Sortie**: Deux événements. La propriété **location** est copiée dans chaque événement.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Quatrième exemple

* **Entrée**: Objet JSON avec tableau JSON imbriqué contenant deux objets JSON. Cette entrée montre que les propriétés globales peuvent être représentées par l'objet JSON complexe.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Sortie**: Deux événements.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Étapes suivantes

- [Affichez votre environnement](https://insights.timeseries.azure.com) dans l’explorateur Time Series Insights.
