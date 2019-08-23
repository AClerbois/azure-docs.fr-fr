---
title: Rubriques de mise à niveau d’application avancée | Microsoft Docs
description: Cet article traite de sujets avancés se rapportant à la mise à niveau d’une application Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: a3d0d6077da4df9a7f0d1b246c9752d38488a175
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963817"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Mise à niveau d’une application Service Fabric : rubriques avancées
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Ajout ou suppression de types de services pendant la mise à niveau d’une application
Si un type de service est ajouté à une application publiée dans le cadre d’une mise à niveau, le nouveau type de service est ajouté à l’application déployée. Une telle mise à niveau n’affecte pas les instances de service qui faisaient déjà partie de l’application, mais une instance du type de service qui a été ajoutée doit être créée pour que le nouveau type de service soit actif (voir [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

De même, des types de services peuvent également être supprimés d’une application dans le cadre d’une mise à niveau. Toutefois, toutes les instances de service du type de service à supprimer doivent être supprimées avant de procéder à la mise à niveau (voir [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Mode de mise à niveau manuelle
> [!NOTE]
> Le mode de mise à niveau *Monitored* est recommandé pour toutes les mises à niveau de Service Fabric.
> Le mode de mise à niveau *UnmonitoredManual* doit uniquement être envisagé pour une mise à niveau ayant échoué ou suspendue. 
>
>

En mode *Monitored*, Service Fabric applique des stratégies de contrôle d’intégrité pour s’assurer que l’application est saine au fil de l’avancement de la mise à niveau. En cas de non-respect des stratégies de contrôle d’intégrité, la mise à niveau est interrompue ou restaurée automatiquement en fonction de la valeur *FailureAction* spécifiée.

En mode *UnmonitoredManual*, l’administrateur de l’application possède un contrôle total sur la progression de la mise à niveau. Ce mode est utile lorsque vous appliquez des stratégies d’évaluation de l’intégrité personnalisées ou lorsque vous effectuez des mises à niveau non conventionnelles pour contourner totalement la surveillance de l’intégrité (par exemple, si l’application présente déjà une perte de données). Une mise à niveau en cours d’exécution dans ce mode s’interrompt à la fin de chaque UD et doit être reprise explicitement à l’aide de la commande [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Lorsqu’une mise à niveau est interrompue et prête à être reprise par l’utilisateur, son état de mise à niveau sera *RollforwardPending* (voir [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Enfin, le mode *UnmonitoredAuto* est utile pour effectuer des itérations de mise à niveau rapides pendant le test ou le développement du service, car aucune entrée utilisateur n’est requise et aucune stratégie de contrôle d’intégrité d’application n’est évaluée.

## <a name="upgrade-with-a-diff-package"></a>Effectuer une mise à niveau avec un package différentiel
Au lieu d’approvisionner un package d’application complet, les mises à niveau peuvent également être effectuées en approvisionnant des packages différentiels qui contiennent uniquement les packages de code/configuration/données mis à jour, ainsi que l’intégralité du manifeste d’application complet et des manifestes de service. Il n’est pas nécessaire de disposer de packages d’application complets pour la première installation d’une application sur le cluster. Les mises à niveau suivantes peuvent se faire à partir de packages d’application complets ou différentiels.  

Toute référence dans le manifeste d’application ou les manifestes de service à un package différentiel qui ne figure pas dans le package d’application est automatiquement remplacée par la version actuellement approvisionnée.

Les scénarios d’utilisation d’un package différentiel sont les suivants :

* Quand vous disposez d’un package d’application volumineux qui référence plusieurs fichiers manifeste de service et/ou plusieurs packages de code, de configuration ou de données.
* Quand vous disposez d’un système de déploiement qui génère la disposition de la build directement à partir de votre processus de génération d’application. Dans ce cas, même si le code n’a pas changé, les assemblys nouvellement générés obtiennent une somme de contrôle différente. Si vous utilisez un package d'application complet, vous devez mettre à jour la version installée sur tous les packages de code. Si vous utilisez un package différentiel, vous fournissez uniquement les fichiers qui ont changé et les fichiers manifeste dont la version a changé.

Lorsqu’une application est mise à niveau à l’aide de Visual Studio, un package différentiel est automatiquement publié. Pour créer manuellement un package différentiel, le manifeste d’application et les manifestes de service doivent être mis à jour, mais seuls les packages modifiés doivent être inclus dans le package d’application final.

Commençons par exemple par l'application suivante (numéros de version fournis pour faciliter la compréhension) :

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Supposons que vous souhaitiez mettre à jour uniquement le package de code de service1 à l’aide d’un package différentiel. Votre application mise à jour présente les modifications de version suivantes :

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Dans ce cas, vous mettez à jour le manifeste d’application à la version 2.0.0 ainsi que le manifeste de service pour service1 afin de refléter la mise à jour du package de code. La structure des dossiers de votre package d’application serait la suivante :

```text
app1/
  service1/
    code/
```

En d’autres termes, créez un package d’application complet normalement, puis supprimez tous les dossiers de packages de code/configuration/données pour lesquels la version n’a pas changé.

## <a name="upgrade-application-parameters-independently-of-version"></a>Mettre à niveau les paramètres d’application indépendamment de la version

Il est parfois souhaitable de modifier les paramètres d’une application Service Fabric sans modifier la version du manifeste. Vous pouvez effectuer cette opération facilement à l’aide de l’indicateur **-ApplicationParameter** avec la cmdlet Azure Service Fabric PowerShell **Start-ServiceFabricApplicationUpgrade**. Supposons une application Service Fabric avec les propriétés suivantes :

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Mettez à présent à niveau l’application à l’aide de la cmdlet **Start-ServiceFabricApplicationUpgrade**. Cet exemple montre une mise à niveau surveillée, mais une mise à niveau non surveillée est également possible. Pour obtenir une description complète des indicateurs acceptés par cette cmdlet, consultez la [référence du module Azure Service Fabric PowerShell](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Après la mise à niveau, vérifiez que l’application contient les paramètres mis à jour et la même version :

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="rolling-back-application-upgrades"></a>Restauration de mises à niveau d’application

Tandis que les mises à niveau peuvent être restaurées par progression dans un des trois modes (*Monitored*, *UnmonitoredAuto* ou *UnmonitoredManual*), elles peuvent uniquement être restaurées en mode *UnmonitoredAuto* ou *UnmonitoredManual*. La restauration en mode *UnmonitoredAuto* fonctionne de la même façon que la restauration par progression, à la différence que la valeur par défaut de *UpgradeReplicaSetCheckTimeout* change (voir [Paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md)). La restauration en mode *UnmonitoredManual* fonctionne de la même façon que la restauration par progression : la restauration s’interrompt à la fin de chaque UD et doit être reprise explicitement à l’aide de la commande [ Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps).

Les restaurations peuvent être déclenchées automatiquement lorsque les stratégies d’intégrité d’une mise à niveau en mode *Monitored* avec une valeur *FailureAction* définie sur *Rollback* sont enfreintes (voir [Paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md)) ou explicitement à l’aide de la commande [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Pendant la restauration, la valeur *UpgradeReplicaSetCheckTimeout* et le mode peuvent être modifiés à tout moment à l’aide de la commande [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Étapes suivantes
[mise à niveau de votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l’application à l’aide de Visual Studio.

[mise à niveau de votre application à l’aide de PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide à travers une mise à niveau de l’application à l’aide de PowerShell.

Contrôlez les mises à niveau de votre application à l'aide des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Rendez les mises à niveau de votre application compatibles en apprenant à utilisez la [Sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Résolvez les problèmes courants de mise à niveau de l’application en vous reportant aux étapes de [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md).
