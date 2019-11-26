---
title: Configurer Azure Private Link pour un compte Azure Cosmos
description: Découvrez comment configurer Azure Private Link pour accéder à un compte Azure Cosmos à l’aide d’une adresse IP privée dans un réseau virtuel.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 826fe1195a142bd0826d6311eab5eb208bbc7e35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007435"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Configurer Azure Private Link pour un compte Azure Cosmos (préversion)

À l’aide d’Azure Private Link, vous pouvez vous connecter à un compte Azure Cosmos via un point de terminaison privé. Le point de terminaison privé est un ensemble d’adresses IP privées dans un sous-réseau au sein de votre réseau virtuel. Vous pouvez alors limiter l’accès à un compte Azure Cosmos via des adresses IP privées. Lorsque Private Link est combiné à des stratégies NSG limitées, il permet de réduire le risque d’exfiltration de données. Pour plus d’informations sur les points de terminaison privés, consultez l’article [Azure Private Link](../private-link/private-link-overview.md).

Private Link permet aux utilisateurs d’accéder à un compte Azure Cosmos à partir du réseau virtuel ou de tout réseau virtuel appairé. Les ressources mappées à Private Link sont également accessibles localement via un Peering privé par le biais d’un VPN ou d’Azure ExpressRoute. 

Vous pouvez vous connecter à un compte Azure Cosmos configuré avec Private Link en utilisant les méthodes d’approbation automatique ou manuelle. Pour plus d’informations, consultez la section [Flux de travail d’approbation](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) de la documentation Private Link. 

Cet article décrit pas à pas la création d’un point de terminaison privé. Il suppose que vous utilisez la méthode d’approbation automatique.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Créer un point de terminaison privé au moyen du Portail Azure

Afin de créer un point de terminaison privé pour un compte Azure Cosmos existant à l’aide du Portail Azure, procédez comme suit :

1. Dans le volet **Toutes les ressources**, choisissez un compte Azure Cosmos.

