---
title: Commandes PowerShell courantes pour les réseaux virtuels Azure | Microsoft Docs
description: Il s’agit de commandes PowerShell courantes, qui vous aident à démarrer la création d’un réseau virtuel et des ressources associées pour des machines virtuelles.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 31a0d486f2540ea75a57b29b8f1da21839783468
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984633"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Commandes PowerShell courantes pour les réseaux virtuels Azure

Pour créer une machine virtuelle, vous devez d’abord créer un [réseau virtuel](../../virtual-network/virtual-networks-overview.md) ou identifier un réseau virtuel existant, au sein duquel la machine virtuelle peut être ajoutée. En général, lorsque vous créez une machine virtuelle, vous devez également envisager la création des ressources décrites dans cet article.

Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

Certaines variables peuvent être utiles si vous utilisez plusieurs des commandes de cet article :

- $location : l’emplacement des ressources réseau. Vous pouvez utiliser [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) pour rechercher une [région géographique](https://azure.microsoft.com/regions/) qui vous convient.
- $myResourceGroup - le nom du groupe de ressources dans lequel les ressources réseau se trouvent.

## <a name="create-network-resources"></a>Créer des ressources réseau

| Tâche | Commande |
| ---- | ------- |
| Créez des configurations de sous-réseau |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Un réseau classique peut avoir un sous-réseau pour un [équilibrage de charge accessible sur Internet](../../load-balancer/load-balancer-internet-overview.md) et un sous-réseau distinct pour un [équilibrage de charge interne](../../load-balancer/load-balancer-internal-overview.md). |
| Créez un réseau virtuel |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Test d’un nom de domaine unique |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Vous pouvez spécifier un nom de domaine DNS pour une [ressource IP publique](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), qui crée un mappage pour l’élément domainname.location.cloudapp.azure.com vers l’adresse IP publique dans les serveurs DNS gérés par Azure. Le nom ne peut contenir que des lettres, des chiffres et des traits d’union. Le premier et le dernier caractère doivent correspondre à une lettre ou à un chiffre, et le nom de domaine doit être unique au sein de son emplacement Azure. Si la valeur **True** est renvoyée, cela signifie que le nom proposé est bien unique au niveau global. |
| Créer une adresse IP publique |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>L’adresse IP publique utilise le nom de domaine que vous avez testé. Elle est utilisée par la configuration de serveur frontal de l’équilibrage de charge. |
| Créer une configuration d’adresse IP frontale |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>La configuration du serveur frontal inclut l’adresse IP publique que vous avez créée pour le trafic réseau entrant. |
| Créer un pool d’adresses principal |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Fournit des adresses internes pour le serveur principal de l’équilibreur de charge, accessibles via une interface réseau. |
| Créer une sonde |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contient les sondes d’intégrité utilisées pour vérifier la disponibilité des instances de machine virtuelle dans le pool d’adresses du serveur principal. |
| Créer une règle d’équilibrage de charge |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contient des règles qui attribuent un port public de l’équilibrage de charge à un port du pool d’adresses principal. |
| Créer une règle NAT entrante |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contient des règles qui mappent un port public de l’équilibreur de charge à un port associé à une machine virtuelle spécifique dans le pool d’adresses du serveur principal. |
| Créer un équilibrage de charge |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Créer une interface réseau |$nic1= [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crée une interface réseau via l’adresse IP publique et le sous-réseau associé à un réseau virtuel que vous avez créé. |

## <a name="get-information-about-network-resources"></a>Obtenir des informations sur les ressources réseau

| Tâche | Commande |
| ---- | ------- |
| Répertorier les réseaux virtuels |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Répertorie tous les réseaux virtuels du groupe de ressources. |
| Obtenir des informations sur un réseau virtuel |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Répertorier les sous-réseaux d’un réseau virtuel |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Obtenir des informations sur un sous-réseau |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Obtient des informations sur le sous-réseau du réseau virtuel spécifié. La valeur $vnet représente l'objet renvoyé par Get-AzVirtualNetwork. |
| Répertorier des adresses IP |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Répertorie les adresses IP publiques du groupe de ressources. |
| Répertorier les équilibrages de charge |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Répertorie tous les équilibrages de charge du groupe de ressources. |
| Répertorier les interfaces réseau |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Répertorie toutes les interfaces réseau du groupe de ressources. |
| Obtenir des informations sur une interface réseau |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Obtient des informations sur une interface réseau spécifique. |
| Obtenir la configuration IP d’une interface réseau |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Obtient des informations sur la configuration IP de l’interface réseau spécifiée. La valeur $nic représente l'objet renvoyé par Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Gérer des ressources réseau

| Tâche | Commande |
| ---- | ------- |
| Ajouter un sous-réseau à un réseau virtuel |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Ajoute un sous-réseau à un réseau virtuel existant. La valeur $vnet représente l'objet renvoyé par Get-AzVirtualNetwork. |
| Supprimer un réseau virtuel |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Supprime le réseau virtuel spécifié du groupe de ressources. |
| Supprimer une interface réseau |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Supprime l’interface réseau spécifiée du groupe de ressources. |
| Suppression d’un équilibreur de charge |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Supprime l’équilibrage de charge spécifié du groupe de ressources. |
| Supprimer une adresse IP publique |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Supprime l’adresse IP publique spécifiée du groupe de ressources. |

## <a name="next-steps"></a>Étapes suivantes
* Utilisez l’interface réseau que vous avez créée en même temps que [la machine virtuelle](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Découvrez comment [créer une machine virtuelle avec plusieurs interfaces réseau](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

