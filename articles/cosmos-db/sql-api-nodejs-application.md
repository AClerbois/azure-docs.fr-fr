---
title: Créer une application web Node.js avec le kit SDK JavaScript pour gérer les données de l’API SQL Azure Cosmos DB
description: Ce didacticiel Node.js explique comment utiliser Microsoft Azure Cosmos DB pour stocker des données et y accéder à partir d’une application web Express Node.js hébergée sur les sites web Azure.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: sngun
ms.openlocfilehash: 04f634406eacd05e772d2b672cdfb2af6fb42054
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874479"
---
# <a name="tutorial-build-a-nodejs-web-app-using-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Tutoriel : Créer une application web Node.js avec le SDK JavaScript pour gérer les données de l’API SQL Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Ce tutoriel Node.js vous explique comment stocker des données et y accéder à partir d’un compte d’API SQL Azure Cosmos DB à l’aide d’une application Express Node.js hébergée sur les sites web Azure. Dans ce tutoriel, vous allez créer une application basée sur le web (application de tâches), qui vous permet de créer, récupérer, et terminer des tâches. Ces dernières sont stockées en tant que documents JSON dans AzureCosmos DB. 

Ce tutoriel montre comment créer un compte d’API SQL Azure Cosmos DB à l’aide du Portail Azure. Vous générerez et exécuterez ensuite une application web basée sur le Kit de développement logiciel (SDK) Node.js pour créer une base de données, un conteneur et ajouter des éléments au conteneur. Ce tutoriel utilise le kit de développement logiciel (SDK) JavaScript version 2.0.

Vous pouvez également obtenir l’exemple complet depuis [GitHub][GitHub] et consultez le fichier [Lisez-moi](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) pour obtenir des instructions sur l’exécution de l’application.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Création d’un compte Azure Cosmos DB
> * Création d’une application Node.js
> * Connecter une application à Azure Cosmos DB
> * Exécuter et déployer l’application dans Azure

## <a name="_Toc395783176"></a>Configuration requise

