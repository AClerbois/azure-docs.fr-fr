---
title: Démarrage rapide Azure - Créer un objet blob dans un stockage d’objets à l’aide d’Azure PowerShell | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous utilisez Azure PowerShell dans un stockage d’objets (blob). Ensuite, vous utilisez PowerShell pour charger un objet blob dans Stockage Azure, télécharger un objet blob et répertorier les objets blob dans un conteneur.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: rogarana
ms.openlocfilehash: 13d28e43f9f712f5e597da8171ba9ebf4118bd49
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712039"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-azure-powershell"></a>Guide de démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide de Azure PowerShell

Utilisez le module Azure PowerShell pour créer et gérer les ressources Azure. La création ou la gestion des ressources Azure peut se faire depuis une ligne de commande PowerShell ou dans des scripts. Ce guide décrit l’utilisation de PowerShell pour transférer des fichiers entre un disque local et le Stockage Blob Azure.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ce démarrage rapide requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-container"></a>Créez un conteneur.

Les objets blob sont toujours chargés dans un conteneur. Vous pouvez organiser des groupes d’objets blob de la même façon que vous organisez vos fichiers dans les dossiers de l’ordinateur.

Définissez le nom du conteneur, puis créez le conteneur avec la commande [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). Définissez les autorisations dans `blob` pour autoriser l’accès aux fichiers. Le nom du conteneur dans cet exemple est *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les fichiers de disque dur virtuel utilisés pour appuyer les machines virtuelles IaaS sont des objets blob de pages. Utilisez des objets blob d’ajout pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. La plupart des fichiers stockés dans Stockage Blob sont des objets blob de blocs. 

Pour charger un fichier sur un objet blob de blocs, obtenez une référence de conteneur, puis obtenez une référence à l’objet blob de blocs dans ce conteneur. Une fois que vous avez la référence à l’objet blob, vous pouvez y charger des données à l’aide de [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Si l’objet blob n’existe pas, cette opération le crée. S’il existe, il est remplacé.

Les exemples suivants chargent *Image001.jpg* et *Image002.png* à partir du dossier *D:\\_TestImages* sur le disque local, dans le conteneur que vous avez créé.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Chargez autant de fichiers que vous le souhaitez avant de continuer.

## <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Obtenez la liste des objets blob dans le conteneur à l’aide de la commande [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). Cet exemple montre uniquement les noms des objets blob chargés.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez les objets blob sur votre disque local. Pour chaque objet blob à télécharger, définissez le nom et appelez la commande [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) pour télécharger l’objet blob.

Cet exemple télécharge les objets blob dans *D:\\_TestImages\Downloads* sur le disque local. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>Transfert de données avec AzCopy

L’utilitaire [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) est une autre option hautes performances pour transférer des données par script avec le stockage Azure. Utilisez AzCopy pour transférer des données à destination et à partir du stockage Blob, Table et Fichier.

Exemple rapide : voici la commande AzCopy qui permet de charger un fichier appelé *myfile.txt* dans le conteneur *mystoragecontainer* à partir de la fenêtre de PowerShell.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Supprimez toutes les ressources que vous avez créées. Pour ce faire, le plus simple consiste à supprimer le groupe de ressources. La suppression du groupe de ressources supprime aussi toutes les ressources qu’il contient. Dans l’exemple suivant, la suppression du groupe de ressources supprime aussi le compte de stockage et le groupe de ressources.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez transféré des fichiers entre un disque local et le stockage Blob Azure. Pour en savoir plus sur le fonctionnement de stockage d’objets Blob à l’aide de PowerShell, continuer avec le guide d’utilisation d’Azure PowerShell avec le stockage Azure.

> [!div class="nextstepaction"]
> [Utilisation d'Azure PowerShell avec Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Référence des applets de commande de stockage Microsoft Azure PowerShell

* [Applets de commande PowerShell - Stockage](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorateur Stockage Microsoft Azure

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.
