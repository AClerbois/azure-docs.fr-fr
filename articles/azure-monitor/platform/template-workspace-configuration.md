---
title: Utilisation de modèles Azure Resource Manager pour créer et configurer un espace de travail Log Analytics | Microsoft Docs
description: Vous pouvez utiliser des modèles Azure Resource Manager pour créer et configurer des espaces de travail Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d21ca1b0-847d-4716-bb30-2a8c02a606aa
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: magoedte
ms.openlocfilehash: 2acaba4e82f499ce1ca08a0ce17469ccb0a7e541
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698445"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Gérer un espace de travail Log Analytics à l’aide de modèles Azure Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Vous pouvez utiliser des [modèles Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) pour créer et configurer des espaces de travail Log Analytics dans Azure Monitor. Voici quelques exemples de tâches que vous pouvez effectuer avec des modèles :

* Créer un espace de travail, y compris la définition d’un niveau tarifaire 
* Ajouter une solution
* Créer des recherches enregistrées
* Créer un groupe d’ordinateurs
* Activer la collecte de journaux d’activité IIS à partir d’ordinateurs sur lesquels l’agent Windows est installé
* Collecter les compteurs de performances d’ordinateurs Linux et Windows
* Collecter les événements de Syslog sur des ordinateurs Linux 
* Collecter les événements des journaux des événements Windows
* Ajouter l’agent Log Analytics à une machine virtuelle Azure
* Configurer Log Analytics pour indexer les données collectées à l’aide des diagnostics Azure

Cet article fournit des exemples de modèle qui illustrent des opérations de configuration que vous pouvez effectuer avec des modèles.

## <a name="api-versions"></a>Versions d’API

La table suivante répertorie la version d’API pour les ressources utilisées dans cet exemple.

| Ressource | Type de ressource | Version de l'API |
|:---|:---|:---|
| Espace de travail   | workspaces    | 2017-03-15-preview |
| Recherche      | savedSearches | 2015-03-20 |
| Source de données | datasources   | 2015-11-01-preview |
| Solution    | solutions     | 2015-11-01-preview |

## <a name="create-a-log-analytics-workspace"></a>Créer un espace de travail Log Analytics

L’exemple suivant crée un espace de travail avec un modèle à partir de votre ordinateur local. Le modèle JSON est configuré pour exiger uniquement le nom et l’emplacement du nouvel espace de travail (à l’aide des valeurs par défaut des autres paramètres de l’espace de travail, tels que le niveau tarifaire et la rétention).  

### <a name="create-and-deploy-template"></a>Créer et déployer un modèle

1. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "australiacentral", 
              "australiaeast", 
              "australiasoutheast", 
              "brazilsouth",
              "canadacentral", 
              "centralindia", 
              "centralus", 
              "eastasia", 
              "eastus", 
              "eastus2", 
              "francecentral", 
              "japaneast", 
              "koreacentral", 
              "northcentralus", 
              "northeurope", 
              "southafricanorth", 
              "southcentralus", 
              "southeastasia", 
              "uksouth", 
              "ukwest", 
              "westcentralus", 
              "westeurope", 
              "westus", 
              "westus2" 
            ],
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Modifiez le modèle en fonction de vos besoins. Consultez la référence [Microsoft.OperationalInsights/workspaces modèle](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) pour découvrir les propriétés et les valeurs prises en charge. 
3. Enregistrez ce fichier au format **deploylaworkspacetemplate.json** dans un dossier local.
4. Vous êtes prêt à déployer ce modèle. Vous utilisez PowerShell ou la ligne de commande pour créer l’espace de travail, en spécifiant le nom et l’emplacement de l’espace de travail au sein de la commande.

   * Pour PowerShell, utilisez les commandes suivantes à partir du dossier qui contient le modèle :
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * Pour la ligne de commande, utilisez les commandes suivantes à partir du dossier qui contient le modèle :

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

