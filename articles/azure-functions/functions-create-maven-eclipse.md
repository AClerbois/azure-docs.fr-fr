---
title: Créer une application de fonction Azure avec Java et Eclipse | Microsoft Docs
description: Guide pratique pour créer et publier sur Azure Functions une application serverless simple déclenchée par HTTP avec Java et Eclipse.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: azure functions, fonctions, traitement des événements, calcul, architecture serverless, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 123a24eb13de584d8e3b70d0d8b1173f583867c1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61341413"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Créer votre première fonction avec Java et Eclipse 

Cet article vous explique comment créer un projet de fonction [serverless](https://azure.microsoft.com/solutions/serverless/) avec l’environnement de développement intégré (IDE) Eclipse et Apache Maven, comment le tester et le déboguer, puis comment le déployer sur Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configuration de l'environnement de développement

Pour développer une application de fonction avec Java et Eclipse, vous devez avoir installé les éléments suivants :

-  [Java Developer Kit (JDK)](https://www.azul.com/downloads/zulu/) version 8.
-  [Apache Maven](https://maven.apache.org) version 3.0 ou ultérieure.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), avec prise en charge de Java et Maven.
-  [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Pour pouvoir effectuer ce démarrage rapide, vous devez définir la variable d’environnement JAVA_HOME sur l’emplacement d’installation du JDK.

Il est vivement recommandé d’installer également [Azure Functions Core Tools, version 2](functions-run-local.md#v2), qui offre un environnement local pour l’exécution et le débogage d’Azure Functions. 

## <a name="create-a-functions-project"></a>Créer un projet Functions

1. Dans Eclipse, sélectionnez le menu **Fichier**, puis **Projet**. 
1. Dans la fenêtre **Nouveau projet**, ouvrez le dossier **Projet Java**, sélectionnez **Projet Maven**, puis sélectionnez **Suivant**.
1. Acceptez les valeurs par défaut de la boîte de dialogue **Nouveau projet Maven**, puis sélectionnez **Suivant**.
1. Sélectionnez **Add Archetype** (Ajouter un archétype), puis ajoutez les entrées pour [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - Archetype Group ID (ID de groupe d’archétypes) : com.microsoft.azure
    - Archetype Artifact ID (ID d’artefact d’archétype) : azure-functions-archetype
    - Version : Utilisez la dernière version du [référentiel central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Création de projet Maven dans Eclipse](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Cliquez sur **OK**, entrez les détails du projet actuel, puis cliquez sur **Terminer**.

Maven crée les fichiers projet dans un nouveau dossier avec le nom de _artifactId_. Le code généré dans le projet est une fonction simple [déclenchée par HTTP](/azure/azure-functions/functions-bindings-http-webhook) qui retourne le corps de la demande HTTP à l’origine du déclenchement.

## <a name="run-functions-locally-in-the-ide"></a>Exécuter des fonctions localement dans l’IDE

> [!NOTE]
> [Azure Functions Core Tools, version 2](functions-run-local.md#v2) doit être installé pour pouvoir exécuter et déboguer des fonctions localement.

1. Cliquez avec le bouton droit sur le projet généré, puis choisissez **Exécuter en tant que** et **Build Maven**.
1. Dans la boîte de dialogue **Edit Configuration** (Modifier la configuration), entrez `package` dans les champs **Goals** (Objectifs) et **Nom**, puis sélectionnez **Exécuter**. Cette opération entraîne la génération et l’empaquetage du code de fonction.
1. Une fois la génération terminée, créez une autre configuration Exécuter comme ci-dessus, en utilisant `azure-functions:run` en tant qu’objectif et que nom. Sélectionnez **Exécuter** pour exécuter la fonction dans l’IDE.

Lorsque vous avez fini de tester votre fonction, terminez le runtime dans la fenêtre de console. Un seul hôte de fonction à la fois peut être actif et en cours d’exécution localement.

### <a name="debug-the-function-in-eclipse"></a>Déboguer la fonction dans Eclipse

Dans votre configuration **Exécuter en tant que** définie à l’étape précédente, remplacez `azure-functions:run` par `mvn azure-functions:run -DenableDebug`, puis exécutez la configuration mise à jour pour démarrer l’application de fonction en mode débogage.

Sélectionnez le menu **Exécuter**, puis ouvrez **Déboguer les configurations**. Choisissez **Application Java distante** et créez une configuration. Donnez un nom à votre configuration et renseignez les paramètres. Le port doit être cohérent avec le port de débogage ouvert par l’hôte de fonction, qui correspond par défaut à `5005`. Après la configuration, cliquez sur `Debug` pour démarrer le débogage.

![Débogage de fonctions dans Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Définissez des points d’arrêt et inspectez les objets de votre fonction à l’aide de l’IDE. Quand vous avez terminé, arrêtez le débogueur et l’hôte de fonction en cours d’exécution. Un seul hôte de fonction à la fois peut être actif et en cours d’exécution localement.

## <a name="deploy-the-function-to-azure"></a>Déployer la fonction sur Azure

Le processus de déploiement sur Azure Functions utilise les informations d’identification du compte provenant d’Azure CLI. [Connectez-vous avec Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) avant de continuer en utilisant l’invite de commandes de votre ordinateur.

```azurecli
az login
```

Déployez votre code dans une nouvelle application de fonction en utilisant l’objectif Maven `azure-functions:deploy` dans une nouvelle configuration **Exécuter en tant que**.

Quand le déploiement est terminé, vous voyez l’URL que vous pouvez utiliser pour accéder à votre application de fonction Azure :

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le développement de fonctions Java, consultez le [Guide du développeur de fonctions Java](functions-reference-java.md).
- Ajoutez des fonctions supplémentaires avec différents déclencheurs à votre projet en utilisant la cible Maven `azure-functions:add`.
