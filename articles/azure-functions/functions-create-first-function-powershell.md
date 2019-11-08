---
title: Créer votre première fonction PowerShell dans Azure
description: Découvrez comment créer votre première fonction PowerShell dans Azure à l’aide de Visual Studio Code.
author: joeyaiello
manager: gwallace
ms.author: jaiello
ms.reviewer: glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.openlocfilehash: 1d6d641e141862b12fed40b800589aad70af2789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469415"
---
# <a name="create-your-first-powershell-function-in-azure"></a>Créer votre première fonction PowerShell dans Azure

Ce guide de démarrage rapide vous explique comment créer votre première fonction PowerShell [serverless](https://azure.com/serverless) à l’aide de Visual Studio Code.

![Code Azure Functions dans un projet Visual Studio Code](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

L’[extension Azure Functions pour Visual Studio Code] vous permet de créer une fonction PowerShell localement et de la déployer dans une nouvelle application de fonction dans Azure. L’extension est actuellement en préversion. Pour plus d’informations, consultez la page d’extension [Extension Azure Functions pour Visual Studio Code].

> [!NOTE]  
> La prise en charge de PowerShell pour l’[extension Azure Functions][extension Azure Functions pour Visual Studio Code] est actuellement désactivée par défaut. L’activation de la prise en charge de PowerShell est l’une des étapes de cet article.

Les étapes suivantes sont prises en charge sur les systèmes d’exploitation macOS, Windows et Linux.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide :

* Installez [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows).

* Installer [Visual Studio Code](https://code.visualstudio.com/) sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Installez l’[extension PowerShell pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Installez le [SDK .NET Core 2.2+](https://www.microsoft.com/net/download) (requis par Azure Functions Core Tools et disponible sur toutes les plateformes prises en charge).

* Installez la version 2.x d’[Azure Functions Core Tools](functions-run-local.md#v2).

* Vous avez également besoin d’un abonnement Azure actif.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Créer un projet d’application de fonction

Le modèle de projet Azure Functions dans Visual Studio Code crée un projet qui peut être publié dans une application de fonction sur Azure. Une application de fonctions vous permet de regrouper les fonctions dans une unité logique pour la gestion, le déploiement et le partage des ressources. 

1. Dans Visual Studio Code, sélectionnez le logo Azure pour afficher la zone **Azure : Fonctions**, puis sélectionnez l’icône Créer un projet.

    ![Créer un projet d’application de fonction](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Choisissez un emplacement pour votre espace de travail de projet Functions et optez pour **Sélectionner**.

    > [!NOTE]
    > Cet article a été conçu pour être terminé en dehors d’un espace de travail. Dans ce cas, ne sélectionnez pas de dossier de projet qui fait partie d’un espace de travail.

1. Sélectionnez **Powershell** comme langage de votre projet d’application de fonction, puis **Azure Functions v2**.

1. Choisissez **Déclencheur HTTP** comme modèle pour votre première fonction, utilisez `HTTPTrigger` comme nom de fonction, puis choisissez le niveau d’autorisation **Fonction**.

    > [!NOTE]
    > Le niveau d’autorisation **Fonction** nécessite une valeur de [clé de fonction](functions-bindings-http-webhook.md#authorization-keys) lors de l’appel du point de terminaison de la fonction dans Azure. Il est donc plus difficile pour un utilisateur d’appeler votre fonction.

1. Lorsque vous y êtes invité, choisissez **ajouter à l’espace de travail**.

Visual Studio Code crée le projet d’application de fonction PowerShell dans un nouvel espace de travail. Ce projet contient les fichiers de configuration [host.json](functions-host-json.md) et [local.settings.json](functions-run-local.md#local-settings-file) qui s’appliquent à toutes les fonctions du projet. Ce [projet PowerShell](functions-reference-powershell.md#folder-structure) est identique à une application de fonction s’exécutant dans Azure.

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Azure Functions Core Tools s’intègre à Visual Studio Code pour vous permettre d’exécuter et de déboguer un projet Azure Functions localement.  

1. Pour déboguer votre fonction, insérez un appel à l’applet de commande [`Wait-Debugger`] dans le code de fonction avant d’attacher le débogueur, puis appuyez sur F5 pour démarrer le projet d’application de fonction et attacher le débogueur. La sortie de Core Tools est affichée dans le panneau **Terminal**.

1. Dans le panneau **Terminal**, copiez le point de terminaison de l’URL de votre fonction déclenchée via HTTP.

    ![Sortie Azure locale](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL, puis utilisez `Invoke-RestMethod` pour exécuter la demande :

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Vous pouvez également exécuter la demande GET à partir d’un navigateur.

    Quand vous appelez le point de terminaison HttpTrigger sans passer `name` comme paramètre de requête ou dans le corps, la fonction retourne une erreur [HttpStatusCode]::BadRequest. Quand vous examinez le code dans run.ps1, vous voyez que cette erreur est voulue.

1. Pour arrêter le débogage, appuyez sur MAJ + F5.

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de publier le projet sur Azure.

> [!NOTE]
> N’oubliez pas de supprimer tous les appels à `Wait-Debugger` avant de publier vos fonctions sur Azure. 
>
> Création d’une application de fonction dans Azure, vous êtes uniquement invité à entrer le nom de votre application de fonction. D’autres valeurs sont définies pour vous.
> Définissez `azureFunctions.advancedCreation` sur `true` pour être invité à entrer toutes les autres valeurs.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Exécuter la fonction dans Azure

Pour vérifier que votre fonction publiée s’exécute dans Azure, exécutez la commande PowerShell suivante en remplaçant le paramètre `Uri` par l’URL de la fonction HTTPTrigger copiée à l’étape précédente. Ajoutez la chaîne de requête `&name=<yourname>` à l’URL, comme dans l’exemple suivant :

```powershell
PS > Invoke-WebRequest -Method Get -Uri "https://glengatest-vscode-powershell.azurewebsites.net/api/HttpTrigger?code=nrY05eZutfPqLo0som...&name=PowerShell"

StatusCode        : 200
StatusDescription : OK
Content           : Hello PowerShell
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 16
                    Content-Type: text/plain; charset=utf-8
                    Date: Thu, 25 Apr 2019 16:01:22 GMT

                    Hello PowerShell
Forms             : {}
Headers           : {[Content-Length, 16], [Content-Type, text/plain; charset=utf-8], [Date, Thu, 25 Apr 2019 16:01:22 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 16
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez utilisé Visual Studio Code pour créer une application de fonction PowerShell avec une fonction simple déclenchée par HTTP. Si vous le souhaitez, vous pouvez en apprendre davantage sur le [débogage local d’une fonction PowerShell](functions-debug-powershell-local.md) à l’aide d’Azure Functions Core Tools. Consultez le [guide du développeur de fonctions Azure avec PowerShell](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Activer l’intégration à Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Extension Azure Functions pour Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[`Wait-Debugger`]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
