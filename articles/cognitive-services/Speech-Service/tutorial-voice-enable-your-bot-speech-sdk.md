---
title: 'Didacticiel : Activer les fonctions vocales dans votre bot à l’aide du SDK Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez créer un bot d’écho à l’aide de Microsoft Bot Framework, le déployer dans Azure, puis l’inscrire auprès du canal Bot Framework Direct Line Speech. Ensuite, vous allez configurer un exemple d’application cliente pour Windows qui vous permet de parler à votre bot et d’entendre ses réponses vocales.
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: dcohen
ms.openlocfilehash: 839bb24996ab782a386d7d28bcc1c06c686e6cd5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73578042"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Didacticiel : Activer les fonctions vocales dans votre bot à l’aide du SDK Speech

Vous pouvez désormais utiliser la puissance des services Speech pour activer les fonctions vocales dans un bot conversationnel en toute simplicité.

Dans ce tutoriel, vous allez créer un bot d’écho à l’aide de Microsoft Bot Framework, le déployer dans Azure, puis l’inscrire auprès du canal Bot Framework Direct Line Speech. Ensuite, vous allez configurer un exemple d’application cliente pour Windows qui vous permet de parler à votre bot et d’entendre ses réponses vocales.

Ce tutoriel est destiné aux développeurs qui débutent avec Azure, les bots Bot Framework, Direct Line Speech ou le SDK Speech, ainsi qu’à ceux qui souhaitent créer rapidement un système opérationnel avec peu de code. Aucune connaissance ni expérience de ces services ne sont nécessaires.

À la fin de cet exercice, vous aurez configuré un système qui fonctionne de la façon suivante :

1. L’exemple d’application cliente est configuré pour se connecter au canal Direct Line Speech et au bot d’écho.
1. L’audio est enregistré à l’aide du micro par défaut lorsque l’utilisateur appuie sur le bouton correspondant (il peut aussi être enregistré en continu si un mot clé personnalisé a été activé)
1. Si vous le souhaitez, la détection du mot clé personnalisé peut servir à contrôler le streaming audio vers le cloud
1. À l’aide du SDK Speech, l’application se connecte au canal Direct Line Speech et diffuse l’audio en continu.
1. Si vous le souhaitez, il est possible de configurer avec plus de précision la vérification du mot clé dans le service.
1. L’audio est transmis au service de reconnaissance vocale, puis il est retranscrit en texte.
1. Le texte reconnu est passé au bot d’écho en tant qu’activité Bot Framework. 
1. Le texte de la réponse est converti en audio par le service TTS (Text-to-Speech), puis il diffusé en continu vers l’application cliente afin d’être lu.

