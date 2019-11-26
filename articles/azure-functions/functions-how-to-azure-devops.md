---
title: Proposer en permanence des mises à jour de code fonction à l’aide d’Azure DevOps - Azure Functions
description: Découvrez comment configurer un pipeline Azure DevOps qui cible Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 4fb01eac53151799a0def00d13f18619faf437f6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72927537"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Livraison continue à l’aide d’Azure DevOps

Vous pouvez déployer automatiquement votre fonction sur une application Azure Functions à l’aide d’[Azure Pipelines](/azure/devops/pipelines/).

Vous avez deux possibilités pour définir votre pipeline :

- **Fichier YAML :** un fichier YAML décrit le pipeline. Ce fichier peut avoir une section d’étapes de création et une section de mise en production. Le fichier YAML doit se trouver dans le même référentiel que l’application.
- **Modèle** : les modèles sont des tâches prêtes à l’emploi qui créent ou déploient votre application.

## <a name="yaml-based-pipeline"></a>Pipeline basé sur le fichier YAML

Pour créer un pipeline YAML, commencez par créer votre application et ensuite déployez-la.

### <a name="build-your-app"></a>Générer votre application

La façon dont vous créez votre application dans Azure Pipelines varie en fonction du langage de programmation de votre application. Chaque langage possède des étapes spécifiques qui créent un artefact de déploiement. Un artefact de déploiement est utilisé pour déployer votre application Functions dans Azure.

#### <a name="net"></a>.NET

Vous pouvez utiliser l’exemple suivant pour créer un fichier YAML pour générer votre application .NET :

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

Vous pouvez utiliser l’exemple suivant pour créer un fichier YAML pour générer votre application JavaScript :

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

Vous pouvez utiliser l’exemple suivant pour créer un fichier YAML pour générer votre application Python. Python est pris en charge uniquement pour Azure Functions sur Linux. Pour générer YAML pour Python 3.7, vous pouvez remplacer toutes les instances de 3.6 par 3.7 dans ce fichier YAML.

```yaml
pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

Vous pouvez utiliser l’exemple suivant pour créer un fichier YAML pour générer une application PowerShell. PowerShell est pris en charge uniquement Azure Functions sur Windows.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>Déployer votre application

Vous devez inclure l’exemple YAML suivant dans votre fichier YAML, selon le système d’exploitation hôte.

#### <a name="windows-function-app"></a>Application de fonction Windows

Vous pouvez utiliser l’extrait de code suivant pour effectuer un déploiement sur une application de fonction Windows :

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Application de fonction Linux

Vous pouvez utiliser l’extrait de code suivant pour effectuer un déploiement sur une application de fonction Linux  :

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Pipeline basé sur un modèle

Les modèles dans Azure DevOps sont des groupes prédéfinis de tâches qui génèrent ou déploient une application.

### <a name="build-your-app"></a>Générer votre application

La façon dont vous créez votre application dans Azure Pipelines varie en fonction du langage de programmation de votre application. Chaque langage possède des étapes spécifiques qui créent un artefact de déploiement. Un artefact de déploiement est utilisé pour mettre à jour votre application Functions dans Azure.

Pour utiliser les modèles intégrés, lors de la création d’un nouveau pipeline de build, sélectionnez **Utiliser l’éditeur classique** pour créer un pipeline à l’aide des modèles de concepteur.

![Sélectionner l’éditeur classique d’Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

Après avoir configuré la source de votre code, recherchez les modèles de build Azure Functions. Sélectionnez le modèle qui correspond au langage de votre application.

![Sélectionner un modèle de build Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

Dans certains cas, les artefacts de build ont une structure de dossier spécifique. Vous devrez peut-être activer la case à cocher **Placer le nom du dossier racine au début des chemins d’archives**.

![Option pour placer le nom du dossier racine au début](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Applications JavaScript

Si votre application JavaScript a une dépendance sur des modules natifs de Windows, vous devez mettre à jour la version du pool d’agents vers **Hosted VS2017**.

![Mise à jour la version du pool d’agents](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Déployer votre application

Lorsque vous créez un nouveau pipeline de mise en production, recherchez le modèle de mise en production d’Azure Functions.

![Rechercher le modèle de mise en production Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Le déploiement vers un emplacement de déploiement n’est pas pris en charge dans le modèle de mise en production.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Création d’un pipeline de build à l’aide de l’interface de ligne de commande Azure

Pour créer un pipeline de build dans Azure, utilisez la [commande](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create) `az functionapp devops-pipeline create`. Le pipeline de build est créé pour créer et mettre en production toutes les modifications du code de votre référentiel. Cette commande génère un nouveau fichier YAML qui définit le pipeline de génération et de mise en production et le valide dans votre référentiel. Les conditions préalables de cette commande varient selon l’emplacement de votre code.

- Si votre code se trouve dans GitHub :

    - Vous devez disposer des autorisations en **écriture** dans votre abonnement.

    - Vous devez être l’administrateur de projet dans Azure DevOps.

    - Vous devez disposer des autorisations nécessaires pour créer un jeton d’accès personnel GitHub disposant d’autorisations suffisantes. Pour plus d’informations, consultez [Autorisations requises du jeton d’accès personnel GitHub](https://aka.ms/azure-devops-source-repos).

    - Vous devez disposer des autorisations nécessaires pour valider dans la branche principale de votre référentiel GitHub pour valider le fichier YAML généré automatiquement.

- Si votre code se trouve dans Azure Repos :

    - Vous devez disposer des autorisations en **écriture** dans votre abonnement.

    - Vous devez être l’administrateur de projet dans Azure DevOps.

## <a name="next-steps"></a>Étapes suivantes

- Consultez la [vue d’ensemble d’Azure Functions](functions-overview.md)
- Consultez la [vue d’ensemble d’Azure DevOps](/azure/devops/pipelines/).
