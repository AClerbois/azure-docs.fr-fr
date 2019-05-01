---
title: Intégration du contrôle de code source dans Azure Automation - Hérité
description: Cet article décrit l’intégration du contrôle de code source avec GitHub dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/01/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c95af40c3fa3f9dad2bfb5ea4a1b9f585c636928
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881127"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Intégration du contrôle de code source dans Azure Automation - Hérité

> [!NOTE]
> Il existe une nouvelle expérience de contrôle de code source. Pour plus d’informations sur cette nouvelle expérience, consultez l’article concernant le [contrôle de code source (préversion)](source-control-integration.md).

L’intégration du contrôle de code source vous permet d’associer des runbooks dans votre compte Automation à un référentiel de contrôle de code source GitHub. Le contrôle du code source vous permet de collaborer avec votre équipe en toute simplicité, de suivre les modifications et de restaurer des versions antérieures de vos runbooks. Par exemple, le contrôle de code source vous permet de synchroniser différentes branches avec vos comptes de développement, de test ou de production Automation, et contribue à promouvoir le code qui a été testé dans votre environnement de développement vers votre compte Automation de production.

Le contrôle du code source vous permet de transmettre le code à partir d’Azure Automation vers le contrôle de code source ou d’extraire vos runbooks à partir du contrôle de code source vers Azure Automation. Cet article décrit comment configurer le contrôle de code source dans votre environnement Azure Automation. Nous allons commencer par configurer le logiciel Azure Automation afin qu’il accède à votre référentiel GitHub, et effectuer les différentes opérations pouvant être réalisées à l’aide de l’intégration du contrôle du code source. 

