---
title: "Créer une application web Node.js dans un conteneur Linux dans Azure | Microsoft Docs"
description: "Déployez votre premier programme Hello World Node.js dans Azure App Service Web Apps en quelques minutes."
services: app-service\web
documentationcenter: 
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/05/2017
ms.author: cfowler;cephalin
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 8dba82b37a1584199f6a16cd2b05129e2adca276
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---
# <a name="create-a-nodejs-web-app-in-a-linux-container-in-azure"></a>Créer une application web Node.js dans un conteneur Linux dans Azure

[Web Apps for Containers](app-service-linux-intro.md) est un service d’hébergement web hautement scalable qui applique automatiquement des mises à jour correctives et utilise le système d’exploitation Linux. Ce guide de démarrage rapide vous montre comment déployer une application Node.js dans Azure Web Apps for Containers. Vous allez créer l’application web à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) et utiliser Git pour déployer le code Node.js dans l’application web.

![Exemple d’application s’exécutant dans Azure](media/quickstart-nodejs/hello-world-in-browser.png)

Vous pouvez suivre les étapes ci-dessous en utilisant un ordinateur Mac, Windows ou Linux.

## <a name="prerequisites"></a>Composants requis

Pour effectuer ce démarrage rapide :

* [Installez Git](https://git-scm.com/)
* [Installez Node.js et NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Téléchargez l’exemple

Dans une fenêtre de terminal sur votre machine, exécutez la commande ci-après pour cloner le dépôt de l’exemple d’application sur votre machine locale.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Vous utilisez cette fenêtre de terminal pour exécuter toutes les commandes de ce guide de démarrage rapide.

Passez au répertoire qui contient l’exemple de code.

```bash
cd nodejs-docs-hello-world
```

## <a name="run-the-app-locally"></a>Exécutez l’application localement.

Exécutez l’application localement en ouvrant une fenêtre de terminal et en utilisant le script `npm start` pour lancer le serveur HTTP Node.js intégré.

```bash
npm start
```

Ouvrez un navigateur web et accédez à l’exemple d’application à l’adresse http://localhost:1337.

Vous voyez apparaître sur la page le message **Hello World** de l’exemple d’application.

![Exemple d’application s’exécutant localement](media/quickstart-nodejs/localhost-hello-world-in-browser.png)

Dans la fenêtre de terminal, appuyez sur **Ctrl + C** pour quitter le serveur web.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Créer une application web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-linux-nodejs-no-h.md)]

Accédez au site pour voir votre nouvelle application web. Remplacez _&lt;app name>_ par un nom d’application unique.

```bash
http://<app name>.azurewebsites.net
```

![Page d’application web vide](media/quickstart-nodejs/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)]

```bash
Counting objects: 23, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (23/23), 3.71 KiB | 0 bytes/s, done.
Total 23 (delta 8), reused 7 (delta 1)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'bf114df591'.
remote: Generating deployment script.
remote: Generating deployment script for node.js Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.js'
remote: Copying file: 'package.json'
remote: Copying file: 'process.json'
remote: Deleting file: 'hostingstart.html'
remote: Ignoring: .git
remote: Using start-up script index.js from package.json.
remote: Node.js versions available on the platform are: 4.4.7, 4.5.0, 6.2.2, 6.6.0, 6.9.1.
remote: Selected node.js version 6.9.1. Use package.json file to choose a different version.
remote: Selected npm version 3.10.8
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net:443/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée à l’aide de votre navigateur web.

```bash
http://<app_name>.azurewebsites.net
```

L’exemple de code Node.js s’exécute dans une application web Azure App Service.

![Exemple d’application s’exécutant dans Azure](media/quickstart-nodejs/hello-world-in-browser.png)

**Félicitations !** Vous avez déployé votre première application Node.js dans App Service.

## <a name="update-and-redeploy-the-code"></a>Mettre à jour et redéployer le code

À l’aide d’un éditeur de texte, ouvrez le fichier `index.js` dans l’application Node.js et apportez une petite modification au texte contenu dans l’appel pour `response.end` :

```nodejs
response.end("Hello Azure!");
```

Validez vos modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git commit -am "updated output"
git push azure master
```

Une fois le déploiement terminé, revenez à la fenêtre du navigateur ouverte à l’étape **Accéder à l’application**, puis cliquez sur Actualiser.

![Mise à jour de l’exemple d’application s’exécutant dans Azure](media/quickstart-nodejs/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Gérer votre nouvelle application web Azure

Accédez au <a href="https://portal.azure.com" target="_blank">Portail Azure</a> pour gérer l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

Vous voyez apparaître la page Vue d’ensemble de votre application web. Ici, vous pouvez également des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). 

![Page App Service du Portail Azure](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

Le menu de gauche fournit différentes pages vous permettant de configurer votre application. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Node.js avec MongoDB](tutorial-nodejs-mongodb-app.md)

