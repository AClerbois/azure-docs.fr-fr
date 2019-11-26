---
title: 'Virtual WAN : Créer une table d’itinéraires de hub virtuel sur des appliances réseau virtuelles : Portail Azure'
description: Table de routage de hub virtuel Azure Virtual WAN pour diriger le trafic vers une appliance réseau virtuelle à l’aide du portail.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8f24b94226daffb769993c9f6659909fdff039b6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014978"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Créer une table de routage de hub Virtual WAN pour des appliances réseau virtuelles : Portail Azure

Dans cet article, découvrez comment diriger le trafic à partir d’un hub vers une appliance réseau virtuelle (NVA).

![Diagramme WAN virtuel](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous respectez les critères suivants :

*  Vous avez une appliance réseau virtuelle (NVA). Une appliance réseau virtuelle est un logiciel tiers de votre choix qui est généralement provisionné à partir de la Place de marché Azure dans un réseau virtuel.

    * Une adresse IP privée doit être attribuée à l’interface réseau de l’appliance réseau virtuelle.

    * L’appliance réseau virtuelle n’est pas déployée dans le hub virtuel. Elle doit être déployée dans un réseau virtuel distinct.

    *  Le réseau virtuel de l’appliance réseau virtuelle peut avoir un ou plusieurs réseaux virtuels qui y sont connectés. Dans cet article, nous désignons le réseau virtuel de l’appliance réseau virtuelle par le terme « réseau virtuel spoke indirect ». Ces réseaux virtuels peuvent être connectés au réseau virtuel de l’appliance réseau virtuelle à l’aide de VNet Peering.
*  Vous avez créé deux réseaux virtuels. Ils seront utilisés en tant que réseaux virtuels spoke.

    * Dans cet exercice, les réseaux virtuels spoke ont les espaces d’adressage suivants : VNet1 : 10.0.2.0/24 et VNet2 : 10.0.3.0/24. Pour plus d’informations sur la création d’un réseau virtuel, consultez [Créer un réseau virtuel](../virtual-network/quick-create-portal.md).

    * Assurez-vous qu’aucune passerelle de réseau virtuel n’existe dans les réseaux virtuels.
    * Pour cette configuration, ces réseaux virtuels ne nécessitent pas de sous-réseau de passerelle.

## <a name="signin"></a>1. Se connecter

Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

## <a name="vwan"></a>2. Créer un WAN virtuel

Créez un WAN virtuel. Dans le cadre de cet exercice, vous pouvez utiliser les valeurs suivantes :

* **Nom du WAN virtuel :** monWANvirtuel
* **Groupe de ressources :** testRG
* **Emplacement :** USA Ouest

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Créer un hub

Créez le hub. Dans le cadre de cet exercice, vous pouvez utiliser les valeurs suivantes :

* **Emplacement :** USA Ouest
* **Nom :** westushub
* **Espace d’adressage privé du hub :** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Créer et implémenter une table de routage de hub

Mettez à jour le hub avec une table de routage de hub. Dans le cadre de cet exercice, vous pouvez utiliser les valeurs suivantes :

* **Espaces d’adressage du réseau virtuel spoke indirect :** (VNet1 et VNet2) 10.0.2.0/24 et 10.0.3.0/24
* **Adresses IP privées pour l’interface réseau de l’appliance réseau virtuelle DMZ :** 10.0.4.5

1. Accédez à votre WAN virtuel.
2. Cliquez sur le hub pour lequel vous souhaitez créer une table de routage.
3. Cliquez sur **...** , puis cliquez sur **Modifier le hub virtuel**.
4. Dans la page **Modifier le hub virtuel**, faites défiler le contenu vers le bas et cochez la case **Utiliser la table pour le routage**.
5. Dans la colonne **Si le préfixe de destination est**, ajoutez les espaces d’adressage. Dans la colonne **Envoyer à l'adresse du tronçon suivant**, ajoutez l’adresse IP privée pour l’interface réseau de l’appliance réseau virtuelle DMZ.
6. Cliquez sur **Confirmer** pour mettre à jour la ressource hub avec les paramètres de la table de routage.

## <a name="connections"></a>5. Créer les connexions de réseau virtuel

Créez une connexion au hub à partir de chaque réseau virtuel spoke indirect (VNet1 et VNet2). Ensuite, créez une connexion au hub à partir du réseau virtuel de l’appliance réseau virtuelle.

 Pour cette étape, vous pouvez utiliser les valeurs suivantes :

| Nom du réseau virtuel| Nom de connexion|
| --- | --- |
| VNet1 | testconnexion1 |
| VNet2 | testconnexion2 |
| NVAVNet | testconnexion3 |

Répétez la procédure suivante pour chaque réseau virtuel que vous souhaitez connecter.

1. Sur la page de votre WAN virtuel, cliquez sur **Connexion de réseau virtuel**.
2. Dans la page de connexion de réseau virtuel, cliquez sur **+ Ajouter une connexion**.
3. Dans la page **Ajouter une connexion**, renseignez les champs suivants :

    * **Nom de connexion** : nommez votre connexion.
    * **Hubs** : sélectionnez le hub que vous souhaitez associer à cette connexion.
    * **Abonnement** : vérifiez l’abonnement.
    * **Réseau virtuel** : sélectionnez le réseau virtuel que vous souhaitez connecter à ce hub. Le réseau virtuel ne peut pas avoir une passerelle de réseau virtuel déjà existante.
4. Cliquez sur **OK** pour créer la connexion.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).