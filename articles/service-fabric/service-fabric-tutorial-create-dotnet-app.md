---
title: "Créer une application .NET pour Service Fabric | Microsoft Docs"
description: "Découvrez comment créer une application avec un service frontal ASP.NET Core et un service principal fiable avec état, et déployer l’application sur un cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 5a095663b7e716fd63322c9f89f67a1f3187638b
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2017
---
# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Créer et déployer une application avec un service frontal API Web ASP.NET Core et un service principal avec état
Ce didacticiel est la première partie d’une série d’étapes.  Vous allez découvrir comment créer une application Azure Service Fabric avec un service frontal API Web ASP.NET Core et un service principal avec état pour stocker vos données. Lorsque vous avez terminé, vous disposez d’une application de vote avec un composant web frontal ASP.NET Core qui enregistre les résultats de vote dans un service principal avec état dans le cluster. Si vous ne souhaitez pas créer l’application de vote manuellement, vous pouvez [télécharger le code source](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) pour obtenir l’application terminée et passer directement au [Guide de l’exemple d’application de vote](#walkthrough_anchor).

![Diagramme de l’application](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Dans ce premier volet, vous apprenez à :

> [!div class="checklist"]
> * Créer un service API Web ASP.NET Core en tant que service fiable avec état
> * Créer un service d’application Web ASP.NET Core en tant que service web sans état
> * Utiliser le proxy inverse pour communiquer avec le service avec état

Cette série de didacticiels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer une application .NET Service Fabric
> * [Déployer l’application sur un cluster distant](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurer l’intégration et le déploiement continus à l’aide de Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurer la surveillance et les diagnostics pour l’application](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Installez Visual Studio 2017](https://www.visualstudio.com/) et les charges de travail **Développement Azure** et **Développement web et ASP.NET**.
- [Installez le kit SDK Service Fabric](service-fabric-get-started.md).

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Créer un service API Web ASP.NET en tant que service fiable
Commencez par créer le web frontal de l’application de vote à l’aide d’ASP.NET Core. ASP.NET Core est une infrastructure légère de développement web inter-plateformes, que vous pouvez utiliser pour créer des API web et d’interfaces utilisateur web modernes. Pour bien comprendre comment ASP.NET Core s’intègre avec Service Fabric, il est vivement recommandé de lire l’article [ASP.NET Core dans le modèle Reliable Services de Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md). Pour l’instant, vous pouvez suivre ce didacticiel pour démarrer rapidement. Pour en savoir plus sur ASP.NET Core, consultez la [Documentation d’ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

> [!NOTE]
> Ce didacticiel est basé sur les [outils ASP.NET Core pour Visual Studio 2017](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc). Les outils .NET Core pour Visual Studio 2015 ne sont plus mis à jour.

1. Lancez Visual Studio en tant qu’**administrateur**.

2. Créez un projet en sélectionnant **Fichier**->**Nouveau**->**Projet**.

3. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Cloud > Application Service Fabric**.

4. Nommez l’application **Voting**, puis cliquez sur **OK**.

   ![Boîte de dialogue Nouveau projet dans Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Dans la page **Nouveau service Service Fabric**, choisissez **ASP.NET Core sans état** et nommez votre service **VotingWeb**.
   
   ![Choix du service web ASP.NET dans la boîte de dialogue Nouveau service](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. La page suivante fournit un ensemble de modèles de projets ASP.NET Core. Pour ce didacticiel, sélectionnez **Application web**. 
   
   ![Choisir le type de projet ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio crée une application et un projet de service, et les affiche dans l’Explorateur de solutions.

   ![Explorateur de solutions après la création de l’application avec un service API Web ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>Ajouter AngularJS au service VotingWeb
Ajoutez [AngularJS](http://angularjs.org/) à votre service à l’aide de la fonction [Assistant Bower](/aspnet/core/client-side/bower) intégrée. Ouvrez *bower.json* et ajoutez des entrées pour angular et angular-bootstrap, puis enregistrez vos modifications.

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "2.2.0",
    "jquery-validation": "1.14.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.5",
    "angular-bootstrap": "v1.1.0"
  }
}
```
Lors de l’enregistrement du fichier *bower.json*, Angular est installé dans votre dossier de projet *wwwroot/lib*. En outre, il est répertorié dans le dossier *Dependencies/Bower*.

### <a name="update-the-sitejs-file"></a>Mettre à jour le fichier site.js
Ouvrez le fichier *wwwroot/js/site.js*.  Remplacez son contenu par le code JavaScript utilisé par les vues d’accueil :

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Mettre à jour le fichier Index.cshtml
Ouvrez le fichier *Views/Home/Index.cshtml*. Il s’agit de la vue spécifique au contrôleur Home.  Remplacez son contenu par la commande suivante, puis enregistrez vos modifications.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>Mettre à jour le fichier layout.cshtml
Ouvrez le fichier *Views/Shared/_Layout.cshtml*. Il s’agit de la disposition par défaut pour l’application ASP.NET.  Remplacez son contenu par la commande suivante, puis enregistrez vos modifications.

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="~/css/site.css" rel="stylesheet" />

</head>
<body>
    <div class="container body-content">
        @RenderBody()
    </div>

    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/lib/angular/angular.js"></script>
    <script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
    <script src="~/js/site.js"></script>

    @RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Mettre à jour le fichier VotingWeb.cs
Ouvrez le fichier *VotingWeb.cs*. Il crée le WebHost Core ASP.NET dans le service sans état à l’aide du serveur web WebListener.  Ajoutez la directive `using System.Net.Http;` au début du fichier.  Remplacez la fonction `CreateServiceInstanceListeners()` par la commande suivante, puis enregistrez vos modifications.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
            {
                ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting WebListener on {url}");

                return new WebHostBuilder().UseWebListener()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<StatelessServiceContext>(serviceContext)
                                    .AddSingleton<HttpClient>())
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseApplicationInsights()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
            }))
    };
}
```

### <a name="add-the-votescontrollercs-file"></a>Ajouter le fichier VotesController.cs
Ajoutez un contrôleur qui définit les actions de vote. Cliquez avec le bouton droit sur le dossier **Contrôleurs** et sélectionnez **Ajouter -> Nouvel élément -> Classe**.  Nommez le fichier « VotesController.cs » et cliquez sur **Ajouter**.  Remplacez le contenu du fichier par la commande suivante, puis enregistrez vos modifications.  Plus tard, dans [Mettre à jour le fichier VotesController.cs](#updatevotecontroller_anchor), ce fichier est modifié pour lire et écrire des données de vote à partir du service principal.  Pour l’instant, le contrôleur retourne des données de chaîne statique à la vue.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using Newtonsoft.Json;
using System.Text;
using System.Net.Http;
using System.Net.Http.Headers;

namespace VotingWeb.Controllers
{
    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```



### <a name="deploy-and-run-the-application-locally"></a>Déployer et exécuter l’application localement
Vous pouvez maintenant continuer et exécuter l’application. Appuyez sur `F5` dans Visual Studio pour déployer l’application en vue d’un débogage. La commande `F5` échoue si vous n’avez pas précédemment ouvert Visual Studio en tant qu’**administrateur**.

> [!NOTE]
> La première fois que vous exécutez et déployez l’application localement, Visual Studio crée un cluster local pour le débogage.  La création de cluster peut prendre un certain temps. L’état de la création du cluster est affiché dans la fenêtre Sortie Visual Studio.

À ce stade, votre application web doit ressembler à ceci :

![ASP.NET Core frontal](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Pour arrêter le débogage de l’application, revenez à Visual Studio et appuyez sur **Maj + F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Ajouter un service principal avec état à votre application
Maintenant qu’un service API Web ASP.NET est exécuté dans notre application, ajoutons un service fiable avec état pour stocker des données dans notre application.

Service Fabric permet de stocker de manière cohérente et fiable vos données directement dans votre service à l’aide de collections fiables. Les collections fiables sont un ensemble de classes de collections hautement disponibles et fiables avec lesquelles tous ceux qui ont utilisé les collections C# sont familiers.

Dans ce didacticiel, vous créez un service qui stocke une valeur de compteur dans une collection fiable.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Services** dans le projet d’application et choisissez **Ajouter > Nouveau Service Fabric**.
   
    ![Ajout d’un nouveau service à une application existante](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. Dans la boîte de dialogue **Nouveau service Service Fabric**, choisissez **ASP.NET Core avec état**, nommez le service **VotingData**, puis appuyez sur **OK**.

    ![Boîte de dialogue Nouveau service dans Visual Studio.](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Une fois votre projet de service créé, votre application contient deux services. À mesure que vous continuez à développer votre application, vous pouvez ajouter d’autres services de la même façon. Chacun peut être mis à niveau et faire l'objet d'un contrôle de version indépendamment.

3. La page suivante fournit un ensemble de modèles de projets ASP.NET Core. Pour ce didacticiel, sélectionnez **API web**.

    ![Choisir le type de projet ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Visual Studio crée un projet de service et l’affiche dans l’Explorateur de solutions.

    ![Explorateur de solutions](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Ajouter le fichier VoteDataController.cs

Dans le projet **VotingData**, cliquez avec le bouton droit sur le dossier **Contrôleurs** et sélectionnez **Ajouter -> Nouvel élément -> Classe**. Nommez le fichier « VoteDataController.cs » et cliquez sur **Ajouter**. Remplacez le contenu du fichier par la commande suivante, puis enregistrez vos modifications.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.ServiceFabric.Data;
using System.Threading;
using Microsoft.ServiceFabric.Data.Collections;

namespace VotingData.Controllers
{
    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            var ct = new CancellationToken();

            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                var list = await votesDictionary.CreateEnumerableAsync(tx);

                var enumerator = list.GetAsyncEnumerator();

                var result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```


## <a name="connect-the-services"></a>Connecter les services
Dans l’étape qui suit, nous allons connecter les deux services et faire en sorte que l’application Web frontale obtienne et définisse les informations de vote à partir du service principal.

Service Fabric fournit une flexibilité complète sur votre façon de communiquer avec Reliable Services. Dans une même application, vous pouvez avoir des services qui sont accessibles via TCP, d’autres services accessibles via une API REST HTTP et encore d’autres services accessibles via des sockets web. Pour obtenir des informations sur les options disponibles et leurs avantages/inconvénients respectifs, consultez [Communication avec les services](service-fabric-connect-and-communicate-with-services.md).

Dans ce didacticiel, nous utilisons [l’API Web ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Mettre à jour le fichier VotesController.cs
Dans le projet **VotingWeb**, ouvrez le fichier *Controllers/VotesController.cs*.  Remplacez le contenu de définition de classe `VotesController` par la commande suivante, puis enregistrez vos modifications.

```csharp
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;
        string serviceProxyUrl = "http://localhost:19081/Voting/VotingData/api/VoteData";
        string partitionKind = "Int64Range";
        string partitionKey = "0";

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            IEnumerable<KeyValuePair<string, int>> votes;

            HttpResponseMessage response = await this.httpClient.GetAsync($"{serviceProxyUrl}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            votes = JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync());

            return Json(votes);
        }

        // PUT: api/Votes/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            string payload = $"{{ 'name' : '{name}' }}";
            StringContent putContent = new StringContent(payload, Encoding.UTF8, "application/json");
            putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

            string proxyUrl = $"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}";

            HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent);

            return new ContentResult()
            {
                StatusCode = (int)response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }

        // DELETE: api/Votes/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            HttpResponseMessage response = await this.httpClient.DeleteAsync($"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            return new OkResult();

        }
    }
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Guide de l’exemple d’application de vote
L’application de vote se compose de deux services :
- Service frontal web (VotingWeb) : service frontal web ASP.NET Core qui fait office de page web et expose les API web pour communiquer avec le service principal.
- Service principal (VotingData) : service web ASP.NET Core qui expose une API pour stocker les résultats de vote dans un dictionnaire fiable rendu persistant sur disque.

![Diagramme de l’application](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

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
    
    1. Il s’agit de l’emplacement où le JavaScript dans le navigateur envoie une demande au contrôleur d’API web dans le service frontal.
    
    ![Ajouter un service de vote frontend](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. Nous allons d’abord créer l’URL du ReverseProxy pour notre service backend **(1)**.
    3. Puis, nous allons envoyer la demande HTTP PUT pour le ReverseProxy **(2)**.
    4. Enfin, nous allons retourner la réponse du service backend au client **(3)**.

4. Appuyez sur **F5** pour continuer.
    1. Vous êtes à présent au point d’arrêt dans le service principal.
    
    ![Ajouter un service de vote backend](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. Dans la première ligne de la méthode **(1)**, nous utilisons le `StateManager` pour obtenir ou à ajouter un dictionnaire fiable nommé `counts`.
    3. Toutes les interactions avec des valeurs d’un dictionnaire fiable requièrent une transaction. Cette instruction using **(2)** crée cette transaction.
    4. Dans la transaction, nous mettons à jour la valeur de la clé appropriée pour l’option de vote et validons l’opération **(3)**. Lorsque la méthode commit retourne des données, celles-ci sont mises à jour dans le dictionnaire et répliquées sur d’autres nœuds du cluster. Les données sont à présent stockées en sécurité dans le cluster, et le service principal peut basculer vers d’autres nœuds, tout en gardant les données disponibles.
5. Appuyez sur **F5** pour continuer.

Pour arrêter la session de débogage, appuyez sur **Maj+F5**.


## <a name="next-steps"></a>Étapes suivantes
Dans cette partie du didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un service API Web ASP.NET Core en tant que service fiable avec état
> * Créer un service d’application Web ASP.NET Core en tant que service web sans état
> * Utiliser le proxy inverse pour communiquer avec le service avec état

Passez au didacticiel suivant :
> [!div class="nextstepaction"]
> [Déploiement de l’application dans Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
