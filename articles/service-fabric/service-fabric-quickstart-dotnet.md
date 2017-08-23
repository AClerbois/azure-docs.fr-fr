---
title: "Créer une application Service Fabric .NET dans Azure | Microsoft Docs"
description: "Créez une application .NET pour Azure à l’aide de l’exemple de démarrage rapide de Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 530749275b720caefd7e7e57291b4bc0d313faf0
ms.contentlocale: fr-fr
ms.lasthandoff: 08/10/2017

---

# <a name="create-a-net-service-fabric-application-in-azure"></a>Créer une application Service Fabric dans Azure
Azure Service Fabric est une plateforme de systèmes distribués pour le déploiement et la gestion de microservices et conteneurs extensibles et fiables. 

Ce démarrage rapide montre comment déployer votre première application .NET sur Service Fabric. Lorsque vous avez terminé, vous disposez d’une application de vote avec un composant web frontal ASP.NET Core qui enregistre les résultats de vote dans un service principal avec état dans le cluster.

![Capture d’écran de l’application](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Cette application vous permet d’apprendre à :
> [!div class="checklist"]
> * Créer une application à l’aide de .NET et Service Fabric
> * Utiliser ASP.NET Core en tant que service frontal web
> * Stocker des données d’application dans un service avec état
> * Déboguez votre application localement
> * Déployer l’application sur un cluster dans Azure
> * Mettre à l’échelle avec une montée en puissance parallèle sur plusieurs nœuds
> * Effectuer une mise à niveau propagée d’application

## <a name="prerequisites"></a>Composants requis
Pour effectuer ce démarrage rapide :
1. [Installez Visual Studio 2017](https://www.visualstudio.com/) avec les charges de travail **Développement Azure** et **Développement web et ASP.NET**.
2. [Installez Git](https://git-scm.com/)
3. [Installez le Kit de développement logiciel (SDK) Microsoft Azure Service Fabric](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. Exécutez la commande suivante pour permettre à Visual Studio de déployer sur le cluster Service Fabric local :
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
    ```

## <a name="download-the-sample"></a>Téléchargez l’exemple
Dans une fenêtre Commande, exécutez la commande suivante pour cloner le référentiel de l’exemple d’application sur votre ordinateur local.
```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>Exécution locale de l'application
Dans le Menu Démarrer, cliquez avec le bouton droit sur l’icône Visual Studio, puis choisissez **Exécuter en tant qu’administrateur**. Pour attacher le débogueur à vos services, vous devez exécuter Visual Studio en tant qu’administrateur.

Ouvrez la solution Visual Studio **Voting.sln** à partir du référentiel que vous avez cloné.

Pour déployer l’application, appuyez sur **F5**.

> [!NOTE]
> La première fois que vous exécutez et déployez l’application, Visual Studio crée un cluster local pour le débogage. Cette opération peut prendre un certain temps. L’état de la création du cluster est affiché dans la fenêtre Sortie Visual Studio.

Une fois le déploiement terminé, lancez un navigateur et ouvrez cette page : `http://localhost:8080` - service frontal web de l’application.

![Frontal de l’application](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

Vous pouvez à présent ajouter un ensemble d’options de vote et commencer à enregistrer les votes. L’application s’exécute et stocke toutes les données dans votre cluster Service Fabric, sans recourir à une base de données séparée.

## <a name="walk-through-the-voting-sample-application"></a>Guide de l’exemple d’application de vote
L’application de vote se compose de deux services :
- Service frontal web (VotingWeb) : service frontal web ASP.NET Core qui fait office de page web et expose les API web pour communiquer avec le service principal.
- Service principal (VotingData) : service web ASP.NET Core qui expose une API pour stocker les résultats de vote dans un dictionnaire fiable rendu persistant sur disque.

![Diagramme de l’application](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Lorsque vous votez dans l’application, les événements suivants se produisent :
1. Un JavaScript envoie la demande de vote à l’API web dans le service frontal web en tant que demande HTTP PUT.

2. Le service frontal web utilise un proxy pour localiser et transférer une demande HTTP PUT au service principal.

3. Le service principal accepte la demande entrante, puis stocke le résultat mis à jour dans un dictionnaire fiable, qui est répliqué sur plusieurs nœuds au sein du cluster et rendu persistant sur disque. Toutes les données de l’application étant stockées dans le cluster, aucune base de données n’est nécessaire.

## <a name="debug-in-visual-studio"></a>Déboguer dans Visual Studio
Lors du débogage d’application dans Visual Studio, vous utilisez un cluster de développement Service Fabric local. Vous avez la possibilité d’adapter votre expérience de débogage à votre scénario. Dans cette application, nous stockons les données dans notre service principal à l’aide d’un dictionnaire fiable. Visual Studio supprime l’application par défaut lorsque vous arrêtez le débogueur. La suppression de l’application a pour effet de supprimer également les données dans le service principal. Pour rendre les données persistantes entre les sessions de débogage, vous pouvez modifier le **Mode de débogage de l'application** en tant que propriété sur le projet **Voting** dans Visual Studio.

Pour examiner ce qui se produit dans le code, procédez comme suit :
1. Ouvrez le fichier **VotesController.cs** et définissez un point d’arrêt dans la méthode **Put** de l’API web (ligne 47). Vous pouvez rechercher le fichier dans l’Explorateur de solutions dans Visual Studio.

2. Ouvrez le fichier **VoteDataController.cs** et définissez un point d’arrêt dans la méthode **Put** de l’API web (ligne 50).

3. Revenez en arrière dans le navigateur, puis cliquez sur une option de vote ou ajoutez une nouvelle option de vote. Vous avez atteint le premier point d’arrêt dans le contrôleur d’api du service frontal web.
    - Il s’agit de l’emplacement où le JavaScript dans le navigateur envoie une demande au contrôleur d’API web dans le service frontal.
    
    ![Ajouter un service de vote frontend](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

    - Nous allons d’abord créer l’URL du ReverseProxy pour notre service backend **(1)**.
    - Puis, nous allons envoyer la demande HTTP PUT pour le ReverseProxy **(2)**.
    - Enfin, nous allons retourner la réponse du service backend au client **(3)**.

4. Appuyez sur **F5** pour continuer.
    - Vous êtes à présent au point d’arrêt dans le service principal.
    
    ![Ajouter un service de vote backend](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

    - Dans la première ligne de la méthode **(1)**, nous utilisons le `StateManager` pour obtenir ou à ajouter un dictionnaire fiable nommé `counts`.
    - Toutes les interactions avec des valeurs d’un dictionnaire fiable requièrent une transaction. Cette instruction using **(2)** crée cette transaction.
    - Dans la transaction, nous mettons à jour la valeur de la clé appropriée pour l’option de vote et validons l’opération **(3)**. Lorsque la méthode commit retourne des données, celles-ci sont mises à jour dans le dictionnaire et répliquées sur d’autres nœuds du cluster. Les données sont à présent stockées en sécurité dans le cluster, et le service principal peut basculer vers d’autres nœuds, tout en gardant les données disponibles.
5. Appuyez sur **F5** pour continuer.

Pour arrêter la session de débogage, appuyez sur **Maj+F5**.

## <a name="deploy-the-application-to-azure"></a>Déploiement de l'application dans Azure
Pour déployer l’application sur un cluster dans Azure, vous pouvez choisir de créer votre propre cluster ou d’utiliser un cluster tiers.

Les clusters tiers sont des clusters Service Fabric gratuits et limités dans le temps, hébergés sur Azure et gérés par l’équipe Service Fabric, où chacun peut déployer des applications et découvrir la plateforme. Pour obtenir l’accès à un cluster tiers, [suivez ces instructions](http://aka.ms/tryservicefabric). 

Pour plus d’informations sur la création de votre propre cluster, voir [Créer votre premier cluster Service Fabric sur Azure](service-fabric-get-started-azure-cluster.md).

> [!Note]
> Le service frontal web est configuré pour écouter le trafic entrant sur le port 8080. Assurez-vous que ce port est ouvert dans votre cluster. Si vous utilisez le cluster tiers, ce port est ouvert.
>

### <a name="deploy-the-application-using-visual-studio"></a>Déployer l’application à l’aide de Visual Studio
À présent que l’application est prête, vous pouvez la déployer sur un cluster directement à partir de Visual Studio.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Voting**, puis choisissez **Publier**. La boîte de dialogue Publier s’affiche.

    ![Boîte de dialogue Publier](./media/service-fabric-quickstart-dotnet/publish-app.png)

2. Saisissez le point de terminaison de connexion du cluster dans le champ **Point de terminaison de connexion**, puis cliquez sur **Publier**. Lors de l’inscription pour le cluster tiers, le point de terminaison de connexion est fourni dans le navigateur. - par exemple, `winh1x87d1d.westus.cloudapp.azure.com:19000`.

3. Ouvrez un navigateur et entrez l’adresse du cluster, par exemple, `http://winh1x87d1d.westus.cloudapp.azure.com`. Vous devez à présent voir l’application en cours d’exécution dans le cluster dans Azure.

![Frontal de l’application](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Mettre à l’échelle les applications et services dans un cluster
Les services Service Fabric peuvent facilement être mis à l’échelle dans un cluster pour prendre en compte une modification de la charge sur les services. Pour mettre à l’échelle un service, vous modifiez le nombre d’instances s’exécutant dans le cluster. Vous avez plusieurs moyens de mettre à l’échelle vos services. Vous pouvez utiliser des scripts ou des commandes de PowerShell ou d’Azure CLI 2.0. Dans cet exemple, nous utilisons Service Fabric Explorer.

Service Fabric Explorer s’exécute dans tous les clusters Service Fabric et est accessible à partir d’un navigateur, en accédant au port de gestion HTTP des clusters (19080), par exemple, `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.

Pour mettre à l’échelle le service frontal web, procédez comme suit :

1. Ouvrez Service Fabric Explorer dans votre cluster. Par exemple, `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Cliquez sur le bouton de sélection (points de suspension) à côté du nœud **fabric:/Voting/VotingWeb** dans l’arborescence, puis choisissez **Scale Service** (Mettre à l’échelle le service).

    ![Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scale.png)

    Vous pouvez maintenant choisir de mettre à l’échelle le nombre d’instances du service frontal web.

3. Définissez le nombre sur **2**, puis cliquez sur **Scale Service** (Mettre à l’échelle le service).
4. Cliquez sur le nœud **fabric:/Voting/VotingWeb** dans l’arborescence, puis développez le nœud de la partition (représenté par un GUID).

    ![Mettre à l’échelle le service dans Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scaled-service.png)

    Vous pouvez maintenant voir que le service a deux instances et, dans l’arborescence, vous voyez les nœuds sur lesquels les instances s’exécutent.

Par cette tâche de gestion simple, nous avons doublé les ressources disponibles pour permettre à notre service frontal de traiter la charge utilisateur. Il est important de comprendre que vous n’avez pas besoin de plusieurs instances d’un service pour que celui-ci s’exécute de manière fiable. En cas de défaillance d’un service, Service Fabric veille à ce qu'une nouvelle instance du service s’exécute dans le cluster.

## <a name="perform-a-rolling-application-upgrade"></a>Effectuer une mise à niveau propagée d’application
Lors du déploiement de nouvelles mises à jour de votre application, Service Fabric procède au déploiement en toute sécurité. Les mises à niveau propagées n’occasionnent aucun temps d'arrêt, et assurent une restauration automatique en cas d’erreur.

Pour mettre à niveau l'application, procédez comme suit :

1. Ouvrez le fichier **Index.cshtml** dans Visual Studio. Vous pouvez rechercher le fichier dans l’Explorateur de solutions de Visual Studio.
2. Modifiez le titre sur la page en ajoutant du texte. Par exemple :
    ```html
        <div class="col-xs-8 col-xs-offset-2 text-center">
            <h2>Service Fabric Voting Sample v2</h2>
        </div>
    ```
3. Enregistrez le fichier .
4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Voting**, puis choisissez **Publier**. La boîte de dialogue Publier s’affiche.
5. Cliquez sur le bouton **Version du manifeste** pour modifier la version du service et de l’application.
6. Modifiez la version de l’élément **Code** sous **VotingWebPkg**, par exemple sur « 2.0.0 », puis cliquez sur **Enregistrer**.

    ![Boîte de dialogue Modifier la version](./media/service-fabric-quickstart-dotnet/change-version.png)
7. Dans la boîte de dialogue **Publier une application Service Fabric**, activez la case à cocher Mettre à niveau l'application, puis cliquez sur **Publier**.

    ![Paramètre Mettre à niveau dans la boîte de dialogue Publier](./media/service-fabric-quickstart-dotnet/upgrade-app.png)
8. Ouvrez votre navigateur et accédez à l’adresse de cluster sur le port 19080, par exemple, `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.
9. Cliquez sur le nœud **Applications** dans l’arborescence, puis sur **Mises à niveau en cours d’exécution** dans le volet droit. Vous voyez comment la mise à niveau se déroule au travers des domaines de mise à niveau dans votre cluster, en s’assurant que chaque domaine est intègre avant de passer au suivant.
    ![Vue Mise à niveau dans mise à niveau de logiciel](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Service Fabric sécurise les mises à niveau en attendant deux minutes après la mise à niveau du service sur chaque nœud du cluster. La mise à jour entière prend environ huit minutes.

10. Pendant l’exécution de la mise à niveau, vous pouvez continuer à utiliser l’application. Étant donné que vous avez deux instances du service en cours d’exécution dans le cluster, certaines de vos demandes peuvent obtenir une version mise à niveau de l’application, et d’autres utilisateurs l’ancienne version.

## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez appris comment :

> [!div class="checklist"]
> * Créer une application à l’aide de .NET et Service Fabric
> * Utiliser ASP.NET Core en tant que service frontal web
> * Stocker des données d’application dans un service avec état
> * Déboguez votre application localement
> * Déployer l’application sur un cluster dans Azure
> * Mettre à l’échelle avec une montée en puissance parallèle sur plusieurs nœuds
> * Effectuer une mise à niveau propagée d’application

Pour plus d’informations sur Service Fabric et .NET, suivez ce didacticiel :
> [!div class="nextstepaction"]
> [Application .NET sur Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
