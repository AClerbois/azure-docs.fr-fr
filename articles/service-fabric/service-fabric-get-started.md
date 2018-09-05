---
title: Configurer un environnement de développement Windows pour les microservices Azure | Microsoft Docs
description: Installez le runtime, le kit de développement logiciel et créez un cluster de développement local. Une fois la configuration terminée, vous serez prêt à générer des applications sur Windows.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: ryanwi
ms.openlocfilehash: 48e04d3bdd783d1dc04ac5cc77c4183ef5f87865
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818437"
---
# <a name="prepare-your-development-environment-on-windows"></a>Préparer votre environnement de développement sur Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Pour générer et exécuter des [applications Azure Service Fabric][1] sur votre machine de développement Windows, installez le runtime, le Kit de développement logiciel (SDK) et les outils Service Fabric. Vous devez également [activer l’exécution des scripts Windows PowerShell](#enable-powershell-script-execution) inclus dans le Kit de développement logiciel (SDK).

## <a name="prerequisites"></a>Prérequis
### <a name="supported-operating-system-versions"></a>Versions du système d’exploitation prises en charge
Les versions de système d’exploitation prises en charge pour le développement sont les suivantes :

* Windows 7
* Windows 8 et Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7 prend en charge :
> - Windows 7 inclut uniquement Windows PowerShell 2.0 par défaut. Les applets de commande PowerShell de Service Fabric nécessitent PowerShell 3.0 ou version ultérieure. Vous pouvez [télécharger Windows PowerShell 5.0][powershell5-download] à partir du Centre de téléchargement Microsoft.
> - Le Proxy inverse de Service Fabric n’est pas disponible sur Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>Installer le Kit de développement logiciel (SDK) et les outils
### <a name="to-use-visual-studio-2017"></a>Pour utiliser Visual Studio 2017
Les outils Service Fabric font partie de la charge de travail de développement Azure dans Visual Studio 2017. Activez cette charge de travail dans le cadre de votre installation de Visual Studio.
En outre, vous devez installer le Kit de développement logiciel (SDK) et le runtime Microsoft Azure Service Fabric à l’aide de Web Platform Installer.

* [Installer le Kit de développement logiciel (SDK) Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Pour utiliser Visual Studio 2015 (requiert Visual Studio 2015 Update 2 ou une version ultérieure)
Pour Visual Studio 2015, les outils Service Fabric sont installés avec le Kit de développement logiciel (SDK) et le runtime, à l’aide de Web Platform Installer :

* [Installer le Kit de développement logiciel (SDK) et les outils de Microsoft Azure Service Fabric][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Installation du Kit de développement logiciel (SDK) uniquement
Si vous avez uniquement besoin du SDK, vous pouvez installer ce package :
* [Installer le Kit de développement logiciel (SDK) Microsoft Azure Service Fabric][core-sdk]

Les versions actuelles sont les suivantes :
* Outils et SDK Service Fabric 3.2.176
* Runtime Service Fabric 6.3.176
* Outils Service Fabric pour Visual Studio 2015 2.3.10710.3
* Visual Studio 2017 15.7 inclut les outils Service Fabric pour Visual Studio 2.3.10710.1 

Pour obtenir la liste des versions prises en charge, consultez l’article [Service Fabric support (Options de prise en charge de Service Fabric)](service-fabric-support.md).

## <a name="enable-powershell-script-execution"></a>Activer l'exécution du script PowerShell
Service Fabric utilise des scripts Windows PowerShell pour créer un cluster de développement local et déployer des applications à partir de Visual Studio. Par défaut, Windows bloque l’exécution de ces scripts. Pour les activer, vous devez modifier votre stratégie d'exécution PowerShell. Ouvrez PowerShell en tant qu'administrateur et entrez la commande suivante :

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Installer Docker (facultatif)
[Service Fabric est un orchestrateur de conteneurs](service-fabric-containers-overview.md) qui permet de déployer des microservices sur un cluster de machines. Pour exécuter des applications de conteneur Windows sur votre cluster de développement local, vous devez d’abord installer Docker pour Windows. Obtenez [Docker CE pour Windows (stable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Après l’installation et le démarrage de Docker, cliquez avec le bouton droit de la souris sur l’icône de la barre d’état, puis sélectionnez **Switch to Windows containers** (Basculer vers les conteneurs Windows). Cette étape est nécessaire pour exécuter des images Docker basées sur Windows.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez fini de configurer votre environnement de développement, commencez à créer et à exécuter des applications.

* [Créer votre première application Service Fabric dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Découvrez comment déployer et gérer des applications sur votre cluster local](service-fabric-get-started-with-a-local-cluster.md)
* [En savoir plus sur les modèles de programmation : acteurs fiables et services fiables](service-fabric-choose-framework.md)
* [Consulter les exemples de code Service Fabric sur GitHub](https://aka.ms/servicefabricsamples)
* [Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Suivre le parcours d’apprentissage Service Fabric pour une introduction générale à la plate-forme](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Page de campagne Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Lien WebPI VS 2015"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Lien WebPI Dev15"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Lien WebPI du Kit de développement logiciel principal"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
