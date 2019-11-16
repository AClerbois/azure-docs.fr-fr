---
title: Utiliser la fonctionnalité de base de données d’abonné pour joindre des bases de données dans Azure Data Explorer
description: Découvrez comment joindre des bases de données dans Azure Data Explorer à l’aide de la fonctionnalité de base de données d’abonné.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 2306b6cbdd347e3be9921b196ae06385ef5ca90a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083187"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Utiliser une base de données d’abonné pour joindre des bases de données dans Azure Data Explorer

La fonctionnalité de **base de données d’abonné** vous permet de joindre une base de données située dans un cluster différent à votre cluster Azure Data Explorer. La **base de données d’abonné** est jointe en mode *lecture seule*, ce qui permet d’afficher les données et d’exécuter des requêtes sur les données ingérées dans la **base de données du responsable**. La base de données d’abonné synchronise les modifications apportées aux bases de données de responsable. En raison de la synchronisation, on constate un décalage de données de quelques secondes à quelques minutes au niveau de la disponibilité des données. La durée du décalage dépend de la taille globale des métadonnées de la base de données du responsable. Les bases de données de responsable et d’abonné utilisent le même compte de stockage pour extraire les données. Le stockage appartient à la base de données de responsable. La base de données d’abonné affiche les données sans qu’il soit nécessaire de les ingérer. Étant donné que la base de données jointe est une base de données en lecture seule, les données, les tables et les stratégies de la base de données ne peuvent pas être modifiées, à l’exception de la [stratégie de mise en cache](#configure-caching-policy), des [principaux](#manage-principals) et des [autorisations](#manage-permissions). Les bases de données jointes ne peuvent pas être supprimées. Elles doivent être détachées par le responsable ou l’abonné avant de pouvoir être supprimées. 

L’attachement d’une base de données à un autre cluster à l’aide de la fonctionnalité de l’abonné est utilisé en tant qu’infrastructure pour partager des données entre les organisations et les équipes. La fonctionnalité est utile pour séparer les ressources de calcul afin de protéger un environnement de production contre les cas d’utilisation hors production. L’abonné peut également être utilisé pour associer le coût du cluster Azure Data Explorer au tiers qui exécute des requêtes sur les données.

## <a name="which-databases-are-followed"></a>Quelles sont les bases de données suivies ?

* Un cluster peut suivre une base de données, plusieurs bases de données ou toutes les bases de données d’un cluster de responsable. 
* Un cluster unique peut suivre des bases de données à partir de plusieurs clusters de responsable. 
* Un cluster peut contenir à la fois des bases de données d’abonné et des bases de données de responsable

## <a name="prerequisites"></a>Prérequis

1. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
1. [Créez un cluster et une base de données](/azure/data-explorer/create-cluster-database-portal) pour le responsable et l’abonné.
1. [Ingérez des données](/azure/data-explorer/ingest-sample-data) dans la base de données de responsable à l’aide de l’une des différentes méthodes présentées dans [Vue d’ensemble de l’ingestion](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Attacher une base de données

Vous pouvez utiliser différentes méthodes pour joindre une base de données. Dans cet article, nous abordons l’attachement d’une base de données à l’aide de C# ou d’un modèle Azure Resource Manager. Pour joindre une base de données, vous devez disposer d’autorisations sur le cluster du responsable et le cluster de l’abonné. Pour plus d’informations sur les autorisations, consultez [Gérer les autorisations](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Attacher une base de données avec C#

**Packages NuGet requis**

* Installez [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installez [Microsoft.Rest.ClientRuntime.Azure.Authentication à des fins d’authentification](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).


```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "adc";
var databaseName = "db" // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Joindre une base de données à l’aide d’un modèle Azure Resource Manager

Dans cette section, vous allez apprendre à joindre une base de données à l’aide d’un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Déployer le modèle 

Vous pouvez déployer le modèle Azure Resource Manager [à l’aide du Portail Azure](https://portal.azure.com) ou à l’aide de PowerShell.

   ![déploiement de modèle](media/follower/template-deployment.png)


|**Paramètre**  |**Description**  |
|---------|---------|
|Nom du cluster de l’abonné     |  Le nom du cluster de l’abonné       |
|Nom de configurations de base de données attachée    |    Le nom de l’objet de configurations de base de données attachée. Le nom doit être unique au niveau du cluster.     |
|Nom de la base de données     |      Le nom de la base de données à suivre. Si vous souhaitez suivre toutes les bases de données du responsable, utilisez « * ».   |
|ID de ressource du cluster du responsable    |   L’ID de la ressource du cluster du responsable.      |
|Type de modification des principaux par défaut    |   Le type de modification du principal par défaut. Peut être `Union`, `Replace` ou `None`. Pour plus d’informations sur le type de modification du principal par défaut, consultez [Commande de contrôle du type de modification du principal](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Location   |   L’emplacement de toutes les ressources. Le responsable et l’abonné doivent se trouver au même emplacement.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Vérifiez que la base de données a bien été attachée.

Pour vérifier que la base de données a été attachée avec succès, recherchez vos bases de données attachées dans le [Portail Azure](https://portal.azure.com). 

1. Accédez au cluster de l’abonné et sélectionnez **Bases de données**
1. Recherchez les nouvelles bases de données en lecture seule dans la liste des bases de données.

    ![Base de données d’abonné en lecture seule](media/follower/read-only-follower-database.png)

Sinon :

1. Accédez au cluster du responsable et sélectionnez **Bases de données**
2. Vérifiez que les bases de données concernées sont marquées comme **PARTAGÉES AVEC D’AUTRES** > **Oui**

    ![Lire et écrire des bases de données attachées](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Détacher la base de données de l’abonné à l’aide de C# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Détacher la base de données de l’abonné attachée du cluster de l’abonné

Le cluster de l’abonné peut détacher toute base de données attachée comme suit :

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Détacher la base de données de l’abonné attachée du cluster du responsable

Le cluster du responsable peut détacher toute base de données attachée comme suit :

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Gérer les principaux, les autorisations et la stratégie de mise en cache

### <a name="manage-principals"></a>Gérer les principaux

Lors de l’attachement d’une base de données, spécifiez le **type de modification des principaux par défaut**. Le type par défaut conserve la collection de bases de données du responsable des [principaux autorisés](/azure/kusto/management/access-control/index#authorization)

|**Type** |**Description**  |
|---------|---------|
|**Union**     |   Les principaux de la base de données attachée incluent toujours les principaux de la base de données d’origine, ainsi que les nouveaux principaux ajoutés à la base de données de l’abonné.      |
|**Replace**   |    Aucun héritage des principaux de la base de données d’origine. De nouveaux principaux doivent être créés pour la base de données attachée. Au moins un principal doit être ajouté pour bloquer l’héritage du principal.     |
|**Aucun**   |   Les principaux de la base de données attachée incluent uniquement les principaux de la base de données d’origine sans principaux supplémentaires.      |

Pour plus d’informations sur l’utilisation des commandes de contrôle pour configurer les principaux autorisés, consultez [Commandes de contrôle pour la gestion du cluster de l’abonné](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>Gérer les autorisations

La gestion de l’autorisation de base de données en lecture seule est identique à celle de tous les types de bases de données. Consultez [Gérer les autorisations dans le Portail Azure](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Configurer la stratégie de mise en cache

L’administrateur de base de données d’abonné peut modifier la [stratégie de mise en cache](/azure/kusto/management/cache-policy) de la base de données attachée ou de l’une de ses tables sur le cluster hôte. Le type par défaut conserve la collection de bases de données du responsable et les stratégies de mise en cache au niveau de la table. Vous pouvez, par exemple, disposer d’une stratégie de mise en cache de 30 jours sur la base de données du responsable pour exécuter des rapports mensuels et d’une stratégie de mise en cache de trois jours sur la base de données de l’abonné pour interroger uniquement les données récentes pour la résolution des problèmes. Pour plus d’informations sur l’utilisation des commandes de contrôle pour configurer la stratégie de mise en cache sur la table ou la base de données de l’abonné, consultez [Commandes de contrôle pour la gestion du cluster de l’abonné](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Limites

* Les clusters de l’abonné et du responsable doivent se trouver dans la région.
* [L’ingestion de streaming](/azure/data-explorer/ingest-data-streaming) ne peut pas être utilisée sur une base de données suivie.
* Vous ne pouvez pas supprimer une base de données attachée à un autre cluster avant de la détacher.
* Vous ne pouvez pas supprimer un cluster qui dispose d’une base de données attachée à un autre cluster avant de la détacher.
* Vous ne pouvez pas arrêter un cluster qui dispose d’une ou plusieurs bases de données de responsable ou d’abonné. 

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la configuration du cluster d’abonné, consultez [Commandes de contrôle pour la gestion d’un cluster d’abonné](/azure/kusto/management/cluster-follower).