![diagram-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Flux du canal Speech")

> [!NOTE]
> Les étapes de ce tutoriel ne nécessitent pas l’utilisation d’un service payant. Pour suivre ce tutoriel, en tant que nouvel utilisateur Azure, vous pouvez utiliser les crédits de votre essai gratuit Azure, ainsi que le niveau de service gratuit des services Speech.

Voici ce qui est couvert par ce tutoriel :
> [!div class="checklist"]
> * Créer des ressources Azure
> * Générer, tester et déployer l’exemple de bot d’écho dans Azure App Service
> * Inscrire votre bot auprès d’un canal Direct Line Speech
> * Générer et exécuter le client Direct Line Speech pour interagir avec le bot d’écho
> * Ajouter l’activation du mot clé personnalisée
> * Découvrir comment modifier la langue du texte reconnu et prononcé

## <a name="prerequisites"></a>Prérequis

Voici ce dont vous aurez besoin pour suivre ce tutoriel :

- Un poste de travail Windows 10 avec un micro et des haut-parleurs (ou des écouteurs) qui fonctionnent
- [Visual Studio 2017 ou version ultérieure](https://visualstudio.microsoft.com/downloads/)
- [SDK .NET Core](https://dotnet.microsoft.com/download) version 2.1 ou ultérieure
- Un compte Azure. [Inscrivez-vous gratuitement](https://azure.microsoft.com/free/ai/).
- Un compte [GitHub](https://github.com/)
- [Git pour Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

L’application cliente que vous allez créer dans ce tutoriel utilise un petit nombre de services Azure. Pour réduire la durée d’aller-retour des réponses envoyées par votre bot, vous devez placer ces services dans la même région Azure. Dans cette section, vous allez créer un groupe de ressources dans la région **USA Ouest**. Ce groupe de ressources sera utilisé lors de la création de ressources pour Bot Framework, le canal Direct Line Speech et les services Speech.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**. Cliquez sur **Ajouter** pour ajouter un nouveau groupe de ressources.
1. Vous serez invité à fournir certaines informations :
   * Configurez **Abonnement** sur **Essai gratuit** (vous pouvez également utiliser un abonnement existant).
   * Entrez le nom de votre **Groupe de ressources**. Nous recommandons d’utiliser le nom suivant : **SpeechEchoBotTutorial-ResourceGroup**.
   * Dans la liste déroulante **Région**, sélectionnez **USA Ouest**.
1. Cliquez sur **Vérifier et créer**. Vous devez voir une bannière indiquant : **Validation réussie**.
1. Cliquez sur **Créer**. La création du groupe de ressources peut prendre quelques minutes.
1. Comme pour les ressources que vous allez créer plus loin dans ce tutoriel, il est recommandé d’épingler ce groupe de ressources à votre tableau de bord afin d’y accéder plus facilement. Si vous souhaitez épingler ce groupe de ressources, cliquez sur l’icône d’épingle en haut à droite du tableau de bord.

### <a name="choosing-an-azure-region"></a>Sélection d’une région Azure

Si vous souhaitez utiliser une autre région pour ce tutoriel, les facteurs suivants peuvent limiter vos choix :

* Veillez à utiliser une [région Azure prise en charge](regions.md#voice-assistants).
* Le canal Direct Line Speech utilise le service de conversion de texte par synthèse vocale, qui comprend les options de voix standard et neurale. L’option de voix neurale peut être [limitée à certaines régions Azure](regions.md#standard-and-neural-voices).
* Les clés d’essai gratuit peuvent être limitées à une certaine région.

Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Créer des ressources

Maintenant que vous disposez d’un groupe de ressources dans la région **USA Ouest**, l’étape suivante consiste à créer des ressources pour chaque service que vous allez utiliser dans ce tutoriel.

### <a name="create-a-speech-services-resource"></a>Créer une ressource pour les services Speech

Pour créer une ressource Speech, suivez ces instructions :

1. Dans le volet de navigation gauche, sélectionnez **Créer une ressource**.
2. Dans la barre de recherche, tapez **Speech**.
3. Sélectionnez **Speech**, puis cliquez sur **Créer**.
4. Vous serez invité à fournir certaines informations :
   * Donnez un **Nom**à votre ressource. Nous recommandons d’utiliser le nom suivant : **SpeechEchoBotTutorial-Speech**.
   * Pour **Abonnement**, vérifiez que l’option **Essai gratuit** est bien sélectionnée.
   * Pour **Emplacement**, sélectionnez **USA Ouest**.
   * Pour **Niveau tarifaire**, sélectionnez **F0**. Il s’agit du niveau de service gratuit.
   * Pour **Groupe de ressources**, sélectionnez **SpeechEchoBotTutorial-ResourceGroup**.
5. Après avoir entré toutes les informations nécessaires, cliquez sur **Créer**. La création de la ressource peut prendre plusieurs minutes.
6. Plus loin dans ce tutoriel, vous aurez besoin d’une clé d’abonnement pour ce service. Vous pouvez accéder à ces clés à tout moment à partir de la page **Vue d’ensemble** (Gérer les clés) ou **Clés** de votre ressource.

À ce stade, vérifiez que votre groupe de ressources (**SpeechEchoBotTutorial-ResourceGroup**) comprend une ressource Speech :

| NOM | TYPE  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Cognitive Services | USA Ouest |

### <a name="create-an-azure-app-service-plan"></a>Créer un plan Azure App Service

L’étape suivante consiste à créer un plan App Service. Un plan App Service définit un ensemble de ressources de calcul nécessaires à l’exécution d’une application web.

1. Dans le volet de navigation gauche, sélectionnez **Créer une ressource**.
2. Dans la barre de recherche, tapez **plan App Service**. Ensuite, localisez puis sélectionnez la carte **Plan App Service** dans les résultats de recherche.
3. Cliquez sur **Créer**.
4. Vous serez invité à fournir certaines informations :
   * Configurez **Abonnement** sur **Essai gratuit** (vous pouvez également utiliser un abonnement existant).
   * Pour **Groupe de ressources**, sélectionnez **SpeechEchoBotTutorial-ResourceGroup**.
   * Donnez un **Nom**à votre ressource. Nous recommandons d’utiliser le nom suivant : **SpeechEchoBotTutorial-AppServicePlan**.
   * Pour **Système d’exploitation**, sélectionnez **Windows**.
   * Pour **Région**, sélectionnez **USA Ouest**.
   * Pour **Niveau tarifaire**, vérifiez que l’option **Standard S1** est bien sélectionnée. Il doit s’agir de la valeur par défaut.
5. Cliquez sur **Vérifier et créer**. Vous devez voir une bannière indiquant : **Validation réussie**.
6. Cliquez sur **Créer**. La création du groupe de ressources peut prendre quelques minutes.

À ce stade, vérifiez que votre groupe de ressources (**SpeechEchoBotTutorial-ResourceGroup**) comprend les deux ressources suivantes :

| NOM | TYPE  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plan App Service | USA Ouest |
| SpeechEchoBotTutorial-Speech | Cognitive Services | USA Ouest |

## <a name="build-an-echo-bot"></a>Créer un bot d’écho

Maintenant que vous avez créé des ressources, vous allez créer un bot. Vous allez commencer par l’exemple de bot d’écho, qui, comme son nom l’indique, renvoie par écho le texte que vous avez entré comme réponse. L’exemple de code est prêt à être utilisé et ne nécessite aucune modification de votre part. Il est configuré pour fonctionner avec le canal Direct Line Speech, auquel nous nous connecterons après avoir déployé le bot dans Azure.

> [!NOTE]
> Les instructions qui suivent, ainsi que les informations supplémentaires sur le bot d’écho, sont disponibles dans le [fichier README de l’exemple sur GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="download-and-run-the-sample"></a>Télécharger et exécuter l’exemple

1. Clonez le référentiel de l’exemple :

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Lancez Visual Studio.
3. Dans la barre d’outils, sélectionnez **Fichier** > **Ouvrir** > **Projet/Solution**, puis ouvrez le fichier projet du bot d’écho qui a été configuré pour être utilisé avec le canal Direct Line Speech :

   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```

4. Une fois le projet chargé, appuyez sur `F5` pour exécuter le projet.

### <a name="test-with-the-bot-framework-emulator"></a>Effectuer des tests avec Bot Framework Emulator

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) est une application de bureau qui permet aux développeurs de bots de tester et de déboguer leurs bots localement ou à distance, via un tunnel. Effectuez les étapes suivantes pour Bot Framework Emulator en vue de tester votre bot d’écho.

1. Installez [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) version 4.3.0 ou ultérieure.
2. Lancez Bot Framework Emulator, puis ouvrez votre bot :
   * **Fichier** -> **Ouvrir le bot**.
3. Entrez l’URL de votre bot. Par exemple :

   ```
   http://localhost:3978/api/messages
   ```

4. Utilisez l’interface utilisateur pour communiquer avec votre bot à l’aide d’un texte tapé. Vérifiez que vous avez reçu une réponse.

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Déployer votre bot dans Azure App Service

L’étape suivante consiste à déployer le bot d’écho dans Azure. Il existe plusieurs façons de déployer un bot. Cela dit, dans ce tutoriel, nous allons nous concentrer sur la méthode de publication qui est effectuée directement à partir de Visual Studio.

> [!NOTE]
> Vous pouvez également déployer un bot à l’aide d’[Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) et de [modèles de déploiement](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates).

1. Dans Visual Studio, ouvrez le bot d’écho qui a été configuré pour être utilisé avec le canal Direct Line Speech :

   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur la solution **EchoBot**, puis sélectionnez **Publier...** .
1. Une nouvelle fenêtre intitulée **Choisir une cible de publication** s’ouvre.
1. Sélectionnez **App service** dans le volet de navigation de gauche, sélectionnez **Créer**, puis cliquez sur **Publier**.
1. Lorsque la fenêtre **Créer App Service** s’affiche :
   * Cliquez sur **Ajouter un compte**, puis connectez-vous avec les informations d’identification de votre compte Azure. Si vous êtes déjà connecté, sélectionnez le compte que vous souhaitez dans la liste déroulante.
   * Dans **Nom de l’application**, vous devez entrer un nom global unique pour votre bot. Ce nom sera utilisé pour créer l’URL unique du bot. Une valeur par défaut comprenant la date et l’heure (par exemple, « EchoBot20190805125647 ») sera fournie. Pour ce tutoriel, vous pouvez utiliser le nom par défaut.
   * Pour **Abonnement**, sélectionnez **Essai gratuit**.
   * Pour **Groupe de ressources**, sélectionnez **SpeechEchoBotTutorial-ResourceGroup**.
   * Pour **Plan d’hébergement**, sélectionnez **SpeechEchoBotTutorial-AppServicePlan**.
1. Cliquez sur **Créer**
1. Un message de réussite similaire à celui-ci doit s’afficher dans Visual Studio :

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Votre navigateur par défaut doit s’ouvrir sur une page indiquant ceci : « Votre bot est prêt ! ».
1. Dans le portail Azure, vérifiez que les trois ressources suivantes se trouvent dans votre groupe de ressources **SpeechEchoBotTutorial-ResourceGroup** :

| NOM | TYPE  | LOCATION |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA Ouest |
| SpeechEchoBotTutorial-AppServicePlan | Plan App Service | USA Ouest |
| SpeechEchoBotTutorial-Speech | Cognitive Services | USA Ouest |

## <a name="enable-web-sockets"></a>Activer les sockets web

Vous devez apporter une petite modification à la configuration afin que votre bot puisse communiquer avec le canal Direct Line Speech à l’aide de WebSockets. Effectuez les étapes suivantes pour activer les WebSockets :

1. Accédez au [portail Azure](https://portal.azure.com), puis à l’application App Service. La ressource doit porter un nom similaire à **EchoBot20190805125647** (le nom unique de votre application).
2. Dans le volet de navigation de gauche, sélectionnez **Paramètres**, puis cliquez sur **Configuration**.
3. Sélectionnez l’onglet **Paramètres généraux**.
4. Localisez le bouton bascule des **Web Sockets** et sélectionnez la valeur **Activé**.
5. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez utiliser les contrôles situés en haut de la page Azure App Service pour arrêter ou redémarrer le service. Cela peut être utile lors de la résolution des problèmes.

## <a name="create-a-channel-registration"></a>Créer une inscription de canal

Maintenant que vous avez créé une instance Azure App Service pour héberger votre bot, vous devez créer une inscription de canal de bot (**Bot Channels Registration** ). La création d’une inscription de canal est un prérequis à l’inscription de votre bot auprès de canaux Bot Framework, notamment auprès du canal Direct Line Speech.

> [!NOTE]
> Si vous souhaitez en savoir plus sur la façon dont les bots utilisent les canaux, consultez [Connecter un bot à des canaux](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).

1. La première étape consiste à créer une ressource pour l’inscription. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource**.
2. Dans la barre de recherche, tapez **bot**. Une fois les résultats affichés, sélectionnez **Bot Channels Registration** (Inscription de canal de bot).
3. Cliquez sur **Créer**.
4. Vous serez invité à fournir certaines informations :
   * Pour **Nom du bot**, entrez **SpeechEchoBotTutorial-BotRegistration**.
   * Pour **Abonnement**, sélectionnez **Essai gratuit**.
   * Pour **Groupe de ressources**, sélectionnez **SpeechEchoBotTutorial-ResourceGroup**.
   * Pour **Emplacement**, sélectionnez **USA Ouest**.
     * Pour **Niveau tarifaire**, sélectionnez **F0**.
     * Pour **Point de terminaison de messagerie**, entrez l’URL de votre application web, à la fin de laquelle vous ajoutez le chemin `/api/messages`. Par exemple, si votre nom d’application global unique est **EchoBot20190805125647**, votre point de terminaison de messagerie sera : `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * Pour **Application Insights**, vous pouvez sélectionner la valeur **Désactivé**. Pour plus d’informations, consultez [Analytique des bots](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignorez **Création automatique de l’ID d’application et du mot de passe**.
5. Revenez à **Bot Channels Registration** (Inscription de canal de bot), puis cliquez sur **Créer**.

À ce stade, accédez à votre groupe de ressources **SpeechEchoBotTutorial-ResourceGroup** dans le portail Azure. Il doit maintenant comprendre quatre ressources :

| NOM | TYPE  | LOCATION |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA Ouest |
| SpeechEchoBotTutorial-AppServicePlan | Plan App Service | USA Ouest |
| SpeechEchoBotTutorial-BotRegistration | Bot Channels Registration | Globale |
| SpeechEchoBotTutorial-Speech | Cognitive Services | USA Ouest |

> [!IMPORTANT]
> La ressource Bot Channels Registration affiche la région Globale alors que vous avez sélectionné USA Ouest. Ceci est normal.

## <a name="register-the-direct-line-speech-channel"></a>Inscrire le canal Direct Line Speech

Il est temps d’inscrire votre bot auprès du canal Direct Line Speech. Ce canal est utilisé pour créer une connexion entre votre bot d’écho et une application cliente compilée avec le SDK Speech.

1. Recherchez puis ouvrez votre ressource **SpeechEchoBotTutorial-BotRegistration** dans le [portail Azure](https://portal.azure.com).
1. Dans le volet de navigation de gauche, sélectionnez **Canaux**.
   * Recherchez **More channels** (Autres canaux), puis cliquez sur **Direct Line Speech**.
   * Passez en revue le texte de la page intitulée **Configurer Direct line Speech**, puis développez le menu déroulant intitulé « Compte Cognitive Services ».
   * Sélectionnez la ressource Speech que vous avez créée précédemment (par exemple **SpeechEchoBotTutorial-Speech**) dans le menu pour associer votre bot à votre clé d’abonnement Speech.

1. Dans la barre de navigation gauche, cliquez sur **Paramètres**.
   * Cochez la case **Enable Streaming Endpoint** (Activer le point de terminaison de streaming). Cela permet d’activer un protocole de communication reposant sur les WebSockets entre votre bot et le canal Direct Line Speech.
   * Cliquez sur **Enregistrer**.

> [!TIP]
> Pour plus d’informations, consultez [Connecter un bot à Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Cette page contient des informations supplémentaires et mentionne les problèmes connus.

## <a name="build-the-direct-line-speech-client"></a>Créer le client Direct Line Speech

Dans cette étape, vous allez créer le client Direct Line Speech. Le client est une application Windows Presentation Foundation (WPF) en langage C# qui utilise le [SDK Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) pour gérer les communications avec votre bot à l’aide du canal Direct Line Speech. Utilisez-le pour interagir avec votre bot et pour le tester avant d’écrire une application cliente personnalisée.

Le client Direct Line Speech offre une interface utilisateur simple qui vous permet de configurer la connexion à votre bot, d’afficher la conversation textuelle, d’afficher les activités Bot Framework au format JSON et d’afficher les cartes adaptatives. Il prend également en charge l’utilisation de mots clés personnalisés. Vous utiliserez ce client pour parler avec votre bot et recevoir ses réponses vocales.

Avant de continuer, vérifiez que votre micro et vos haut-parleurs sont activés et qu’ils fonctionnent.

1. Accédez au dépôt GitHub du [client Direct Line Speech](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md).
2. Suivez les instructions fournies pour cloner le dépôt, générer le projet, configurer le client, puis lancer le client.
3. Cliquez sur **Reconnect** et vérifiez que le message suivant s’affiche : **Press the mic button, or type to start talking to your bot** (Appuyez sur le bouton du micro ou tapez du texte pour parler au bot).
4. Nous allons tester cette fonctionnalité. Cliquez sur le bouton du micro et prononcez quelques mots en anglais. Le texte reconnu s’affichera à mesure que vous parlez. Lorsque vous avez fini de parler, le bot répond de sa propre voix, en disant « écho », puis les mots qu’il a reconnus.
5. Vous pouvez également utiliser du texte pour communiquer avec le bot. Il vous suffit pour cela de taper du texte dans la barre inférieure. 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Résolution des problèmes dans le client Direct Line Speech

Si un message d’erreur s’affiche dans la fenêtre principale de votre application, utilisez le tableau suivant pour identifier et résoudre le problème :

| Error | Que faire ? |
|-------|----------------------|
|App Error (see log for details): Microsoft.CognitiveServices.Speech.csharp: Value cannot be null. Parameter name: speechConfig | Il s’agit d’une erreur liée à l’application cliente. Vérifiez qu’une valeur a bien été fournie pour *Bot Secret* (Secret du bot) dans la fenêtre principale de l’application (voir la section [Inscrire votre bot auprès d’un canal Direct Line Speech](#register-the-direct-line-speech-channel)). |
|Error AuthenticationFailure: WebSocket Upgrade failed with an authentication error (401). Check for correct subscription key (or authorization token) and region name| Dans la page des paramètres de l’application, vérifiez que vous avez entré correctement la clé d’abonnement Speech ainsi que sa région.<br>Vérifiez que le secret du bot a été entré correctement. |
|Error ConnectionFailure: Connection was closed by the remote host. Code d’erreur : 1011. Détails de l’erreur : We could not connect to the bot before sending a message | Vérifiez que vous avez bien [coché la case « Enable Streaming Endpoint »](#register-the-direct-line-speech-channel) et/ou que les [**Web Sockets**](#enable-web-sockets) sont activés.<br>Vérifiez qu’Azure App Service est en cours d’exécution. Si c’est le cas, essayez de le redémarrer.|
|Error ConnectionFailure: Connection was closed by the remote host. Code d’erreur : 1011. Détails de l’erreur : Response status code does not indicate success: 500 (InternalServerError)| Votre bot a spécifié une voix neurale dans le champ [Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) de l’activité de sortie, mais la région Azure qui est associée à votre clé d’abonnement Speech ne prend pas en charge les voix neurales. Voir [Voix standard et neurales](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Error ConnectionFailure: Connection was closed by the remote host. Code d’erreur : 1000. Détails de l’erreur : Exceeded maximum web socket connection idle duration(> 300000 ms)| Il s’agit d’une erreur attendue qui se produit lorsqu’une connexion au canal reste ouverte et inactive pendant plus de cinq minutes. |

Si vous n’avez pas trouvé la solution à votre problème dans le tableau, consultez [Assistants vocaux : questions fréquentes](faq-voice-assistants.md).

### <a name="view-bot-activities"></a>Afficher les activités du bot

Chaque bot envoie et reçoit des messages liés aux **activités**. Dans la fenêtre **Activity Log** (Journal d’activité) du client Direct Line Speech, vous verrez des journaux horodatés pour chaque activité que le client reçoit du bot. Vous voyez également les activités que le client a envoyées au bot à l’aide de la méthode [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync). Lorsque vous sélectionnez un élément du journal, les détails de l’activité associée sont affichés au format JSON.

Voici un exemple de code JSON provenant d’une activité reçue par le client :
```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Pour plus d’informations sur la sortie JSON, consultez [Champs de l’activité](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Pour ce tutoriel, vous pouvez vous concentrer sur les champs [Text](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) et [Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak).

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Afficher le code source client pour les appels au SDK Speech

Le client Direct Line Speech utilise le package NuGet [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), qui contient le SDK Speech. Pour passer en revue l’exemple de code, nous vous conseillons de commencer par la méthode InitSpeechConnector() dans le fichier [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs), qui crée les deux objets suivants dans le SDK Speech :
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) - Pour les paramètres de configuration (clé d’abonnement Speech, région de la clé, secret du bot)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) - Pour gérer les événements de connexion au canal et d’abonnement client dans le cadre de la gestion des mots reconnus et des réponses du bot.

## <a name="add-custom-keyword-activation"></a>Ajouter l’activation du mot clé personnalisée

Le SDK Speech prend en charge l’activation du mot clé personnalisé. Comme avec « Hey Cortana » pour l’Assistant Microsoft, vous pouvez écrire une application qui écoute en permanence dans le but de détecter le mot clé de votre choix. Le mot clé peut comporter un ou plusieurs mots.

> [!NOTE]
> Les termes *phrase de sortie de veille* et *mot clé* sont interchangeables. Vous pouvez donc rencontrer les deux dans la documentation Microsoft.

La détection du mot clé est effectuée dans l’application cliente. Si vous utilisez un mot clé, l’audio ne sera diffusé en continu vers le canal Direct Line Speech que si le mot clé est détecté. Le canal Direct Line Speech comprend un composant appelé *Key Word Verification (KWV)* (Vérification du mot clé), qui effectue un traitement plus complexe dans le cloud afin de vérifier que le mot clé que vous avez choisi se trouve au début du flux audio. Si la vérification du mot clé aboutit à un résultat positif, le canal communiquera avec le bot.

Effectuez les étapes suivantes pour créer un modèle de mot clé, pour configurer le client Direct Line Speech de sorte qu’il utilise ce modèle, et enfin, pour le tester avec votre bot.

1. Suivez ces instructions pour [créer un mot clé personnalisé à l’aide du Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Décompressez le fichier de modèle que vous avez téléchargé à l’étape précédente. Son nom doit se composer de votre mot clé. Le fichier que vous recherchez doit se nommer `kws.table`.
3. Dans le client Direct Line Speech, accédez au menu des **paramètres**. Celui-ci est accessible via l’icône en forme de roue dentée qui est située dans le coin supérieur droit. Dans **Model file path** (Chemin du fichier de modèle), entrez le nom complet du chemin du fichier `kws.table` de l’étape 2.
4. Veillez à cocher la case **Enabled** (Activé). Le message suivant doit s’afficher à côté de la case à cocher : « Will listen for the keyword upon next connection ». (Écoutera pour détecter le mot clé à la prochaine connexion) Si vous avez fourni le mauvais fichier ou un chemin non valide, un message d’erreur doit s’afficher.
5. Entrez la **clé d’abonnement** Speech et la **région de la clé d’abonnement**, puis cliquez sur **OK** pour fermer le menu des **paramètres**.
6. Sélectionnez un **secret de bot**, puis cliquez sur **Reconnect** (Se reconnecter). Le message suivant doit s’afficher : « New conversation started - type, press the microphone button, or say the keyword » (Une nouvelle conversation a démarré : tapez du texte, appuyez sur le bouton du micro ou prononcez le mot clé). L’application écoute désormais de façon continue.
7. Prononcez n’importe quelle phrase précédée de votre mot clé. Par exemple: « **{mot clé**}, what time is it? ». Il n’est pas nécessaire de faire une pause après avoir prononcé le mot clé. Lorsque vous avez terminé, deux événements se produisent :
   * Vous verrez la transcription de ce que vous venez de dire.
   * Vous entendrez tout de suite après la réponse du bot.
8. Continuez de tester les trois types d’entrées pris en charge par le bot :
   * Taper du texte dans la barre inférieure
   * Appuyer sur l’icône du micro et parler
   * Prononcer une phrase précédée de votre mot clé

### <a name="view-the-source-code-that-enables-keyword"></a>Afficher le code source qui active le mot clé

Dans le code source du client Direct Line Client, examinez les fichiers suivants pour passer en revue le code qui est utilisé pour activer la détection du mot clé :

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs) comprend un appel à la méthode [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)du SDK Speech, qui est utilisé pour instancier le modèle à partir d’un fichier local situé sur le disque.
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs) comprend un appel à la méthode [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)du SDK Speech, qui active la détection continue du mot clé.

## <a name="optional-change-the-language-and-redeploy-your-bot"></a>(Facultatif) Changer la langue et redéployer le bot

Le bot que vous avez créé écoute et répond en anglais. Toutefois, vous n’êtes pas obligé d’utiliser l’anglais. Dans cette section, vous allez voir comment changer la langue utilisée par votre bot pour écouter et répondre, puis vous verrez comment redéployer le bot.

### <a name="change-the-language"></a>Changer la langue

1. Commençons par ouvrir `\experimental\directline-speech\csharp_dotnetcore\02.echo-bot\Bots\echo-bot.cs`.
2. Ensuite, nous allons accéder au code SSML. Il est facile à trouver, car il est compris entre les balises `<speak></speak>`.
3. Dans la chaîne SSML, trouvez la balise `<voice name>`, remplacez-la par `<voice name='de-DE-Stefan-Apollo'>`, puis enregistrez. Cette chaîne mise en forme indique au service de conversion de texte par synthèse vocale de retourner une réponse vocale synthétisée à l’aide de la voix `de-DE-Stefan-Apollo`, qui est optimisée pour l’allemand.

>[!NOTE]
> Vous n’êtes pas obligé d’utiliser l’allemand et pouvez choisir n’importe quelle voix parmi celles disponibles dans la liste [Speech service](language-support.md#text-to-speech).

### <a name="redeploy-your-bot"></a>Redéployer le bot

Maintenant que vous avez apporté les modifications nécessaires au bot, l’étape suivante consiste à le republier dans Azure App Service et à le tester :

1. Générez votre solution dans Visual Studio et corrigez les éventuelles erreurs de build.
2. Dans la fenêtre de l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **EchoBot**, puis sélectionnez **Publier**.
3. Votre configuration de déploiement précédente a déjà été chargée comme configuration par défaut. Cliquez sur **Publier** à côté de **EchoBot20190805125647 - Web Deploy**.
4. Le message **Publication réussie** s’affiche dans la fenêtre sortie de Visual Studio, et une page web s’ouvre avec le message « Votre bot est prêt ! ».
5. Ouvrez l’application cliente Direct Line Speech, cliquez sur le bouton des paramètres (l’icône en forme de roue dentée située en haut à droite), puis entrez `de-de` dans le champ Language. Cela définit la langue qui doit être reconnue, et remplace donc la valeur par défaut (`en-us`).
6. Suivez les instructions fournies dans la section [Créer le client Direct Line Speech](#build-the-direct-line-speech-client) pour vous reconnecter au bot nouvellement déployé, prononcez des mots dans la nouvelle langue et attendez la réponse du bot dans la langue et la voix définies.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne souhaitez pas continuer à utiliser le bot d’écho déployé dans ce tutoriel, vous pouvez le supprimer, ainsi que toutes les ressources Azure associées, en supprimant le groupe de ressources Azure **SpeechEchoBotTutorial-ResourceGroup**.

1. Sur le [portail Azure](https://portal.azure.com), cliquez sur **Groupes de ressources** dans le menu de gauche.
2. Recherchez le groupe de ressources nommé **SpeechEchoBotTutorial-ResourceGroup**. Cliquez sur les points de suspension (...).
3. Sélectionnez **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer votre propre application cliente avec le SDK Speech](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Voir aussi

* Effectuer un déploiement dans une [région Azure près de chez vous](https://azure.microsoft.com/global-infrastructure/locations/) pour voir l’amélioration du temps de réponse des bots
* Effectuer un déploiement dans une [région Azure qui prend en charge les voix de synthèse vocale neurales haute qualité](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Tarifs associés au canal Direct Line Speech :
  * [Tarification de Service Bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Services Speech](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Création et déploiement de votre propre bot avec fonctions vocales :
  * Créez un [bot Bot Framework](https://dev.botframework.com/). Inscrivez le bot auprès du [canal Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) et [personnalisez-le avec les fonctions vocales](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0).
  * Explorez les [solutions Bot Framework](https://github.com/microsoft/botframework-solutions) existantes : Créez un [assistant vocal personnalisé](https://docs.microsoft.com/azure/cognitive-services/speech-service/voice-assistants), puis activez les [fonctions vocales](https://github.com/microsoft/botframework-solutions/blob/master/docs/howto/assistant/csharp/speechenablement.md) dans cet assistant
