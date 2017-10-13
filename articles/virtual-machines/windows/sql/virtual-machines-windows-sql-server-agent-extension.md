---
title: "Automatiser les tâches de gestion sur des machines virtuelles SQL (Resource Manager) | Microsoft Docs"
description: "Cette rubrique indique comment gérer l’extension Agent SQL Server, qui automatise certaines tâches d’administration SQL Server. Celles-ci incluent Sauvegarde automatisée, Mise à jour corrective automatisée et Azure Key Vault Integration. Cette rubrique utilise le mode de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7152d184bb6d1d4b81aeb47e2c7c9160ada36023
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatiser les tâches de gestion sur des machines virtuelles Azure avec l’extension SQL Server Agent (Resource Manager)
> [!div class="op_single_selector"]
> * [Gestionnaire de ressources](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classique](../classic/sql-server-agent-extension.md)
> 
> 

L’extension Agent IaaS SQL Server (SQLIaaSExtension) s’exécute sur les machines virtuelles Azure pour automatiser les tâches d’administration. Cette rubrique présente les services pris en charge par l’extension, ainsi que des instructions d’installation, d’état et de suppression.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Pour afficher la version de cet article pour un déploiement Classic, consultez [Extension Agent SQL Server pour machines virtuelles SQL Server (Classic)](../classic/sql-server-agent-extension.md).

## <a name="supported-services"></a>Services pris en charge
L’extension Agent IaaS SQL Server prend en charge les tâches d’administration suivantes :

| Fonction d’administration | Description |
| --- | --- |
| **Sauvegarde automatisée SQL** |Automatise la planification des sauvegardes de toutes les bases de données pour l’instance par défaut de SQL Server dans la machine virtuelle. Pour plus d’informations, consultez [Sauvegarde automatisée pour SQL Server dans Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Mise à jour corrective automatisée SQL** |Configure une fenêtre de maintenance pendant laquelle les mises à jour de votre machine virtuelle peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe de votre charge de travail. Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Intégration du coffre de clés Azure** |Permet d’installer et de configurer automatiquement Azure Key Vault sur votre machine virtuelle SQL Server. Pour plus d’informations, consultez [Configurer l’intégration du coffre de clés Azure SQL Server sur des machines virtuelles (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Une fois installée et en cours d’exécution, l’extension de l’agent IaaS SQL Server rend ces fonctionnalités d’administration disponibles sur le panneau SQL Server de la machine virtuelle dans le portail Azure et via Azure PowerShell pour les images de place de marché SQL Server, et via Azure PowerShell pour les installations manuelles de l’extension. 

## <a name="prerequisites"></a>Composants requis
Configuration requise pour utiliser l’extension Agent IaaS SQL Server sur votre machine virtuelle :

**Système d’exploitation**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versions de SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Télécharger et configurer les dernières commandes Azure PowerShell](/powershell/azure/overview)

## <a name="installation"></a>Installation
L’Extension Agent IaaS SQL Server s’installe automatiquement lorsque vous approvisionnez une des images de galerie de machine virtuelle SQL Server. Si vous avez besoin de réinstaller l’extension manuellement sur l’une de ces machines virtuelles SQL Server, utilisez la commande PowerShell suivante :

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

Il est également possible d’installer l’extension Agent IaaS SQL Server sur une machine virtuelle Windows Server. Cette opération est autorisée uniquement si vous avez installé manuellement SQL Server sur cette machine. Installez ensuite l’extension manuellement par le biais de l’applet de commande PowerShell **Set-AzureVMSqlServerExtension**.

> [!NOTE]
> Si vous installez manuellement l’extension Agent IaaS SQL Server sur une machine virtuelle Windows Server de type « système d’exploitation uniquement », vous ne pouvez pas gérer les paramètres de configuration de SQL Server sur le portail Azure. Dans ce cas, vous devez utiliser PowerShell pour apporter toutes vos modifications.

## <a name="status"></a>État
Pour vérifier que l’extension est installée, un moyen consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez **All settings (Tous les paramètres)** dans le panneau de la machine virtuelle, puis cliquez sur **Extensions**. L’extension **SQLIaaSExtension** doit s’afficher.

![Extension Agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Vous pouvez également utiliser l’applet de commande Azure PowerShell **Get-AzureVMSqlServerExtension** .

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

La commande précédente confirme que l’agent est installé et fournit des informations générales sur son état. Vous pouvez également obtenir des informations d’état sur Sauvegarde automatisée et Mise à jour corrective automatisée, grâce aux commandes suivantes.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Suppression
Dans le portail Azure, vous pouvez désinstaller l’extension en cliquant sur le bouton de sélection dans le panneau **Extensions** des propriétés de votre machine virtuelle. Ensuite, cliquez sur **Supprimer**.

![Désinstaller l’extension Agent IaaS SQL Server dans le portail Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser l’applet de commande PowerShell **Remove-AzureRmVMSqlServerExtension** .

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Étapes suivantes
Commencez par utiliser l’un des services pris en charge par l’extension. Pour plus d’informations, consultez les rubriques référencées dans la section [Services pris en charge](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