> [!NOTE]
> Le contrôle de code source prend en charge l’extraction et la transmission de [Runbooks de workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks), ainsi que de [Runbooks PowerShell](automation-runbook-types.md#powershell-runbooks). Les [Runbooks graphiques](automation-runbook-types.md#graphical-runbooks) ne sont pas encore pris en charge.

Deux étapes simples sont requises pour configurer le contrôle de code source pour votre compte Automation. Une seule étape suffit si vous possédez déjà un compte GitHub. Il s'agit de :

## <a name="step-1--create-a-github-repository"></a>Étape 1 : Création d’un référentiel GitHub

Si vous possédez déjà un compte GitHub et un référentiel que vous souhaitez lier à Azure Automation, connectez-vous à votre compte existant et commencez à partir de l’étape 2, ci-dessous. Sinon, accédez à [GitHub](https://github.com/), inscrivez-vous à un nouveau compte, et [créer un nouveau référentiel](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Étape 2 : Configuration du contrôle de code source dans Azure Automation

1. Sur la page Compte Automation du portail Azure, sous **Paramètres de compte**, cliquez sur **Contrôle de code source**.

2. La page **Contrôle de code source** s’ouvre. Vous pouvez configurer les détails de votre compte GitHub. Vous trouverez ci-dessous la liste des paramètres à configurer :  

   | **Paramètre** | **Description** |
   |:--- |:--- |
   | Choisir une source |Sélectionnez la source. Actuellement, seul **GitHub** est pris en charge. |
   | Authorization |Cliquez sur le bouton **Autoriser** pour accorder à Azure Automation l'accès à votre référentiel GitHub. Si vous êtes déjà connecté à votre compte GitHub dans une autre fenêtre, les informations d’identification de ce compte sont utilisées. Une fois que l’autorisation est accordée, la page affiche votre nom d’utilisateur GitHub sous **Propriété d’autorisation**. |
   | Choisir un référentiel |Sélectionnez un référentiel GitHub à partir de la liste des référentiels disponibles. |
   | Choisir une branche |Sélectionnez la branche à partir de la liste des branches disponibles. Seule la branche **master** s'affiche si vous n'avez pas créé de branches. |
   | Chemin d’accès au dossier de runbooks |Le chemin d’accès au dossier de runbooks spécifie le chemin d’accès dans le référentiel GitHub à partir duquel vous souhaitez transmettre ou extraire votre code. Il doit être saisi au format **/nomdossier/nomsous-dossier**. Seuls les runbooks du chemin d’accès au dossier de runbooks seront synchronisés avec votre compte Automation. Les runbooks des sous-dossiers du chemin d’accès au dossier de runbooks ne seront **PAS** synchronisés. Utilisez **/** pour synchroniser tous les runbooks dans le référentiel. |
3. Par exemple, si vous disposez d’un référentiel nommé **PowerShellScripts** qui contient un dossier nommé **RootFolder**, lequel contient un dossier nommé **SubFolder**. Vous pouvez utiliser les chaînes suivantes pour synchroniser chaque niveau de dossier :

   1. Pour synchroniser des runbooks à partir du **référentiel**, le chemin d'accès au dossier de runbooks est */*
   2. Pour synchroniser des runbooks à partir du **RootFolder**, le chemin d'accès au dossier de runbooks est */RootFolder*
   3. Pour synchroniser les runbooks à partir de **SubFolder**, le chemin d'accès au dossier de runbooks est */RootFolder/SubFolder*.
4. Une fois configurés, les paramètres sont affichés sur la page **Définition du contrôle de code source**.  

    ![La page de contrôle Source indique les paramètres](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Une fois que vous avez cliqué sur **OK**, l’intégration du contrôle de code source est configurée pour votre compte Automation. Elle doit être mise à jour avec vos informations GitHub. Vous pouvez maintenant cliquer sur cette partie pour afficher tout l’historique de la tâche de synchronisation de contrôle de code source.  

    ![Valeurs pour la configuration actuelle du contrôle de source configurée](media/source-control-integration-legacy/automation-RepoValues.png)
6. Après avoir configuré le contrôle de code source, deux [les ressources de variables](automation-variables.md) sont créés dans votre compte Automation. En outre, une application autorisée est ajoutée à votre compte GitHub.

   * La variable **Microsoft.Azure.Automation.SourceControl.Connection** contient les valeurs de la chaîne de connexion, comme illustré ci-dessous.  

     | **Paramètre** | **Valeur** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |Chaîne |
     | `Value` |{"Branch" :\<*Nom de votre branche*>,"RunbookFolderPath" :\<*Chemin d’accès au dossier de Runbooks*>,"ProviderType" :\<*possède une valeur 1 pour GitHub*>,"Repository" :\<*Nom de votre référentiel*>,"Username" :\<*Votre nom d’utilisateur GitHub*>} |

   * La variable **Microsoft.Azure.Automation.SourceControl.OauthToken**contient la valeur chiffrée sécurisée de votre OAuthToken.  

     |**Paramètre**            |**Valeur** |
     |:---|:---|
     | `Name`  | Microsoft.Azure.Automation.SourceControl.OauthToken |
     | `Type`  | Inconnue (chiffrée) |
     | `Value` | <*OAuthToken chiffré*> |  

     ![Une fenêtre de variables de contrôle de code source](media/source-control-integration-legacy/automation-Variables.png)  

   * **contrôle de code source Automation** est ajouté en tant qu'application autorisée à votre compte GitHub. Pour voir l’application : à partir de votre page d’accueil GitHub, accédez à **Profil** > **Paramètres** > **Applications**. Cette application permet à Azure Automation de synchroniser votre référentiel GitHub sur un compte Automation.  

     ![Paramètres de l’application dans GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Utilisation du contrôle de code source dans Automation

### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Archiver un runbook à partir d’Azure Automation vers le contrôle de code source

L’archivage de runbooks vous permet de transmettre les modifications apportées à un runbook dans Azure Automation dans votre référentiel de contrôle de code source. Voici les étapes permettant d’archiver un runbook :

1. À partir de votre compte Automation, [créez un Runbook textuel](automation-first-runbook-textual.md) ou [modifiez un Runbook textuel existant](automation-edit-textual-runbook.md). Ce runbook peut être un flux de travail PowerShell ou un runbook de script PowerShell.  
2. Après avoir modifié votre runbook, enregistrez-le et cliquez sur **Archiver** dans la page **Modifier**.  

    ![Une fenêtre montrant l’intégration au bouton de GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > L’archivage à partir d’Azure Automation remplace le code qui existe actuellement dans votre contrôle de code source. L’instruction de ligne de commande équivalant à Git pour l’archivage est la suivante : **git add + git commit + git push**  

3. Lorsque vous cliquez sur **Archiver**, un message de confirmation s’affiche. Cliquez sur **Oui** pour continuer.  

    ![Une boîte de dialogue confirmant l’archivage pour le contrôle de code source](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. L’archivage démarre le runbook de contrôle de code source : **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Ce runbook se connecte à GitHub et transmet les modifications d’Azure Automation à votre référentiel. Pour afficher l’historique des travaux archivé, revenez à l’onglet **Intégration du contrôle de code source**, puis cliquez pour ouvrir la page Synchronisation du référentiel. Cette page affiche tous vos travaux de contrôle de code source.  Sélectionnez la tâche à afficher et cliquez dessus pour afficher les détails.  

    ![Une fenêtre qui affiche les résultats d’une tâche de synchronisation](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Les runbooks de contrôle de code source sont des runbooks Automation spéciaux que vous ne pouvez pas afficher ni modifier. Ils n’apparaîtront pas dans votre liste de runbooks, mais les travaux de synchronisation s’afficheront dans votre liste de travaux.

5. Le nom du runbook modifié est envoyé en tant que paramètre d’entrée au runbook archivé. Vous pouvez [afficher les détails du travail](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) en développant le runbook dans la page **Synchronisation du référentiel**.  

    ![Une fenêtre de l’entrée pour une tâche de synchronisation](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Actualisez votre référentiel GitHub une fois la tâche terminée pour afficher les modifications.  Votre dépôt doit intégrer une validation avec un message de validation : ***Nom du runbook* mis à jour dans Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synchroniser des runbooks à partir du contrôle de code source vers Azure Automation

Le bouton de synchronisation de la page Synchronisation du référentiel vous permet d’extraire tous les runbooks dans le chemin d’accès au dossier de runbooks de votre référentiel vers votre compte Automation. Le même référentiel peut être synchronisé vers plusieurs comptes Automation. Voici les étapes permettant de synchroniser un runbook :

1. À partir du compte Automation dans lequel vous avez configuré le contrôle de code source, ouvrez la page **Intégration du contrôle de code source/Synchronisation du référentiel** et cliquez sur **Synchroniser**.  Un message de confirmation s’affiche. Cliquez sur **Oui** pour continuer.  

    ![Bouton de synchronisation avec un message confirme que tous les runbooks est synchronisé.](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. La synchronisation démarre le runbook : **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Ce runbook se connecte à GitHub et extrait les modifications de votre référentiel vers Azure Automation. Vous devez voir une nouvelle tâche dans la page **Synchronisation du référentiel** de cette action. Pour afficher plus d’informations sur le travail de synchronisation, cliquez pour ouvrir la page des détails du travail.  

    ![Une fenêtre qui affiche les résultats de la synchronisation d’un travail de synchronisation sur un référentiel GitHub](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Une synchronisation à partir du contrôle de code source remplace les brouillons de runbooks qui existent actuellement dans votre compte Automation pour **TOUS** les runbooks actuellement dans le contrôle de code source. L’instruction de ligne de commande équivalant à Git pour la synchronisation est la suivante : **git pull**

![Une fenêtre qui affiche tous les journaux à partir d’une tâche de synchronisation de contrôle source suspendu](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Déconnexion du contrôle de code source

Pour vous déconnecter de votre compte GitHub, ouvrez la page Synchronisation du référentiel et cliquez sur **Déconnecter**. Une fois que vous déconnectez le contrôle de code source, les runbooks précédemment synchronisés figurent toujours dans votre compte Automation, mais la page Synchronisation du référentiel n’est pas activée.  

  ![Une fenêtre montrant le bouton de déconnexion pour déconnecter le contrôle de code source](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’informations sur le contrôle de code source, consultez les ressources suivantes :  

* [Azure Automation : Intégration du contrôle de code source dans Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Voter pour votre système de contrôle de code source préféré](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Azure Automation : Intégration du contrôle de code source de runbook à l’aide d’Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
