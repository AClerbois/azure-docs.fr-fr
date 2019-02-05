---
title: Charger, encoder et diffuser en streaming avec Azure Media Services v3 à l’aide de .NET | Microsoft Docs
description: Suivez les étapes décrites dans ce tutoriel pour charger un fichier et encoder la vidéo, puis diffuser en streaming votre contenu avec Media Services v3 à l’aide de .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: c3671df61eea5c826227706106cbb48dc70ad55f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157753"
---
# <a name="tutorial-upload-encode-and-stream-videos-using-net"></a>Didacticiel : Charger, encoder et diffuser en streaming des vidéos à l’aide de .NET

Azure Media Services vous permet d’encoder vos fichiers multimédias dans des formats pouvant être lus sur un large choix de navigateurs et d’appareils. Par exemple, vous pouvez streamer votre contenu au format HLS ou MPEG DASH d’Apple. Avant la diffusion en continu, vous devez encoder votre fichier multimédia numérique haute qualité. Pour obtenir des instructions d’encodage, consultez [Encoding concept](encoding-concept.md) (Concept d’encodage). Ce didacticiel charge un fichier vidéo local et encode le fichier chargé. Vous pouvez également encoder du contenu que vous mettez à disposition via une URL HTTPS. Pour plus d’informations, consultez [Créer une entrée de travail à partir d’une URL HTTP(s)](job-input-from-http-how-to.md).

![Lire la vidéo](./media/stream-files-tutorial-with-api/final-video.png)

Ce didacticiel vous explique les procédures suivantes :    

> [!div class="checklist"]
> * Télécharger l’exemple d’application décrit dans la rubrique
> * Examiner le code qui charge, encode et diffuse en continu
> * Exécution de l'application
> * Tester l’URL de diffusion en continu
> * Supprimer des ressources

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas Visual Studio, vous pouvez obtenir [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Créer un compte Media Services](create-account-cli-how-to.md).<br/>Veillez à mémoriser les valeurs que vous avez utilisées pour le nom du groupe de ressources et le nom du compte Media Services.
- Suivez les étapes mentionnées dans l’article [Accéder à l’API Azure Media Services avec Azure CLI](access-api-cli-how-to.md) et enregistrez les informations d’identification. Vous en aurez besoin pour accéder à l’API.

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Clonez un référentiel GitHub qui contient l’exemple .NET de diffusion en continu sur votre machine à l’aide de la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

