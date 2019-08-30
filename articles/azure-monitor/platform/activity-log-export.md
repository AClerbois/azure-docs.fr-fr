---
title: Exporter le journal d’activité Azure
description: Exportez le journal d'activité Azure à des fins d’archivage ou Azure Event Hubs à des fins d'exportation en dehors d’Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: d34040722ac8793fd4bbb02f2d3fa59247f8267c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639636"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exporter le journal d’activité vers le stockage ou Azure Event Hubs
Le [journal d’activité Azure](activity-logs-overview.md) apporte des insights sur les événements liés aux abonnements qui se sont produits dans votre abonnement Azure. En plus d'afficher le journal d’activité dans le portail Azure ou de le copier dans un espace de travail Log Analytics où il peut être analysé avec d’autres données collectées par Azure Monitor, vous pouvez créer un profil de journal pour archiver le journal d’activité dans un compte de stockage Azure ou le diffuser en continu dans un Event Hub.

## <a name="archive-activity-log"></a>Archiver le journal d’activité
Archiver le journal d'activité vers un compte de stockage est utile si vous souhaitez conserver vos données de journal pendant une période supérieure à 90 jours (en disposant d’un contrôle total sur la stratégie de rétention) à des fins d’audit, d’analyse statique ou de sauvegarde. Si vous devez conserver vos événements pendant 90 jours ou moins, il est inutile de configurer l’archivage sur un compte de stockage, puisque les événements du journal d’activité sont conservés dans la plateforme Azure pendant 90 jours.

## <a name="stream-activity-log-to-event-hub"></a>Diffuser en continu le journal d’activité vers Event Hub
[Azure Event Hubs](/azure/event-hubs/) est une plateforme de streaming de données et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Voici deux façons d’utiliser la fonctionnalité de diffusion en continu pour le journal d’activité :
* **Diffuser en continu sur des systèmes de journalisation et de télémétrie tiers** : Au fil du temps, la diffusion en continu sur Azure Event Hubs deviendra le mécanisme de diffusion de votre journal d’activité vers les solutions tierces SIEM et Log Analytics.
* **Créer une plateforme de journalisation et de télémétrie personnalisée** : Si vous disposez déjà d’une plate-forme de télémétrie personnalisée, ou si vous envisagez d’en créer une, la nature hautement évolutive de publication et d’abonnement d’Event Hubs vous permet d’intégrer avec souplesse le journal d’activité. 

## <a name="prerequisites"></a>Prérequis

### <a name="storage-account"></a>Compte de stockage
Si vous archivez votre journal d’activité, vous devez [créer un compte de stockage](../../storage/common/storage-quickstart-create-account.md), si vous n'en avez pas déjà. Nous vous déconseillons d'utiliser un compte de stockage existant sur lequel sont stockées d’autres données de non-analyse, afin de pouvoir mieux contrôler l’accès aux données d’analyse. Si vous archivez également des journaux de diagnostic et des métriques sur un compte de stockage cependant, vous pouvez choisir d’utiliser ce même compte pour regrouper toutes vos données d’analyse au même emplacement.

Il n’est pas nécessaire que le compte de stockage se trouve dans le même abonnement que l’abonnement générant des journaux d’activité, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.
> [!NOTE]
>  Vous ne pouvez pas archiver les données dans un stockage situé derrière un réseau virtuel sécurisé.

### <a name="event-hubs"></a>Event Hubs
Si vous transférez votre journal d’activité vers un Event Hub, vous devez [créer un Event Hub](../../event-hubs/event-hubs-create.md), si vous n'en avez pas déjà. Si vous avez précédemment diffusé en continu des événements du journal d’activité vers cet espace de noms Event Hubs, cet Event Hub sera réutilisé.

La stratégie d’accès partagé définit les autorisations dont dispose le mécanisme de diffusion en continu. La diffusion en continu vers Event Hubs requiert des autorisations de gestion, d’envoi et d’écoute. Vous pouvez créer ou modifier des stratégies d’accès partagé de l’espace de noms Event Hubs dans le portail Azure, sous l’onglet Configurer de votre espace de noms Event Hubs.

Pour mettre à jour le profil de journal d’activité afin d’inclure la diffusion en continu, vous devez disposer de l’autorisation ListKey sur la règle d’autorisation Event Hubs. Il n’est pas nécessaire que l’espace de noms Event Hubs se trouve dans le même abonnement que l’abonnement qui génère des journaux d’activité, à condition que l’utilisateur configurant le paramètre dispose d’un accès RBAC aux deux abonnements, et que ces derniers se trouvent dans le même locataire AAD.

