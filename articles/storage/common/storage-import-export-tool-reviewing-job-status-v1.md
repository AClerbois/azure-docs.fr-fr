---
title: Vérification de l’état des travaux Azure Import/Export - v1 | Microsoft Docs
description: Découvrez comment utiliser les fichiers journaux créés lors de l’exécution du travail d’importation ou d’exportation pour connaître l’état du travail Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: c5b9d1993c9e90411c7b05d9874721a159275f22
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44021826"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Vérification de l’état des travaux Azure Import/Export avec les copies des fichiers journaux
Lorsque le service Microsoft Azure Import/Export traite des lecteurs associés à un travail d’importation ou d’exportation, il écrit les fichiers journaux de copie dans le compte de stockage dans ou à partir duquel vous importez ou exportez des objets blob. Le fichier journal contient l’état détaillé de chaque fichier importé ou exporté. L’URL de chaque fichier journal de copie est renvoyée lorsque vous interrogez l’état d’un travail effectué ; consultez la page [Get Job](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) pour plus d’informations.  

## <a name="example-urls"></a>Exemples d’URL

Voici des exemples d’URL de fichiers journaux de copie pour un travail d’importation avec deux lecteurs :  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Consultez la page [Format des fichiers journaux du Service Import-Export](../storage-import-export-file-format-log.md) pour connaître le format des journaux de copie et la liste complète des codes d’état.  
  
## <a name="next-steps"></a>Étapes suivantes
 
 * [Configuration de l’outil Azure Import/Export](storage-import-export-tool-setup-v1.md)   
 * [Préparation des disques durs pour un travail d’importation](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Réparation d’un travail d’importation](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Réparation d’un travail d’exportation](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Résolution des problèmes associés à l’outil Azure Import-Export](storage-import-export-tool-troubleshooting-v1.md)
