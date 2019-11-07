---
title: 'Procédure : Effectuer des commandes personnalisées sur le client avec le Kit de développement logiciel (SDK) Speech (préversion)'
titleSuffix: Azure Cognitive Services
description: Dans cet article, gérez des activités de commandes personnalisées sur le client avec le Kit de développement logiciel (SDK) Speech
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: a986da74a668075457e28a9a37b6a11fd04a84e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506508"
---
# <a name="how-to-fulfill-commands-on-the-client-with-the-speech-sdk-preview"></a>Procédure : Effectuer des commandes sur le client avec le Kit de développement logiciel (SDK) Speech (préversion)

Pour effectuer des tâches à l’aide d’une application de commandes personnalisées, vous pouvez envoyer des charges utiles personnalisées à un appareil client connecté.

Dans cet article, vous allez :

- Définir et envoyer une charge utile JSON personnalisée à partir de votre application de commandes personnalisées
- Recevoir et visualiser le contenu de la charge utile JSON personnalisée d’une application cliente du Kit de développement logiciel (SDK) Speech UWP C#

## <a name="prerequisites"></a>Prérequis

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Clé d’abonnement Azure pour Speech Services
   - [Obtenez-en une gratuitement](get-started.md) ou créez-la sur le [portail Azure](https://portal.azure.com)
- Application de commandes personnalisées créée précédemment
   - [Démarrage rapide : Créer une commande personnalisée avec des paramètres (préversion)](./quickstart-custom-speech-commands-create-parameters.md)
- Application cliente compatible avec le Kit de développement logiciel (SDK) Speech
   - [Démarrage rapide : Se connecter à une application de commandes personnalisées avec le Kit de développement logiciel (SDK) Speech (préversion)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Facultatif : Démarrer rapidement

Cet article explique, étape par étape, comment créer une application cliente pour communiquer avec votre application de commandes personnalisées. Si vous préférez vous y plonger dès maintenant, le code source complet et prêt à compiler utilisé dans cet article est disponible dans les [exemples du Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Avec une charge utile JSON

1. Ouvrir votre application de commandes personnalisées précédemment créée à partir du [Studio Speech](https://speech.microsoft.com/)
1. Consulter la section des **règles d’exécution** pour vous assurer que la règle créée précédemment répond à l’utilisateur
1. Pour envoyer une charge utile directement au client, créer une nouvelle règle avec une action Send Activity (Activité d’envoi)
   > [!div class="mx-imgBorder"]
   > ![Règle d’exécution Send Activity (Activité d’envoi)](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Paramètre    | Valeur suggérée                                  | Description                                        |
   | ---------- | ------------------------------------------------ | -------------------------------------------------- |
   | Nom de la règle  | UpdateDeviceState                                | Nom décrivant l’objectif de la règle          |
   | Conditions | Paramètre obligatoire : `OnOff` et `SubjectDevice` | Conditions déterminant le moment où la règle peut s’exécuter    |
   | Actions    | `SendActivity` (voir ci-dessous)                        | Action à exécuter lorsque la condition de règle est remplie |

   > [!div class="mx-imgBorder"]
   > ![Charge utile d’activité d’envoi](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Créer des éléments visuels pour l’état activé ou désactivé de l’appareil

Dans [Démarrage rapide : Se connecter à une application de commandes personnalisées avec le Kit de développement logiciel (SDK) Speech (préversion)](./quickstart-custom-speech-commands-speech-sdk.md), vous avez créé une application cliente du Kit de développement logiciel (SDK) Speech qui gérait des commandes telles que `turn on the tv`, `turn off the fan`. Ajoutez maintenant des éléments visuels pour que vous puissiez voir le résultat de ces commandes.

Ajoutez des zones étiquetées avec du texte qui indique **Activé** ou **Désactivé** à l’aide du code XML suivant ajouté à `MainPage.xaml.cs`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Gérer la charge utile personnalisable

Maintenant que vous avez créé une charge utile JSON, vous pouvez ajouter une référence à la bibliothèque [JSON.NET](https://www.newtonsoft.com/json) pour gérer la désérialisation.

> [!div class="mx-imgBorder"]
> ![Charge utile d’activité d’envoi](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Dans `InitializeDialogServiceConnector`, ajoutez ce qui suit à votre gestionnaire d’événements `ActivityReceived`. Le code supplémentaire extraira la charge utile de l’activité et modifiera l’état visuel du téléviseur ou du ventilateur en conséquence.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var payload = activity?.Value;

    if(payload?.name == "SetDeviceState")
    {
        var state = payload?.state;
        var device = payload?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Faites un essai

1. Lancer l’application
1. Sélectionner Activer le microphone
1. Cliquer sur le bouton Parler
1. Dire `turn on the tv`
1. L’état visuel du téléviseur doit passer à « Activé »

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Guide pratique pour Ajouter des validations aux paramètres de commandes personnalisées (préversion)](./how-to-custom-speech-commands-validations.md)

