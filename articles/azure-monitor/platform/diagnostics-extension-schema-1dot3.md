---
title: Schéma de configuration de l’extension Microsoft Azure Diagnostics 1.3 et versions ultérieures
description: Schéma version 1.3 et versions ultérieures pour les diagnostics Azure fournis avec le kit Microsoft Azure SDK 2.4 et versions ultérieures.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: fa03017c35c76d986139eeee00eea8a9b4a00e62
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59497081"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Schéma de configuration de Microsoft Azure Diagnostics 1.3 et versions ultérieures
> [!NOTE]
> L’Extension Microsoft Azure Diagnostics est le composant utilisé pour collecter les compteurs de performances et d’autres statistiques à partir de :
> - Machines virtuelles Azure
> - Virtual Machine Scale Sets
> - Service Fabric
> - Cloud Services
> - Network Security Group
>
> Cette page vous concerne uniquement si vous utilisez l’un de ces services.

Cette page a trait aux versions 1.3 et ultérieures (Azure SDK 2.4 et ultérieur). Les sections de configuration les plus récentes sont commentées pour montrer dans quelle version elles ont été ajoutées.  

Le fichier de configuration décrit ici est utilisé pour définir les paramètres de configuration de diagnostic lorsque le moniteur de diagnostic démarre.  

L’extension est utilisée conjointement avec d’autres produits de diagnostic Microsoft comme Azure Monitor, qui inclut l’Application Insights et Analytique de journal.



