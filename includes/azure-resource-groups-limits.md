---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 25928ef35da1ce4b3824303a5d46749c32aa701f
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626329"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Ressources par [groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), par type de ressource |800 |Certains types de ressources peuvent dépasser la limite de 800. Voir la section relative aux [ressources non limitées à 800 instances par groupe de ressources](../articles/azure-resource-manager/resources-without-rg-limit.md). |
| Déploiements par groupe de ressources dans l’historique des déploiements |800<sup>1</sup> |800 |
| Ressources par déploiement |800 |800 |
| Verrous de gestion par étendue unique |20 |20 |
| Nombre de balises par ressource ou groupe de ressources |50 |50 |
| Longueur de clé de la balise |512 |512 |
| Longueur de valeur de la balise |256 |256 |

<sup>1</sup>Si vous atteignez la limite des 800 déploiements par groupe de ressources, supprimez les déploiements inutiles dans l’historique. La suppression d’une entrée à partir de l’historique des déploiements n’affecte pas les ressources déployées. Vous pouvez supprimer des entrées de l'historique avec la commande [az group deployment delete](/cli/azure/group/deployment) dans Azure CLI ou la commande [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) dans PowerShell.  Pour un script PowerShell script qui automatise la suppression de déploiements dans un scénario d’intégration continue (CI) et de livraison continue (CD), consultez [remove-deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Limites de modèle

| Valeur | Limite par défaut | Limite maximale |
| --- | --- | --- |
| parameters |256 |256 |
| variables |256 |256 |
| Ressources (incluant le nombre de copies) |800 |800 |
| Outputs |64 |64 |
| Expression de modèle |24 576 caractères |24 576 caractères |
| Ressources dans les modèles exportés |200 |200 | 
| Taille du modèle |4 Mo |4 Mo |
| Taille du fichier de paramètres |64 Ko |64 Ko |

Vous pouvez dépasser certaines limites de modèle en utilisant un modèle imbriqué. Pour plus d’informations, consultez l’article [Utilisation de modèles liés lors du déploiement des ressources Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Pour réduire le nombre de paramètres, de variables ou de sorties, vous pouvez combiner plusieurs valeurs dans un même objet. Pour plus d’informations, consultez l’article [Objects as parameters](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md) (Utiliser un objet en tant que paramètre).
