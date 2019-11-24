---
title: Informations de référence sur l’API de l’agent Azure Application Insights
description: Référence API d’Application Insights Agent. Set-ApplicationInsightsMonitoringConfig. Surveillez les performances d’un site web sans avoir à le redéployer. Fonctionne avec les applications web ASP.NET hébergées en local, dans des machines virtuelles ou sur Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: d90739fbdc862d67dc2ce0f1dfdf5af5f4089a44
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899674"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>API d’Application Insights Agent : Set-ApplicationInsightsMonitoringConfig

Ce document décrit une cmdlet appartenant au [module PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Description

Définit le fichier de configuration sans effectuer de réinstallation complète.
Redémarrez IIS pour que vos modifications prennent effet.

> [!IMPORTANT] 
> Cette cmdlet requiert une session PowerShell avec des autorisations d’administrateur.


## <a name="examples"></a>Exemples

### <a name="example-with-a-single-instrumentation-key"></a>Exemple avec une clé d’instrumentation unique
Dans cet exemple, toutes les applications sur l’ordinateur actuel recevront une clé d’instrumentation unique.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exemple avec un mappage de clé d’instrumentation
Dans cet exemple :
- `MachineFilter` effectue la correspondance avec l’ordinateur actuel en utilisant le caractère générique `'.*'`.
- `AppFilter='WebAppExclude'` fournit une clé d’instrumentation `null`. L’application spécifiée ne sera pas instrumentée.
- `AppFilter='WebAppOne'` attribue à l’application spécifiée une clé d’instrumentation unique.
- `AppFilter='WebAppTwo'` attribue à l’application spécifiée une clé d’instrumentation unique.
- Enfin, `AppFilter` utilise également le caractère générique `'.*'` pour établir les correspondances de toutes les applications web pour lesquelles les règles précédentes n’ont effectué aucune correspondance, et attribuer une clé d’instrumentation par défaut.
- Des espaces ont été ajoutées pour faciliter la lisibilité.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obligatoire.** Utilisez ce paramètre pour fournir une clé d’instrumentation unique que l’ensemble des applications utiliseront sur l’ordinateur cible.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obligatoire.** Utilisez ce paramètre pour fournir plusieurs clés d’instrumentation et un mappage des clés d’instrumentation utilisées par chaque application.
Vous pouvez créer un script d’installation unique pour plusieurs ordinateurs en définissant le paramètre `MachineFilter`.

> [!IMPORTANT]
> Les applications effectueront le mappage avec les règles dans l’ordre d’apparition de ces dernières. Par conséquent, vous devez d’abord spécifier les règles les plus spécifiques et finir par les règles les plus génériques.

#### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** est une expression régulière C# obligatoire associée au nom de la machine virtuelle ou de l’ordinateur.
    - « .* » effectue la correspondance avec tous les éléments.
    - « ComputerName » effectue la correspondance avec les ordinateurs présentant le nom spécifié uniquement.
- **MachineFilter** est une expression régulière C# obligatoire associée au nom de la machine virtuelle ou de l’ordinateur.
    - « .* » effectue la correspondance avec tous les éléments.
    - « ComputerName » effectue la correspondance avec les applications IIS présentant le nom spécifié uniquement.
- Le paramètre **Clé d’instrumentation** est requis pour activer la surveillance des applications qui correspondent aux deux filtres précédents.
    - Conservez la valeur null si vous souhaitez définir des règles pour exclure la surveillance.


### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez ce commutateur pour afficher les journaux détaillés.


## <a name="output"></a>Output

Par défaut, aucune sortie.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exemple détaillé de la sortie à partir de la définition du fichier de configuration via -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exemple détaillé de la sortie à partir de la définition du fichier de configuration via -InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Étapes suivantes

  Affichez vos données de télémétrie :
 - [Explorez les métriques](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
- [Effectuez des recherches dans les événements et les journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- [Utilisez la fonctionnalité Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées.
- [Créez des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).
 
 Ajoutez des données de télémétrie :
 - [Créez des tests web](monitor-web-app-availability.md) pour vous assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de la page web et pour activer le suivi des appels.
- [Ajoutez le Kit de développement logiciel (SDK) Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et journaliser les appels.
 
 En faire plus avec Application Insights Agent :
 - Utilisez notre guide pour [résoudre les problèmes](status-monitor-v2-troubleshoot.md) d’Application Insights Agent.
 - [Obtenez la configuration](status-monitor-v2-api-get-config.md) pour confirmer que vos paramètres ont été enregistrés correctement.
 - [Obtenez l’état](status-monitor-v2-api-get-status.md) pour inspecter la surveillance.
