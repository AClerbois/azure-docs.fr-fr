---
title: Gérer des machines virtuelles SQL Server dans Azure à partir du portail Azure | Microsoft Docs
description: Découvrez comment accéder à la ressource Machine virtuelle SQL dans le portail Azure pour une machine virtuelle SQL Server hébergée sur Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 398eea4b968bb77017415e1dc259004c697b8dda
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846171"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Gérer des machines virtuelles SQL Server dans Azure à partir du portail Azure

Sur le [portail Azure](https://portal.azure.com), la ressource **Machines virtuelles SQL** est un service de gestion indépendant. Vous pouvez vous en servir pour voir toutes vos machines virtuelles SQL Server simultanément et modifier les paramètres dédiés à SQL Server : 

![Ressource Machines virtuelles SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Remarques

- Nous vous recommandons d’utiliser la ressource **Machines virtuelles SQL** pour voir et gérer vos machines virtuelles SQL Server dans Azure. Mais pour l’heure, la ressource **Machines virtuelles SQL** ne prend pas en charge la gestion des machines virtuelles SQL Server [en fin de support](virtual-machines-windows-sql-server-2008-eos-extend-support.md). Pour gérer les paramètres de vos machines virtuelles SQL Server en fin de support, utilisez plutôt l’[onglet Configuration de SQL Server](#access-the-sql-server-configuration-tab) déprécié. 
- La ressource **Machines virtuelles SQL** est disponible uniquement pour les machines virtuelles SQL Server qui ont été [inscrites auprès du fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Accéder à la ressource Machines virtuelles SQL
Pour accéder à la ressource **Machines virtuelles SQL**, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com). 
1. Sélectionnez **Tous les services**. 
1. Entrez **machines virtuelles SQL** dans la zone de recherche.
1. (Facultatif) : Sélectionnez l’étoile en regard de **Machines virtuelles SQL** pour ajouter cette option à votre menu **Favoris**. 
1. Sélectionnez **Machines virtuelles SQL**. 

   ![Rechercher les machines virtuelles SQL Server dans tous les services](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Le portail liste toutes les machines virtuelles SQL Server disponibles dans l’abonnement. Sélectionnez celle que vous voulez gérer pour ouvrir la ressource **Machines virtuelles SQL**. Utilisez la zone de recherche si votre machine virtuelle SQL Server n’apparaît pas. 

   ![Toutes les machines virtuelles SQL Server disponibles](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   La sélection de votre machine virtuelle SQL Server ouvre la ressource **Machines virtuelles SQL** : 


   ![Ressource Machines virtuelles SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> La ressource **Machines virtuelles SQL** est destinée aux paramètres SQL Server dédiés. Sélectionnez le nom de la machine virtuelle dans la zone **Machine virtuelle** pour ouvrir les paramètres qui sont propres à la machine virtuelle, mais pas exclusifs à SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Accéder à l’onglet Configuration de SQL Server
L’onglet **Configuration de SQL Server** est désormais déconseillé. Pour l’heure, il s’agit de la seule méthode permettant de gérer les machines virtuelles SQL Server [en fin de support](virtual-machines-windows-sql-server-2008-eos-extend-support.md) et celles qui n’ont pas été [inscrites auprès du fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md).

Pour accéder à l’onglet déconseillé **Configuration de SQL Server**, accédez à la ressource **Machines virtuelles**. Procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com). 
1. Sélectionnez **Tous les services**. 
1. Entrez **machines virtuelles** dans la zone de recherche.
1. (Facultatif) : Sélectionnez l’étoile en regard de **Machines virtuelles** pour ajouter cette option à votre menu **Favoris**. 
1. Sélectionnez **Machines virtuelles**. 

   ![Rechercher des machines virtuelles](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Le portail liste toutes les machines virtuelles de l’abonnement. Sélectionnez celle que vous voulez gérer pour ouvrir la ressource **Machines virtuelles**. Utilisez la zone de recherche si votre machine virtuelle SQL Server n’apparaît pas. 
1. Sélectionnez **Configuration de SQL Server** dans le volet **Paramètres** pour gérer votre machine virtuelle SQL Server. 

   ![Configuration de SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication pour SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


