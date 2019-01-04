---
title: Script PowerShell de création d’une ressource Application Insights | Microsoft Docs
description: Automatisez la création des ressources Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 3fe6a89073da731332a91ece40a6ea6f667d150a
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54004414"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Script PowerShell de création d’une ressource Application Insights


Lorsque vous voulez surveiller une nouvelle application, ou une nouvelle version d’une application, avec [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), vous définissez une nouvelle ressource dans Microsoft Azure. Cette ressource est l’endroit où les données de télémétrie de votre application sont analysées et affichées. 

Vous pouvez automatiser la création d’une nouvelle ressource à l’aide de PowerShell.

Par exemple, si vous développez une application pour appareil mobile, il est probable qu’à tout moment, plusieurs versions publiées de votre application soient utilisées par vos clients. Vous ne voulez pas que les résultats de télémétrie de différentes versions soient mélangés. Par conséquent, vous obtenez votre processus de génération pour créer une nouvelle ressource pour chaque build.

> [!NOTE]
> Si vous souhaitez créer un ensemble de ressources en même temps, vous pouvez [créer les ressources à l’aide d’un modèle Azure](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Script de création d’une ressource Application Insights
Consultez les spécifications d’applet de commande appropriées :

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Script PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzureRmAccount / Connect-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Que faire avec l’iKey ?
Chaque ressource est identifiée par sa clé d’instrumentation (iKey). L’iKey est une sortie du script de création de ressources. Votre script de génération doit fournir l’iKey au kit de développement logiciel (SDK) Application Insights intégrée à votre application.

Il existe deux façons de mettre l’iKey à disposition du kit de développement logiciel (SDK) :

* Dans [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Ou dans le [code d’initialisation](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Voir aussi
* [Créer des ressources Application Insights et de test Web  templates à partir de modèles (en anglais)](powershell.md)
* [Configurer la surveillance de diagnostics Azure avec PowerShell (en anglais](powershell-azure-diagnostics.md) 
* [Définition d’alertes à l’aide de PowerShell](powershell-alerts.md)

