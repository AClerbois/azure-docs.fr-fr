---
title: Acheminer le trafic web selon l’URL - Azure PowerShell
description: Découvrez comment acheminer le trafic web selon l’URL vers des pools évolutifs spécifiques de serveurs à l’aide d’Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cf89d814d6d46482c54d6991ba16b3050b882d05
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57993990"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Acheminer le trafic web selon l’URL à l’aide d’Azure PowerShell

Vous pouvez utiliser Azure PowerShell pour configurer l’acheminement du trafic web vers des pools de serveurs évolutifs spécifiques selon l’URL utilisée pour accéder à votre application. Dans ce tutoriel, vous créez une [passerelle d’application Azure](application-gateway-introduction.md) avec trois pools backend à l’aide de [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Chaque pool backend a un usage spécifique comme les données courantes, les images et la vidéo.  Avec l’acheminement du trafic vers des pools distincts, vos clients ont la garantie d’obtenir les informations dont ils ont besoin lorsqu’ils en ont besoin.

Pour activer l’acheminement du trafic, vous créez des [règles d’acheminement](application-gateway-url-route-overview.md) assignées à des écouteurs qui écoutent des ports spécifiques pour veiller à ce que le trafic web arrive sur les serveurs appropriés dans les pools.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer des écouteurs, un mappage de chemins d’URL et des règles
> * Créer des pools backend scalables

![Exemple d’acheminement d’URL](./media/tutorial-url-route-powershell/scenario.png)

Si vous préférez, vous pouvez suivre ce didacticiel en utilisant [Azure CLI](tutorial-url-route-cli.md) ou le [portail Azure](create-url-route-portal.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 1.0.0 ou version ultérieure pour les besoins de ce tutoriel. Pour trouver la version, exécutez ` Get-Module -ListAvailable Az`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzAccount` pour créer une connexion avec Azure.

En raison du temps nécessaire pour créer des ressources, ce tutoriel peut durer jusqu’à 90 minutes.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Vous devez créer un groupe de ressources qui contient toutes les ressources de votre application. 

Créez un groupe de ressources Azure à l’aide de [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau

Si vous disposez déjà d’un réseau virtuel ou si vous en créez un, vous devez vérifier qu’il contient un sous-réseau utilisé uniquement pour les passerelles d’application. Dans ce tutoriel, vous créez un sous-réseau pour la passerelle d’application et un sous-réseau pour les groupes identiques. Vous créez une adresse IP publique pour permettre l’accès aux ressources dans la passerelle d’application.

Créez les configurations de sous-réseau *myAGSubnet* et *myBackendSubnet* à l’aide de [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Créez le réseau virtuel nommé *myVNet* à l’aide de [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) avec les configurations de sous-réseau. Enfin, créez l’adresse IP publique nommée *myAGPublicIPAddress* à l’aide de [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Ces ressources sont utilisées pour fournir la connectivité réseau à la passerelle d’application et à ses ressources associées.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Dans cette section, vous créez des ressources qui prennent en charge la passerelle d’application avant de créer cette dernière. Les ressources que vous créez sont les suivantes :

- *Configurations IP et port frontend* : associe le sous-réseau que vous avez créé précédemment pour la passerelle d’application et assigne un port à utiliser pour y accéder.
- *Pool par défaut* : toutes les passerelles d’application doivent avoir au moins un pool principal de serveurs.
- *Écouteur et règle par défaut* : l’écouteur par défaut écoute le trafic sur le port assigné et la règle par défaut envoie le trafic au pool par défaut.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Créer les configurations IP et le port frontal

Associez *myAGSubnet* créé précédemment à la passerelle d’application à l’aide de [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Affectez *myAGPublicIPAddress* à la passerelle d’application à l’aide de [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>Créer le pool par défaut et les paramètres

Créez le pool back-end par défaut nommé *appGatewayBackendPool* pour la passerelle d’application à l’aide de [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configurez les paramètres pour le pool back-end à l’aide de [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Créer l’écouteur et la règle par défaut

Un écouteur est requis pour permettre à la passerelle d’application d’acheminer le trafic de manière appropriée vers le pool principal. Dans ce didacticiel, vous créez deux écouteurs. Le premier écouteur de base que vous créez écoute le trafic vers l’URL racine. Le second écouteur que vous créez écoute le trafic vers des URL spécifiques.

Créez l’écouteur par défaut nommé *myDefaultListener* à l’aide de [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) avec la configuration front-end et le port front-end créés précédemment. 

Une règle est requise pour que l’écouteur sache quel pool principal utiliser pour le trafic entrant. Créez une règle de base nommée *rule1* à l’aide de [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myDefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Maintenant que vous avez créé les ressources nécessaires pour la prise en charge, spécifiez des paramètres de la passerelle d’application nommée *myAppGateway* à l’aide de [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), puis créez-la à l’aide de [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

La création de la passerelle d’application peut prendre jusqu’à 30 minutes. Patientez jusqu’à ce que le déploiement soit terminé avant de passer à la section suivante. À ce stade du tutoriel, vous avez une passerelle d’application qui écoute le trafic sur le port 80 et envoie ce trafic à un pool par défaut de serveurs.

### <a name="add-image-and-video-backend-pools-and-port"></a>Ajouter le port et les pools principaux image et vidéo

Ajoutez des pools back-end nommés *imagesBackendPool* et *videoBackendPool* à votre passerelle d’application avec [Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool). Ajoutez le port front-end des pools avec [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport). Soumettez les changements à la passerelle d’application à l’aide de [Set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Set-AzApplicationGateway -ApplicationGateway $appgw
```

La mise à jour de la passerelle d’application peut également prendre jusqu’à 20 minutes.

### <a name="add-backend-listener"></a>Ajouter un écouteur principal

Ajoutez l’écouteur back-end nommé *backendListener*, nécessaire pour router le trafic à l’aide d’[Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>Ajouter une carte de chemins d’accès URL

Les mappages de chemins d’URL permettent de veiller à ce que les URL envoyées à votre application soient acheminées vers des pools backend spécifiques. Créez des mappages de chemins d’URL nommés *imagePathRule* et *videoPathRule* à l’aide de [New-AzApplicationGatewayPathRuleConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) et [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>Ajouter une règle d’acheminement

La règle d’acheminement associe la carte d’URL à l’écouteur créé. Ajoutez la règle nommée *rule2* avec [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Créer des groupes de machines virtuelles identiques

Cet exemple crée trois groupes de machines virtuelles identiques prenant en charge les trois pools principaux qui ont été créés. Ils sont nommés *myvmss1*, *myvmss2* et *myvmss3*. Vous assignez le groupe identique au pool principal lorsque vous configurez les paramètres IP.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Installer IIS

Chaque groupe identique contient deux instances de machine virtuelle sur lesquelles vous installez IIS, qui exécute un exemple de page pour vérifier si la passerelle d’application fonctionne.

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Utilisez [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) pour obtenir l’adresse IP publique de la passerelle d’application. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. Par exemple, `http://52.168.55.24`, `http://52.168.55.24:8080/images/test.htm` ou `http://52.168.55.24:8080/video/test.htm`.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Tester l’URL de base dans la passerelle d’application](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

Remplacez l’URL par http://&lt;ip-address&gt;:8080/images/test.htm, en indiquant votre adresse IP à la place de &lt;ip-address&gt;, de façon à voir apparaître quelque chose ressemblant à l’exemple suivant :

![Tester l’URL images dans la passerelle d’application](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

Changez l’URL en http://&lt;ip-address&gt;:8080/video/test.htm, en remplaçant &lt;ip-address&gt; par votre adresse IP, de façon à voir apparaître quelque chose ressemblant à l’exemple suivant :

![Tester l’URL vidéo dans la passerelle d’application](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources, la passerelle d’application et toutes les ressources associées à l’aide de [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer des écouteurs, un mappage de chemins d’URL et des règles
> * Créer des pools backend scalables

> [!div class="nextstepaction"]
> [Rediriger le trafic web selon l’URL](./tutorial-url-redirect-powershell.md)
