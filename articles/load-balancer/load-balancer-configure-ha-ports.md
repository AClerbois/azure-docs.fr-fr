---
title: "Configurer des ports de haute disponibilité pour Azure Load Balancer | Microsoft Docs"
description: "Découvrez comment utiliser les ports de haute disponibilité pour équilibrer la charge du trafic interne entre tous les ports."
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 7256548b988812c64ca9a9f8a84fec377646635d
ms.contentlocale: fr-fr
ms.lasthandoff: 09/28/2017

---

# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>Comment configurer des ports de haute disponibilité pour l’équilibreur de charge interne

Cet article montre un exemple de déploiement de ports de haute disponibilité dans un équilibreur de charge interne. Pour connaître les configurations d’appliances virtuelles réseau spécifiques, reportez-vous aux sites web des fournisseurs correspondants.

>[!NOTE]
> Les ports de haute disponibilité sont actuellement en préversion. Le niveau de disponibilité et la fiabilité des fonctionnalités de la préversion peuvent différer de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La figure 1 montre la configuration de l’exemple de déploiement décrit dans cet article :
- Les appliances virtuelles réseau sont déployées dans le pool backend d’un équilibreur de charge interne, derrière la configuration des ports de haute disponibilité. 
- L’itinéraire défini par l’utilisateur qui est appliqué au sous-réseau DMZ route tout le trafic vers <?> en faisant du tronçon suivant l’adresse IP virtuelle de l’équilibreur de charge interne. 
- L’équilibreur de charge interne distribue le trafic vers l’une des appliances virtuelles réseau actives en fonction de l’algorithme d’équilibrage de charge.
- L’appliance virtuelle réseau traite le trafic et le transfère vers la destination d’origine sur le sous-réseau backend.
- Le chemin de retour peut également emprunter le même itinéraire si un itinéraire défini par l’utilisateur correspondant est configuré sur le sous-réseau backend. 

![exemple de déploiement de ports de haute disponibilité](./media/load-balancer-configure-ha-ports/haports.png)

Figure 1 : Appliances virtuelles réseau déployées derrière un équilibreur de charge interne avec ports de haute disponibilité 

## <a name="preview-sign-up"></a>S’inscrire à la préversion

Pour découvrir la préversion des ports de haute disponibilité associés aux références SKU de l’équilibreur de charge standard, inscrivez votre abonnement et bénéficiez d’un accès via PowerShell ou Azure CLI 2.0.

- S’inscrire à l’aide de PowerShell

   ```powershell
   Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- S’inscrire à l’aide d’Azure CLI 2.0

    ```cli
  az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network  
    ```

## <a name="configuring-ha-ports"></a>Configuration des ports de haute disponibilité

La configuration des ports de haute disponibilité implique la configuration d’un équilibreur de charge interne, avec des appliances virtuelles réseau dans le pool backend, une configuration correspondante de sonde d’intégrité d’équilibreur de charge pour détecter l’état d’intégrité de l’appliance virtuelle réseau, et la règle d’équilibreur de charge avec ports de haute disponibilité. La configuration générale de l’équilibreur de charge est abordée dans [Bien démarrer avec les équilibreurs de charge](load-balancer-get-started-ilb-arm-portal.md). Cet article explique la configuration des ports de haute disponibilité.

La configuration consiste essentiellement à définir le port frontend et le port backend sur **0**, et à définir le protocole sur **Tous**. Cet article explique comment configurer des ports de haute disponibilité à l’aide du portail Azure, de PowerShell et d’Azure CLI 2.0.

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>Configurer la règle d’équilibreur de charge des ports de haute disponibilité avec le portail Azure

Le portail Azure inclut l’option **Ports HA** qui se présente sous la forme d’une case à cocher. Lorsqu’elle est cochée, la configuration correspondante du protocole et des ports est automatiquement remplie. 

![Configuration des ports de haute disponibilité via le portail Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)

Figure 2 : Configuration de ports de haute disponibilité via le portail

### <a name="configure-ha-ports-load-balancer-rule-with-powershell"></a>Configurer la règle d’équilibreur de charge des ports de haute disponibilité avec PowerShell

Utilisez la commande suivante pour créer la règle d’équilibreur de charge des ports de haute disponibilité au moment de la création de l’équilibreur de charge interne avec PowerShell :

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-ha-ports-load-balancer-rule-with-azure-cli-20"></a>Configurer la règle d’équilibreur de charge des ports de haute disponibilité avec Azure CLI 2.0

À l’étape 4 de la section [Créer un jeu d’équilibrage de charge interne](load-balancer-get-started-ilb-arm-cli.md), utilisez la commande suivante pour créer la règle d’équilibreur de charge des ports de haute disponibilité.

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [ports de haute disponibilité](load-balancer-ha-ports-overview.md)

