---
title: Information de référence pour les développeurs JavaScript sur Azure Functions | Microsoft Docs
description: Découvrez comment développer des fonctions à l’aide de JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: d9de47ad83f37fa976c3816a0cb2e3e3beaa5472
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437575"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guide des développeurs JavaScript sur Azure Functions

Ce guide contient des informations sur les complexités de l’écriture de fonctions Azure avec JavaScript.

Une fonction JavaScript est une `function` exportée qui s’exécute quand elle est déclenchée ([les déclencheurs sont configurés dans function.json](functions-triggers-bindings.md)). Le premier argument passé à chaque fonction est un `context` objet, qui est utilisé pour l’envoi et réception liaison de données, la journalisation et de communiquer avec le runtime.

Cet article suppose que vous ayez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md). Terminez le démarrage rapide de Functions pour créer votre première fonction, à l’aide de [Visual Studio Code](functions-create-first-function-vs-code.md) ou [dans le portail](functions-create-first-azure-function.md).

Cet article prend également en charge [développement d’applications TypeScript](#typescript).

## <a name="folder-structure"></a>Structure de dossiers

La structure de dossiers requise pour un projet JavaScript est similaire à la structure ci-après. Cette valeur par défaut peut être modifiée. Pour plus d’informations, consultez la section [scriptFile](#using-scriptfile)ci-dessous.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

À la racine du projet se trouve un fichier [host.json](functions-host-json.md) partagé que vous pouvez utiliser pour configurer l’application de fonction. Chaque fonction a un dossier avec ses propres fichier de code (.js) et fichier de configuration de liaison (function.json). Le nom du répertoire parent de `function.json` est toujours le nom de votre fonction.

Les extensions de liaison requises dans la [version 2.x](functions-versions.md) du runtime Functions sont définies dans le fichier `extensions.csproj`, les fichiers de bibliothèque proprement dits se trouvant dans le dossier `bin`. Quand vous développez localement, vous devez [inscrire les extensions de liaison](./functions-bindings-register.md#local-development-azure-functions-core-tools). Quand vous développez des fonctions dans le portail Azure, cet enregistrement est effectué pour vous.

## <a name="exporting-a-function"></a>Exporter une fonction

Les fonctions JavaScript doivent être exportées au moyen de [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (ou [`exports`](https://nodejs.org/api/modules.html#modules_exports)). Votre fonction exportée doit être une fonction JavaScript qui s’exécute quand elle est déclenchée.

Par défaut, le runtime Functions recherche votre fonction dans `index.js`, où `index.js` partage le même répertoire parent en tant que `function.json` correspondant. Dans le cas par défaut, votre fonction exportée doit être la seule exportation à partir de son fichier ou l’export nommé `run` ou `index`. Pour configurer l’emplacement de fichier et le nom d’exportation de votre fonction, consultez les informations relatives à la [configuration du point d’entrée de votre fonction](functions-reference-node.md#configure-function-entry-point) plus loin.

Votre fonction exportée doit valider un certain nombre d’arguments à l’exécution. Le premier argument est toujours un objet `context`. Si votre fonction est synchrone (ne retourne pas un objet Promise), vous devez valider l’objet `context`, car il est obligatoire d’appeler `context.done` pour une utilisation correcte.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportation d’une fonction asynchrone
Quand vous utilisez la déclaration [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) ou l’objet [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) JavaScript simple dans la version 2.x du runtime Functions, vous n’avez pas besoin d’appeler explicitement le rappel [`context.done`](#contextdone-method) pour signaler que votre fonction s’est exécutée. Votre fonction s’exécute en même temps que l’exécution de la fonction asynchrone/l’objet Promise exporté. Pour les fonctions ciblant le runtime version 1.x, vous devez toujours appeler [`context.done`](#contextdone-method) lorsque votre code a terminé de s’exécuter.

L’exemple suivant est une fonction simple qui enregistre qu’elle a été déclenchée et dont l’exécution se termine immédiatement.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Quand vous exportez une fonction asynchrone, vous pouvez également configurer une liaison de sortie avec la valeur `return`. Cette approche est recommandée si vous n’avez qu’une seule liaison de sortie.

Pour assigner une sortie en utilisant `return`, remplacez la propriété `name` par `$return` dans `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

Dans ce cas, votre fonction doit ressembler à l'exemple suivant :

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Liaisons 
Dans JavaScript, les [liaisons](functions-triggers-bindings.md) sont configurées et définies dans le fichier function.json d’une fonction. Les fonctions interagissent avec des liaisons de plusieurs façons.

### <a name="inputs"></a>Entrées
Les entrées sont réparties en deux catégories dans Azure Functions : l’une correspond à l’entrée du déclencheur et l’autre, à l’entrée supplémentaire. Le déclencheur et autres liaisons d’entrée (liaisons de `direction === "in"`) peuvent être lus par une fonction de trois façons :
 - **_[Recommandé]_  En tant que paramètres transmis à votre fonction.** Elles sont transmises à la fonction dans l’ordre dans lequel elles sont définies dans le fichier *function.json*. Le `name` propriété définie dans *function.json* pas nécessairement correspondre au nom de votre paramètre, bien qu’il le devrait.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **En tant que membres de l’objet [`context.bindings`](#contextbindings-property).** Chaque membre est nommé par la propriété `name` définie dans *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **En tant qu’entrées à l’aide de l’objet [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) JavaScript.** Cette approche revient pratiquement au même que la transmission des entrées en tant que paramètres, mais elle vous permet de gérer des entrées de manière dynamique.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Outputs
Une fonction peut écrire des données dans les sorties (liaisons de `direction === "out"`) de plusieurs façons. Dans tous les cas, la propriété `name` de la liaison, comme définie dans *function.json* correspond au nom du membre d’objet faisant l’objet d’une écriture dans votre fonction. 

Vous pouvez affecter des données aux liaisons de sortie de l’une des manières suivantes (ne pas de combiner ces méthodes) :

- **_[Recommandé pour plusieurs sorties]_  Retourner un objet.** Si vous utilisez une fonction de retour async/Promise, vous pouvez retourner un objet avec des données de sortie assignées. Dans l’exemple ci-dessous, les liaisons de sortie sont nommées « httpResponse » et « queueOutput » dans *function.json*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Si vous utilisez une fonction synchrone, vous pouvez retourner cet objet à l’aide de [`context.done`](#contextdone-method) (voir l’exemple).
- **_[Recommandé en cas de sortie unique]_  Retourner une valeur directement et utiliser le nom de la liaison $return.** Cela fonctionne uniquement pour les fonctions de retour async/Promise. Voir l’exemple dans l’[exportation en tant que fonction asynchrone](#exporting-an-async-function). 
- **Assigner des valeurs à `context.bindings`** Vous pouvez affecter des valeurs directement à context.bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Type de données de liaisons

Pour définir le type de données pour une liaison d’entrée, utilisez la propriété `dataType` dans la définition de la liaison. Par exemple, pour lire le contenu d’une requête HTTP au format binaire, utilisez le type `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Les options pour `dataType` sont `binary`, `stream` et `string`.

## <a name="context-object"></a>Objet de contexte
Le runtime utilise un objet `context` pour transmettre des données vers et à partir de votre fonction et vous permettre de communiquer avec le runtime. L’objet de contexte peut être utilisé pour lire et définir des données à partir de liaisons, écrire des journaux et utiliser le rappel `context.done` lorsque votre fonction exportée est synchrone.

L’objet `context` est toujours le premier paramètre d’une fonction. Il doit être inclus, car il possède des méthodes importantes telles que `context.done` et `context.log`. Vous pouvez nommer l’objet comme vous le souhaitez (par exemple, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Propriété context.bindings

```js
context.bindings
```

Retourne un objet nommé qui contient toutes vos données d’entrée et de sortie. Par exemple, les définitions de liaison suivantes dans votre function.json vous permettent d’accéder au contenu d’une file d’attente à partir de l’objet `context.bindings.myInput` et d’assigner des sorties à une file d’attente à l’aide de `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Vous pouvez choisir de définir les données de liaison de sortie à l’aide de la méthode `context.done` au lieu de l’objet `context.binding` (voir ci-dessous).

### <a name="contextbindingdata-property"></a>Propriété context.bindingData

```js
context.bindingData
```

Retourne un objet nommé qui contient les métadonnées de déclencheur et les données d’appel de fonction (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Pour obtenir un exemple de métadonnées de déclencheur, consultez cet [exemple de hubs d’événement](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Méthode context.done

```js
context.done([err],[propertyBag])
```

Permet au runtime de savoir que votre code s’est exécuté. Lorsque votre fonction utilise la déclaration [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function), vous n’avez pas besoin d’utiliser `context.done()`. Le rappel `context.done` est appelé de manière implicite. Les fonctions asynchrones sont disponibles dans Node 8 ou version ultérieure, qui nécessite la version 2.x du runtime Functions.

Si votre fonction n’est pas une fonction asynchrone, **vous devez appeler**  `context.done` pour indiquer au runtime que votre fonction est terminée. Sinon, l’exécution arrive à expiration.

La méthode `context.done` vous permet de transmettre à la fois une erreur définie par l’utilisateur au runtime et un objet JSON contenant les données de liaison de sortie. Les propriétés transmises à `context.done` remplacent tout ce qui est défini sur l’objet `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Méthode context.log  

```js
context.log(message)
```

Vous permet d’écrire dans les journaux de fonction de streaming au niveau de trace par défaut. Des méthodes de journalisation supplémentaires sont disponibles sur `context.log` pour vous permettre d’écrire des journaux de fonction à d’autres niveaux de trace :


| Méthode                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Écrit dans la journalisation du niveau d’erreur, ou à un niveau inférieur.   |
| **warn(_message_)**    | Écrit dans la journalisation du niveau d’avertissement, ou à un niveau inférieur. |
| **info(_message_)**    | Écrit dans la journalisation du niveau d’information, ou à un niveau inférieur.    |
| **verbose(_message_)** | Écrit dans la journalisation du niveau détaillé.           |

L’exemple suivant écrit un journal au niveau de trace d’avertissement :

```javascript
context.log.warn("Something has happened."); 
```

Vous pouvez [configurer le seuil du niveau de trace pour la journalisation](#configure-the-trace-level-for-console-logging) dans le fichier host.json. Pour plus d’informations sur l’écriture de journaux, consultez [Écriture de sorties de trace](#writing-trace-output-to-the-console) plus loin.

Consultez [Supervision des fonctions Azure](functions-monitoring.md) pour en savoir plus sur l’affichage et l’interrogation des journaux de fonction.

## <a name="writing-trace-output-to-the-console"></a>Écrire la sortie de trace dans la console 

Dans Functions, vous utilisez les méthodes `context.log` pour écrire la sortie de trace dans la console. Dans Functions v2.x, les sorties de trace via `console.log` sont capturées au niveau de l’application Functions. Cela signifie que les sorties de `console.log` ne sont pas liées à un appel de fonction spécifique et qu’elles ne sont donc pas affichées dans les journaux d’une fonction spécifique. Elles sont, toutefois, propagées à Application Insights. Dans Functions v1.x, vous ne pouvez pas utiliser `console.log` pour écrire dans la console.

Lorsque vous appelez `context.log()`, votre message est écrit dans la console au niveau de trace par défaut, qui est le niveau de trace d’_informations_. Le code suivant écrit dans la console au niveau de trace d’informations :

```javascript
context.log({hello: 'world'});  
```

Ce code est équivalent au code ci-dessus :

```javascript
context.log.info({hello: 'world'});  
```

Ce code écrit dans la console au niveau de trace d’erreur :

```javascript
context.log.error("An error has occurred.");  
```

Étant donné que le niveau d’_erreur_ constitue le niveau de trace le plus élevé, cette trace est écrite dans la sortie à tous les niveaux de trace tant que la journalisation est activée.

Toutes les méthodes `context.log` prennent en charge le même format de paramètre que celui pris en charge par la [méthode util.format](https://nodejs.org/api/util.html#util_util_format_format) Node.js. Prenons le code suivant, qui écrit des journaux de fonction en utilisant le niveau de trace par défaut :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Vous pouvez écrire ce même code au format suivant :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurer le niveau de trace pour la journalisation de la console

Functions 1.x vous permet de définir le niveau de trace du seuil pour écrire dans la console, ce qui facilite le contrôle de l’écriture des traces dans la console à partir de votre fonction. Utilisez la propriété `tracing.consoleLevel` dans le fichier host.json pour définir le seuil de toutes les traces écrites dans la console. Ce paramètre s’applique à toutes les fonctions dans votre Function App. L’exemple suivant définit le seuil de trace permettant d’activer la journalisation détaillée :

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Les valeurs de **consoleLevel** correspondent aux noms des méthodes `context.log`. Pour désactiver toutes les journalisations de trace dans la console, définissez **consoleLevel** sur _désactivé_. Pour plus d’informations, consultez l’article de référence sur [host.json](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Déclencheurs et liaisons HTTP

Les déclencheurs HTTP et webhook ainsi que les liaisons de sortie HTTP utilisent les objets de requête et de réponse pour représenter la messagerie HTTP.  

### <a name="request-object"></a>Objet Requête

L’objet (de demande) `context.req` comporte les propriétés suivantes :

| Propriété      | Description                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Objet qui contient le corps de la demande.               |
| _headers_     | Objet qui contient les en-têtes de la demande.                   |
| _method_      | Méthode HTTP de la demande.                                |
| _originalUrl_ | URL de la demande.                                        |
| _params_      | Objet qui contient les paramètres de routage de la demande. |
| _query_       | Objet qui contient les paramètres de la requête.                  |
| _rawBody_     | Corps du message en tant que chaîne.                           |


### <a name="response-object"></a>Objet Réponse

L’objet (de réponse) `context.res` comporte les propriétés suivantes :

| Propriété  | Description                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Objet qui contient le corps de la réponse.         |
| _headers_ | Objet qui contient les en-têtes de la réponse.             |
| _isRaw_   | Indique que la mise en forme est ignorée pour la réponse.    |
| _statut_  | Code d’état HTTP de la réponse.                     |

### <a name="accessing-the-request-and-response"></a>Accès à la demande et à la réponse 

Quand vous utilisez des déclencheurs HTTP, de nombreuses méthodes vous permettent d’accéder à des objets de demande et de réponse HTTP :

+ **À partir des propriétés `req` et `res` sur l’objet `context`.** Avec cette méthode, vous pouvez utiliser le modèle classique pour accéder aux données HTTP à partir de l’objet de contexte, au lieu d’utiliser le modèle `context.bindings.name` complet. L’exemple suivant montre comment accéder aux objets `req` et `res` sur `context` :

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **À partir de des liaisons dites d’entrée et de sortie.** Avec cette méthode, le déclencheur HTTP et les liaisons fonctionnent de la même manière que toute autre liaison. L’exemple suivant définit l’objet de réponse en utilisant une liaison nommée `response` : 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Réponse uniquement]_ En appelant `context.res.send(body?: any)`.** Une réponse HTTP est créée avec l’entrée `body` comme corps de réponse. `context.done()` est appelé de manière implicite.

+ **_[Réponse uniquement]_ En appelant `context.done()`.** Un type spécial de liaison HTTP renvoie la réponse transmise à la méthode `context.done()`. La liaison de sortie HTTP suivante définit un paramètre de sortie `$return` :

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Version de nœud

Le tableau suivant montre la version de Node.js qui est utilisée par chaque version majeure du runtime Functions :

| Version de Functions | Version de Node.js | 
|---|---|
| 1.x | 6.11.2 (verrouillée par le runtime) |
| 2.x  | Versions de Node.js _Active LTS_ et _Current_ paires (8.11.1 et 10.14.1 sont recommandées). Définissez la version à l’aide du [paramètre d’application](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION.|

Vous pouvez voir la version que le runtime utilise en vérifiant le paramètre d’application ci-dessus ou en affichant `process.version` à partir de n’importe quelle fonction.

## <a name="dependency-management"></a>Gestion des dépendances
Pour utiliser les bibliothèques de communauté dans votre code JavaScript, comme indiqué dans l’exemple ci-dessous, vous devez vérifier que toutes les dépendances sont installées sur votre application de fonction dans Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Vous devez définir un fichier `package.json` à la racine de votre application de fonction. Cela permet à toutes les fonctions de l’application de partager les mêmes packages mis en cache, pour des performances optimales. En cas de conflit de version, vous pouvez ajouter un fichier `package.json` dans le dossier d’une fonction spécifique pour le résoudre.  

Quand vous déployez des applications de fonction à partir du contrôle de code source, la présence d’un fichier `package.json` dans votre référentiel déclenche un `npm install` dans son dossier pendant le déploiement. Toutefois, si vous effectuez le déploiement par le biais du portail ou de l’interface CLI, vous devrez installer manuellement les packages.

Vous pouvez installer des packages sur votre application de fonction de deux façons : 

### <a name="deploying-with-dependencies"></a>Déploiement avec des dépendances
1. Installez tous les packages requis localement en exécutant `npm install`.

2. Déployez votre code, puis vérifiez que le dossier `node_modules` est inclus dans le déploiement. 


### <a name="using-kudu"></a>Utilisation de Kudu
1. Accédez à `https://<function_app_name>.scm.azurewebsites.net`

2. Cliquez sur **Console de débogage** > **CMD**.

3. Accédez à `D:\home\site\wwwroot`, puis faites glisser le fichier package.json vers le dossier **wwwroot** dans la partie supérieure de la page.  
    Il existe d’autres manières de télécharger des fichiers dans votre Function App. Pour plus d’informations, consultez [Comment mettre à jour les fichiers du conteneur de fonctions](functions-reference.md#fileupdate). 

4. Une fois le fichier package.json chargé, exécutez la commande `npm install` dans la **console d’exécution à distance Kudu**.  
    Les packages d’actions indiqués dans le fichier package.json sont téléchargés et Function App redémarre.

## <a name="environment-variables"></a>Variables d’environnement

Dans Functions, les [paramètres de l’application](functions-app-settings.md), par exemple, les chaînes de connexion de service, sont exposées en tant que variables d’environnement pendant l’exécution. Vous pouvez accéder à ces paramètres à l’aide de `process.env`, comme illustré ici dans la fonction `GetEnvironmentVariable` :

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Lors de l’exécution en local, les paramètres de l’application sont lus à partir du fichier projet [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="configure-function-entry-point"></a>Configurer le point d’entrée de la fonction

Vous pouvez utiliser les propriétés `function.json` `scriptFile` et `entryPoint` pour configurer l’emplacement et le nom de votre fonction exportée. Ces propriétés peuvent être importantes lorsque votre code JavaScript est transpilé.

### <a name="using-scriptfile"></a>Utilisation de `scriptFile`

Par défaut, une fonction JavaScript est exécutée à partir de `index.js`, fichier qui partage le même répertoire parent que son `function.json` correspondant.

Vous pouvez utiliser la propriété `scriptFile` pour obtenir une structure de dossiers semblable à l’exemple suivant :

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

Le `function.json` pour `myNodeFunction` doit inclure une propriété `scriptFile` qui pointe vers le fichier contenant la fonction exportée à exécuter.

```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Utilisation de `entryPoint`

Dans `scriptFile` (ou `index.js`), une fonction doit être exportée à l’aide de `module.exports` afin qu’elle soit trouvée et exécutée. Par défaut, la fonction qui s’exécute quand elle est déclenchée est la seule exportation à partir de ce fichier, l’exportation nommée `run` ou l’exportation nommée `index`.

Cela peut être configuré à l’aide de `entryPoint` dans `function.json`, comme dans l’exemple suivant :

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Dans Functions v2.x, qui prend en charge le paramètre `this` dans les fonctions utilisateur, le code de la fonction peut alors se présenter comme dans l’exemple suivant :

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Dans cet exemple, il est important de noter que même si un objet est en cours d’exportation, il n’existe aucune garantie pour conserver l’état entre les exécutions.

## <a name="local-debugging"></a>Le débogage local

Démarrage avec la `--inspect` paramètre, un processus Node.js écoute pour un client de débogage sur le port spécifié. Dans Azure Functions 2.x, vous pouvez spécifier des arguments à passer au processus Node.js qui exécute votre code en ajoutant la variable d’environnement ou d’un paramètre d’application `languageWorkers:node:arguments = <args>`. 

Pour déboguer localement, vous devez ajouter `"languageWorkers:node:arguments": "--inspect=5858"` sous `Values` dans votre [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) de fichiers et d’attacher un débogueur au port 5858.

Lors du débogage à l’aide de VS Code, le `--inspect` paramètre est automatiquement ajouté à l’aide de la `port` valeur dans le fichier du projet launch.json.

Dans la version 1.x, le paramètre `languageWorkers:node:arguments` ne fonctionnera pas. Le port de débogage peut être sélectionné avec le [ `--nodeDebugPort` ](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) paramètre sur Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Quand vous ciblez la version 2.x du runtime Functions, les deux [Azure Functions pour Visual Studio Code](functions-create-first-function-vs-code.md) et [Azure Functions Core Tools](functions-run-local.md) vous permettent de créer des applications de fonction à l’aide d’un modèle qui prennent en charge Projets d’application de fonction TypeScript. Le modèle génère `package.json` et `tsconfig.json` les fichiers projet qui le rendent plus faciles à transpiler du code, exécuter et publier des fonctions JavaScript à partir du code TypeScript grâce à ces outils.

Un généré `.funcignore` fichier est utilisé pour indiquer les fichiers qui sont exclus lorsqu’un projet est publié dans Azure.  

Fichiers TypeScript (.ts) sont transpilé en fichiers JavaScript (.js) dans le `dist` répertoire de sortie. Utilisent des modèles de TypeScript le [ `scriptFile` paramètre](#using-scriptfile) dans `function.json` pour indiquer l’emplacement du fichier .js correspondant dans le `dist` dossier. L’emplacement de sortie est défini par le modèle à l’aide de `outDir` paramètre dans le `tsconfig.json` fichier. Si vous modifiez ce paramètre ou le nom du dossier, le runtime n’est pas en mesure de trouver le code à exécuter.

> [!NOTE]
> Il existe une prise en charge expérimentale pour TypeScript version 1.x du runtime Functions. La version expérimentale transpiles fichiers TypeScript dans les fichiers JavaScript lorsque la fonction est appelée. Dans la version 2.x, cette prise en charge expérimentale a été remplacée par la méthode contrôlée par l’outil qui effectue la transpilation avant l’initialisation de l’hôte et pendant le processus de déploiement.

La façon dont vous développez localement et que vous déployez à partir d’un projet TypeScript dépend de votre outil de développement.

### <a name="visual-studio-code"></a>Visual Studio Code

Le [Azure Functions pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extension vous permet de développer vos fonctions à l’aide de TypeScript. Les outils Core est une exigence de l’extension d’Azure Functions.

Pour créer une application de fonction TypeScript dans Visual Studio Code, vous choisissez simplement `TypeScript` lorsque vous créez une application de fonction et que vous êtes invité à choisir la langue.

Quand vous appuyez sur **F5** pour exécuter l’application localement, transpilation est effectuée avant l’initialisation de l’hôte (func.exe). 

Lorsque vous déployez votre application de fonction sur Azure à l’aide du **déployer à l’application de fonction...**  bouton, l’extension d’Azure Functions génère d’abord une build prête pour la production de fichiers JavaScript à partir des fichiers source TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Pour créer un projet d’application de fonction TypeScript à l’aide des outils Core, vous devez spécifier l’option de langage typescript lorsque vous créez votre application de fonction. Vous pouvez effectuer cela dans une des manières suivantes :

- Exécutez le `func init` commande, sélectionnez `node` comme votre pile de langage, puis sélectionnez `typescript`.

- Exécutez la commande `func init --worker-runtime typescript`.

Pour exécuter votre code d’application de fonction localement à l’aide des outils de base, utilisez le `npm start` commande, au lieu de `func host start`. Le `npm start` commande revient aux commandes suivantes :

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

Avant d’utiliser le [ `func azure functionapp publish` ] de commande pour déployer sur Azure, vous devez d’abord exécuter la `npm run build:production` commande. Cette commande crée une build prête pour la production de fichiers JavaScript à partir des fichiers source TypeScript qui peuvent être déployés à l’aide de [ `func azure functionapp publish` ].

## <a name="considerations-for-javascript-functions"></a>Considérations relatives aux fonctions JavaScript

Quand vous utilisez des fonctions JavaScript, tenez compte des considérations décrites dans les sections suivantes.

### <a name="choose-single-vcpu-app-service-plans"></a>Choisir des plans App Service à processeur virtuel unique

Lorsque vous créez une application de fonction qui utilise le plan App Service, nous vous recommandons de sélectionner un plan à processeur virtuel unique plutôt qu’un plan à plusieurs processeurs virtuels. À l’heure actuelle, Functions exécute les fonctions JavaScript plus efficacement sur des machines virtuelles à processeur virtuel unique. Le recours à de plus grandes machines virtuelles ne produit pas les améliorations de performances attendues. Lorsque cela est nécessaire, vous pouvez faire évoluer manuellement en ajoutant des instances de machine virtuelle de processeur virtuel unique, ou vous pouvez activer la mise à l’échelle. Pour plus d’informations, consultez [Mettre à l’échelle le nombre d’instances manuellement ou automatiquement](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Démarrage à froid

Quand vous développez des fonctions Azure dans le modèle d’hébergement serverless, les démarrages à froid sont une réalité. *Démarrage à froid* fait référence au fait que le démarrage de votre application de fonction prend plus de temps quand elle démarre pour la première fois après une période d’inactivité. En particulier, pour les fonctions JavaScript avec de grandes arborescences de dépendances, le démarrage à froid peut prendre un temps considérable. Pour accélérer le processus de démarrage à froid, [exécutez vos fonctions en tant que fichier de package](run-functions-from-deployment-package.md) lorsque cela est possible. De nombreuses méthodes de déploiement utilisent par défaut le modèle d’exécution à partir d’un package, mais si vous constatez des démarrages à froid particulièrement lents et que vous n’utilisez pas cette méthode d’exécution, cette approche peut accélérer considérablement le démarrage.

### <a name="connection-limits"></a>Limites de connexion

Lorsque vous utilisez un client de service spécifique dans une application Azure Functions, ne créez pas un nouveau client à chaque invocation de fonction. Au lieu de cela, créez un client unique et statique dans la portée globale. Pour plus d’informations, consultez [la gestion des connexions dans Azure Functions](manage-connections.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

+ [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
+ [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
+ [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)

[`func azure functionapp publish`]: functions-run-local.md#project-file-deployment
