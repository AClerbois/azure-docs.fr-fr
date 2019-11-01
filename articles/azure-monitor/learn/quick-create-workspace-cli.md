---
title: Créer un espace de travail Log Analytics à l’aide d’Azure CLI | Microsoft Docs
description: Découvrez comment créer un espace de travail Log Analytics pour permettre la collecte de données et les solutions de gestion à partir de vos environnements locaux et cloud avec Azure CLI.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.openlocfilehash: 18bfc99ded6e3e9171fbb20fbf329700817829de
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894993"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Créer un espace de travail Log Analytics avec Azure CLI 2.0

Vous utilisez Azure CLI 2.0 pour créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce démarrage rapide vous montre comment utiliser l’interface de ligne de commande Azure 2.0 pour déployer un espace de travail Log Analytics dans Azure Monitor. Un espace de travail Log Analytics est un environnement unique pour les données de journal d’activité Azure Monitor. Chaque espace de travail dispose d’un référentiel de données et d’une configuration propres. Les sources de données et les solutions sont configurées de façon à stocker leurs données dans un espace de travail particulier. Vous devez avoir un espace de travail Log Analytics si vous souhaitez collecter des données à partir des sources suivantes :

* Ressources Azure dans votre abonnement  
* Ordinateurs locaux surveillés par System Center Operations Manager  
* Collections d’appareils de System Center Configuration Manager  
* Données de diagnostics et de journaux du Stockage Azure  
 
Pour les autres sources, telles que les machines virtuelles Azure et les machines virtuelles Windows ou Linux dans votre environnement, consultez les rubriques suivantes :

* [Collect data from Azure virtual machines](../learn/quick-collect-azurevm.md) (Collecter des données à partir de machines virtuelles Azure)
* [Collect data from hybrid Linux computer](../learn/quick-collect-linux-computer.md) (Collecter des données à partir d’un ordinateur Linux hybride)
* [Collect data from hybrid Windows computer](quick-collect-windows-computer.md) (Collecter des données à partir d’un ordinateur Windows hybride)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce guide de démarrage rapide nécessite que vous exécutiez la version 2.0.30 d’Azure CLI, ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Créer un espace de travail
Créez un espace de travail avec [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). L’exemple suivant crée un espace de travail à l’emplacement *eastus* à l’aide d’un modèle Resource Manager à partir de votre ordinateur local. Le modèle JSON est configuré pour vous demander uniquement le nom de l’espace de travail et spécifie une valeur par défaut pour les autres paramètres susceptibles d’être utilisés comme configuration standard dans votre environnement. Vous pouvez aussi stocker le modèle dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation. Pour plus d’informations sur le fonctionnement des modèles, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md).

Pour plus d’informations sur les régions prises en charge, consultez les [régions dans lesquelles Log Analytics est disponible](https://azure.microsoft.com/regions/services/) et recherchez Azure Monitor à partir du champ **Rechercher un produit**. 

Les paramètres suivants sont définis comme une valeur par défaut :

* Emplacement : par défaut, USA Est
* Référence SKU : la valeur par défaut est le nouveau niveau de tarification par Go publié dans le modèle de tarification d’avril 2018.

>[!WARNING]
>Lors de la création ou de la configuration d’un espace de travail Log Analytics dans un abonnement pour lequel le nouveau modèle de tarification d’avril 2018 a été choisi, le seul niveau tarifaire Log Analytics valide est **PerGB2018**. 
>

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
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
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
                "sku": {
                    "Name": "[parameters('sku')]"
                },
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
4. Vous êtes prêt à déployer ce modèle. Utilisez les commandes suivantes à partir du dossier qui contient le modèle. Lorsque vous êtes invité à entrer un nom d’espace de travail, fournissez un nom globalement unique dans tous les abonnements Azure.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Le déploiement peut prendre plusieurs minutes. Lorsqu’il est terminé, vous voyez un message similaire au message suivant qui inclut le résultat :

![Exemple de résultat lorsque le déploiement est achevé](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Étapes suivantes
Disposant à présent d’un espace de travail, vous pouvez configurer la collecte des données de télémétrie de surveillance, exécuter des recherches dans les journaux pour analyser ces données et ajouter une solution de gestion pour fournir des données et insights analytiques supplémentaires.  

* Pour activer la collecte de données à partir de ressources Azure avec Diagnostics Azure ou le stockage Azure, consultez [Collecte des journaux et des métriques des services Azure à utiliser dans Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Ajoutez [System Center Operations Manager comme source de données](../platform/om-agents.md) pour collecter des données à partir des agents rattachés à votre groupe d’administration Operations Manager et les stocker dans votre espace de travail Log Analytics.  
* Connectez [Configuration Manager](../platform/collect-sccm.md) pour importer des ordinateurs qui sont membres de collections dans la hiérarchie.  
* Examinez les [solutions de surveillance](../insights/solutions.md) disponibles et la façon d’ajouter ou de supprimer une solution dans votre espace de travail.
