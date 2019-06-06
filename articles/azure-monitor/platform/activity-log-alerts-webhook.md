---
title: Comprendre le schéma Webhook utilisé dans les alertes du journal d’activité
description: Découvrez le schéma du JSON publié sur une URL de Webhook en cas d’activation d’une alerte du journal d’activité.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: johnkem
ms.subservice: alerts
ms.openlocfilehash: 63f59d59712d851f9bb7ace27335fe665a598f9f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66477922"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhook des alertes du journal d’activité Azure
Dans le cadre de la définition d’un groupe d’actions, vous pouvez configurer des points de terminaison Webhook pour qu’ils reçoivent des notifications d’alerte du journal d’activité. Grâce aux Webhooks, vous pouvez acheminer ces notifications vers d’autres systèmes à des fins de post-traitement ou d’exécution d’actions personnalisées. Cet article montre également à quoi ressemble la charge utile d’une requête HTTP POST pour un webhook.

Pour plus d’informations sur les alertes du journal d’activité, découvrez comment [créer des alertes du journal d’activité Azure](activity-log-alerts.md).

Pour plus d’informations sur les groupes d’actions, découvrez comment [créer des groupes d’actions](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Vous pouvez également utiliser le [schéma alerte commun](https://aka.ms/commonAlertSchemaDocs), qui offre l’avantage de générer un seul extensible et unifiée charge utile et alerte toutes les alertes des services dans Azure Monitor, pour les intégrations de votre webhook. [En savoir plus sur les définitions de schéma alerte courants.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>Authentifier le Webhook
Le Webhook peut éventuellement utiliser l’autorisation par jeton à des fins d’authentification. L’URI du Webhook est enregistrée avec un ID de jeton, par exemple, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Schéma de la charge utile
La charge utile JSON contenue dans l’opération POST varie en fonction de champ data.context.activityLog.eventSource de la charge utile.

### <a name="common"></a>Courant
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
### <a name="administrative"></a>Administratif
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
### <a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

### <a name="resourcehealth"></a>ResourceHealth
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

Pour obtenir des informations spécifiques au sujet des schémas des alertes du journal d’activité sur les notifications d’intégrité du service, consultez la page [Notifications d’intégrité du service](../../azure-monitor/platform/service-notifications.md). En outre, découvrez comment [configurer des notifications de Webhook relatives à l’intégrité du service avec vos solutions existantes de gestion des problèmes](../../service-health/service-health-alert-webhook-guide.md).

Pour obtenir des informations spécifiques au sujet des schémas de toutes les autres alertes du journal d’activité, consultez la page [Vue d’ensemble du journal d’activité Azure](../../azure-monitor/platform/activity-logs-overview.md).

| Nom de l'élément | Description |
| --- | --- |
| status |Utilisé pour les alertes de métrique. Toujours défini sur « activé » pour les alertes du journal d’activité. |
| context |Contexte de l’événement. |
| resourceProviderName |Fournisseur de la ressource affectée. |
| conditionType |Toujours défini sur « Event ». |
| name |Nom de la règle d’alerte. |
| id |ID de ressource de l’alerte. |
| description |Description de l’alerte définie à la création de l’alerte. |
| subscriptionId |ID d’abonnement Azure. |
| timestamp |Heure à laquelle l’événement a été généré par le service Azure qui a traité la demande. |
| ResourceId |ID de ressource de la ressource affectée. |
| nom_groupe_ressources |Nom du groupe de ressources de la ressource affectée. |
| properties |Ensemble de paires `<Key, Value>` (c’est-à-dire, `Dictionary<String, String>`) incluant des détails sur l’événement. |
| événement |Élément contenant des métadonnées relatives à l’événement. |
| autorisation |Propriétés de contrôle d’accès en fonction du rôle de l’événement. Il s’agit généralement de l’action, du rôle et de l’étendue. |
| category |Catégorie de l’événement. Les valeurs prises en charge sont : Administrative, Alert, Security, ServiceHealth et Recommendation. |
| caller |Adresse e-mail de l’utilisateur ayant effectué l’opération, la revendication de nom d’utilisateur principal (UPN) ou la revendication de nom de principal du service (SPN) basée sur la disponibilité. Peut être null pour certains appels système. |
| correlationId |Généralement un GUID au format chaîne. Les événements avec correlationId appartiennent à la même action et partagent généralement un correlationId. |
| eventDescription |Description textuelle statique de l’événement. |
| eventDataId |Identificateur unique de l’événement. |
| eventSource |Nom de l’infrastructure ou du service Azure ayant généré l’événement. |
| httpRequest |Il s’agit généralement des requêtes clientRequestId, clientIpAddress et de la méthode HTTP (PUT, par exemple). |
| level |L’une des valeurs suivantes : Critique, Erreur, Avertissement et Informatif. |
| operationId |Généralement, GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| properties |Les propriétés de l’événement. |
| status |Chaîne. État de l’opération. Les valeurs courantes sont : Started, In Progress, Succeeded, Failed, Active et Resolved. |
| subStatus |Inclut généralement le code d’état HTTP de l’appel REST correspondant. Peut également inclure d’autres chaînes décrivant un sous-état. Les valeurs de sous-état courantes incluent OK (Code d’état HTTP : 200), Créé (code d’état HTTP : 201), Accepté (code d’état HTTP : 202, Aucun contenu (code d’état HTTP : 204, Requête incorrecte (code d’état HTTP : 400, Introuvable (code d’état HTTP : 404), Conflit (code d’état HTTP : 409), Erreur interne du serveur (code d’état HTTP : 500), Service indisponible (code d’état HTTP : 503) et Dépassement de délai de la passerelle (code d’état HTTP : 504). |

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur le journal d’activité](../../azure-monitor/platform/activity-logs-overview.md).
* [Exécuter des scripts Azure Automation (Runbooks) sur des alertes Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Utiliser une application logique pour envoyer un SMS par le biais de Twilio à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais il peut être modifié pour fonctionner avec une alerte du journal d’activité.
* [Utiliser une application logique pour envoyer un message Slack à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais il peut être modifié pour fonctionner avec une alerte du journal d’activité.
* [Utiliser une application logique pour envoyer un message à une file d’attente Azure à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais il peut être modifié pour fonctionner avec une alerte du journal d’activité.

