---
title: Schéma d’abonnement à Azure Event Grid
description: Cet article décrit les propriétés d’abonnement à un événement avec Azure Event Grid. Schéma d’abonnement à Event Grid.
services: event-grid
author: femila
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: femila
ms.openlocfilehash: 933987f405b306607ae36c3f7b626108d641abf9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84558977"
---
# <a name="event-grid-subscription-schema"></a>Schéma d’abonnement à Event Grid

Pour créer un abonnement Event Grid, envoyez une requête à l’opération Créer un abonnement aux événements. Utilisez le format suivant :

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Par exemple, pour créer un abonnement aux événements pour un compte de stockage nommé `examplestorage` dans un groupe de ressources nommé `examplegroup`, utilisez le format suivant :

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Le nom de l’abonnement aux événements doit contenir entre 3 et 64 caractères et ne peut contenir que les caractères a-z, A-Z, 0-9 et "-". L’article décrit les propriétés et le schéma pour le corps de la requête.
 
## <a name="event-subscription-properties"></a>Propriétés de l’abonnement aux événements

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| destination | object | Objet qui définit le point de terminaison. |
| Filter | object | Champ facultatif pour filtrer les types d’événements. |

### <a name="destination-object"></a>objet de destination

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| endpointType | string | Type de point de terminaison pour l’abonnement (webhook/HTTP, concentrateur d’événements ou file d’attente). | 
| endpointUrl | string | URL de destination des événements dans cet abonnement aux événements. | 

### <a name="filter-object"></a>objet de filtre

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| includedEventTypes | tableau | Affiche une correspondance lorsque le type d’événement du message d’événement correspond exactement à l’un de ces noms de type d’événement. Génère une erreur lorsque le nom de l’événement ne correspond pas aux noms de type d’événement inscrits pour la source d’événements. Génère une correspondance pour tous les types d’événements. |
| subjectBeginsWith | string | Filtre de correspondance de préfixe appliqué au champ objet du message de l’événement. La chaîne vide ou par défaut représente une correspondance générale. | 
| subjectEndsWith | string | Filtre de correspondance de suffixe appliqué au champ objet du message de l’événement. La chaîne vide ou par défaut représente une correspondance générale. |
| isSubjectCaseSensitive | string | Contrôle la correspondance sensible à la casse pour les filtres. |


## <a name="example-subscription-schema"></a>Exemple de schéma d’abonnement

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez l’article [What is Event Grid?](overview.md) (Présentation d’Event Grid).
