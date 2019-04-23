---
title: Mettre à l’échelle verticalement des groupes identiques de machines virtuelles Azure | Microsoft Docs
description: Mettre à l’échelle une machine virtuelle en réponse aux alertes avec Azure Automation
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: manayar
ms.openlocfilehash: 3846815dabdc9e351f3d8449feb88affb9c6efdb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001196"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Mise à l’échelle verticale avec des groupes de machines virtuelles identiques

Cet article décrit comment mettre à l’échelle verticalement des [jeux de mise à l’échelle de machine virtuelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/) Azure avec ou sans réapprovisionnement. Pour mettre à l’échelle verticalement des machines virtuelles qui n’appartiennent pas à des groupes de machines virtuelles identiques, consultez [Mettre à l’échelle verticalement une machine virtuelle Azure avec Azure Automation](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

La mise à l’échelle verticale, également appelée *montée en puissance* ou *descente en puissance* consiste à augmenter ou diminuer la taille de machines virtuelles en réponse à une charge de travail. Ne confondez pas ce comportement avec la [mise à l’échelle horizontale](virtual-machine-scale-sets-autoscale-overview.md), également appelée *augmentation du nombre* ou *diminution du nombre*, dans laquelle c’est le nombre de machines virtuelles qui est adapté à la charge de travail.

Le terme « réapprovisionner » signifie supprimer une machine virtuelle et la remplacer par une autre. Lorsque vous augmentez ou diminuez la taille des machines virtuelles dans un groupe de machines virtuelles identiques, vous voudrez parfois redimensionner des machines virtuelles et conserver vos données, et d’autres fois déployer de nouvelles machines virtuelles d’une nouvelle taille. Ce document couvre les deux cas de figure.

La mise à l’échelle verticale peut être utile dans les cas suivants :

* Sous-utilisation d’un service résidant sur des machines virtuelles (par exemple, le week-end). La diminution de la taille des machines virtuelles peut réduire les coûts mensuels.
* Augmentation de la taille des machines virtuelles pour répondre à une demande accrue sans créer d’autres machines virtuelles.

Vous pouvez configurer la mise à l’échelle verticale pour qu’elle se déclenche en fonction d’alertes définies dans votre groupe de machines virtuelles identiques. Lorsque l’alerte est activée, elle déclenche un webhook, lequel déclenche un runbook qui effectue la mise à l’échelle. La mise à l’échelle verticale peut être configurée comme suit :

1. Créez un compte Azure Automation avec fonctionnalité d’identification.
2. Importez les runbooks de mise à l’échelle verticale Azure Automation dans votre abonnement.
3. Ajoutez un webhook dans votre runbook.
4. Ajoutez une alerte dans votre groupe de machines virtuelles identiques, à l’aide d’une notification de webhook.

> [!NOTE]
> En raison de la taille de la première machine virtuelle, les différentes tailles de mise à l’échelle peuvent être limitées en fonction de la disponibilité des autres tailles dans le cluster dans lequel la machine virtuelle actuelle est déployée. Dans les runbooks publiés et utilisés dans cet article, nous nous chargeons de ce cas et appliquons uniquement la mise à l’échelle dans les paires de machines virtuelles suivantes. Cela signifie qu’une machine virtuelle Standard_D1v2 ne peut pas de suite être convertie en Standard_G5 ou Basic_A0. Également la Machine virtuelle tailles échelle / n’est pas pris en charge. Vous pouvez choisir d’effectuer une mise à l’échelle entre les paires de tailles suivantes :
> 
> | Paires de mise à l’échelle des machines virtuelles |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | D1 standard |D4 standard |
> | D11 standard |D14 standard |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Créer un compte Azure Automation avec fonctionnalité d’identification
La première chose à faire est de créer un compte Azure Automation qui héberge les runbooks utilisés pour mettre à l’échelle les instances de groupe de machines virtuelles identiques. Depuis peu, [Azure Automation](https://azure.microsoft.com/services/automation/) dispose de la fonctionnalité « Compte d’identification » qui facilite la configuration du principal du service en vue de l’exécution automatique de runbooks pour le compte d’un utilisateur. Pour plus d'informations, consultez les pages suivantes :

* [Authentifier des Runbooks avec un compte d’identification Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importer les runbooks de mise à l’échelle verticale Azure Automation dans votre abonnement

Les runbooks nécessaires à la mise à l’échelle verticale de vos groupes de machines virtuelles identiques sont déjà publiés dans la galerie de runbooks Azure Automation. Pour les importer dans votre abonnement, suivez les étapes décrites dans cet article :

* [Galeries de runbooks et de modules pour Azure Automation](../automation/automation-runbook-gallery.md)

Choisissez l’option Parcourir la galerie dans le menu Runbooks :

![Runbooks à importer][runbooks]

Les runbooks à importer sont affichés dans l’image ci-dessous. Sélectionnez le runbook selon que la mise à l’échelle verticale inclut ou exclut le réapprovisionnement :

![Galerie de runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Ajouter un webhook à votre runbook

Une fois que vous avez importé les runbooks, ajoutez un webhook au runbook afin qu’il puisse être déclenché par une alerte émanant d’un groupe de machines virtuelles identiques. La procédure de création d’un webhook pour votre runbook est décrite dans cet article :

* [Webhooks Azure Automation](../automation/automation-webhooks.md)

> [!NOTE]
> Veillez à copier l’URI du webhook avant de fermer la boîte de dialogue, car vous aurez besoin de cette adresse dans la section suivante.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Ajouter une alerte à votre groupe de machines virtuelles identiques

Voici un script PowerShell qui décrit comment ajouter une alerte dans un groupe de machines virtuelles identiques. Pour obtenir le nom de la métrique déclenchant l’alerte, voir l’article [Métriques courantes de la mise à l’échelle automatique Azure Monitor](../azure-monitor/platform/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Il est recommandé de configurer une période suffisante pour l’alerte afin de ne pas déclencher une mise à l’échelle verticale, et toute interruption de service qui en résulte, trop souvent. Envisagez une période d’au moins 20 à 30 minutes. Préférez une mise à l’échelle horizontale pour éviter toute interruption.
> 
> 

Pour plus d’informations sur la création d’alertes, consultez les articles suivants :

* [Exemples de démarrage rapide Azure Monitor PowerShell](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Exemples de démarrage rapide de l’interface de ligne de commande multiplateforme Azure Monitor](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Résumé

Cet article vous a montré des exemples simples de mise à l’échelle verticale. Grâce à ces éléments (compte Automation, runbooks, webhooks et alertes), vous pouvez connecter des événements très différents à un jeu d’actions personnalisé.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
