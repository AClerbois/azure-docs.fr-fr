---
title: Téléchargement du modèle d’une machine virtuelle Azure | Microsoft Docs
description: Téléchargez un modèle de machine virtuelle pour faciliter l’automatisation de déploiements dans le modèle de déploiement Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 574227e010a37340ce7248d2e4657f6a3f231d0a
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984526"
---
# <a name="download-the-template-for-a-vm"></a>Télécharger le modèle d’une machine virtuelle
Lorsque vous créez une machine virtuelle dans Azure à l’aide du portail ou de PowerShell, un modèle Resource Manager est automatiquement créé pour vous. Vous pouvez utiliser ce modèle pour dupliquer rapidement un déploiement. Le modèle contient des informations sur toutes les ressources d’un groupe de ressources. Pour une machine virtuelle, cela signifie que le modèle contient tout ce qui est créé pour prendre en charge la machine virtuelle dans ce groupe de ressources, notamment les ressources réseau.

## <a name="download-the-template-using-the-portal"></a>Télécharger le modèle à l’aide du portail
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
4. Sélectionnez **Script Automation**.
5. Sélectionnez **Télécharger** depuis le menu en haut et enregistrez le fichier .zip sur votre ordinateur local.
6. Ouvrez le fichier .zip et extrayez les fichiers dans un dossier. Le fichier .zip contient :
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

Le fichier template.json est le modèle.

## <a name="download-the-template-using-powershell"></a>Télécharger le modèle à l’aide de PowerShell
Vous pouvez également télécharger le fichier de modèle .json à l’aide de la cmdlet [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup). Vous pouvez utiliser le paramètre `-path` afin de fournir le nom et le chemin d’accès du fichier .json. Cet exemple montre comment télécharger le modèle pour le groupe de ressources nommé **myResourceGroup** vers le dossier **C:\users\public\downloads** sur votre ordinateur local.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le déploiement des ressources à l’aide de modèles, consultez la page [Procédure pas à pas du modèle Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

