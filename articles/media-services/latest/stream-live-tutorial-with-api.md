---
title: Diffuser en direct avec Azure Media Services v3 à l’aide de .NET | Microsoft Docs
description: Ce didacticiel vous présente les étapes pour diffuser en temps réel avec Media Services v3 à l’aide de .NET Core.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: e4f32e14e8c1035055bd8a37bb453764984fbe4d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149134"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-net"></a>Didacticiel : Diffuser en direct avec Media Services v3 à l’aide de .NET

Dans Azure Media Services, les [événements en direct](https://docs.microsoft.com/rest/api/media/liveevents) sont responsables du traitement de contenu de streaming en direct. Un événement en direct fournit un point de terminaison d’entrée (URL de réception) que vous fournissez ensuite à un encodeur live. Il reçoit des flux d’entrée en temps réel d’un encodeur live et le rend disponible pour le streaming par le biais d’un ou de plusieurs [points de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints). Les événements en direct fournissent également un point de terminaison d’aperçu (URL d’aperçu) que vous utilisez pour prévisualiser et valider votre flux avant de poursuivre le traitement ou la distribution. Ce didacticiel explique comment utiliser .NET Core pour créer un type de **canal direct** d’un événement en temps réel. 

> [!NOTE]
> Veillez à consulter [Diffusion en continu avec Media Services v3](live-streaming-overview.md) avant de commencer. 

Ce didacticiel explique les procédures suivantes :    

> [!div class="checklist"]
> * Télécharger l’exemple d’application décrit dans la rubrique
> * Examiner le code qui effectue la diffusion en continu
> * Surveiller l’événement avec [Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) à l’adresse https://ampdemo.azureedge.net
> * Supprimer des ressources

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour suivre le didacticiel.

- Installez Visual Studio Code ou Visual Studio.
- [Créer un compte Media Services](create-account-cli-how-to.md).<br/>Veillez à mémoriser les valeurs que vous avez utilisées pour le nom du groupe de ressources et le nom du compte Media Services.
- Suivez les étapes décrites dans [Accéder à l’API Azure Media Services avec Azure CLI](access-api-cli-how-to.md) et enregistrez les informations d’identification. Vous en aurez besoin pour accéder à l’API.
- Une caméra ou appareil (ordinateur portable) utilisé pour diffuser un événement.
- Un encodeur vidéo dynamique local qui convertit les signaux de la caméra en flux de données qui sont envoyés vers le service de vidéo en flux continu Media Services. La diffusion doit se faire au format **RTMP** ou **Smooth Streaming**.

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Clonez un référentiel GitHub qui contient l’exemple .NET de diffusion en continu sur votre machine à l’aide de la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

L’exemple de diffusion en continu est situé dans le dossier [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Ouvrez [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) dans votre projet téléchargé. Remplacez les valeurs par les informations d’identification que vous avez obtenues en [accédant aux API](access-api-cli-how-to.md).

> [!IMPORTANT]
> Cet exemple utilise un suffixe unique pour chaque ressource. Si vous annulez le débogage ou que vous fermez l’application sans l’avoir effectué, vous obtenez plusieurs événements en direct sur votre compte. <br/>Veillez à arrêter les événements en direct en cours d’exécution. Sinon, vous serez **facturé** !

## <a name="examine-the-code-that-performs-live-streaming"></a>Examiner le code qui effectue la diffusion en continu

Cette section examine les fonctions définies dans le fichier [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) du projet *MediaV3LiveApp*.

L’exemple crée un suffixe unique pour chaque ressource afin d’éviter tout conflit de noms si vous exécutez l’exemple plusieurs fois sans le supprimer.

> [!IMPORTANT]
> Cet exemple utilise un suffixe unique pour chaque ressource. Si vous annulez le débogage ou que vous fermez l’application sans l’avoir effectué, vous obtenez plusieurs événements en direct sur votre compte. <br/>
> Veillez à arrêter les événements en direct en cours d’exécution. Sinon, vous serez **facturé** !
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Commencer à utiliser les API Media Services avec le Kit de développement logiciel (SDK) .NET

Pour commencer à utiliser les API Media Services avec .NET, vous devez créer un objet **AzureMediaServicesClient**. Pour créer l’objet, vous devez fournir les informations d’identification nécessaires pour que le client puisse se connecter à Azure à l’aide d’Azure AD. Dans le code que vous avez cloné au début de l’article, la fonction **GetCredentialsAsync** crée l’objet ServiceClientCredentials basé sur les informations d’identification fournies dans le fichier de configuration local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Créer un événement en temps réel

Cette section montre comment créer un type de **transmission directe** d’événement en direct (LiveEventEncodingType défini sur Aucun). Pour plus d’informations sur les types d’événements en direct disponibles, consultez [Types d’événements en direct](live-events-outputs-concept.md#live-event-types). 
 
Voici quelques éléments que vous voudrez probablement spécifier lors de la création de l’événement en temps réel :

* Emplacement Media Services 
* Le protocole de streaming de l’événement en direct (les protocoles RTMP et Smooth Streaming sont actuellement pris en charge).<br/>Vous ne pouvez pas changer l’option de protocole pendant l’exécution de l’événement en direct ou des sorties en direct qui lui sont associées. Si vous avez besoin d’autres protocoles, vous devez créer des événements en direct distincts pour chaque protocole de streaming.  
* Restictions IP sur l’ingestion et la préversion. Vous pouvez définir les adresses IP autorisées à recevoir du contenu vidéo sur cet événement en direct. Les adresses IP autorisées peuvent être définies sous forme d’adresse IP unique (par exemple, « 10.0.0.1 »), de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau CIDR (par exemple, « 10.0.0.1/22 ») ou de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau au format décimal séparé par des points (par exemple, « 10.0.0.1(255.255.252.0) »).<br/>Si aucune adresse IP n’est spécifiée et qu’il n’existe pas de définition de règle, alors aucune adresse IP ne sera autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur 0.0.0.0/0.<br/>Les adresses IP doivent utiliser un des formats suivants : adresses IpV4 avec 4 nombres, plage d’adresses CIDR.
* Lors de la création de l’événement, vous pouvez préciser que vous voulez que le démarrage soit automatique. <br/>Lorsque le démarrage automatique est défini sur true, l’événement en direct démarre après sa création. La facturation commence donc dès que son exécution commence. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en direct pour arrêter toute facturation supplémentaire. Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obtenir les URL de réception

Une fois l’événement en direct créé, vous pouvez obtenir des URL de réception que vous devez fournir à l’encodeur live. L’encodeur utilise ces URL pour entrer un flux dynamique.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obtenir l’URL d’aperçu

Utilisez le point de terminaison d’aperçu et vérifiez que l’entrée de l’encodeur est bien reçue.

> [!IMPORTANT]
> Veillez à ce que la vidéo transite par l’URL d’aperçu avant de poursuivre !

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Créer et gérer des événements en direct et des sorties en direct

Une fois que le flux transite dans l’événement en direct, vous pouvez commencer l’événement de streaming en créant un actif multimédia, une sortie en direct et un localisateur de streaming. Le flux est alors archivé et mis à la disposition des observateurs via le point de terminaison de diffusion en continu. 

#### <a name="create-an-asset"></a>Créer une ressource

Créez un actif multimédia pour la sortie en direct à utiliser.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Créer une sortie en direct

Les sorties en direct démarrent dès leur création et s’arrêtent à leur suppression. Quand vous supprimez une sortie en direct, vous ne supprimez pas l’actif multimédia sous-jacent ni le contenu de celui-ci.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Créer un localisateur de streaming

> [!NOTE]
> Une fois votre compte Media Services créé, un point de terminaison de streaming **par défaut** est ajouté à votre compte à l’état **Arrêté**. Pour démarrer le streaming de votre contenu et tirer parti de l’[empaquetage dynamique](dynamic-packaging-overview.md) et du chiffrement dynamique, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état **En cours d’exécution**. 

Quand vous publiez l’actif Sortie en direct à l’aide d’un localisateur de streaming, l’événement en direct (jusqu’à la longueur de la fenêtre DVR) reste visible jusqu’à l’expiration ou la suppression du localisateur de streaming, en fonction de ce qui se produit en premier.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Suppression des ressources de votre compte Media Services

Si vous avez terminé de diffuser en continu les événements et que vous voulez nettoyer les ressources configurées précédemment, suivez la procédure ci-dessous.

* Arrêtez d’envoyer le flux à partir de l’encodeur.
* Arrêtez l’événement en direct. Une fois l’événement en direct arrêté, aucuns frais ne sont encourus. Lorsque vous devez le redémarrer, il possède la même URL de réception. Vous n’avez donc pas besoin de reconfigurer votre encodeur.
* Vous pouvez arrêter votre point de terminaison de diffusion en continu, sauf si vous souhaitez continuer à fournir l’archive de votre événement en direct en tant que flux à la demande. Si l’événement en direct est dans l’état Arrêté, aucuns frais ne sont encourus.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

Le code suivant montre comment nettoyer votre compte de tous les événements en direct :

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>Regarder l’événement

Pour regarder l’événement, copiez l’URL de streaming obtenue à l’exécution du code décrit dans Créer un localisateur de streaming et utilisez le lecteur de votre choix. Vous pouvez utiliser le [Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) pour tester votre flux à l’adresse https://ampdemo.azureedge.net. 

Une fois arrêté, l’événement en direct est automatiquement converti en contenu à la demande. Même après l’arrêt et la suppression de l’événement, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n’aurez pas supprimé l’élément multimédia. Un élément multimédia ne peut pas être supprimé s’il est utilisé par un événement ; vous devez d’abord supprimer l’événement. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes de stockage et Media Services que vous avez créés pour ce didacticiel, supprimez le groupe de ressources créé précédemment.

Exécutez la commande CLI suivante :

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Laisser l’événement en direct s’exécuter entraîne des frais. Sachez que si le projet/programme plante ou est fermé pour une quelconque raison, l’événement peut continuer de s’exécuter et ainsi entraîner des frais.

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

[Diffuser des fichiers](stream-files-tutorial-with-api.md)
 
