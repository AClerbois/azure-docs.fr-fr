---
title: Ouvrir des ports sur une machine virtuelle à l’aide d’Azure PowerShell | Microsoft Docs
description: Découvrez comment ouvrir un port / créer un point de terminaison sur votre machine virtuelle Windows à l’aide du modèle de déploiement Azure Resource Manager et d’Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 2910882424326f5a09b00d31c0e0fedb45d1e5d8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720111"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Guide d’ouverture de ports et de points de terminaison sur une machine virtuelle dans Azure à l’aide de PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Commandes rapides
Pour créer un groupe de sécurité réseau et des règles de liste de contrôle d’accès, vous devez installer [la version la plus récente d’Azure PowerShell](/powershell/azureps-cmdlets-docs). Vous pouvez également [effectuer ces étapes à l’aide du Portail Azure](nsg-quickstart-portal.md).

Connectez-vous à votre compte Azure :

```powershell
Connect-AzAccount
```

Dans les exemples suivants, remplacez les noms de paramètres avec vos propres valeurs. Les exemples de noms de paramètre comprennent *myResourceGroup*, *myNetworkSecurityGroup* et *myVMnet*.

Créez une règle avec [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). L’exemple suivant crée une règle nommée *myNetworkSecurityGroupRule* pour autoriser le trafic *TCP* sur le port *80* :

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Ensuite, créez votre groupe de sécurité réseau avec [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) et attribuez la règle HTTP que vous venez de créer comme suit. L’exemple suivant crée un groupe de sécurité réseau nommé *myNetworkSecurityGroup* :

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Maintenant, il s’agit d’affecter votre groupe de sécurité réseau à un sous-réseau. L’exemple suivant affecte un réseau virtuel existant nommé *myVnet* à la variable *$vnet* avec [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) :

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associez votre groupe de sécurité réseau à votre sous-réseau avec [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). L’exemple suivant associe le sous-réseau nommé *mySubnet* à votre groupe de sécurité réseau :

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Enfin, mettez à jour votre réseau virtuel avec [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) pour que vos modifications prennent effet :

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>En savoir plus sur les groupes de sécurité réseau
Les commandes rapides vous permettent d’être opérationnel avec le trafic entrant vers votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Découvrez plus d’informations sur la [création d’un groupe de sécurité réseau et de règles de liste de contrôle d’accès ici](tutorial-virtual-network.md#secure-network-traffic).

Pour les applications Web hautement disponibles, vous devez placer vos machines virtuelles derrière un équilibreur de charge Azure. L’équilibreur de charge répartit le trafic entre les machines virtuelles, avec un groupe de sécurité réseau qui assure le filtrage du trafic. Pour plus d’informations, consultez [Guide pratique pour équilibrer la charge des machines virtuelles Linux dans Azure pour créer une application hautement disponible](tutorial-load-balancer.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :

* [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Présentation du groupe de sécurité réseau](../../virtual-network/security-overview.md)
* [Présentation d’Azure Resource Manager](../../load-balancer/load-balancer-arm.md)

