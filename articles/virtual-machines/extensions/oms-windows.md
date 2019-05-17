---
title: Extension de machine virtuelle Azure Monitor pour Windows | Microsoft Docs
description: Déployez l’agent Log Analytics sur une machine virtuelle Windows avec une extension de machine virtuelle.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/29/2019
ms.author: roiyz
ms.openlocfilehash: 270b3ae49a815c9e12fce9377c8298192237f28a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790367"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Extension de machine virtuelle Azure Monitor pour Windows

Journaux d’Azure Monitor fournit des fonctionnalités de surveillance entre les ressources de cloud et locales. L’extension de machine virtuelle de l’agent Log Analytics pour Windows est publiée et prise en charge par Microsoft. L’extension installe l’agent Log Analytics sur les machines virtuelles Azure et inscrit les machines virtuelles dans un espace de travail Log Analytics existant. Ce document décrit en détail les plateformes prises en charge, les configurations et les options de déploiement pour l’extension de machine virtuelle Azure Monitor pour Windows.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Conditions préalables

### <a name="operating-system"></a>Système d’exploitation

L’extension de l’agent d’Analytique de journal pour Windows prend en charge suivants des versions du système d’exploitation Windows :

- Windows Server 2019
- Windows Server 2008 R2, 2012, 2012 R2, 2016, version 1709 et 1803

### <a name="agent-and-vm-extension-version"></a>Version de l’agent et de l’extension de machine virtuelle
Le tableau suivant fournit un mappage de la version de l’extension de machine virtuelle de Azure Monitor et le bundle de l’agent Analytique de journal pour chaque version. 

| Version d’extension de machine virtuelle Linux de moniteur Azure | Version du bundle de l’Agent Log Analytics | Date de mise en production | Notes de publication |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 8.0.11049.0 | 1.0.11049.1 | Février 2017 | |
| 8.0.11072.0 | 1.0.11072.1 | Septembre 2017 | |
| 8.0.11081.0 | 1.0.11081.5 | Novembre 2017 | | 
| 8.0.11103.0 | n/a |  Avril 2018 | |
| 8.0.11136.0 | n/a | Septembre 2018 |  <ul><li> Prise en charge pour la détection de changement d’ID de ressource sur le déplacement de la machine virtuelle </li><li> Prise en charge ajoutée pour installer ID lors de l’utilisation sans extension de ressource des rapports </li></ul>| 
| 10.19.10006.0 | n/a | Décembre 2018 | <ul><li> Correctifs de stabilisation mineurs </li></ul> | 
| 10.19.13515.0 | 1.0.13515.1 | Mars 2019 | <ul><li>Correctifs de stabilisation mineurs </li></ul> |

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center approvisionne l’agent Log Analytique automatiquement et se connecte avec l’espace de travail Analytique de journal par défaut de l’abonnement Azure. Si vous utilisez Azure Security Center, ne suivez pas la procédure de ce document. Si vous le faites, vous écrasez l’espace de travail configuré et interrompez la connexion à Azure Security Center.

### <a name="internet-connectivity"></a>Connectivité Internet
L’extension de l’agent Log Analytics pour Windows nécessite que la machine virtuelle cible soit connectée à Internet. 

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant illustre le schéma de l’extension d’agent Log Analytics. L’extension nécessite l’ID de l’espace de travail et de la clé d’espace de travail à partir de l’espace de travail Analytique de journal. Vous les trouverez dans les paramètres de l’espace de travail dans le portail Azure. La clé de l’espace de travail devant être traitée comme une donnée sensible, elle est stockée dans une configuration protégée. Les données du paramètre de protection de l’extension de machine virtuelle Azure sont chiffrées et ne sont déchiffrées que sur la machine virtuelle cible. Notez que **workspaceId** et **workspaceKey** respectent la casse.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Valeurs de propriétés

| Nom | Valeur/Exemple |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (par exemple)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (par exemple) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* La propriété workspaceId est appelée consumerId dans l’API Log Analytics.

## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Le schéma JSON détaillé dans la section précédente peut être utilisé dans un modèle Azure Resource Manager pour exécuter l’extension d’agent Log Analytics pendant le déploiement d’un modèle Azure Resource Manager. Vous trouverez un exemple de modèle qui inclut l’extension de machine virtuelle d’agent Log Analytics dans la [Galerie de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Le modèle ne prend pas en charge la spécification de plusieurs ID d’espace de travail et de la clé de l’espace de travail lorsque vous souhaitez configurer l’agent pour signaler à plusieurs espaces de travail. Pour configurer l’agent pour signaler à plusieurs espaces de travail, consultez [Ajout ou suppression d’un espace de travail](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

Le code JSON pour une extension de machine virtuelle peut être imbriqué à l’intérieur de la ressource de machine virtuelle ou placé à la racine ou au niveau supérieur d’un modèle de Resource Manager JSON. Le positionnement du JSON affecte la valeur du nom de la ressource et son type. Pour plus d’informations, consultez [Définition du nom et du type des ressources enfants](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

L’exemple suivant suppose que l’extension d’Azure Monitor est imbriquée dans la ressource de machine virtuelle. Lors de l’imbrication de la ressource d’extension, le JSON est placé dans l’objet `"resources": []` de la machine virtuelle.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Lorsque vous placez l’extension JSON à la racine du modèle, le nom de ressource inclut une référence à la machine virtuelle parente, et le type reflète la configuration imbriquée. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Déploiement PowerShell

Vous pouvez utiliser la commande `Set-AzVMExtension` pour déployer l’extension de machine virtuelle d’agent Log Analytics sur une machine virtuelle existante. Avant d’exécuter la commande, les configurations publique et privée doivent être stockées dans une table de hachage PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide du module Azure PowerShell. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante à l’aide du module PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

La sortie de l’exécution de l’extension est enregistrée dans les fichiers qui que se trouvent dans le répertoire suivant :

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
