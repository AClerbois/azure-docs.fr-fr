---
title: Schéma des événements Azure Maps dans Azure Event Grid
description: Décrit les propriétés et le schéma qui sont fournis pour les événements Azure Maps avec Azure Event Grid
services: event-grid
author: walsehgal
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: v-musehg
ms.openlocfilehash: 74a3674e632f8dc3f0755bc2ad48376708c7966f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60861852"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Schéma des événements Azure Event Grid pour Azure Maps

Cet article fournit les propriétés et les schémas des événements Azure Maps. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Types d’événement disponibles

Un compte Azure Maps émet les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Déclenché lorsque les coordonnées reçues sont passées de l’extérieur d’une limite géographique donnée à l’intérieur d’une zone donnée. |
| Microsoft.Maps.GeofenceExited | Déclenché lorsque les coordonnées reçues sont passées de l’intérieur d’une limite géographique donnée à l’extérieur de celle-ci. |
| Microsoft.Maps.GeofenceResult | Déclenché chaque fois qu’une requête de geofencing renvoie un résultat, quel que soit l’état. |

## <a name="event-examples"></a>Exemples d’événement

L’exemple suivant montre le schéma d’un événement **GeofenceEntered**

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

L’exemple suivant montre le schéma de **GeofenceResult**. 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="event-properties"></a>Propriétés d’événement

Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| rubrique | chaîne | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| subject | chaîne | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | chaîne | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | chaîne | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | chaîne | Identificateur unique de l’événement. |
| données | objet | Données d’événement de geofencing. |
| dataVersion | chaîne | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | chaîne | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| apiCategory | chaîne | Catégorie d’API de l’événement. |
| apiName | chaîne | Nom d’API de l’événement. |
| problèmes | objet | Répertorie les problèmes rencontrés durant le traitement. Si des problèmes sont retournés, aucune géométrie n’est retournée avec la réponse. |
| responseCode | number | Code de réponse HTTP |
| geometries | objet | Répertorie les géométries de la limite qui contiennent la position des coordonnées ou chevauchent le searchBuffer autour de la position. |

L’objet d’erreur est retourné lorsqu’une erreur se produit dans l’API Maps. L’objet erreur comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| error | ErrorDetails |Cet objet est retourné lorsqu’une erreur se produit dans l’API Maps.  |

L’objet ErrorDetails est retourné lorsqu’une erreur se produit dans l’API Maps. L’ErrorDetails ou l’objet comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| code | chaîne | Code d’état HTTP. |
| message | chaîne | Si elle est disponible, description de l’erreur compréhensible par l’utilisateur. |
| innererror | InnerError | En cas de disponibilité, un objet contenant des informations sur l’intention reconnue. |

L’InnerError est un objet contenant des informations spécifiques au service pour l’erreur. L’objet InnerError comporte les propriétés suivantes : 

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| code | chaîne | Message d’erreur. |

L’objet geometries énumère les ID de géométrie des limites géographiques qui ont expiré par rapport au délai d’attente utilisateur dans la requête. L’objet geometries possède des éléments géométriques avec les propriétés suivantes : 

| Propriété | Type | Description |
|:-------- |:---- |:----------- |
| deviceid | chaîne | ID de l’appareil. |
| distance | chaîne | <p>Distance entre les coordonnées et la bordure la plus proche de la limite géographique. Une valeur positive signifie que la coordonnée se situe à l’extérieur de la limite géographique. Si la coordonnée se situe à l’extérieur de la limite géographique mais que la valeur de searchBuffer est en dehors de la bordure de limite géographique la plus proche, la valeur est égale à 999. Une valeur négative signifie que la coordonnée se situe à l’intérieur de la limite géographique. Si la coordonnée se situe à l’intérieur du polygone mais que la valeur de searchBuffer est en dehors de la bordure de limite géographique la plus proche, la valeur est égale à -999. Une valeur égale à 999 signifie qu’il y a une grande probabilité que la coordonnée se trouve en dehors de la limite géographique. Une valeur égale à 999 signifie qu’il y a une grande probabilité que la coordonnée se trouve en deçà de la limite géographique.<p> |
| geometryid |chaîne | ID unique identifiant la géométrie de la limite géographique. |
| nearestlat | number | Latitude du point plus proche de la géométrie. |
| nearestlon | number | Longitude du point plus proche de la géométrie. |
| udId | chaîne | ID unique renvoyé par le service de téléchargement de l’utilisateur lors du téléchargement d’une limite géographique. N’appartient pas à l’API de publication de geofencing. |

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | Répertorie l’ID de géométrie de la limite géographique qui a expiré par rapport au délai d’attente utilisateur dans la requête. |
| geometries | geometries[] |Répertorie les géométries de la limite qui contiennent la position des coordonnées ou chevauchent le searchBuffer autour de la position. |
| invalidPeriodGeofenceGeometryId | string[]  | Répertorie l’ID de géométrie de la limite géographique dont la période n’est pas valide par rapport au délai d’attente utilisateur dans la requête. |
| isEventPublished | booléenne | Vrai si au moins un événement est publié à l’abonné de l’événement Azure Maps, faux si aucun événement n’est publié à l’abonné de l’événement Azure Maps |

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).