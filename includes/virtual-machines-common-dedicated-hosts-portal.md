---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 262880997c6b065dc5293a18d9a07c52ac836f37
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73590719"
---
> [!IMPORTANT]
> Les hôtes dédiés Azure sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitations connues de la préversion**
> - Actuellement, les hôtes dédiés ne prennent pas en charge les groupes de machines virtuelles identiques.
> - La préversion initiale prend en charge les séries de machines virtuelles suivantes : DSv3 et ESv3. 


## <a name="create-a-host-group"></a>Créer un groupe hôte

Un **groupe hôte** est une nouvelle ressource qui représente une collection d’hôtes dédiés. Vous créez un groupe hôte dans une région et une zone de disponibilité, et lui ajoutez des hôtes. Lors de la planification de la haute disponibilité, vous pouvez accéder à des options supplémentaires. Vous pouvez utiliser l’une des options suivantes, ou les deux, avec vos hôtes dédiés : 
- Application sur plusieurs zones de disponibilité. Dans ce cas, vous devez disposer d’un groupe hôte dans chacune des zones que vous souhaitez utiliser.
- Application sur plusieurs domaines d’erreur mappés à des racks physiques. 
 
Dans les deux cas, vous devez fournir le nombre de domaines d’erreur pour votre groupe hôte. Si vous ne souhaitez pas appliquer plusieurs domaines d’erreur dans votre groupe, utilisez un seul domaine d’erreur. 

Vous pouvez également choisir d’utiliser des zones de disponibilité et des domaines d’erreur. 

Dans cet exemple, nous allons créer un groupe hôte en utilisant 1 zone de disponibilité et 2 domaines d’erreur. 


1. Ouvrez le [Portail Microsoft Azure](https://portal.azure.com).
1. Sélectionnez l’option **Créer une ressource**, en haut à gauche du portail.
1. Recherchez des **groupes hôtes** et sélectionnez **Host Groups (preview)** (Groupes hôtes (préversion)) dans les résultats.

    ![Résultats d’une recherche de groupes hôtes.](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. Dans la page **Host Groups (preview)** (Groupes hôtes (préversion)), sélectionnez **Créer**.
1. Sélectionnez l’abonnement que vous souhaitez utiliser, puis sélectionnez **Création** pour créer un groupe de ressources.
1. Indiquez *myDedicatedHostsRG* dans la zone **Nom**, puis sélectionnez **OK**.
1. Dans **Host Group Name** (Nom du groupe hôte), saisissez *myHostGroup*.
1. Pour **Emplacement**, sélectionnez **USA Est**.
1. Pour **Zone de disponibilité**, sélectionnez **1**.
1. Pour **Fault Domain Count** (Nombre de domaines d’erreur), sélectionnez **2**.
1. Sélectionnez **Vérifier + Créer**, puis attendez la validation.

    ![Paramètres des groupes hôtes](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer** pour créer le groupe hôte.

Cette opération ne prend que quelques instants.

## <a name="create-a-dedicated-host"></a>Créer un hôte dédié

Créez maintenant un hôte dédié dans le groupe hôte. En plus d’un nom pour l’hôte, vous devez fournir la référence SKU pour l’hôte. La référence SKU de l’hôte capture la série de machines virtuelles prises en charge, ainsi que la génération du matériel pour l’hôte dédié.  Lors de la préversion, nous allons prendre en charge les valeurs de SKU d’hôte suivantes : DSv3_Type1 et ESv3_Type1.

Pour en savoir plus sur les références SKU et la tarification des hôtes, consultez la section relative à la [tarification des hôtes dédiés Azure](https://aka.ms/ADHPricing).

Si vous définissez un nombre de domaines d’erreur pour votre groupe hôte, vous êtes invité à spécifier le domaine d’erreur de votre hôte.  

1. Sélectionnez l’option **Créer une ressource**, en haut à gauche du portail.
1. Recherchez un **hôte dédié** et sélectionnez **Dedicated Hosts (preview)** (Hôtes dédiés (préversion)) dans les résultats.

    ![Résultats d’une recherche de groupes hôtes.](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. Dans la page **Dedicated Hosts (preview)** (Hôtes dédiés ((préversion)), sélectionnez **Créer**.
1. Sélectionnez l’abonnement à utiliser.
1. Sélectionnez *myDedicatedHostsRG* parmi les **groupes de ressources**.
1. Dans **Détails de l’instance**, saisissez *myHost* dans la zone **Nom** et sélectionnez l’emplacement *USA Est*.
1. Dans **Hardware Profile** (Profil matériel), sélectionnez *Standard Es3 family – Type 1* (Famille ES3 standard – Type 1) pour **Size Family** (Famille de tailles), *myHostGroup* pour **Host Group** (Groupe hôte) et *1* pour **Fault Domain** (Domaine d’erreur). Conservez les valeurs par défaut pour les autres champs.
1. Cela fait, sélectionnez **Vérifier + Créer** et attendez la validation.

    ![Paramètres d’hôte](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer** pour créer l’hôte.


