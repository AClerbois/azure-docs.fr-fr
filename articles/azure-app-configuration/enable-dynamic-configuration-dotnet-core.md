---
title: Tutoriel sur l’utilisation de la configuration dynamique d’Azure App Configuration dans une application .NET Core | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à mettre à jour dynamiquement les données de configuration pour les applications .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: 3eee34f594cb23a8b64f6fd10837c9a641eda62d
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075968"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Didacticiel : Utiliser la configuration dynamique dans une application .NET Core

La bibliothèque cliente .NET Core App Configuration permet d’effectuer la mise à jour à la demande d’un ensemble de paramètres de configuration, sans entraîner le redémarrage de l’application. Vous pouvez implémenter cette configuration en obtenant d’abord une instance de `IConfigurationRefresher` parmi les options du fournisseur de configuration, puis en appelant `Refresh` sur cette instance, à n’importe quel endroit de votre code.

Pour maintenir les paramètres à jour et éviter trop d’appels au magasin de configuration, un cache est utilisé pour chaque paramètre. Tant que la valeur mise en cache d’un paramètre n’a pas expiré, l’opération d’actualisation ne met pas à jour la valeur, même si celle-ci a été modifiée dans le magasin de configuration. Pour chaque requête, le délai d’expiration par défaut est de 30 secondes. Toutefois, vous pouvez le modifier selon vos besoins.

Ce tutoriel montre comment vous pouvez implémenter des mises à jour de la configuration dynamique dans votre code. Il s’appuie sur l’application mentionnée dans les guides de démarrage rapide. Avant de continuer, terminez d’abord l’étape [Créer une application .NET Core avec App Configuration](./quickstart-dotnet-core-app.md).

Vous pouvez utiliser l’éditeur de code de votre choix pour exécuter les étapes de ce tutoriel. [Visual Studio Code](https://code.visualstudio.com/) est une excellente option qui est disponible sur les plateformes Windows, macOS et Linux.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer votre application afin de mettre à jour sa configuration à la demande à l’aide d’un magasin de configuration d’application.
> * Injecter la configuration la plus récente dans les contrôleurs de votre application.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, installez le [kit SDK .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recharger des données à partir d’Azure App Configuration

Ouvrez le fichier *Program.cs* et mettez-le à jour pour ajouter une référence à l’espace de noms `System.Threading.Tasks`, ceci afin de spécifier la configuration de l’actualisation dans la méthode `AddAzureAppConfiguration` et de déclencher l’actualisation manuelle à l’aide de la méthode `Refresh`.

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        IConfigurationRefresher refresher = null;

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });
                    
                    _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

La méthode `ConfigureRefresh` permet de spécifier les paramètres utilisés pour mettre à jour les données de configuration à l’aide du magasin de configuration d’application, lorsqu’une opération d’actualisation est déclenchée. Vous pouvez récupérer une instance de `IConfigurationRefresher` en appelant la méthode `GetRefresher` dans les options fournies à la méthode `AddAzureAppConfiguration`. Vous pouvez aussi utiliser la méthode `Refresh` de cette instance pour déclencher une opération d’actualisation n’importe où dans votre code.
    
> [!NOTE]
> Pour un paramètre de configuration, le délai d’expiration du cache par défaut est de 30 secondes. Toutefois, vous pouvez le modifier en appelant la méthode `SetCacheExpiration` de l’initialiseur d’options qui est passé en tant qu’argument à la méthode `ConfigureRefresh`.

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Définissez une variable d’environnement nommée **ConnectionString** et affectez-lui la valeur de la clé d’accès à votre magasin de configuration d’application. Si vous utilisez l’invite de commandes Windows, exécutez la commande suivante et redémarrez l’invite pour que la modification soit prise en compte :

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Si vous utilisez Windows PowerShell, exécutez la commande suivante :

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Si vous utilisez macOS ou Linux, exécutez la commande suivante :

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Exécutez la commande suivante pour générer l’application console :

        dotnet build

1. La génération terminée correctement, lancez la commande suivante pour exécuter l’application localement :

        dotnet run

    ![Démarrage rapide du lancement d’application local](./media/quickstarts/dotnet-core-app-run.png)

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Toutes les ressources**, puis sélectionnez l’instance de magasin de configuration d’application que vous avez créée dans le démarrage rapide.

1. Sélectionnez **Explorateur de configuration**, puis mettez à jour les valeurs des clés suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Données issues d’Azure App Configuration - Mise à jour |

1. Appuyez sur la touche Entrée pour déclencher une actualisation et afficher la valeur mise à jour dans l’invite de commandes ou dans la fenêtre PowerShell.

    ![Démarrage rapide de l’actualisation locale de l’application](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Étant donné que le délai d’expiration du cache a été défini sur 10 secondes à l’aide de la méthode `SetCacheExpiration` lors de la spécification de la configuration de l’opération d’actualisation, la valeur du paramètre de configuration ne sera actualisée que si 10 secondes se sont écoulées depuis la dernière actualisation de ce paramètre.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté une identité de service managée Azure pour simplifier l’accès à App Configuration et améliorer la gestion des informations d’identification pour votre application. Pour en savoir plus sur l’utilisation d’App Configuration, passez aux exemples Azure CLI.

> [!div class="nextstepaction"]
> [Exemples d’interface de ligne de commande](./cli-samples.md)