Le déploiement peut prendre plusieurs minutes. Lorsqu’il est terminé, vous voyez un message similaire au message suivant qui inclut le résultat :<br><br> ![Exemple de résultat lorsque le déploiement est achevé](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Configurer un espace de travail Log Analytics

L’exemple de modèle suivant illustre comment :

1. Ajouter des solutions à l’espace de travail
2. Créer des recherches enregistrées
3. Créer un groupe d’ordinateurs
4. Activer la collecte de journaux d’activité IIS à partir d’ordinateurs sur lesquels l’agent Windows est installé
5. Collecter les compteurs de performances de disque logique d’ordinateurs Linux (% d’Inodes utilisés, Mo libres, % d’espace utilisé, Transferts disque/s, Lectures disque/s, Écritures disque/s)
6. Collecter les événements Syslog d’ordinateurs Linux
7. Collecter les événements d’erreur et d’avertissement du journal des événements d’application d’ordinateurs Windows
8. Collecter le compteur de performances Mo de mémoire disponible d’ordinateurs Windows
9. Collecter les journaux d’activité IIS et les journaux des événements Windows écrits par les diagnostics Azure dans un compte de stockage

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Workspace name"
      }
    },
    "pricingTier": {
      "type": "string",
      "allowedValues": [
        "PerGB2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "defaultValue": "PerGB2018",
      "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
      }
    },
    "immediatePurgeDataOn30Days": {
      "type": "bool",
      "metadata": {
        "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral", 
        "australiaeast", 
        "australiasoutheast", 
        "brazilsouth",
        "canadacentral", 
        "centralindia", 
        "centralus", 
        "eastasia", 
        "eastus", 
        "eastus2", 
        "francecentral", 
        "japaneast", 
        "koreacentral", 
        "northcentralus", 
        "northeurope", 
        "southafricanorth", 
        "southcentralus", 
        "southeastasia", 
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
      }
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    }
  },
  "variables": {
    "Updates": {
      "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "Updates"
    },
    "AntiMalware": {
      "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "AntiMalware"
    },
    "SQLAssessment": {
      "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "SQLAssessment"
    },
    "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-11-01-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "name": "[parameters('pricingTier')]"
          "features": {
            "immediatePurgeDataOn30Days": "[parameters('immediatePurgeDataOn30Days')]"
          }
        },
    "retentionInDays": "[parameters('dataRetention')]"
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "Category": "VMSS",
            "ETag": "*",
            "DisplayName": "VMSS Instance Count",
            "Query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "Version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-03-20",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "pricingTier": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "immediatePurgeDataOn30Days": {  
      "type": "bool",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').features.immediatePurgeDataOn30Days]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}

```
### <a name="deploying-the-sample-template"></a>Déploiement de l’exemple de modèle

Pour déployer l’exemple de modèle :

1. Enregistrez l’exemple joint dans un fichier, par exemple `azuredeploy.json`. 
2. Modifiez le modèle afin de disposer de la configuration souhaitée.
3. Utilisez PowerShell ou la ligne de commande pour déployer le modèle.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Ligne de commande

```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Exemples de modèles Azure Resource Manager

La galerie de modèles de démarrage rapide Azure comprend plusieurs modèles pour Log Analytics, destinés aux opérations suivantes :

* [Déployer une machine virtuelle exécutant Windows avec l’extension de machine virtuelle Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Déployer une machine virtuelle exécutant Linux avec l’extension de machine virtuelle Log Analytics](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Analyser Azure Site Recovery à l’aide d’un espace de travail Log Analytics existant](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Analyser les applications web Azure à l’aide d’un espace de travail Log Analytics existant](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Ajouter un compte de stockage existant à Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Étapes suivantes

* [Déployez l’agent Windows sur des machines virtuelles Azure à l’aide du modèle Resource Manager](../../virtual-machines/extensions/oms-windows.md).

* [Déployez l’agent Linux sur des machines virtuelles Azure à l’aide du modèle Resource Manager](../../virtual-machines/extensions/oms-linux.md).
