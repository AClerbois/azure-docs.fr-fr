---
title: Préparer l’ordinateur hôte du Kit de développement Azure Stack (ASDK) | Microsoft Docs
description: Explique comment préparer l’ordinateur hôte du Kit de développement Azure Stack (ASDK) en vue de son installation.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: ec7b56a7324f3c8c3e3459639e4fd00e92d93e8f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249746"
---
# <a name="prepare-the-asdk-host-computer"></a>Préparer l’ordinateur hôte de l’ASDK
Avant de pouvoir installer le kit ASDK sur l’ordinateur hôte, vous devez préparer l’environnement. Une fois préparé, l’ordinateur hôte du kit de développement Azure Stack démarre à partir du disque dur de la machine virtuelle CloudBuilder.vhdx pour commencer le déploiement du kit.

## <a name="prepare-the-development-kit-host-computer"></a>Préparer l’ordinateur hôte du kit de développement
Avant de pouvoir installer le kit ASDK sur l’ordinateur hôte, vous devez préparer l’environnement de l’ordinateur.
1. Connectez-vous à l’ordinateur hôte du kit de développement en tant qu’administrateur local.
2. Vérifiez que le fichier CloudBuilder.vhdx a été déplacé à la racine du lecteur C:\ (C:\CloudBuilder.vhdx).
3. Exécutez le script suivant pour télécharger le fichier du programme d’installation du kit de développement (asdk-installer.ps1) du [répertoire des outils GitHub Azure Stack](https://github.com/Azure/AzureStack-Tools) au dossier **C:\AzureStack_Installer** de votre ordinateur hôte du kit de développement :

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Enforce usage of TLSv1.2 to download from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Depuis une console PowerShell avec élévation de privilèges, lancez le script **C:\AzureStack_Installer\asdk-installer.ps1**, puis cliquez sur **Préparer l’environnement**.

    ![](media/asdk-prepare-host/1.PNG) 

5. Dans la page **Sélectionner Cloudbuilder.vhdx** du programme d’installation, sélectionnez le fichier **cloudbuilder.vhdx** que vous avez téléchargé et extrait dans les [étapes précédentes](asdk-download.md). Dans cette page, vous pouvez également (si vous le souhaitez) cocher la case **Ajouter des pilotes** si vous avez besoin de pilotes supplémentaires sur l’ordinateur hôte du kit de développement. Cliquez sur **Suivant**.  

    ![](media/asdk-prepare-host/2.PNG)

6. Dans la page **Paramètres facultatifs**, entrez les informations d’identification du compte d’administrateur local de l’ordinateur hôte du kit de développement, puis cliquez sur **Suivant**. Vous pouvez également fournir des valeurs pour les paramètres facultatifs suivants :
  - **Nom de l'ordinateur** : cette option définit le nom de l'hôte du kit de développement. Le nom doit respecter les spécifications des noms de domaine complets et ne pas dépasser 15 caractères. La valeur par défaut est un nom d’ordinateur aléatoire généré par Windows.
  - **Configuration IP statique** : indique que votre déploiement doit utiliser une adresse IP statique. Dans le cas contraire, quand le programme d’installation redémarre dans cloudbuilder.vhdx, les interfaces réseau sont configurées avec DHCP.

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > Si vous ne fournissez pas ces informations d’identification, vous aurez besoin d’un accès KVM ou direct à l’hôte après le redémarrage de l’ordinateur, dans le cadre de la procédure de configuration du kit de développement.

7. Si vous avez choisi une configuration IP statique à l’étape précédente, vous devez maintenant :
    - Sélectionner une carte réseau. Vérifiez que vous pouvez vous connecter à la carte avant de cliquer sur **Suivant**.
    - Vérifiez que les valeurs pour **Adresse IP**, **Passerelle** et **DNS** sont correctes, puis cliquez sur **Suivant**.
13. Cliquez sur **Suivant** pour démarrer le processus de préparation.
14. Quand la préparation indique **Terminé**, cliquez sur **Suivant**.
15. Cliquez sur **Redémarrer maintenant** pour démarrer l’ordinateur hôte du kit de développement dans cloudbuilder.vhdx, puis [continuer le processus de déploiement](asdk-install.md).

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>Étapes suivantes
[Installer le kit ASDK](asdk-install.md)
