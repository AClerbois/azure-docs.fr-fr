---
title: Types de ressources et fournisseurs de ressources Azure | Microsoft Docs
description: Décrit les fournisseurs de ressources qui prennent en charge Resource Manager, ainsi que les schémas et versions d’API disponibles et les régions pouvant héberger les ressources.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 2cbc8843d41b760c52b9ca5ccfb6d940bd454136
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164824"
---
# <a name="azure-resource-providers-and-types"></a>Fournisseurs et types de ressources Azure

Lorsque vous déployez des ressources, vous devez fréquemment récupérer des informations sur les fournisseurs et les types de ressources. Par exemple, si vous voulez stocker des clés et des secrets, vous utilisez le fournisseur de ressources Microsoft.KeyVault. Ce fournisseur de ressources offre un type de ressource appelé vaults pour créer le coffre de clés.

Le nom d’un type de ressource est au format : **{fournisseur de ressources}/{type de ressource}** . Le type de ressource pour un coffre de clés est **Microsoft.KeyVault/vaults**.

Dans cet article, vous apprendrez comment :

* Afficher tous les fournisseurs de ressources dans Azure
* Vérifier l’état de l’inscription d’un fournisseur de ressources
* Inscrire un fournisseur de ressources
* Afficher les types de ressources pour un fournisseur de ressources
* Afficher les emplacements valides pour un type de ressource
* Afficher les versions d’API valides pour un type de ressource

Vous pouvez effectuer ces étapes via le portail Azure, Azure PowerShell ou Azure CLI.