L’exemple se trouve dans le dossier [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Ouvrez [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) dans votre projet téléchargé. Remplacez les valeurs par les informations d’identification que vous avez obtenues en [accédant aux API](access-api-cli-how-to.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Examiner le code qui charge, encode et diffuse en continu

Cette section examine les fonctions définies dans le fichier [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) du projet *UploadEncodeAndStreamFiles*.

L’exemple effectue les actions suivantes :

1. Crée une **transformation** (vérifie d’abord si la transformation spécifiée existe). 
2. Crée un **actif multimédia** de sortie qui est utilisé comme sortie du **travail** d’encodage.
3. Crée un **actif multimédia** d’entrée et charge le fichier vidéo local spécifié dans celui-ci. La ressource d’entrée est utilisée en tant qu’entrée du travail. 
4. Soumet le travail d’encodage à l’aide de l’entrée et de la sortie qui ont été créées.
5. Vérifier l’état du travail.
6. Crée un **localisateur de streaming**.
7. Crée des URL de diffusion en continu.

### <a name="a-idstartusingdotnet-start-using-media-services-apis-with-net-sdk"></a><a id="start_using_dotnet" />Commencer à utiliser les API Media Services avec le SDK .NET

Pour commencer à utiliser les API Media Services avec .NET, vous devez créer un objet **AzureMediaServicesClient**. Pour créer l’objet, vous devez fournir les informations d’identification nécessaires pour que le client puisse se connecter à Azure à l’aide d’Azure AD. Dans le code que vous avez cloné au début de l’article, la fonction **GetCredentialsAsync** crée l’objet ServiceClientCredentials basé sur les informations d’identification fournies dans le fichier de configuration local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Créer une ressource d’entrée et charger un fichier local dans celle-ci 

La fonction **CreateInputAsset** crée une nouvelle [ressource](https://docs.microsoft.com/rest/api/media/assets) d’entrée et charge le fichier vidéo local spécifié dans celle-ci. Cet **actif multimédia** est utilisé comme entrée de votre travail d’encodage. Dans Media Services v3, l’entrée d’un **travail** peut être un **actif multimédia** ou du contenu que vous mettez à la disposition de votre compte Media Services par le biais d’URL HTTPS. Si vous souhaitez savoir comment encoder à partir d’une URL HTTPS, consultez [cet](job-input-from-http-how-to.md) article.  

Dans Media Services v3, vous utilisez des API Stockage Azure pour charger des fichiers. L’extrait de code .NET suivant vous explique comment faire.

La fonction suivante effectue les actions ci-après :

* Crée un **actif multimédia** 
* Obtient une [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) accessible en écriture vers le [conteneur de stockage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) de l’actif multimédia
* Elle charge le fichier dans le conteneur de stockage à l’aide de l’URL SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Créer une ressource de sortie pour stocker le résultat d’un travail 

La [ressource](https://docs.microsoft.com/rest/api/media/assets) de sortie stocke le résultat de votre travail d’encodage. Le projet définit la fonction **DownloadResults** qui télécharge les résultats à partir de cette ressource de sortie dans le dossier « output », afin de voir ce que vous avez obtenu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Créer une transformation et un travail qui encode le fichier chargé

Lors de l’encodage ou du traitement de contenus dans Media Services, il est courant de configurer les paramètres d’encodage en tant que formule. Vous envoyez ensuite un **travail** pour appliquer cette formule à une vidéo. En envoyant de nouveaux travaux pour chaque nouvelle vidéo, vous appliquez cette formule à toutes les vidéos de votre bibliothèque. Une formule dans Media Services est référencée comme une **transformation**. Pour plus d’informations, consultez [Transformations et travaux](transform-concept.md). L’exemple décrit dans ce didacticiel définit une formule qui encode la vidéo, afin de la diffuser en continu sur divers appareils iOS et Android. 

#### <a name="transform"></a>Transformer

Lorsque vous créez une instance de [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez spécifier ce qu’elle doit produire comme sortie. Le paramètre requis est un objet **TransformOutput**, comme indiqué dans le code ci-dessous. Chaque objet **TransformOutput** contient un **préréglage**. Le **préréglage** décrit les instructions détaillées concernant les opérations de traitement vidéo et/ou audio qui doivent être utilisées pour générer l’objet **TransformOutput** souhaité. L’exemple décrit dans cet article utilise un préréglage appelé **AdaptiveStreaming**. Le préréglage encode la vidéo d’entrée dans une échelle des vitesses de transmission générée automatiquement (paires vitesse de transmission-résolution) basée sur la vitesse de transmission et la résolution de la sortie, et crée des fichiers MP4 ISO avec des fichiers audio AAC et des fichiers vidéo H.264 qui correspondent à chaque paire vitesse de transmission-résolution. Pour plus d’informations sur ce préréglage, consultez [Encode with an auto-generated bitrate ladder](autogen-bitrate-ladder.md) (Encoder avec une échelle des vitesses de transmission générée automatiquement).

Vous pouvez utiliser un préréglage EncoderNamedPreset intégré ou des préréglages personnalisés. Pour plus d’informations, consultez [How to customize encoder presets](customize-encoder-presets-how-to.md) (Procédure : personnaliser les présélections de l’encodeur).

Lorsque vous créez une [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez tout d’abord vérifier s’il en existe déjà une à l’aide de la méthode **Get**, comme indiqué dans le code qui suit.  Dans Media Services v3, les méthodes **Get** appliquées sur les entités renvoient **null** si l’entité n’existe pas (une vérification du nom respectant la casse).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Travail

Comme indiqué ci-dessus, l’objet [Transformation](https://docs.microsoft.com/rest/api/media/transforms) est la formule et un [travail](https://docs.microsoft.com/rest/api/media/jobs) est la requête réelle envoyée à Media Services pour appliquer cette **transformation** à un contenu vidéo ou audio d’entrée donné. Le **travail** spécifie des informations telles que l’emplacement de la vidéo d’entrée et celui de la sortie.

Dans cet exemple, la vidéo d’entrée a été chargée à partir de votre ordinateur local. Si vous souhaitez savoir comment encoder à partir d’une URL HTTPS, consultez [cet](job-input-from-http-how-to.md) article.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Attendre la fin du travail

Le travail prend du temps à se terminer et vous voulez être prévenu lorsque c’est le cas. L’exemple de code ci-dessous montre comment interroger le service pour connaître l’état du [travail](https://docs.microsoft.com/rest/api/media/jobs). L’interrogation n’est pas une meilleure pratique recommandée pour les applications de production en raison de la latence potentielle. L’interrogation peut être limitée si elle est utilisée de façon excessive sur un compte. À la place, les développeurs doivent utiliser Event Grid.

Event Grid est conçu pour une haute disponibilité, des performances cohérentes et une mise à l’échelle dynamique. Avec Event Grid, vos applications peuvent écouter les événements de presque tous les services Azure ou de toute source personnalisée, et y réagir. La gestion simple et réactive des événements basée sur HTTP vous aide à générer des solutions efficaces grâce au filtrage et au routage intelligents des événements.  Consultez [Acheminer des événements Azure Media Services vers un point de terminaison personnalisé à l’aide de CLI](job-state-events-cli-how-to.md).

Le **travail** passe généralement par les états suivants : **Planifié**, **En attente**,  **Traitement en cours**, **Terminé** (l’état final). Si le travail a rencontré une erreur, vous obtenez l’état **Erreur**. Si le travail est en cours d’annulation, vous obtenez **Annulation en cours** et **Annulé** une fois l’opération terminée.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="get-a-streaming-locator"></a>Obtenir un localisateur de streaming

Une fois l’encodage terminé, l’étape suivante consiste à mettre à la disposition des clients la vidéo dans la ressource de sortie pour qu’ils puissent la lire. Vous pouvez le faire en deux étapes : d’abord, créez un [localisateur de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators), puis générez les URL de streaming que les clients peuvent utiliser. 

Le processus de création d’un **localisateur de streaming** est appelée « publication ». Par défaut, le **localisateur de streaming** est valide immédiatement après avoir effectué les appels d’API et dure jusqu’à ce qu’il soit supprimé, sauf si vous configurez les durées de début et de fin optionnelles. 

Lors de la création d’un élément [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), vous devez spécifier le **StreamingPolicyName** souhaité. Dans cet exemple, vous allez diffuser en continu du contenu en clair ou non chiffré ; la stratégie de diffusion en continu en clair prédéfinie (**PredefinedStreamingPolicy.ClearStreamingOnly**) est donc utilisée.

> [!IMPORTANT]
> Quand vous utilisez une [stratégie de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) personnalisée, vous devez concevoir un ensemble limité de ces stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes protocoles et options de chiffrement sont nécessaires. Votre compte Media Services a un quota en matière de nombre d’entrées de stratégie de streaming. Vous ne devez pas créer une stratégie de diffusion en continu pour chaque localisateur de diffusion en continu.

Le code suivant suppose que vous appelez la fonction avec un locatorName unique.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Tandis que l’exemple de cette rubrique traite de streaming, vous pouvez utiliser le même appel pour créer un localisateur de streaming pour la diffusion de contenu vidéo par le biais d’un téléchargement progressif.

### <a name="get-streaming-urls"></a>Obtenir des URL de diffusion en continu

Maintenant que le [localisateur de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) a été créé, vous pouvez obtenir les URL de streaming, comme indiqué dans **GetStreamingURLs**. Pour générer une URL, vous devez concaténer le nom d’hôte du [point de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) et le chemin du **localisateur de streaming**. Dans cet exemple, le **point de terminaison de streaming** *par défaut* est utilisé. Quand vous créez pour la première fois un compte Media Services, ce **point de terminaison de streaming** *par défaut* est dans l’état Arrêté. Vous devez donc appeler **Start**.

> [!NOTE]
> Dans cette méthode, vous avez besoin de la valeur locatorName qui a été utilisé lors de la création du **localisateur de streaming** pour l’actif multimédia de sortie.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Supprimer les ressources de votre compte Media Services

En règle générale, vous devez supprimer tous les éléments à l’exception des objets que vous envisagez de réutiliser (habituellement, vous allez réutiliser les transformations et conserver les éléments StreamingLocators, etc.). Si vous souhaitez que votre compte soit propre après avoir effectué vos expériences, vous devez supprimer les ressources que vous n’envisagez pas de réutiliser.  Par exemple, le code suivant supprime les travaux.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

1. Appuyez sur Ctrl + F5 pour exécuter l’application *EncodeAndStreamFiles*.
2. Copiez l’une des URL de diffusion en continu à partir de la console.

Cet exemple affiche les URL qui peuvent être utilisées pour lire la vidéo à l’aide de différents protocoles :

![Sortie](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Tester l’URL de diffusion en continu

Pour tester la diffusion en continu, cet article utilise le lecteur multimédia Azure. 

> [!NOTE]
> Si un lecteur est hébergé sur un site https, veillez à mettre à jour l’URL vers « https ».

1. Ouvrez un navigateur web et accédez à [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Dans le champ **URL :**, collez l’une des valeurs d’URL de diffusion en continu que vous avez obtenues lors de l’exécution de l’application. 
3. Appuyez sur **Mise à jour du Lecteur Windows Media**.

Le lecteur multimédia Azure peut être utilisé pour effectuer des tests, mais ne doit pas être utilisé dans un environnement de production. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes de stockage et Media Services que vous avez créés pour ce didacticiel, supprimez le groupe de ressources créé précédemment.

Exécutez la commande CLI suivante :

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Les Kits de développement logiciel (SDK) Azure Media Services v3 ne sont pas thread-safe. Lorsque vous développez une application multithread, vous devez générer et utiliser un nouvel objet AzureMediaServicesClient par thread.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment charger, encoder et diffuser en continu votre vidéo, consultez l’article suivant : 

> [!div class="nextstepaction"]
> [Analyser des vidéos](analyze-videos-tutorial-with-api.md)
