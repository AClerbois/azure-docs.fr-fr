---
title: Créer une table d’itinéraires de hub virtuel Azure Virtual WAN pour diriger vers une appliance réseau virtuelle | Microsoft Docs
description: Table d’itinéraires de hub virtuel Azure Virtual WAN pour diriger le trafic vers une appliance réseau virtuelle.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 56321c7c2239c0f1f0e73126d90a521e3cafb853
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984189"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Créer une table d’itinéraires de hub virtuel pour diriger le trafic vers une appliance réseau virtuelle

Dans cet article, découvrez comment diriger le trafic à partir d’un hub virtuel vers une appliance réseau virtuelle. 

![Diagramme WAN virtuel](./media/virtual-wan-route-table/vwanroute.png)

Cet article porte sur les points suivants :

* Créer un WAN
* Créer un hub
* Créer des connexions de réseau virtuel du hub
* Créer un itinéraire de hub
* Créer une table de routage
* Appliquer la table d’itinéraires

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous respectez les critères suivants :

1. Vous disposez d’une appliance réseau virtuelle (soit le logiciel tiers de votre choix) qui est approvisionnée à partir de la Place de marché Azure (Lien) dans un réseau virtuel.
2. Vous disposez d’une adresse IP privée assignée à l’interface de l’appliance réseau virtuelle. 
3. L’appliance réseau virtuelle ne peut pas être déployée dans le hub virtuel. Elle doit être déployée dans un réseau virtuel distinct. Dans cet article, le réseau virtuel est appelé « DMZ VNet ».
4. Un ou plusieurs réseaux virtuels peuvent être connectés au réseau « DMZ VNet ». Dans cet article, ce réseau virtuel est appelé « Indirect spoke VNet ». Ces réseaux virtuels peuvent être connectés au réseau DMZ VNet à l’aide de l’homologation.
5. Vérifiez que les deux réseaux virtuels sont créés. Ils seront utilisés en tant que réseaux virtuels spokes. Dans cet article, le réseau virtuel spoke bénéficie des espaces d’adressage 10.0.2.0/24 et 10.0.3.0/24. Si vous avez besoin d’informations sur la création d’un réseau virtuel, consultez l’article [Créer un réseau virtuel à l’aide de PowerShell](../virtual-network/quick-create-powershell.md).
6. Veillez à ce qu’aucune passerelle de réseau virtuel n’existe dans les réseaux virtuels.

## <a name="signin"></a>1. Se connecter

Assurez-vous d’avoir installé la dernière version des cmdlets PowerShell de Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). Ceci est important, car les versions antérieures des cmdlets ne contiennent pas les valeurs actuelles dont vous avez besoin pour cet exercice.

1. Ouvrez la console PowerShell avec des privilèges élevés, puis connectez-vous à votre compte Azure. Ce cmdlet vous invite à entrer vos informations d’identification. Une fois que vous êtes connecté, vos paramètres de compte sont téléchargés afin de les mettre à disposition d’Azure PowerShell.

  ```powershell
  Connect-AzureRmAccount
  ```
2. Obtenez la liste de vos abonnements Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Spécifiez l’abonnement à utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```

## <a name="rg"></a>2. Créer des ressources

1. Créez un groupe de ressources.

  ```powershell
  New-AzureRmResourceGroup -Location "West US" -Name "testRG"
  ```
2. Créez un WAN virtuel.

  ```powershell
  $virtualWan = New-AzureRmVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
  ```
3. Créez un hub virtuel.

  ```powershell
  New-AzureRmVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
  ```

## <a name="connections"></a>3. Créer des connexions

Créez des connexions de réseau virtuel du hub à partir des réseaux Indirect Spoke VNet et DMZ VNet vers le hub virtuel.

  ```powershell
  $remoteVirtualNetwork1= Get-AzureRmVirtualNetwork -Name “indirectspoke1” -ResourceGroupName “testRG”
  $remoteVirtualNetwork2= Get-AzureRmVirtualNetwork -Name “indirectspoke2” -ResourceGroupName “testRG”
  $remoteVirtualNetwork3= Get-AzureRmVirtualNetwork -Name “dmzvnet” -ResourceGroupName “testRG”

  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection1” -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection2” -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection3” -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. Créer un itinéraire de hub virtuel

Dans cet article, le réseau Indirect Spoke VNet bénéficie des espaces d’adressage 10.0.2.0/24 et 10.0.3.0/24 et l’adresse IP privée de l’interface du réseau DMZ VNet est 10.0.4.5.

```powershell
$route1 = New-AzureRmVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. Créer une table d’itinéraires de hub virtuel

Créez une table d’itinéraires de hub virtuel, puis appliquez-y l’itinéraire créé.
 
```powershell
$routeTable = New-AzureRmVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. Valider les modifications

Validez les modifications pour le hub virtuel.

```powershell
Set-AzureRmVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub” -RouteTable $routeTable
```

## <a name="cleanup"></a>Supprimer des ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).