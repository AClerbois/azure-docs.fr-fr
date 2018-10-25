---
title: Identité de service Azure Data Factory | Microsoft Docs
description: Découvrez l’identité de service de fabrique de données dans Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: db0bc0cb64c0b6d7df9319c8d2c5850a27e767a1
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249211"
---
# <a name="azure-data-factory-service-identity"></a>Identité de service Azure Data Factory

Cet article vous aide à comprendre ce qu’est l’identité de service de fabrique de données et comment elle fonctionne.

## <a name="overview"></a>Vue d’ensemble

Lors de la création d’une fabrique de données, une identité du service est créée en même temps que la fabrique. L’identité du service est une application managée qui est inscrite auprès d’Azure Active Directory et qui représente la fabrique de données en question.

L’identité de service de Data Factory bénéficie des fonctionnalités suivantes :

- [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md), pour laquelle l’identité de service de fabrique de données est utilisée pour l’authentification auprès d’Azure Key Vault.
- Connecteurs, notamment [Stockage Blob Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure SQL Database](connector-azure-sql-database.md) et [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).

## <a name="generate-service-identity"></a>Générer l’identité du service

L’identité de service de fabrique de données est générée de la façon suivante :

- Lors de la création d’une fabrique de données via le **Portail Azure ou PowerShell**, l’identité du service est toujours créée automatiquement.
- Lors de la création d’une fabrique de données grâce au **kit de développement logiciel (SDK)**, l’identité du service n’est créée que si vous spécifiez « Identity = new FactoryIdentity() » durant la création de l’objet usine. Consultez l’exemple dans [Démarrage rapide .NET - Créer une fabrique de données](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Lors de la création d’une fabrique de données grâce à l’**API REST**, l’identité du service n’est créée que si vous le spécifiez la section « identity » dans le corps de la requête. Consultez l’exemple dans [Démarrage rapide REST - Créer une fabrique de données](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Si vous constatez que votre fabrique de données n’est pas associée à une identité de service après l’instruction [retrieve service identity](#retrieve-service-identity), vous pouvez en générer une explicitement par programmation, en mettant à jour la fabrique de données avec l’initiateur d’identité :

- [Générer l’identité de service à l’aide de PowerShell](#generate-service-identity-using-powershell)
- [Générer l’identité de service à l’aide de l’API REST](#generate-service-identity-using-rest-api)
- [Générer l’identité de service à l’aide du SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- L’identité de service ne peut pas être modifiée. La mise à jour d’une fabrique de données pour laquelle vous disposez déjà d’une identité de service n’a aucun effet ; l’identité du service reste inchangée.
>- Si vous mettez à jour une fabrique de données qui dispose déjà d’une identité de service, sans spécifier le paramètre « identity » dans l’objet fabrique, ou sans spécifier la section « identity » dans le corps de la requête REST, vous recevez un message d’erreur.
>- Lorsque vous supprimez une fabrique de données, l’identité de service associée est également supprimée.

### <a name="generate-service-identity-using-powershell"></a>Générer l’identité de service à l’aide de PowerShell

Appelez la commande **Set-AzureRmDataFactoryV2** à nouveau, vous verrez alors les champs « identity » qui viennent d’être générés :

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>Générer l’identité de service à l’aide de l’API REST

Appelez ensuite l’API avec la section« identity » dans le corps de la requête :

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Corps de la requête** : ajoutez "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Réponse** : l’identité du service est créée automatiquement, et la section « identity » est remplie en conséquence.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="generate-service-identity-using-sdk"></a>Générer l’identité de service à l’aide du SDK

Appelez la fabrique de données pour créer ou mettre à jour la fonction avec Identity=new FactoryIdentity(). Exemple de code utilisant NET :

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Récupérer l’identité du service

Vous pouvez récupérer l’identité du service à partir du portail Azure ou par programmation. Les sections suivantes vous montrent quelques exemples.

>[!TIP]
> Si vous ne voyez pas l’identité du service, [générez l’identité de service](#generate-service-identity) en mettant à jour votre fabrique.

### <a name="retrieve-service-identity-using-azure-portal"></a>Récupérer l’identité de service à l’aide du portail Azure

Les informations sur l’identité du service se trouvent sur le portail Azure sous : votre fabrique de données -> Paramètres -> Propriétés :

- ID de l’identité du service
- Locataire de l’identité du service
- **ID d’application de l’identité du service** : copiez cette valeur

![Récupérer l’identité du service](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Récupérer l’identité de service à l’aide de PowerShell

L’ID principal et l’ID locataire de l’identité du service seront retournés quand vous aurez obtenu une fabrique de données spécifique comme suit :

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copiez l’ID principal, puis exécutez la commande Azure Active Directory ci-dessous avec l’ID principal comme paramètre pour obtenir **l’ID d’application**, que vous allez utiliser pour obtenir l’accès :

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Étapes suivantes
Consultez les rubriques suivantes qui expliquent quand et comment utiliser l’identité de service de fabrique de données :

- [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md)
- [Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l’aide d’Azure Data Factory](connector-azure-data-lake-store.md)

Pour plus d’informations sur les identités managées des ressources Azure, sur lesquelles l’identité du service de fabrique de données est basée, consultez [Que sont les identités gérées pour les ressources Azure ?](~/articles/active-directory/msi-overview.md). 