Téléchargez la définition de schéma de fichier de configuration publique en exécutant la commande PowerShell suivante :  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Pour plus d’informations sur l’utilisation d’Azure Diagnostics, voir [Extension Microsoft Azure Diagnostics](diagnostics-extension-overview.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemple du fichier de configuration des diagnostics  
 L’exemple suivant montre un fichier de configuration de diagnostic standard :  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> La définition du récepteur Azure Monitor dans le cadre d’une configuration publique inclut deux propriétés, à savoir resourceId et region. Elles sont requises uniquement pour les services de machines virtuelles et de cloud classiques. Ces propriétés ne doivent pas être utilisées pour les machines virtuelles Resource Manager ni pour les groupes de machines virtuelles identiques.
> Il existe également un élément de configuration privée supplémentaire pour le récepteur Azure Monitor, qui transmet un secret et un ID de principal. Il est requis uniquement pour les services de machines virtuelles et de cloud classiques. Pour les machines virtuelles Resource Manager et les groupes de machines virtuelles identiques, la définition Azure Monitor dans l’élément de configuration privée peut être exclue.
>

Équivalent JSON du fichier de configuration XML précédent.

Les éléments PublicConfig et PrivateConfig sont séparés car, dans la plupart des cas d’utilisation de json, ils sont transmis en tant que variables différentes. Ces cas incluent les modèles Resource Manager, le groupe de machines virtuelles identiques PowerShell et Visual Studio.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> La définition du récepteur Azure Monitor dans le cadre d’une configuration publique inclut deux propriétés, à savoir resourceId et region. Elles sont requises uniquement pour les services de machines virtuelles et de cloud classiques.
> Ces propriétés ne doivent pas être utilisées pour les machines virtuelles Resource Manager ni pour les groupes de machines virtuelles identiques.
>

```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

> [!NOTE]
> Il existe un élément de configuration privée supplémentaire pour le récepteur Azure Monitor, qui transmet un secret et un ID de principal. Il est requis uniquement pour les services de machines virtuelles et de cloud classiques. Pour les machines virtuelles Resource Manager et les groupes de machines virtuelles identiques, la définition Azure Monitor dans l’élément de configuration privée peut être exclue.
>


## <a name="reading-this-page"></a>Lecture de cette page  
 Les balises suivantes sont à peu près dans l’ordre indiqué dans l’exemple précédent.  Si vous ne voyez pas de description complète à l’emplacement prévu, recherchez l’élément ou l’attribut dans la page.  

## <a name="common-attribute-types"></a>Types d’attributs courants  
 L’attribut **scheduledTransferPeriod** apparaît dans plusieurs éléments. Il s’agit de l’intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Élément DiagnosticsConfiguration  
 *Arborescence : Root - DiagnosticsConfiguration*

Ajouté à la version 1.3.  

Élément de niveau supérieur du fichier de configuration de diagnostic.  

**Attribute**  xmlns - L’espace de noms XML du fichier de configuration des diagnostics est :  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Éléments enfants|Description|  
|--------------------|-----------------|  
|**PublicConfig**|Requis. Consultez la description sur cette page.|  
|**PrivateConfig**|facultatif. Consultez la description sur cette page.|  
|**IsEnabled**|Booléen. Consultez la description sur cette page.|  

## <a name="publicconfig-element"></a>Élément PublicConfig  
 *Arborescence : Racine - DiagnosticsConfiguration - PublicConfig*

 Décrit la configuration de diagnostic public.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**WadCfg**|Requis. Consultez la description sur cette page.|  
|**StorageAccount**|Nom du compte de stockage Azure où stocker les données. Peut également être spécifié en tant que paramètre lors de l’exécution de l’applet de commande Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Peut être *Table*, *Blob* ou *TableAndBlob*. Table est la valeur par défaut. Si TableAndBlob est choisi, les données de diagnostic sont écrites deux fois : une fois pour chaque type.|  
|**LocalResourceDirectory**|Répertoire se trouvant sur la machine virtuelle sur laquelle Monitoring Agent stocke les données d’événement. S’il n’est pas défini, le répertoire par défaut est utilisé :<br /><br /> Pour un rôle web/de travail : `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Pour une Machine virtuelle : `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Les attributs requis sont :<br /><br /> - **path** - Répertoire sur le système à utiliser par Azure Diagnostics.<br /><br /> - **expandEnvironment** - Contrôle si les variables d’environnement sont développées dans le nom du chemin d’accès.|  

## <a name="wadcfg-element"></a>WadCFG Element  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG*

 Identifie et configure les données de télémétrie à collecter.  


## <a name="diagnosticmonitorconfiguration-element"></a>Élément DiagnosticMonitorConfiguration
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Obligatoire

|Attributs|Description|  
|----------------|-----------------|  
| **overallQuotaInMB** | Quantité maximale d’espace disque local pouvant être utilisé par les différents types de données de diagnostic collectés par Azure Diagnostics. Le paramètre par défaut est 4 096 Mo.<br />
|**useProxyServer** | Configurez Azure Diagnostics pour utiliser les paramètres de serveur proxy tels que définis dans les paramètres d’Internet Explorer.|
|**sinks** | Ajouté à la version 1.5. facultatif. Pointe vers un emplacement de récepteur pour envoyer également des données de diagnostic à tous les éléments enfants qui prennent en charge les récepteurs. Des exemples de récepteur comme Application Insights ou Event Hubs.|  


<br /> <br />

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**CrashDumps**|Consultez la description sur cette page.|  
|**DiagnosticInfrastructureLogs**|Permet la collecte des journaux générés par Azure Diagnostics. Les journaux d’infrastructure de diagnostic sont utiles pour le dépannage du système de diagnostic lui-même. Les attributs facultatifs sont les suivants :<br /><br /> - **scheduledTransferLogLevelFilter** - Configure le niveau de gravité minimal des journaux collectés.<br /><br /> - **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Répertoires**|Consultez la description sur cette page.|  
|**EtwProviders**|Consultez la description sur cette page.|  
|**Mesures**|Consultez la description sur cette page.|  
|**PerformanceCounters**|Consultez la description sur cette page.|  
|**WindowsEventLog**|Consultez la description sur cette page.|
|**DockerSources**|Consultez la description sur cette page. |



## <a name="crashdumps-element"></a>Élément CrashDumps  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 Permet la collecte des vidages sur incident.  

|Attributs|Description|  
|----------------|-----------------|  
|**containerName**|facultatif. Nom du conteneur d’objets blob de votre compte de stockage Azure à utiliser pour stocker les vidages sur incident.|  
|**crashDumpType**|facultatif.  Configure Azure Diagnostics pour collecter les mini-vidages sur incident ou les vidages sur incident complets.|  
|**directoryQuotaPercentage**|facultatif.  Configure le pourcentage de **overallQuotaInMB** à réserver pour les vidages sur incident sur la machine virtuelle.|  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Requis. Définit les valeurs de configuration pour chaque processus.<br /><br /> L’attribut suivant est également requis :<br /><br /> **processName** - Nom du processus pour lequel vous voulez qu’Azure Diagnostics collecte un vidage sur incident.|  

## <a name="directories-element"></a>Élément Directories
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration -  Directories*

 Permet la collecte du contenu d’un répertoire, des journaux de demandes d’accès ayant échouées IIS et/ou des journaux IIS.  

 Attribut **scheduledTransferPeriod** facultatif. Voir l’explication précédente.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**IISLogs**|Incluez cet élément dans la configuration pour permettre la collecte des journaux IIS :<br /><br /> **containerName** - Nom du conteneur d’objets blob de votre compte de stockage Azure à utiliser pour stocker les vidages sur incident.|   
|**FailedRequestLogs**|Incluez cet élément dans la configuration pour permettre la collecte des journaux concernant les demandes ayant échoué sur une application ou un site IIS. Vous devez également activer les options de suivi sous **system.WebServer** dans **Web.config**.|  
|**Sources de données**|Liste de répertoires à analyser.|




## <a name="datasources-element"></a>Élément DataSources  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources*

 Liste de répertoires à analyser.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Requis. Attribut requis :<br /><br /> **containerName** - Nom du conteneur d’objets blob de votre compte de stockage Azure à utiliser pour stocker les fichiers journaux.|  





## <a name="directoryconfiguration-element"></a>Élément DirectoryConfiguration  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources - DirectoryConfiguration*

 Peut inclure l’élément **Absolute** ou **LocalResource**, mais pas les deux.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**Absolu**|Chemin d’accès absolu au répertoire à surveiller. Les attributs suivants sont requis :<br /><br /> - **Path** - Chemin d’accès absolu au répertoire à surveiller.<br /><br /> - **expandEnvironment** - Détermine si les variables d’environnement de Path sont développées.|  
|**LocalResource**|Chemin d’accès relatif à une ressource locale à surveiller. Les attributs requis sont :<br /><br /> - **Name** - Nom de la ressource locale contenant le répertoire à surveiller<br /><br /> - **relativePath** - Chemin d’accès relatif au nom qui contient le répertoire à surveiller|  



## <a name="etwproviders-element"></a>Élément EtwProviders  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Configure la collecte d’événements ETW issus des fournisseurs de manifeste EventSource et/ou ETW.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configure la collection d’événements générés à partir de la [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Attribut requis :<br /><br /> **provider** - Nom de classe de l’événement EventSource.<br /><br /> Les attributs facultatifs sont les suivants :<br /><br /> - **scheduledTransferLogLevelFilter** - Niveau de gravité minimal à transférer vers votre compte de stockage.<br /><br /> - **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Attribut requis :<br /><br /> **provider** -GUID du fournisseur d’événements<br /><br /> Les attributs facultatifs sont les suivants :<br /><br /> - **scheduledTransferLogLevelFilter** - Niveau de gravité minimal à transférer vers votre compte de stockage.<br /><br /> - **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 Configure la collection d’événements générés à partir de la [classe EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|Attribut facultatif :<br/><br/> **eventDestination** -Nom de la table dans laquelle stocker les événements|  
|**Événement**|Attribut requis :<br /><br /> **id** : ID de l’événement.<br /><br /> Attribut facultatif :<br /><br /> **eventDestination** -Nom de la table dans laquelle stocker les événements|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|Attribut facultatif :<br /><br /> **eventDestination** -Nom de la table dans laquelle stocker les événements|  
|**Événement**|Attribut requis :<br /><br /> **id** : ID de l’événement.<br /><br /> Attribut facultatif :<br /><br /> **eventDestination** -Nom de la table dans laquelle stocker les événements|  



## <a name="metrics-element"></a>Élément Metrics  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrics*

 Permet de générer une table de compteur de performance optimisée pour les requêtes rapides. Chaque compteur de performance défini dans l’élement **PerformanceCounters** est stocké dans la table Metrics et dans la table Performance Counter.  

 L’attribut **resourceId** est requis.  ID de ressource de la machine virtuelle ou du groupe de machines virtuelles identiques sur lesquels vous déployez les diagnostics Microsoft Azure. Obtenez le **resourceID** à partir du [portail Azure](https://portal.azure.com). Sélectionnez **Parcourir** -> **Groupe de ressources** -> **<>\>**. Cliquez sur la vignette **Propriétés** et copiez la valeur à partir du champ **ID**.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**MetricAggregation**|Attribut requis :<br /><br /> **scheduledTransferPeriod** - Intervalle entre les transferts planifiés vers le stockage Azure, arrondi à la minute la plus proche. La valeur est un [« Type de données de durée » XML.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Élément PerformanceCounters  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Permet la collecte des compteurs de performance.  

 Attribut facultatif :  

 Attribut **scheduledTransferPeriod** facultatif. Voir l’explication précédente.

|Élément enfant|Description|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Les attributs suivants sont requis :<br /><br /> - **counterSpecifier** - Nom du compteur de performance. Par exemple : `\Processor(_Total)\% Processor Time`. Pour obtenir une liste des compteurs de performances se trouvant sur votre hôte, exécutez la commande `typeperf`.<br /><br /> - **sampleRate** - Fréquence à laquelle le compteur doit être échantillonné.<br /><br /> Attribut facultatif :<br /><br /> **unit** -Unité de mesure du compteur.|
|**sinks** | Ajouté à la version 1.5. facultatif. Pointe vers un emplacement de récepteur permettant d’envoyer également des données de diagnostic. Par exemple, Azure Monitor ou Event Hubs.|    




## <a name="windowseventlog-element"></a>Élément WindowsEventLog
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Permet la collecte des journaux des événements Windows.  

 Attribut **scheduledTransferPeriod** facultatif. Voir l’explication précédente.  

|Élément enfant|Description|  
|-------------------|-----------------|  
|**DataSource**|Journaux des événements Windows à collecter. Attribut requis :<br /><br /> **name** - Requête XPath décrivant les événements windows à collecter. Par exemple : <br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Pour collecter tous les événements, spécifiez « * ».|  




## <a name="logs-element"></a>Élément Logs  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 Présent dans la version 1.0 et 1.1. Absent dans la version 1.2. Rajouté dans la version 1.3.  

 Définit la configuration de la mémoire tampon des journaux Azure de base.  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|facultatif. Définit la quantité maximale de stockage du système de fichiers disponible pour les données spécifiées.<br /><br /> La valeur par défaut est 0.|  
|**scheduledTransferLogLevelFilter**|**string**|facultatif. Définit le niveau de gravité minimal des entrées de journal transférées. La valeur par défaut qui transfère tous les journaux est **Undefined**. Les autres valeurs possibles sont, du plus informatif au moins informatif : **Détaillé**, **Informations**, **Avertissement**, **Erreur**, **Critique**.|  
|**scheduledTransferPeriod**|**duration**|facultatif. Définit l’intervalle entre les transferts planifiés de données, arrondi à la minute la plus proche.<br /><br /> La valeur par défaut est PT0S.|  
|**sinks** |**string**| Ajouté à la version 1.5. facultatif. Pointe vers un emplacement de récepteur permettant d’envoyer également des données de diagnostic. Par exemple, Application Insights ou Event Hubs.|  

## <a name="dockersources"></a>DockerSources
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Ajouté dans 1.9.

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**Statistiques**|Indique au système de collecter les statistiques pour les conteneurs Docker|  

## <a name="sinksconfig-element"></a>Élément SinksConfig  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 Liste d’emplacements vers lesquels envoyer des données de diagnostic et la configuration associée à ces emplacements.  

|Nom de l’élément|Description|  
|------------------|-----------------|  
|**Récepteur**|Consultez la description sur cette page.|  

## <a name="sink-element"></a>Élément Sink
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink*

 Ajouté à la version 1.5.  

 Définit les emplacements vers lesquels envoyer des données de diagnostic. Par exemple, le service Application Insights.  

|Attribut|Type|Description|  
|---------------|----------|-----------------|  
|**Nom**|string|Chaîne identifiant le nom du récepteur.|  

|Élément|Type|Description|  
|-------------|----------|-----------------|  
|**Application Insights**|string|Utilisé uniquement lors de l’envoi de données à Application Insights. Contient la clé d’instrumentation d’un compte Application Insights actif auquel vous avez accès.|  
|**Canaux**|string|Un pour chaque filtrage supplémentaire qui diffuse cela en continu|  

## <a name="channels-element"></a>Élément Channels  
 *Arborescence : Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 Ajouté à la version 1.5.  

 Définit les filtres pour les flux de données de journaux passant par un récepteur.  

|Élément|Type|Description|  
|-------------|----------|-----------------|  
|**Canal**|string|Consultez la description sur cette page.|  

## <a name="channel-element"></a>Élément Channel
 *Arborescence : Chaînes de SinksConfig - récepteur - racine - DiagnosticsConfiguration - PublicConfig - WadCFG - - chaîne*

 Ajouté à la version 1.5.  

 Définit les emplacements vers lesquels envoyer des données de diagnostic. Par exemple, le service Application Insights.  

|Attributs|Type|Description|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Définit le niveau de gravité minimal des entrées de journal transférées. La valeur par défaut qui transfère tous les journaux est **Undefined**. Les autres valeurs possibles sont, du plus informatif au moins informatif : **Détaillé**, **Informations**, **Avertissement**, **Erreur**, **Critique**.|  
|**Nom**|**string**|Nom unique du canal auquel faire référence|  


## <a name="privateconfig-element"></a>Élément PrivateConfig
 *Arborescence : Racine - DiagnosticsConfiguration - PrivateConfig*

 Ajouté à la version 1.3.  

 Facultatif  

 Stocke les détails privés du compte de stockage (nom, clé et point de terminaison). Cette information est envoyée à la machine virtuelle, mais ne peut pas être récupérée à partir de celui-ci.  

|Éléments enfants|Description|  
|--------------------|-----------------|  
|**StorageAccount**|Compte de stockage à utiliser. Les attributs suivants sont requis :<br /><br /> - **name** - Nom du compte de stockage.<br /><br /> - **key** - Clé du compte de stockage.<br /><br /> - **endpoint** - Point de terminaison permettant d’accéder au compte de stockage. <br /><br /> -**sasToken** (ajouté 1.8.1)-, vous pouvez spécifier un jeton SAP au lieu d’une clé de compte de stockage dans la configuration privée. Si la clé de compte de stockage est fournie, elle est ignorée. <br />Configuration requise pour le jeton SAP : <br />- Prend en charge le jeton SAP de compte uniquement. <br />Les types de services - *b*, *t* sont requis. <br /> Les autorisations - *a*, *c*, *u*, *w* sont requises. <br /> Les types de ressources - *c*, *o* sont requis. <br /> - Prend en charge le protocole HTTPS uniquement. <br /> - Les heures de début et d’expiration doivent être valides.|  


## <a name="isenabled-element"></a>Élément IsEnabled  
 *Arborescence : Racine - DiagnosticsConfiguration - IsEnabled*

 Booléen. Utilisez `true` pour activer les diagnostics ou `false` pour les désactiver.

