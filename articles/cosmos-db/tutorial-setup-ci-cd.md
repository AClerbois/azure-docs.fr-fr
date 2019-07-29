---
title: Configurer le pipeline CI/CD avec la tâche de génération d’émulateur Azure Cosmos DB
description: Didacticiel sur la configuration des workflows de création et de mise en production dans Azure DevOps à l’aide de la tâche de génération de l’émulateur Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: dad99a7e3d0463263e912aa05b5312edbcb89c0b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597676"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Configurer un pipeline CI/CD avec la tâche de génération d’émulateur Azure Cosmos DB

L’émulateur Azure Cosmos DB fournit un environnement local qui émule le service Azure Cosmos DB à des fins de développement. L’émulateur vous permet de développer et tester votre application localement, sans créer d’abonnement Azure et sans frais. 

La tâche de génération de l’émulateur Azure Cosmos DB pour Azure DevOps permet de faire de même dans un environnement d’intégration continue. Avec la tâche de génération, vous pouvez exécuter des tests sur l’émulateur dans le cadre de vos flux de création et de mise en production. La tâche active un conteneur Docker avec l’émulateur déjà en cours d’exécution et fournit un point de terminaison qui peut être utilisé par le reste de la définition de build. Vous pouvez créer et démarrer autant d’instances de l’émulateur que vous le souhaitez, chacune étant exécutée dans un conteneur distinct. 

Cet article montre comment configurer un pipeline d’intégration continue dans Azure DevOps pour une application ASP.NET qui utilise la tâche de génération d’émulateur Cosmos DB pour exécuter des tests. Vous pouvez utiliser une approche similaire pour configurer un pipeline CI destiné à une application Node.js ou Python. 

## <a name="install-the-emulator-build-task"></a>Installer la tâche de génération d’émulateur

Pour utiliser la tâche de génération, nous devons d’abord l’installer dans notre organisation Azure DevOps. Recherchez l’extension **Azure Cosmos DB Emulator** sur la [Place de marché](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) et cliquez sur **Obtenir gratuitement.**

![Rechercher la tâche de génération de l’émulateur Azure Cosmos DB dans la Place de marché Azure DevOps et l’installer](./media/tutorial-setup-ci-cd/addExtension_1.png)

Ensuite, choisissez l’organisation dans laquelle installer l’extension. 

> [!NOTE]
> Pour installer une extension dans une organisation Azure DevOps, vous devez être propriétaire de compte ou administrateur de collection de projets. Si vous ne disposez pas des autorisations nécessaires, mais que vous êtes un membre de compte, vous pouvez demander des extensions. [En savoir plus.](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts)

