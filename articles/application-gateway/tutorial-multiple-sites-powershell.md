---
title: Créer une passerelle d’application qui héberge plusieurs sites web - Azure PowerShell
description: Découvrez comment créer une passerelle d’application qui héberge plusieurs sites web à l’aide d’Azure Powershell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/13/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7d376ab958a1fb56753e033129ece84bc1c439c2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044694"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-azure-powershell"></a>Créer une passerelle d’application qui héberge plusieurs sites web à l’aide d’Azure PowerShell

Vous pouvez utiliser Azure PowerShell pour [configurer l’hébergement de plusieurs sites web](multiple-site-overview.md) quand vous créez une [passerelle d’application](overview.md). Dans ce tutoriel, vous définissez des pools d’adresses principaux à l’aide de groupes de machines virtuelles identiques. Vous configurez ensuite des écouteurs et des règles en fonction des domaines qui vous appartiennent pour vérifier que le trafic web arrive sur les serveurs appropriés dans les pools. Ce didacticiel, qui part du principe que vous avez plusieurs domaines, utilise *www.contoso.com* et *www.fabrikam.com* en guise d’exemples.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer une passerelle Application Gateway
> * Créer des écouteurs principaux
> * Créer des règles d’acheminement
> * Créer des groupes de machines virtuelles identiques avec les pools principaux
> * Créer un enregistrement CNAME dans votre domaine

![Exemple de routage multisite](./media/tutorial-multiple-sites-powershell/scenario.png)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou version ultérieure pour les besoins de ce didacticiel. Pour trouver la version, exécutez ` Get-Module -ListAvailable AzureRM`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources Azure à l’aide de [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau

Créez la configuration de sous-réseau à l’aide de [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Créez le réseau virtuel à l’aide de [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) avec les configurations de sous-réseau. Enfin, créez l’adresse IP publique à l’aide de [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Ces ressources sont utilisées pour fournir la connectivité réseau à la passerelle d’application et à ses ressources associées.

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Créer les configurations IP et le port frontal

Associez le sous-réseau que vous avez créé précédemment à la passerelle d’application à l’aide de [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Assignez l’adresse IP publique à la passerelle d’application à l’aide de [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pools-and-settings"></a>Créer les pools backend et les paramètres

Créez le premier pool d’adresses principal pour la passerelle d’application à l’aide de [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Configurez les paramètres pour le pool à l’aide de [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$contosoPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name contosoPool

$fabrikamPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name fabrikamPool

$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listeners-and-rules"></a>Créer les écouteurs et les règles

Des écouteurs sont requis pour permettre à la passerelle d’application d’acheminer le trafic de manière appropriée vers les pools d’adresses principaux. Ce didacticiel vous montre comment créer deux écouteurs pour vos deux domaines. Dans cet exemple, des écouteurs sont créés pour les domaines *www.contoso.com* et *www.fabrikam.com*.

Créez le premier écouteur à l’aide de [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) avec la configuration et le port du serveur frontal que vous avez créé précédemment. Une règle est requise pour que l’écouteur sache quel pool principal utiliser pour le trafic entrant. Créez une règle de base nommée *contosoRule* à l’aide de l’applet de commande [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$contosolistener = New-AzureRmApplicationGatewayHttpListener `
  -Name contosoListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.contoso.com"

$fabrikamlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name fabrikamListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.fabrikam.com"

$contosoRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name contosoRule `
  -RuleType Basic `
  -HttpListener $contosoListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings

$fabrikamRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name fabrikamRule `
  -RuleType Basic `
  -HttpListener $fabrikamListener `
  -BackendAddressPool $fabrikamPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Maintenant que vous avez créé les ressources nécessaires pour la prise en charge, spécifiez des paramètres de la passerelle d’application à l’aide de [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku), puis créez-la à l’aide de [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

$appgw = New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $contosoPool, $fabrikamPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $contosoListener, $fabrikamListener `
  -RequestRoutingRules $contosoRule, $fabrikamRule `
  -Sku $sku
```

## <a name="create-virtual-machine-scale-sets"></a>Créer des groupes de machines virtuelles identiques

Dans cet exemple, vous créez deux groupes de machines virtuelles identiques prenant en charge les deux pools backend créés. Ces groupes identiques sont nommés *myvmss1* et *myvmss2*. Chacun contient deux instances de machines virtuelles sur lesquelles IIS sera installé. Vous assignez le groupe identique au pool principal lorsque vous configurez les paramètres IP.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$contosoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name contosoPool `
  -ApplicationGateway $appgw

$fabrikamPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name fabrikamPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 2; $i++)
{
  if ($i -eq 1) 
  {
    $poolId = $contosoPool.Id
  }
  if ($i -eq 2)
  {
    $poolId = $fabrikamPool.Id
  }

  $ipConfig = New-AzureRmVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzureRmVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzureRmVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
    -OsDiskCreateOption FromImage

  Set-AzureRmVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzureRmVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Installer IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 2; $i++)
{
  $vmss = Get-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -VMScaleSetName myvmss$i

  Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="create-cname-record-in-your-domain"></a>Créer un enregistrement CNAME dans votre domaine

Une fois la passerelle d’application créée avec son adresse IP publique, vous pouvez obtenir l’adresse DNS et l’utiliser pour créer un enregistrement CNAME dans votre domaine. Vous pouvez utiliser [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour obtenir l’adresse DNS de la passerelle d’application. Copiez la valeur *fqdn* de DNSSettings et utilisez-la comme valeur de l’enregistrement CNAME que vous créez. L’utilisation d’enregistrements A n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer au moment du redémarrage de la passerelle d’application.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Entrez votre nom de domaine dans la barre d’adresse de votre navigateur. Par exemple, http://www.contoso.com.

![Tester le site contoso dans la passerelle d’application](./media/tutorial-multiple-sites-powershell/application-gateway-iistest.png)

Remplacez l’adresse par celle de votre autre domaine. Voici ce qui doit apparaître :

![Tester le site fabrikam dans la passerelle d’application](./media/tutorial-multiple-sites-powershell/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la passerelle d’application et toutes les ressources associées à l’aide de [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer une passerelle Application Gateway
> * Créer des écouteurs principaux
> * Créer des règles d’acheminement
> * Créer des groupes de machines virtuelles identiques avec les pools principaux
> * Créer un enregistrement CNAME dans votre domaine

> [!div class="nextstepaction"]
> [Créer une passerelle d’application avec des règles d’acheminement par chemin d’URL](./tutorial-url-route-powershell.md)