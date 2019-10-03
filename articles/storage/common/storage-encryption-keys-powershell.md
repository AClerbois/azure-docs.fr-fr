---
title: Configurer les clés gérées par le client pour le chiffrement du stockage Azure depuis PowerShell
description: Découvrez comment utiliser PowerShell afin de configurer des clés gérées par le client pour le chiffrement du stockage Azure. Les clés gérées par le client vous permettent de créer, faire pivoter, désactiver et révoquer des contrôles d'accès.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 982996bdbc5e618cf90aa4b028c0585a7898f465
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670958"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Configurer les clés gérées par le client pour le chiffrement du stockage Azure depuis PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Cet article explique comment configurer un coffre de clés avec des clés gérées par le client à l’aide de PowerShell.

> [!IMPORTANT]
> Pour utiliser des clés gérées par le client avec un chiffrement Azure Storage, le coffre de clés doit contenir deux propriétés requises configurées, **Soft Delete** (Suppression réversible) et **Do Not Purge** (Ne pas purger). Ces propriétés sont activées par défaut lorsque vous créez un coffre de clés dans le portail Azure. Toutefois, si vous devez activer ces propriétés sur un coffre de clés existant, vous devez utiliser PowerShell ou Azure CLI.
> Seules les clés RSA et la taille de clé 2048 sont prises en charge.

## <a name="assign-an-identity-to-the-storage-account"></a>Affecter une identité au compte de stockage

Pour activer des clés gérées par le client pour votre compte de stockage, affectez tout d’abord à votre compte de stockage une identité managée affectée au système. Cette identité managée vous sera utile pour autoriser le compte de stockage à accéder au coffre de clés.

Pour affecter une identité managée via PowerShell, appelez la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Pour plus d’informations sur la configuration d’identités managées affectées à un système, consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Créer un coffre de clés

Pour créer un coffre de clés via PowerShell, appelez la commande [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Le coffre de clés que vous utilisez pour stocker des clés gérées par le client pour le chiffrement du stockage Azure doivent disposer de deux paramètres de protection de clés, **Suppression réversible** et **Ne pas vider**. 

N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurer la stratégie d’accès au coffre de clés

Configurez ensuite la stratégie d’accès au coffre de clés de sorte que le compte de stockage dispose des autorisations nécessaires pour y accéder. Dans cette étape, vous utiliserez l’identité managée que vous avez précédemment affectée au compte de stockage.

Pour définir la stratégie d’accès du coffre de clés, appelez la commande [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). N’oubliez pas de remplacer les valeurs de l’espace réservé entre crochets par vos propres valeurs et d’utiliser les variables définies dans les exemples précédents.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Créer une clé

Créez ensuite une clé dans le coffre de clés. Pour créer une clé, appelez la commande [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). N’oubliez pas de remplacer les valeurs de l’espace réservé entre crochets par vos propres valeurs et d’utiliser les variables définies dans les exemples précédents.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurer le chiffrement avec des clés gérées par le client

Par défaut, le chiffrement du stockage Azure utilise des clés gérées par Microsoft. Dans cette étape, configurez votre compte de stockage Azure pour utiliser les clés gérées par le client et spécifiez la clé à lui associer.

Appelez la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) pour mettre à jour les paramètres de chiffrement du compte de stockage. N’oubliez pas de remplacer les valeurs de l’espace réservé entre crochets par vos propres valeurs et d’utiliser les variables définies dans les exemples précédents.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Mettre à jour la version de la clé

Lors de la création d’une version d’une clé, vous devez mettre à jour le compte de stockage si vous voulez utiliser cette nouvelle version. Tout d’abord, appelez la commande [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) pour obtenir la dernière version de la clé. Appelez ensuite la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) pour mettre à jour les paramètres de chiffrement du compte de stockage pour utiliser la nouvelle version de la clé, comme indiqué dans la section précédente.

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md) 
- [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