1. Sélectionnez **Connexions au point de terminaison privé** dans la liste de paramètres, puis **Point de terminaison privé** :

   ![Sélections pour créer un point de terminaison privé dans le Portail Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. Dans le volet **Créer un point de terminaison privé (préversion) – Concepts de base**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez un groupe de ressources.|
    | **Détails de l’instance** |  |
    | Nom | Entrez un nom pour votre point de terminaison privé. Si ce nom est utilisé, créez-en un unique. |
    |Région| Sélectionnez la région dans laquelle vous souhaitez déployer Private Link. Créez le point de terminaison privé au même emplacement que votre réseau virtuel.|
    |||
1. Sélectionnez **Suivant : Ressource**.
1. Dans **Créer un point de terminaison privé - Ressource**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |Méthode de connexion  | Sélectionnez **Se connecter à une ressource Azure dans mon répertoire**. <br/><br/> Vous pouvez ensuite choisir l’une de vos ressources pour configurer Private Link. Ou vous pouvez vous connecter à la ressource d’un autre utilisateur à l’aide d’un ID ou d’un alias de ressource qu’il a partagé avec vous.|
    | Subscription| Sélectionnez votre abonnement. |
    | Type de ressource | Sélectionnez **Microsoft.AzureCosmosDB/databaseAccounts**. |
    | Ressource |Sélectionnez votre compte Azure Cosmos. |
    |Sous-ressource cible |Sélectionnez le type d’API Azure Cosmos DB que vous souhaitez mapper. Par défaut, il n’y a qu’un seul choix pour les API SQL, MongoDB et Cassandra. Pour les API Gremlin et Table, vous pouvez également choisir **Sql**, car ces API sont interopérables avec l’API SQL. |
    |||

1. Sélectionnez **Suivant : Configuration**.
1. Dans **Créer un point de terminaison privé (préversion) - Configuration**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |**Mise en réseau**| |
    | Réseau virtuel| Sélectionnez votre réseau virtuel. |
    | Subnet | Sélectionnez votre sous-réseau. |
    |**Intégration à un DNS privé**||
    |Intégrer à une zone DNS privée |Sélectionnez **Oui**. <br><br/> Pour vous connecter en privé à votre point de terminaison privé, vous avez besoin d’un enregistrement DNS. Nous vous recommandons d’intégrer votre point de terminaison privé à une zone DNS privée. Vous pouvez également utiliser vos propres serveurs DNS ou créer des enregistrements DNS à l’aide des fichiers hôtes sur vos machines virtuelles. |
    |Zone DNS privée |Sélectionnez **privatelink.documents.azure.com**. <br><br/> La zone DNS privée est déterminée automatiquement. Vous ne pouvez pas la modifier à l’aide du Portail Azure.|
    |||

1. Sélectionnez **Revoir + créer**. Sur la page **Revoir + créer**, Azure valide votre configuration.
1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

Si vous avez approuvé Private Link pour un compte Azure Cosmos, dans le Portail Azure, l’option **Tous les réseaux** dans le volet **Pare-feu et réseaux virtuels** est désactivée.

Le tableau suivant montre le mappage entre différents types d’API de compte Azure Cosmos, les sous-ressources prises en charge et les noms de zones privées correspondants. Vous pouvez également accéder aux comptes API Gremlin et Table via l’API SQL : il y a donc deux entrées pour ces API.

|Type d’API de compte Azure Cosmos  |Sous-ressources prises en charge (ou ID de groupe) |Nom de zone privée  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Table    |    Table     |   privatelink.table.cosmos.azure.com    |
|Table     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Récupérer les adresses IP privées

Une fois le point de terminaison privé provisionné, vous pouvez interroger les adresses IP. Pour afficher les adresses IP à partir du Portail Azure :

1. Sélectionnez **Toutes les ressources**.
1. Recherchez le point de terminaison privé que vous avez créé précédemment. Dans ce cas, il s’agit de **cdbPrivateEndpoint3**.
1. Sélectionnez l’onglet **Vue d’ensemble** pour afficher les paramètres DNS et les adresses IP.

![Adresses IP privées dans le Portail Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Plusieurs adresses IP sont créées par point de terminaison privé :

* Une pour le point de terminaison global (indépendant de la région) du compte Azure Cosmos
* Une pour chaque région dans laquelle le compte Azure Cosmos est déployé

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Créer un point de terminaison privé à l’aide d’Azure PowerShell

Exécutez le script PowerShell suivant afin de créer un point de terminaison privé nommé « MyPrivateEndpoint » pour un compte Azure Cosmos existant. Remplacez les valeurs des variables par les informations de votre environnement.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Intégrer le point de terminaison privé à une zone DNS privée

Après avoir créé le point de terminaison privé, vous pouvez l’intégrer à une zone DNS privée à l’aide du script PowerShell suivant :

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Récupérer les adresses IP privées

Une fois le point de terminaison privé configuré, vous pouvez interroger les adresses IP et le mappage FQDNS à l’aide du script PowerShell suivant :

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Créer un point de terminaison privé à l’aide d’un modèle Resource Manager

Vous pouvez configurer une Liaison privée (Private Link) en créant un point de terminaison privé dans un sous-réseau de réseau virtuel. Pour ce faire, utilisez un modèle Azure Resource Manager. 

Utilisez le code suivant pour créer un modèle Resource Manager nommé « PrivateEndpoint_template.json ». Ce modèle crée un point de terminaison privé pour un compte d’API SQL Azure Cosmos existant dans un réseau virtuel existant.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

### <a name="define-the-parameters-file-for-the-template"></a>Définir le fichier de paramètres pour le modèle

Créez un fichier de paramètres pour le modèle, puis nommez-le « PrivateEndpoint_parameters.json ». Ajoutez le code suivant au fichier de paramètres :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

### <a name="deploy-the-template-by-using-a-powershell-script"></a>Déployer le modèle à l’aide d’un script PowerShell

Créez un script PowerShell en utilisant le code suivant. Avant d’exécuter le script, remplacez l’ID d’abonnement, le nom du groupe de ressources et d’autres valeurs de variables par les informations de votre environnement.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

Dans le script PowerShell, la variable `GroupId` ne peut contenir qu’une seule valeur. Cette valeur est le type d’API du compte. Les valeurs autorisées sont : `Sql`, `MongoDB`, `Cassandra`, `Gremlin` et `Table`. Certains types de comptes Azure Cosmos sont accessibles par le biais de plusieurs API. Par exemple :

* Un compte d’API Gremlin est accessible à partir de comptes d’API Gremlin et SQL.
* Un compte d’API Table est accessible à partir de comptes d’API Table et SQL.

Pour ces comptes, vous devez créer un point de terminaison privé pour chaque type d’API. Le type d’API correspondant est spécifié dans le tableau `GroupId`.

Une fois que le modèle a bien été déployé, vous pouvez voir une sortie similaire à ce que montre l’image suivante. La valeur `provisioningState` est `Succeeded` si les points de terminaison privés sont configurés correctement.

![Sortie du déploiement pour le modèle Resource Manager](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Une fois le modèle déployé, les adresses IP privées sont réservées dans le sous-réseau. La règle de pare-feu du compte Azure Cosmos est configurée pour accepter uniquement les connexions à partir du point de terminaison privé.

## <a name="configure-custom-dns"></a>Configurer un système DNS personnalisé

Vous devez utiliser une zone DNS privée dans le sous-réseau où vous avez créé le point de terminaison privé. Configurez les points de terminaison de façon à ce que chaque adresse IP privée soit mappée à une entrée DNS. (Consultez la propriété `fqdns` dans la réponse indiquée plus haut.)

Lorsque vous créez le point de terminaison privé, vous pouvez intégrer celui-ci à une zone DNS privée dans Azure. Si vous choisissez plutôt d’utiliser une zone DNS personnalisée, vous devez la configurer afin d’ajouter des enregistrements DNS pour toutes les adresses IP privées réservées au point de terminaison privé.

## <a name="private-link-combined-with-firewall-rules"></a>Private Link combiné à des règles de pare-feu

Les situations et résultats suivants sont possibles lorsque vous utilisez Private Link en association avec des règles de pare-feu :

* Si vous ne configurez pas de règles de pare-feu, par défaut, tout le trafic peut accéder à un compte Azure Cosmos.

* Si vous configurez un trafic public ou un point de terminaison de service et que vous créez des points de terminaison privés, différents types de trafic entrant sont alors autorisés par le type de règle de pare-feu correspondant.

* Si vous ne configurez aucun trafic public ni point de terminaison de service et que vous créez des points de terminaison privés, le compte Azure Cosmos est alors uniquement accessible via les points de terminaison privés. Si vous ne configurez aucun trafic public ni point de terminaison de service, après le rejet ou la suppression de tous les points de terminaison privés approuvés, le compte est ouvert sur l’ensemble du réseau.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Mettre à jour un point de terminaison privé lors de l’ajout ou de la suppression d’une région

Pour ajouter ou supprimer des régions dans un compte Azure Cosmos, vous devez ajouter ou supprimer des entrées DNS pour ce compte. Mettez à jour ces modifications en conséquence dans le point de terminaison privé en procédant comme suit :

1. Lorsque l’administrateur Azure Cosmos DB ajoute ou supprime des régions, l’administrateur réseau reçoit une notification concernant les modifications en attente. Pour le point de terminaison privé mappé à un compte Azure Cosmos, la valeur de la propriété `ActionsRequired` passe de `None` à `Recreate`. Ensuite, l’administrateur réseau met à jour le point de terminaison privé en émettant une requête PUT avec la même charge utile Resource Manager que celle utilisée pour le créer.

1. Après la mise à jour du point de terminaison privé, vous pouvez mettre à jour la zone DNS privée du sous-réseau pour refléter les entrées DNS ajoutées ou supprimées et leurs adresses IP privées correspondantes.

Par exemple, imaginez que vous déployez un compte Azure Cosmos dans trois régions : « USA Ouest », « USA Centre » et « Europe Ouest ». Lorsque vous créez un point de terminaison privé pour votre compte, quatre adresses IP privées sont réservées dans le sous-réseau. Il existe une adresse IP pour chacune des trois régions et une adresse IP pour le point de terminaison global/indépendant de la région.

Plus tard, vous pouvez ajouter une nouvelle région (par exemple « USA Est ») au compte Azure Cosmos. Par défaut, la nouvelle région n’est pas accessible à partir du point de terminaison privé existant. L’administrateur du compte Azure Cosmos doit actualiser la connexion au point de terminaison privé avant d’y accéder à partir de la nouvelle région. 

Lorsque vous exécutez la commande ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>`, la sortie de la commande contient le paramètre `actionsRequired`. Ce paramètre est défini sur `Recreate`. Cette valeur indique que le point de terminaison privé doit être actualisé. Ensuite, l’administrateur du compte Azure Cosmos exécute la commande `Set-AzPrivateEndpoint` pour déclencher l’actualisation du point de terminaison privé.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Une nouvelle adresse IP privée est automatiquement réservée dans le sous-réseau sous ce point de terminaison privé. La valeur de `actionsRequired` devient `None`. Si vous n’avez pas d’intégration de zone DNS privée (en d’autres termes, si vous utilisez une zone DNS privée personnalisée), vous devez configurer votre zone DNS privée pour ajouter un nouvel enregistrement DNS pour l’adresse IP privée correspondant à la nouvelle région.

Vous pouvez utiliser les mêmes étapes lorsque vous supprimez une région. L’adresse IP privée de la région supprimée est récupérée automatiquement et l’indicateur `actionsRequired` devient `None`. Si vous n’avez pas d’intégration de zone DNS privée, vous devez configurer votre zone DNS privée pour supprimer l’enregistrement DNS de la région supprimée.

Les enregistrements DNS dans la zone DNS privée ne sont pas supprimés automatiquement, lorsqu’un point de terminaison privé est supprimé ou qu’une région du compte Azure Cosmos est retirée. Vous devez supprimer manuellement les enregistrements DNS.

## <a name="current-limitations"></a>Limitations actuelles

Les limitations suivantes s’appliquent lorsque vous utilisez Private Link avec un compte Azure Cosmos :

* La prise en charge de Private Link pour les comptes Azure Cosmos et les réseaux virtuels est disponible uniquement dans des régions spécifiques. Pour obtenir la liste des régions prises en charge, reportez-vous à la section [Régions disponibles](../private-link/private-link-overview.md#availability) de l’article Liaison privée (Private Link). 

  > [!NOTE]
  > Pour créer un point de terminaison privé, assurez-vous que le réseau virtuel et le compte Azure Cosmos sont dans des régions prises en charge.

* Lorsque vous utilisez Private Links avec un compte Azure Cosmos à l’aide d’une connexion en mode direct, vous ne pouvez utiliser que le protocole TCP. Le protocole HTTP n’est pas encore pris en charge.

* Lorsque vous utilisez l’API Azure Cosmos DB pour les comptes MongoDB, un point de terminaison privé est pris en charge pour les comptes sur le serveur version 3.6 uniquement (c’est-à-dire les comptes utilisant le point de terminaison au format `*.mongo.cosmos.azure.com`). Private Link n’est pas pris en charge pour les comptes sur le serveur version 3.2 (c’est-à-dire les comptes utilisant le point de terminaison au format `*.documents.azure.com`). Pour utiliser Private Link, vous devez migrer les anciens comptes vers la nouvelle version.

* Lorsque vous utilisez l’API d’Azure Cosmos DB pour les comptes MongoDB qui ont Private Link, vous ne pouvez pas utiliser d’outils tels que Robo 3T, Studio 3T et Mongoose. Le point de terminaison peut prendre en charge Private Link uniquement si le paramètre `appName=<account name>` est spécifié. Par exemple `replicaSet=globaldb&appName=mydbaccountname`. Étant donné que ces outils ne transmettent pas au service le nom de l’application dans la chaîne de connexion, vous ne pouvez pas utiliser Private Link. Toutefois, vous pouvez toujours accéder à ces comptes à l’aide des pilotes de Kit de développement logiciel (SDK) version 3.6.

* Vous ne pouvez pas déplacer ou supprimer un réseau virtuel s’il contient Private Link.

* Vous ne pouvez pas supprimer un compte Azure Cosmos s’il est attaché à un point de terminaison privé.

* Vous ne pouvez pas basculer un compte Azure Cosmos vers une région qui n’est pas mappée à tous les points de terminaison privés attachés au compte.

* Un administrateur réseau doit disposer au moins de l’autorisation «*/PrivateEndpointConnectionsApproval » au niveau de l’étendue du compte Azure Cosmos pour créer des points de terminaison privés approuvés automatiquement.

### <a name="limitations-to-private-dns-zone-integration"></a>Limitations de l’intégration à une zone DNS privée

Les enregistrements DNS dans la zone DNS privée ne sont pas supprimés automatiquement lorsque vous supprimez un point de terminaison privé ou une région du compte Azure Cosmos. Vous devez supprimer manuellement les enregistrements DNS avant d’effectuer les actions suivantes :

* Ajout d’un nouveau point de terminaison privé lié à cette zone DNS privée.
* Ajout d’une nouvelle région à un compte de base de données qui dispose de points de terminaison privés liés à cette zone DNS privée.

Si vous ne nettoyez pas les enregistrements DNS, des problèmes de plan de données inattendus peuvent se produire. Ces problèmes incluent l’indisponibilité des données dans les régions ajoutées après la suppression du point de terminaison privé ou de la région.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités de sécurité d’Azure Cosmos DB, consultez l’article suivant :

* Pour configurer un pare-feu pour Azure Cosmos DB, consultez [Prise en charge du pare-feu](firewall-support.md).

* Pour découvrir comment configurer un point de terminaison de service de réseau virtuel pour votre compte Azure Cosmos, consultez [Configurer l’accès à partir de réseaux virtuels](how-to-configure-vnet-service-endpoint.md).

* Pour en savoir plus sur Liaison privée (Private Link), consultez la documentation [Azure Private Link](../private-link/private-link-overview.md).
