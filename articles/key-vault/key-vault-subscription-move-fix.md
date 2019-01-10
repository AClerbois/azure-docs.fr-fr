---
title: Changer l’ID de locataire du coffre de clés après un déplacement d’abonnement - Azure Key Vault | Microsoft Docs
description: Apprenez à changer l’ID de client d’un coffre de clés après le déplacement d’un abonnement vers un autre client
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: ea6fc4b155075084150d5bb732f3f8a08846974f
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074306"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Modifier l’ID de client du coffre de clés après un déplacement d’abonnement

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>Q : Mon abonnement a été déplacé du client A vers le client B. Comment modifier l’ID de client du coffre de clés existant et définir des ACL correctes pour les principaux dans le client B ?

Lorsque vous créez un coffre de clés dans un abonnement, il est automatiquement lié à l’ID de client Azure Active Directory par défaut pour cet abonnement. Toutes les entrées de stratégie d’accès sont également liées à cet ID de client. Lorsque vous déplacez votre abonnement Azure du client A vers le client B, vos coffres de clés existants ne sont pas accessibles par les principaux (utilisateurs et applications) dans le client B. Pour résoudre ce problème, vous devez :

* modifier l’ID de client associé à tous les coffres de clés existants dans cet abonnement pour le client B ;
* supprimer toutes les entrées de stratégie d’accès existantes ;
* ajouter de nouvelles entrées de stratégie d’accès associées au client B.

Par exemple, si vous avez un coffre de clés 'myvault' dans un abonnement qui a été déplacé du client A vers le client B, voici comment modifier l’ID de client de ce coffre de clés et supprimer d’anciennes stratégies d’accès.

<pre>
Select-AzureRmSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Dans la mesure où ce coffre se trouvait dans le client A avant son déplacement, la valeur d’origine de **$vault. Properties.TenantId** est le client A, tandis que **(Get-AzureRmContext).Tenant.TenantId** est le client B.

Maintenant que votre coffre est associé avec l’ID de client correct et que les anciennes entrées de stratégie d’accès sont supprimées, définissez les nouvelles entrées de stratégie d’accès avec [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy).

## <a name="next-steps"></a>Étapes suivantes

Pour toute question concernant le coffre de clés Azure, rendez-vous sur les [forums sur les coffres de clés Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