Diffusez en continu le journal d’activité dans un Event Hub en [créant un profil de journal](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Créer un profil de journal
Vous définissez la manière dont votre journal d’activité Azure est exporté à l’aide d'un **profil de journal**. Chaque abonnement Azure ne peut avoir qu’un seul profil de journal. Ces paramètres peuvent être configurés via l’option **Exporter** dans le panneau Journal d’activité du portail. Ils peuvent également être configurés par programme [à l’aide de l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), d’applets de commande PowerShell ou de l’interface de ligne de commande.

Le profil de journal définit ce qui suit.

**Destination du journal d'activité.** Actuellement, les options disponibles sont le compte de stockage ou des Event Hubs.

**Catégories d'événements à envoyer.** La signification de *catégorie* est différente dans les événements de profil de journal et de journal d’activité. Dans le profil de journal, *catégorie* désigne le type d’opération (Write, Delete, Action). Dans un événement de journal d’activité, la propriété *catégorie** représente la source ou le type d’événement (par exemple, Administration, ServiceHealth et Alert).

**Les régions (emplacements) qui doivent être exportées.** Vous devez inclure tous les emplacements car de nombreux événements du journal d’activité sont des événements globaux.

**Durée pendant laquelle le journal d’activité doit être conservé dans un compte de stockage.** Une durée de rétention de zéro jour signifie que les journaux d’activité sont conservés indéfiniment. La valeur peut également être n’importe quel nombre de jours, compris entre 1 et 2147483647.

Si des stratégies de rétention sont définies, mais que le stockage des journaux d’activité dans un compte de stockage est désactivé, les stratégies de rétention n’ont aucun effet. Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux d’activité de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’activité d’avant-hier seront supprimés. Le processus de suppression commence à minuit UTC, mais notez que la suppression des journaux d’activité de votre compte de stockage peut prendre jusqu’à 24 heures.



> [!WARNING]
> Depuis le 1er novembre 2018, le format des données de journal dans le compte de stockage est devenu JSON Lines. [Consultez cet article pour en savoir plus sur les conséquences liées à ce changement et pour découvrir comment mettre à jour vos outils pour qu’ils gèrent ce nouveau format.](diagnostic-logs-append-blobs.md)


> [!IMPORTANT]
> Vous pouvez recevoir un message d’erreur lors de la création d’un profil de journal, si le fournisseur de ressources Microsoft.Insights n’est pas inscrit. Consultez [Fournisseurs et types de ressources Azure](../../azure-resource-manager/resource-manager-supported-services.md) pour inscrire ce fournisseur.

### <a name="create-log-profile-using-the-azure-portal"></a>Créer un profil de journal à l’aide du portail Azure

Créez ou modifiez un profil de journal avec l'option **Exporter vers Event Hub** dans le portail Azure.

1. À partir du menu **Superviser** du portail Azure, sélectionnez **Exporter vers Event Hub**.

    ![Bouton Exporter dans le portail](media/activity-log-export/portal-export.png)

3. Dans le panneau qui s’affiche, spécifiez ce qui suit :
   * Régions avec les événements à exporter. Vous devez sélectionner toutes les régions pour être certain de ne manquer aucun événement clé car le journal d'activité est mondial (et non régional) et dès lors, la plupart des événements ne sont pas associés à une région. 
   * Si vous souhaitez écrire dans un compte de stockage :
       * Le compte de stockage pour lequel vous souhaitez enregistrer les événements.
       * Le nombre de jours pendant lesquels vous souhaitez conserver ces événements dans le stockage. Un paramètre de 0 jour conserve les journaux d’activité indéfiniment.
   * Si vous souhaitez écrire dans un Event Hub :
       * L’espace de noms Service Bus dans lequel vous souhaitez qu’un Event Hub soit créé pour diffuser ces événements.

     ![Panneau Exporter le journal d’activité](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Cliquez sur **Enregistrer** pour enregistrer ces paramètres. Les paramètres sont immédiatement appliqués à votre abonnement.


### <a name="configure-log-profile-using-powershell"></a>Configurer le profil de journal à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Si un profil de journal existe déjà, vous devez tout d’abord le supprimer, puis en créer un nouveau.

1. Utilisez `Get-AzLogProfile` pour déterminer s’il existe un profil de journal.  S’il n’existe aucun profil de journal, notez la propriété *name*.

1. Utilisez `Remove-AzLogProfile` pour supprimer le profil de journal à l’aide de la valeur provenant de la propriété *name*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Utilisez `Add-AzLogProfile` pour créer un profil de journal :

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Propriété | Obligatoire | Description |
    | --- | --- | --- |
    | Name |OUI |Nom de votre profil de journal. |
    | StorageAccountId |Non |ID de ressource du compte de stockage dans lequel le journal d’activité doit être enregistré. |
    | serviceBusRuleId |Non |ID de règle Service Bus pour l’espace de noms Service Bus dans lequel vous souhaitez que des concentrateurs d’événements soient créés. Il s'agit d'une chaîne au format `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location |OUI |Liste séparée par des virgules des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. |
    | RetentionInDays |OUI |Nombre de jours pendant lesquels les événements doivent être conservés dans le compte de stockage, compris entre 1 et 2147483647. Une valeur de zéro signifie que les journaux d’activité seront stockés pour une durée indéfinie. |
    | Category |Non |Liste séparée par des virgules des catégories d’événements qui doivent être collectées. Les valeurs possibles sont _Write_, _Delete_ et _Action_. |

### <a name="example-script"></a>Exemple de script
Vous trouverez ci-dessous un exemple de script PowerShell pour créer un profil de journal qui écrit le journal d’activité dans un compte de stockage et un Event Hub.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configurer le profil de journal à l’aide d’Azure CLI

Si un profil de journal existe déjà, vous devez tout d’abord le supprimer, puis créer un nouveau profil de journal.

1. Utilisez `az monitor log-profiles list` pour déterminer s’il existe un profil de journal.
2. Utilisez `az monitor log-profiles delete --name "<log profile name>` pour supprimer le profil de journal à l’aide de la valeur provenant de la propriété *name*.
3. Utilisez `az monitor log-profiles create` pour créer un profil de journal :

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propriété | Obligatoire | Description |
    | --- | --- | --- |
    | name |OUI |Nom de votre profil de journal. |
    | storage-account-id |OUI |ID de ressource du compte de stockage dans lequel les journaux d’activité doivent être enregistrés. |
    | locations |OUI |Liste, séparée par des espaces, des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. Vous pouvez voir une liste de toutes les régions pour votre abonnement à l’aide de `az account list-locations --query [].name`. |
    | days |OUI |Nombre de jours pendant lesquels les événements doivent être conservés, compris entre 1 et 365. Une valeur de zéro signifie que les journaux d’activité seront stockés pour une durée indéfinie (pour toujours).  Si zéro est spécifié, le paramètre activé doit être défini sur true. |
    |enabled | OUI |True ou False.  Utilisée pour activer ou désactiver la stratégie de rétention.  Si la valeur est True, le paramètre days doit être une valeur supérieure à 0.
    | categories |OUI |Liste, séparée par des espaces, des catégories d’événements qui doivent être collectées. Les valeurs possibles sont Write, Delete et Action. |



## <a name="activity-log-schema"></a>Schéma du journal d'activité
Qu'il soit envoyé à Stockage Azure ou Event Hub, le journal d’activité est écrit dans un JSON au format suivant.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```
Les éléments de ce JSON sont décrits dans le tableau suivant.

| Nom de l'élément | Description |
| --- | --- |
| time |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| resourceId |ID de ressource de la ressource affectée. |
| operationName |Nom de l’opération. |
| category |Catégorie de l’action, par ex. Écriture, Lecture, Action. |
| resultType |Le type du résultat, par ex. Réussite, échec, démarrage |
| resultSignature |Dépend du type de ressource. |
| durationMS |Durée de l’opération en millisecondes |
| callerIpAddress |Adresse IP de l’utilisateur qui a effectué l’opération, la revendication UPN ou la revendication SPN basée sur la disponibilité. |
| correlationId |Généralement un GUID au format chaîne. Les événements qui partagent un correlationId appartiennent à la même action uber. |
| identité |Objet blob JSON décrivant l’autorisation et les revendications. |
| autorisation |Objet blob des propriétés RBAC de l’événement. Inclut généralement les propriétés « action », « role » et « scope ». |
| level |Niveau de l’événement. L’une des valeurs suivantes : _Critical_, _Error_, _Warning_, _Informational_ et _Verbose_ |
| location |Région dans laquelle se trouve l’emplacement (ou global). |
| properties |Jeu de paires `<Key, Value>` (p. ex. Dictionary) décrivant les détails de l’événement. |

> [!NOTE]
> Les propriétés et l’utilisation de ces propriétés peuvent varier en fonction de la ressource.



## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le journal d’activité](../../azure-resource-manager/resource-group-audit.md)
* [Collecter le journal d’activité dans les journaux Azure Monitor](activity-log-collect.md)
