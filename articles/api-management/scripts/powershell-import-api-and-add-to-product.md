---
title: Exemple de script Azure PowerShell - Importer une API | Microsoft Docs
description: Exemple de script Azure PowerShell - Importer une API
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 08c597da6795992ff76b43d22c8deff9237e2208
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736693"
---
# <a name="import-an-api"></a>Importer une API

Cet exemple de script importe une API et l’ajoute à un produit Gestion des API. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 1.0 ou une version ultérieure pour les besoins de ce tutoriel. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/api-management/import-api-and-add-to-product/import_an_api_and_add_to_product.ps1 "Import an API")]

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour la Gestion des API Azure sur la page [Exemples PowerShell](../powershell-samples.md).
