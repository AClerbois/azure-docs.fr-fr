---
title: Exemple PowerShell - Créer une instance managée dans Azure SQL Database | Microsoft Docs
description: Exemple de script Azure PowerShell afin de créer une instance managée dans Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 32b3c3b45c627d8dfdb42642228a7f9d9786111e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569964"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Utiliser PowerShell pour créer une instance managée Azure SQL Database

Cet exemple de script PowerShell crée une instance managée Azure SQL Database dans un sous-réseau dédié au sein d’un réseau virtuel. Il configure également une table de routage et un groupe de sécurité réseau pour le réseau virtuel. Une fois le script correctement exécuté, l’instance managée est accessible à partir du réseau virtuel ou d’un environnement local. Consultez [Configurer la machine virtuelle Azure pour qu’elle se connecte à une instance managée Azure SQL Database](../sql-database-managed-instance-configure-vm.md) et [Configurer une connexion point à site à une instance managée Azure SQL Database à partir d’un emplacement local](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Pour connaître les limitations, consultez les [régions prises en charge](../sql-database-managed-instance-resource-limits.md#supported-regions) et les [types d’abonnement pris en charge](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous avez besoin de la version 1.4.0 d’AZ PowerShell ou d’une version ultérieure pour suivre ce tutoriel. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="sample-script"></a>Exemple de script

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crée un réseau virtuel |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Ajoute une configuration de sous-réseau à un réseau virtuel |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Obtient un réseau virtuel dans un groupe de ressources |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Définit l’état visé d’un réseau virtuel |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Obtient un sous-réseau dans un réseau virtuel |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Configure l’état visé d’une configuration de sous-réseau dans un réseau virtuel |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Crée une table de routage |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Obtient les tables de routage |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Définit l’état visé d’une table de routage. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Crée une instance managée Azure SQL Database |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell dans [Scripts PowerShell Azure SQL Database](../sql-database-powershell-samples.md).