![Choisir une organisation Azure DevOps dans laquelle installer une extension](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Créer une définition de build

Maintenant que l’extension est installée, connectez-vous à votre compte Azure DevOps et recherchez votre projet dans le tableau de bord de projets. Vous pouvez ajouter un [pipeline de build](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) à votre projet ou modifier un pipeline de build existant. Si vous disposez déjà d’un pipeline de build, vous pouvez passer directement à la section [Ajouter la tâche de génération d’émulateur à une définition de build](#addEmulatorBuildTaskToBuildDefinition).

1. Pour créer une définition de build, accédez à l’onglet **Builds** dans Azure DevOps. Sélectionnez **+Nouveau.** \> **Nouveau pipeline de build**

   ![Créer un pipeline de build](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. Sélectionnez la **source**, le **projet d’équipe**, le **référentiel** et la **branche par défaut des builds manuels et planifiés** souhaités. Après avoir choisi les options requises, sélectionnez **Continuer**.

   ![Sélectionner le projet d’équipe, le référentiel et la branche pour le pipeline de build](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. Enfin, sélectionnez le modèle souhaité pour le pipeline de build. Nous allons sélectionner le modèle **ASP.NET** dans ce tutoriel. 

> [!NOTE]
> Le pool d’agents à sélectionner pour ce CI doit disposer de Docker pour Windows, sauf si l’installation est effectuée manuellement dans une tâche précédente dans le cadre de ce CI. Consultez l’article [Agents hébergés Microsoft](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml) pour une sélection de pools d’agents ; nous vous recommandons de commencer par `Hosted VS2017` ou `Hosted VS2019`. 

Nous disposons maintenant d’un pipeline de build que nous pouvons configurer pour utiliser la tâche de génération d’émulateur Azure Cosmos DB. 

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Ajouter la tâche à un pipeline de build

1. Avant d’ajouter une tâche au pipeline de build, vous devez ajouter un travail d’agent. Accédez à votre pipeline de build, sélectionnez **...** et choisissez **Ajouter un travail d’agent**.

1. Sélectionnez ensuite le symbole **+** en regard de la tâche d’agent pour ajouter la tâche de génération d’émulateur. Dans la zone de recherche, tapez **cosmos**, puis sélectionnez **Azure Cosmos DB Emulator** (Émulateur Azure Cosmos DB) et ajoutez-le au travail d’agent. La tâche de génération démarre un conteneur avec une instance de l’émulateur Cosmos DB déjà en cours d’exécution sur ce dernier. La tâche de l’émulateur Azure Cosmos DB doit être placée avant toutes les autres tâches qui attendent que l’émulateur soit en cours d’exécution.

   ![Ajouter la tâche de génération d’émulateur à une définition de build](./media/tutorial-setup-ci-cd/addExtension_3.png)

Dans ce didacticiel, vous allez ajouter la tâche au début, afin que l’émulateur soit disponible avant d’exécuter nos tests.

## <a name="configure-tests-to-use-the-emulator"></a>Configurer des tests pour utiliser l’émulateur

Maintenant, nous allons configurer nos tests pour utiliser l’émulateur. La tâche de génération d’émulateur exporte une variable d’environnement (CosmosDbEmulator.Endpoint) vers laquelle toutes les tâches suivantes du pipeline de build peuvent émettre des requêtes. 

Dans ce didacticiel, nous allons utiliser la [tâche Visual Studio Test](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) pour exécuter des tests unitaires configurés via un fichier **.runsettings**. Pour en savoir plus sur l’initialisation de test unitaire, consultez la [documentation](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017). L’exemple complet de code d’application Todo que vous utilisez dans ce document est disponible sur [Github](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

Voici un exemple de fichier **.runsettings** qui définit les paramètres à transmettre dans les tests unitaires d’une application. Remarquez que la variable `authKey` utilisée est la [clé connue](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) pour l’émulateur. Cette `authKey` est la clé attendue par la tâche de génération d’émulateur Elle doit être définie dans votre fichier **.runsettings**.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Si vous configurez un pipeline CI/CD pour une application qui utilise l’API Azure Cosmos DB pour MongoDB, la chaîne de connexion par défaut inclut le numéro de port 10255. Toutefois, ce port n’est pas ouvert actuellement ; en guise d’alternative, utilisez plutôt le port 10250 pour établir la connexion. La chaîne de connexion de l’API Azure Cosmos DB pour MongoDB reste la même, à l’exception du numéro de port pris en charge qui est 10250 au lieu de 10255.

Ces paramètres `TestRunParameters` sont référencés via une propriété `TestContext` dans le projet de test de l’application. Voici un exemple de test qui s’exécute sur Cosmos DB.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Accédez aux options d’exécution dans la tâche de test Visual Studio. Dans l’option **Settings file** (Fichier de paramètres), spécifiez que les tests sont configurés à l’aide du fichier **.runsettings**. Dans l’option **Remplacer les paramètres de série de tests**, ajoutez `-endpoint $(CosmosDbEmulator.Endpoint)`. Cela configurera la tâche de test pour faire référence au point de terminaison de la tâche de génération d’émulateur, au lieu de celle définie dans le fichier **.runsettings**.  

![Remplacer la variable de point de terminaison par le point de terminaison de la tâche de génération d’émulateur](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Exécutez la génération

Maintenant, **enregistrez et mettez en file d’attente** le build. 

![Enregistrer et exécuter la génération](./media/tutorial-setup-ci-cd/runBuild_1.png)

Une fois que la génération a démarré, vous noterez que la tâche d’émulateur Cosmos DB a commencé une extraction de l’image Docker avec l’émulateur installé. 

![Enregistrer et exécuter la génération](./media/tutorial-setup-ci-cd/runBuild_4.png)

Une fois la génération terminée, vous noterez que vos tests réussissent, et qu’ils s’exécutent tous sur l’émulateur Cosmos DB de la tâche de génération !

![Enregistrer et exécuter la génération](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation de l’émulateur pour développer en local et tester, consultez [Utilisation de l’émulateur Azure Cosmos DB pour le développement local et le test](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Pour exporter les certificats SSL de l’émulateur, consultez [Exporter les certificats de l’émulateur Azure Cosmos DB pour une utilisation avec Java, Python et Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
