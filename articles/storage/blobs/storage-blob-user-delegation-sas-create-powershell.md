---
title: Créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec PowerShell (préversion) – Stockage Azure
description: Découvrez comment créer une signature d'accès partagé (SA) à l’aide des informations d’identification Azure Active Directory dans stockage Azure à l’aide de l’interface de ligne de commande PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: bdb66ec65d493c6af2f33bf6ed6e4a2bb2154235
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897038"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell-preview"></a>Créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec PowerShell (préversion)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Cet article explique comment utiliser les informations d’identification Azure Active Directory (Azure AD) pour créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec Azure PowerShell (préversion).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-module"></a>Installez le module en préversion

Pour utiliser PowerShell afin de créer une SAP de délégation d’utilisateur, vous devez d’abord installer le moduleAz.Storage 1.3.1-preview. Pour installer le module, procédez comme suit :

1. Désinstallez les installations précédentes d’Azure PowerShell :

    - Supprimez toutes les anciennes installations d’Azure PowerShell de Windows à l’aide du paramètre **Applications et fonctionnalités** situé sous **Paramètres**.
    - Supprimez tous les modules **Azure** de `%Program Files%\WindowsPowerShell\Modules`.

1. Vérifiez que la dernière version de PowerShellGet est installée. Ouvrez une fenêtre Windows PowerShell et exécutez la commande suivante pour installer la dernière version :

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Fermez, puis rouvrez la fenêtre PowerShell après l’installation de PowerShellGet.

1. Installez la dernière version d’Azure PowerShell :

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installez un module en préversion du stockage Azure qui prend en charge la SAP de délégation d’utilisateur :

    ```powershell
    Install-Module Az.Storage `
        –Repository PSGallery `
        -RequiredVersion 1.3.1-preview `
        –AllowPrerelease `
        –AllowClobber `
        –Force
    ```

1. Fermez, puis rouvrez la fenêtre PowerShell.

Étant donné que PowerShell charge le dernier module Az.Storage par défaut, il se peut que vous deviez charger explicitement le module 1.3.1-preview quand vous démarrez la console. Pour charger explicitement le module de préversion, exécutez la commande [Import-Module](/powershell/module/microsoft.powershell.core/import-module) :

```powershell
Import-Module Az.Storage -RequiredVersion 1.3.1
```

Pour en savoir plus sur l’installation d’Azure PowerShell, voir [Installer Azure PowerShell avec PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Connectez-vous à Azure PowerShell avec Azure AD

Appelez la commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pour vous connecter avec votre compte Azure AD :

```powershell
Connect-AzAccount
```

Pour plus d’informations sur la connexion avec PowerShell, consultez [Se connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-rbac"></a>Assigner des autorisations avec le RBAC

Pour créer une SAP de délégation d’utilisateur à partir d’Azure PowerShell, le compte Azure AD utilisé pour se connecter à PowerShell doit se voir attribuer un rôle incluant l’action **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey**. Cette autorisation permet au compte Azure AD de demander la *clé de délégation d’utilisateur*. La clé de délégation d’utilisateur est utilisée pour signer les SAP de délégation d’utilisateur. Le rôle qui fournit l’action **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** doit être attribué au niveau du compte de stockage, du groupe de ressources ou de l’abonnement. Pour plus d’informations sur les autorisations RBAC pour la création d’une SAP de délégation d’utilisateur, consultez la section **Affecter des autorisations avec RBAC** dans [Créer une SAP de délégation d’utilisateur](/rest/api/storageservices/create-user-delegation-sas).

Si vous ne disposez pas des autorisations suffisantes pour attribuer des rôles RBAC au principal de sécurité Azure AD, vous devrez peut-être demander au propriétaire du compte ou à l’administrateur de donner les autorisations nécessaires.

L’exemple suivant attribue le rôle de **Contributeur de données d’objet blob de stockage**, qui inclut l’action **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey**. Le rôle est étendu au niveau du compte de stockage.

N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Pour plus d’informations sur les rôles intégrés qui incluent l’action **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** , consultez [Rôles intégrés pour les ressources Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Utiliser des informations d’identification Azure AD pour sécuriser une SAP

Lorsque vous créez une SAP de délégation d’utilisateur avec Azure PowerShell, la clé de délégation d’utilisateur utilisée pour signer la SAP est créée de manière implicite. L’heure de début et l’heure d’expiration que vous spécifiez pour la SAP sont également utilisées comme heure de début et heure d’expiration pour la clé de délégation d’utilisateur. 

Étant donné que la clé de délégation d’utilisateur est valide au maximum pendant 7 jours à partir de la date de début, vous devez spécifier pour la SAP un délai d’expiration se situant dans les 7 jours après l’heure de début. La SAP n’est pas valide après l’expiration de la clé de délégation d’utilisateur. Par conséquent, une SAP dont le délai d’expiration est supérieur à 7 jours ne sera toujours valide que pendant 7 jours.

Pour créer une SAP de délégation d’utilisateur pour un conteneur ou un objet blob avec Azure PowerShell, commencez par créer un objet de contexte de stockage Azure, en spécifiant le paramètre `-UseConnectedAccount`. Le paramètre `-UseConnectedAccount` spécifie que la commande crée l’objet de contexte sous le compte Azure AD avec lequel vous vous êtes connecté.

N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Créer une SAP de délégation d’utilisateur pour un conteneur

Pour retourner un jeton SAS de délégation d’utilisateur pour un conteneur, appelez la commande [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) en passant dans l’objet de contexte de stockage Azure que vous avez créé précédemment.

L’exemple suivant retourne un jeton SAS de délégation d’utilisateur pour un conteneur. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Le jeton SAS de délégation d’utilisateur retourné sera semblable à ce qui suit :

```
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Créer une SAP de délégation d’utilisateur pour un objet blob

Pour retourner un jeton SAS de délégation d’utilisateur pour un objet blob, appelez la commande [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) en passant dans l’objet de contexte de stockage Azure que vous avez créé précédemment.

La syntaxe suivante retourne une SAP de délégation d’utilisateur pour un objet blob. L’exemple spécifie le paramètre `-FullUri`, qui retourne l’URI de l’objet blob auquel le jeton SAS est ajouté. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

L’URI de SAP de délégation d’utilisateur retournée sera semblable à ce qui suit :

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Une SAP de délégation d’utilisateur ne prend pas en charge la définition d’autorisations avec une stratégie d’accès stockée.

## <a name="revoke-a-user-delegation-sas"></a>Révoquer une SAP de délégation d’utilisateur

Pour révoquer une SAP de délégation d’utilisateur d’Azure PowerShell, appelez la commande **Revoke-AzStorageAccountUserDelegationKeys**. Cette commande révoque toutes les clés de délégation d’utilisateur associées au compte de stockage spécifié. Toutes les signatures d’accès partagé associées à ces clés sont invalidées.

N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

## <a name="next-steps"></a>Étapes suivantes

- [Créer une SAP de délégation d’utilisateur (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Obtenir une opération de clé de délégation d’utilisateur](/rest/api/storageservices/get-user-delegation-key)