Avant de suivre les instructions de cet article, veillez à disposer des ressources suivantes :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] version 6.10 ou ultérieure.
* [Générateur Express](http://www.expressjs.com/starter/generator.html) (installation possible via `npm install express-generator -g`)
* Installez [Git][Git] sur votre station de travail locale.

## <a name="_Toc395637761"></a>Étape 1 : création d’un compte Azure Cosmos DB
Commençons par créer un compte Azure Cosmos DB. Si vous possédez déjà un compte ou si vous utilisez l’émulateur Azure Cosmos DB pour ce didacticiel, vous pouvez passer à [l’étape 2 : création d’une application Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Étape 2 : création d’une application Node.js
Voyons maintenant comment créer un projet Node.js « Hello World » de base à l’aide de l’infrastructure [Express](http://expressjs.com/) .

1. Ouvrez votre terminal préféré, par exemple l’invite de commande Node.js.

1. Accédez au répertoire dans lequel vous souhaitez stocker la nouvelle application.

1. Utilisez le générateur Express pour générer une nouvelle application appelée **todo**.

   ```bash
   express todo
   ```

1. Ouvrez le répertoire **todo** et installez les dépendances.

   ```bash
   cd todo
   npm install
   ```

1. Exécutez la nouvelle application.

   ```bash
   npm start
   ```

1. Vous pouvez afficher votre nouvelle application en accédant à l’adresse [http://localhost:3000](http://localhost:3000) dans votre navigateur.
   
   ![Découverte de Node.js - Capture d’écran de l’application Hello World dans une fenêtre de navigateur](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Arrêtez l’application en appuyant sur CTRL + C dans la fenêtre de terminal, puis cliquez sur **y** pour arrêter le programme de traitement par lots.

## <a name="_Toc395783179"></a>144tape 3 : Installer les modules requis

Le fichier **package.json** est l'un des fichiers créés à la racine du projet. Il contient une liste de modules supplémentaires qui sont nécessaires pour les applications Node.js. Lorsque vous déployez cette application sur Azure, ce fichier permet de déterminer quels modules doivent être installés sur Azure pour la prise en charge de votre application. Installez deux autres packages pour ce tutoriel.

1. Ouvrez le terminal, installez le module **async** via npm.

   ```bash
   npm install async --save
   ```

2. Installer le module **@azure/cosmos** via npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Étape 4 : Connecter une application Node.js à Azure Cosmos DB
Maintenant que vous avez terminé l’installation et la configuration initiales, ensuite vous écrirez du code qui est requis par l’application de tâches pour communiquer avec Azure Cosmos DB.

### <a name="create-the-model"></a>Création du modèle
1. À la racine du répertoire de projet, créez un répertoire nommé **models**  

2. Dans le répertoire **models**, créez un fichier nommé **taskDao.js**. Ce fichier contient le code nécessaire pour créer la base de données ainsi que le conteneur et définit des méthodes pour lire, mettre à jour, créer et rechercher les tâches dans Azure Cosmos DB. 

3. Copiez le code suivant dans le fichier **taskDao.js**

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Enregistrez et fermez le fichier **taskDao.js** .  

### <a name="create-the-controller"></a>Création du contrôleur

1. Dans le répertoire **routes** de votre projet, créez un fichier nommé **tasklist.js**.  

2. Ajoutez le code suivant dans **tasklist.js**. Ce code charge CosmosClient et les modules asynchrones, qui sont utilisés par **tasklist.js**. Il définit également la classe **TaskList** à qui est transmise en tant qu’instance de l’objet **TaskDao** défini précédemment :
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Enregistrez et fermez le fichier **tasklist.js** .

### <a name="add-configjs"></a>Ajout de config.js

1. À la racine du répertoire de projet, créez un fichier nommé **config.js**. 

2. Ajoutez le code suivant au fichier **config.js**. Ce code définit les paramètres de configuration et les valeurs nécessaires à notre application.
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. Dans le fichier **config.js**, mettez à jour les valeurs des paramètres HOST et AUTH_KEY en indiquant les valeurs trouvées dans la page Clés de votre compte Azure Cosmos DB, dans le [portail Microsoft Azure](https://portal.azure.com). 

4. Enregistrez et fermez le fichier **config.js** .

### <a name="modify-appjs"></a>Modification de app.js

1. Dans le répertoire du projet, ouvrez le fichier **app.js** . Ce fichier a été créé précédemment lors de la création de l'application web Express.  

2. Ajoutez le code suivant au fichier **app.js**. Ce code définit le fichier de configuration à utiliser et charge les valeurs dans des variables que vous utiliserez dans les sections suivantes. 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Enfin, enregistrez et fermez le fichier **app.js**.

## <a name="_Toc395783181"></a>Étape 5 : création d'une interface utilisateur

Nous allons maintenant générer l’interface utilisateur afin qu’un utilisateur puisse interagir avec l’application. L'application Express que nous avons créée dans les sections précédentes utilise **Jade** comme moteur de vue. Pour plus d’informations sur Jade, consultez [Langage Jade](http://jade-lang.com/).

1. Le fichier **layout.jade** du répertoire **views** sert de modèle global aux autres fichiers **.jade**. Dans cette étape, vous allez le modifier pour utiliser [Twitter Bootstrap](https://github.com/twbs/bootstrap), qui est un kit de ressources utilisé pour concevoir des sites web.  

2. Ouvrez le fichier **layout.jade** trouvé dans le dossier **views** et remplacez le contenu par le code suivant :

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Ce code demande au moteur **Jade** de générer un rendu HTML pour notre application et crée un **bloc** intitulé **content** dans lequel nous pouvons fournir la mise en page de nos pages de contenu. Enregistrez et fermez le fichier **layout.jade**.

3. Ouvrez maintenant le fichier **index.jade**, la vue qui sera utilisée par l'application, et remplacez le contenu du fichier par le code suivant :

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Ce code étend la mise en page et fournit du contenu pour l’espace réservé **content** que nous avons vu plus haut dans le fichier **layout.jade**. Dans cette mise en page, nous avons créé deux formulaires HTML.

Le premier formulaire contient un tableau pour vos données et un bouton qui permet de mettre à jour des éléments en appelant la méthode **/completeTask** du contrôleur.
    
Le deuxième formulaire contient deux champs d'entrée et un bouton qui permet de créer un élément en appelant la méthode **/addtask** du contrôleur. C’est tout ce dont nous avons besoin pour faire fonctionner l’application.

## <a name="_Toc395783181"></a>Étape 6 : exécution locale de l'application

1. Pour tester l’application sur votre ordinateur local, exécutez la commande `npm start` dans le terminal pour démarrer votre application, puis actualisez la page [http://localhost:3000](http://localhost:3000) de votre navigateur. La page devrait maintenant ressembler à ce qui est présenté dans la capture d’écran suivante :
   
    ![Capture d'écran de l'application MyTodo List dans une fenêtre de navigateur](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Si vous recevez une erreur liée à la mise en retrait dans le fichier layout.jade ou index.jade, assurez-vous que les deux premières lignes des deux fichiers sont justifiées à gauche et ne présentent aucun espace. Supprimez tout espace éventuel dans les deux premières lignes, enregistrez les deux fichiers, puis actualisez la fenêtre du navigateur. 

2. Utilisez les champs Élément, Nom d’élément et Catégorie pour saisir une nouvelle tâche, puis cliquez sur **Ajouter des éléments**. Cela permet de créer un document présentant ces propriétés dans Azure Cosmos DB. 

3. La page doit se mettre à jour et afficher le nouvel élément créé dans la liste des tâches.
   
    ![Capture d'écran de l'application avec un nouvel élément dans la liste de tâches](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)

4. Pour terminer une tâche, activez la case à cocher dans la colonne Complete, puis cliquez sur **Mettre à jour les tâches**. Cela met à jour le document que vous avez déjà créé et le supprime de la vue.

5. Pour arrêter l’application, appuyez sur CTRL + C dans la fenêtre de terminal, puis cliquez sur **Y** pour arrêter le traitement par lots.

## <a name="_Toc395783182"></a>Étape 7 : Déployer votre application sur les sites web Azure

1. Si vous ne l'avez pas encore fait, activez un référentiel git pour votre site web Azure. Vous trouverez des instructions sur l’activation d’un référentiel git dans la rubrique [Déploiement Git local vers Azure App Service](../app-service/app-service-deploy-local-git.md).

2. Ajoutez votre site web Azure en tant que git distant.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Déployez l’application en l’envoyant à l’appareil distant.
   
   ```bash
   git push azure master
   ```

4. Dans quelques secondes, votre application web sera publiée et lancée dans un navigateur.

Si vous souhaitez télécharger ou vous référer à l’application de référence complète de ce tutoriel, vous pouvez la télécharger à partir de [GitHub][GitHub].

## <a name="_Toc395637775"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer une application web Node.js avec le SDK JavaScript pour gérer les données de l’API SQL Azure Cosmos DB. Vous pouvez maintenant passer à l’article suivant :

> [!div class="nextstepaction"]
> [Créer des applications mobiles avec Xamarin et Azure Cosmos DB](mobile-apps-with-xamarin.md)


[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

