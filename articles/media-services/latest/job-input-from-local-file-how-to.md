---
title: Créer une entrée de travail Azure Media Services à partir d’un fichier local | Microsoft Docs
description: Cette rubrique montre comment créer une entrée de travail à partir d’un fichier local.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: 3eb16034cc6507944ca7bebb59893e0d72a6f4c9
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415457"
---
# <a name="create-a-job-input-from-a-local-file"></a>Créer une entrée de travail à partir d’un fichier local

Dans Media Services v3, lorsque vous soumettez des travaux pour traiter vos vidéos, vous devez indiquer à Media Services où trouver la vidéo d’entrée. La vidéo d’entrée peut être stockée sous la forme d’une ressource Media Service, dans laquelle vous créez une ressource d’entrée basée sur un fichier (stocké en local ou dans le stockage Blob Azure). Cette rubrique montre comment créer une entrée de travail à partir d’un fichier local. Pour obtenir un exemple complet, consultez cet [exemple GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Exemple de code .NET

Le code suivant montre comment créer une ressource d’entrée et l’utiliser comme entrée pour le travail. La fonction CreateInputAsset effectue les actions suivantes :

* Elle crée la ressource
* Elle récupère une [URL SAP](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) accessible en écriture dans le [conteneur de stockage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) de la ressource
* Elle charge le fichier dans le conteneur de stockage à l’aide de l’URL SAP

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Codes d’erreur des tâches

Consultez [Codes d’erreur](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Étapes suivantes

[Créer une entrée de travail à partir d’une URL HTTPS](job-input-from-http-how-to.md).
