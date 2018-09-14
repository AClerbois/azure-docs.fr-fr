---
title: 'Azure Cosmos DB : Développer une application Xamarin.Forms avec .NET et l’API MongoDB | Microsoft Docs'
description: Cet article présente un exemple de code Xamarin que vous pouvez utiliser pour vous connecter à l’API MongoDB d’Azure Cosmos DB, et pour l’interroger.
services: cosmos-db
author: codemillmatt
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quickstart, xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: masoucou
ms.openlocfilehash: 255f23906bc93ce78b28f4f0806d7076a97b0ef2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698687"
---
# <a name="quickstart-build-a-mongodb-api-xamarinforms-app-with-net-and-the-azure-portal"></a>Démarrage rapide : Développer une application Xamarin.Forms API MongoDB avec .NET et le Portail Azure

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB.

Ce guide de démarrage rapide explique comment créer un compte, une base de données de documents et une collection [API MongoDB](mongodb-introduction.md) Azure Cosmos DB à l’aide du portail Azure. Vous allez ensuite créer une application todo Xamarin.Forms à l’aide du [pilote .NET MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Configuration requise pour exécuter l’exemple d’application

Pour exécuter l’exemple, vous devez disposer de [Visual Studio](https://www.visualstudio.com/downloads/) ou [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et d’un compte Azure CosmosDB valide.

Si vous ne disposez pas de Visual Studio, téléchargez [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement mobile ASP.NET** installée au moment de l’installation.

Si vous préférez travailler sur Mac, téléchargez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et exécutez la configuration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

L’exemple décrit dans cet article est compatible avec la version 2.6.1 du pilote MongoDB.Driver.

## <a name="clone-the-sample-app"></a>Clonage de l’exemple d’application

Commencez par télécharger l’exemple d’application API MongoDB à partir de GitHub. Il implémente une application de liste de tâches avec le modèle de stockage de documents de MongoDB.

1. Ouvrez une invite de commandes, créez un nouveau dossier nommé git-samples, puis fermez l’invite de commandes.

    ```bash
    md "C:\git-samples"
    ```

2. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder au nouveau dossier d’installation pour l’exemple d’application.

    ```bash
    cd "C:\git-samples"
    ```

3. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Si vous ne souhaitez pas utiliser git, vous pouvez également [télécharger le projet en tant que fichier .zip](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Pour savoir comment les ressources de base de données sont créées dans le code, vous pouvez examiner les extraits de code suivants. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string).

Les extraits de code suivants sont tirés de la classe `MongoService`, à l’emplacement suivant : src/TaskList.Core/Services/MongoService.cs.

* Initialisez le client Mongo.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Récupérez une référence à la base de données et la collection. Le kit de développement logiciel (SDK) .NET MongoDB crée automatiquement la base de données et la collection si elles n’existent pas déjà.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Récupérez tous les documents en tant que liste.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Recherchez des documents spécifiques.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Créer une tâche et l’insérer dans la collection MongoDB.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Mettre à jour une tâche dans une collection MongoDB.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Supprimer une tâche dans une collection MongoDB.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Dans le [portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation de gauche, cliquez sur **Chaîne de connexion**, puis sur **Clés en lecture-écriture**. Dans les prochaines étapes, vous allez utiliser les boutons de copie sur le côté droit de l’écran pour copier la Chaîne de connexion principale.

2. Ouvrez le fichier **APIKeys.cs** dans le répertoire **Helpers** du projet **TaskList.Core**.

3. Copiez la valeur de votre **chaîne de connexion principale** depuis le portail (avec le bouton copier) et faites-en la valeur du champ **ConnectionString** dans votre fichier **APIKeys.cs**.

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB.

## <a name="run-the-app"></a>Exécution de l'application

### <a name="visual-studio-2017"></a>Visual Studio 2017

1. Dans Visual Studio, cliquez avec le bouton droit sur chaque projet dans l’**Explorateur de solutions**, puis cliquez sur **Gérer les packages NuGet**.
2. Cliquez sur **Restaurer tous les packages NuGet**.
3. Cliquez avec le bouton droit sur le projet **TaskList.Android** puis sélectionnez **Définir comme projet de démarrage**.
4. Appuyez sur F5 pour lancer le débogage de l’application.
5. Si vous voulez exécuter sur iOS, connectez d’abord votre machine à un Mac (voici [comment faire](https://docs.microsoft.com/en-us/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio)).
6. Cliquez avec le bouton droit sur le projet **TaskList.iOS** puis sélectionnez **Définir comme projet de démarrage**.
7. Appuyez sur F5 pour lancer le débogage de l’application.

### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

1. Dans la liste déroulante de la plateforme, sélectionnez TaskList.iOS ou TaskList.Android, en fonction de la plateforme que vous voulez utiliser.
2. Appuyez sur cmd + Entrée pour lancer le débogage de l’application.

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB et à exécuter une application Xamarin.Forms à l’aide de l’API de MongoDB. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB.

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB pour l’API MongoDB](mongodb-migrate.md)