Pour obtenir la liste qui mappe les fournisseurs de ressources aux services Azure, consultez [Fournisseurs de ressources pour les services Azure](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Portail Azure

Pour afficher tous les fournisseurs de ressources et l'état d'inscription de votre abonnement :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services**.

    ![sélectionner les abonnements](./media/resource-manager-supported-services/select-subscriptions.png)
3. Dans la zone **Tous les services**, entrez **Abonnement**, puis sélectionnez **Abonnements**.
4. Sélectionnez l'abonnement dans la liste.
5. Sélectionnez **Fournisseurs de ressources** et affichez la liste des fournisseurs de ressources disponibles.

    ![afficher les fournisseurs de ressources](./media/resource-manager-supported-services/show-resource-providers.png)

6. L’inscription d’un fournisseur de ressources configure votre abonnement pour travailler avec le fournisseur de ressources. L’étendue pour l’inscription est toujours l’abonnement. Par défaut, de nombreux fournisseurs de ressources sont enregistrés automatiquement. Toutefois, vous devrez peut-être inscrire manuellement certains fournisseurs de ressources. Pour inscrire un fournisseur de ressources, vous devez être autorisé à effectuer l’opération `/register/action` pour le fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire. Pour inscrire un fournisseur de ressources, sélectionnez **Inscrire**. Dans la capture d'écran précédente, le lien **Inscrire** est mis en surbrillance pour **Microsoft.Blueprint**.

    Vous ne pouvez pas annuler l’inscription d’un fournisseur de ressources quand vous avez encore des types de ressources de ce fournisseur de ressources dans votre abonnement.

Pour afficher des informations pour un fournisseur de ressources particulier :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services**.

    ![sélectionner Tous les services](./media/resource-manager-supported-services/more-services.png)

3. Dans la zone **Tous les services**, entrez **Explorateur de ressources**, puis sélectionnez **Explorateur de ressources**.
4. Développez **Fournisseurs** en sélectionnant la flèche droite.

    ![sélectionner les fournisseurs](./media/resource-manager-supported-services/select-providers.png)

5. Développez le fournisseur de ressources et le type de ressource que vous souhaitez afficher.

    ![sélectionner le type de ressource](./media/resource-manager-supported-services/select-resource-type.png)

6. Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource. L’Explorateur de ressources affiche les emplacements valides pour le type de ressource.

    ![afficher les emplacements](./media/resource-manager-supported-services/show-locations.png)

7. La version de l'API correspond à une version des opérations de l'API REST publiées par le fournisseur de ressources. Lorsqu'un fournisseur de ressources active de nouvelles fonctionnalités, une nouvelle version de l'API REST sera publiée. L’Explorateur de ressources affiche les versions d’API valides pour le type de ressource.

    ![afficher les versions d’API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour afficher tous les fournisseurs de ressources dans Azure et l’état de l’inscription de votre abonnement, utilisez :

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Qui retourne des résultats semblables à :

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

L’inscription d’un fournisseur de ressources configure votre abonnement pour travailler avec le fournisseur de ressources. L’étendue pour l’inscription est toujours l’abonnement. Par défaut, de nombreux fournisseurs de ressources sont enregistrés automatiquement. Toutefois, vous devrez peut-être inscrire manuellement certains fournisseurs de ressources. Pour inscrire un fournisseur de ressources, vous devez être autorisé à effectuer l’opération `/register/action` pour le fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Qui retourne des résultats semblables à :

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Vous ne pouvez pas annuler l’inscription d’un fournisseur de ressources quand vous avez encore des types de ressources de ce fournisseur de ressources dans votre abonnement.

Pour afficher des informations pour un fournisseur de ressources particulier, utilisez :

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Qui retourne des résultats semblables à :

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Pour afficher les types de ressources pour un fournisseur de ressources, utilisez :

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Résultat :

```powershell
batchAccounts
operations
locations
locations/quotas
```

La version de l'API correspond à une version des opérations de l'API REST publiées par le fournisseur de ressources. Lorsqu'un fournisseur de ressources active de nouvelles fonctionnalités, une nouvelle version de l'API REST sera publiée.

Pour obtenir les versions d’API disponibles pour un type de ressource, utilisez :

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Résultat :

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource.

Pour obtenir les emplacements pris en charge pour un type de ressource, utilisez :

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Résultat :

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>D’Azure CLI

Pour afficher tous les fournisseurs de ressources dans Azure et l’état de l’inscription de votre abonnement, utilisez :

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Qui retourne des résultats semblables à :

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

L’inscription d’un fournisseur de ressources configure votre abonnement pour travailler avec le fournisseur de ressources. L’étendue pour l’inscription est toujours l’abonnement. Par défaut, de nombreux fournisseurs de ressources sont enregistrés automatiquement. Toutefois, vous devrez peut-être inscrire manuellement certains fournisseurs de ressources. Pour inscrire un fournisseur de ressources, vous devez être autorisé à effectuer l’opération `/register/action` pour le fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire.

```azurecli
az provider register --namespace Microsoft.Batch
```

Qui retourne un message indiquant que l’inscription est en cours.

Vous ne pouvez pas annuler l’inscription d’un fournisseur de ressources quand vous avez encore des types de ressources de ce fournisseur de ressources dans votre abonnement.

Pour afficher des informations pour un fournisseur de ressources particulier, utilisez :

```azurecli
az provider show --namespace Microsoft.Batch
```

Qui retourne des résultats semblables à :

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Pour afficher les types de ressources pour un fournisseur de ressources, utilisez :

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Résultat :

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

La version de l'API correspond à une version des opérations de l'API REST publiées par le fournisseur de ressources. Lorsqu'un fournisseur de ressources active de nouvelles fonctionnalités, une nouvelle version de l'API REST sera publiée.

Pour obtenir les versions d’API disponibles pour un type de ressource, utilisez :

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Résultat :

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource.

Pour obtenir les emplacements pris en charge pour un type de ressource, utilisez :

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Résultat :

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md). 
* Pour afficher les schémas liés aux modèles de fournisseurs de ressources, consultez [Référence au modèle](/azure/templates/).
* Pour obtenir la liste qui mappe les fournisseurs de ressources aux services Azure, consultez [Fournisseurs de ressources pour les services Azure](azure-services-resource-providers.md).
* Pour afficher les opérations pour un fournisseur de ressources, consultez [API REST Azure](/rest/api/).
