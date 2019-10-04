---
title: Réaction aux événements Stockage Blob Azure (préversion) | Microsoft Docs
description: Utilisez Azure Event Grid pour vous abonner à des événements de stockage Blob.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: b813ef89bb1a55f769d0ea2391855ba5d671c140
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648794"
---
# <a name="reacting-to-blob-storage-events"></a>Réaction aux événements de Stockage Blob

Les événements de Stockage Azure permettent aux applications de réagir à des événements, tels que la création et la suppression d’objets blob, à l’aide d’architectures modernes serverless. et sans qu’il soit nécessaire de faire appel à du code complexe ou à des services d’interrogation coûteux et inefficaces.

Au lieu de cela, les événements sont envoyés via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) aux abonnés, comme Azure Functions, Azure Logic Apps, ou même à votre propre écouteur http personnalisé, et vous payez seulement pour ce que vous utilisez.

Les événements du stockage d’objets blob sont envoyés de manière fiable au service Event Grid qui fournit des services de livraison fiables à vos applications via des stratégies enrichies de nouvelle tentative et de livraison de lettres mortes.

Les scénarios d’événements de stockage d’objets Blob courants incluent le traitement d’images ou de vidéos, l’indexation pour la recherche ou n’importe quel flux de travail orienté fichier. Les téléchargements de fichier asynchrones sont parfaitement adaptés aux événements. Lorsque les modifications sont peu fréquentes, mais que votre scénario requiert une réactivité immédiate, une architecture basée sur des événements peut être particulièrement efficace.

Si vous souhaitez faire un essai maintenant, consultez l’un des articles de démarrage rapide suivants :

|Si vous souhaitez utiliser cet outil :    |Lisez l’article : |
|--|-|
|Portail Azure    |[Démarrage rapide : Acheminer des événements de stockage Blob vers un point de terminaison web avec le portail Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Démarrage rapide : Acheminer des événements de stockage vers un point de terminaison web avec PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|D’Azure CLI    |[Démarrage rapide : Acheminer des événements de stockage vers un point de terminaison web avec Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Si votre compte dispose d’un espace de noms hiérarchique, ce tutoriel vous montre comment associer un abonnement Event Grid, une fonction Azure et un [travail](https://docs.azuredatabricks.net/user-guide/jobs.html) dans Azure Databricks : [Tutoriel : Utiliser des événements Azure Data Lake Storage Gen2 pour mettre à jour une table Databricks Delta](data-lake-storage-events.md).

## <a name="the-event-model"></a>Le modèle d’événement

Event Grid utilise les [abonnements aux événements](../../event-grid/concepts.md#event-subscriptions) pour acheminer les messages d’événements vers les abonnés. Cette image illustre la relation entre les éditeurs d’événements, les abonnements aux événements et les gestionnaires d’événements.

![Modèle de Event Grid](./media/storage-blob-event-overview/event-grid-functional-model.png)

Tout d’abord, abonnez un point de terminaison à un événement. Ensuite, lorsqu’un événement est déclenché, le service Event Grid envoie les données relatives à cet événement au point de terminaison.

Consultez l’article [Schéma d’événements de stockage Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour obtenir :

> [!div class="checklist"]
> * Une liste complète des événements de stockage Blob et savoir comment chaque événement est déclenché.
> * Un exemple des données que Event Grid peut envoyer pour chacun de ces événements.
> * L’objectif de chaque paire de clé-valeur qui apparaît dans les données.

## <a name="filtering-events"></a>Filtrage des événements

Les abonnements aux événements d’objet Blob peuvent être filtrés en fonction du type d’événement et par le nom du conteneur et le nom de l’objet blob de l’objet qui a été créé ou supprimé.  Des filtres peuvent être appliqués aux abonnements aux événements pendant la [création](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) de l’abonnement aux événements ou [ultérieurement](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Les filtres d’objet dans Event Grid reposent sur les correspondances « commence par » et « se termine par », afin que les événements dont l’objet correspond soient remis à l’abonné.

Pour en savoir plus sur l’application de filtres, consultez [Filtrer des événements pour Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

Le sujet des événements de stockage d’objets Blob utilise le format suivant :

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Pour faire correspondre tous les événements pour un compte de stockage, vous pouvez laisser les filtres de sujet vides.

Pour faire correspondre les événements à partir d’objets Blob créés dans un ensemble de conteneurs partageant un préfixe, utilisez un filtre `subjectBeginsWith`, comme :

```
/blobServices/default/containers/containerprefix
```

Pour faire correspondre les événements d’objet Blob créés dans un conteneur spécifique, utilisez un filtre `subjectBeginsWith`, comme :

```
/blobServices/default/containers/containername/
```

Pour faire correspondre les événements d’objet Blob créés dans un conteneur spécifique partageant un préfixe de nom d’objet Blob, utilisez un filtre `subjectBeginsWith`, comme :

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Pour faire correspondre les événements d’objet Blob créés dans un conteneur spécifique partageant un suffixe d’objet Blob, utilisez un filtre `subjectEndsWith`, comme « .log » ou « .jpg ». Pour plus d’informations, consultez [Concepts d’Event Grid](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Pratiques pour la consommation d’événements

Les applications qui gèrent des événements de stockage d’objets Blob doivent suivre certaines pratiques recommandées :
> [!div class="checklist"]
> * Comme plusieurs abonnements peuvent être configurés pour acheminer les événements vers le même gestionnaire d’événements, il est important de ne pas considérer que les événements proviennent d’une source particulière, mais de vérifier le sujet du message pour vous assurer qu’il provient d’un compte de stockage que vous attendez.
> * De même, vérifiez que vous êtes prêt à traiter son eventType, et ne supposez pas que tous les événements reçus seront aux types que vous attendez.
> * Les messages pouvant arriver en désordre et après un certain temps, utilisez les champs etag pour comprendre si vos informations sur les objets sont toujours à jour.  En outre, utilisez les champs de séquence pour comprendre l’ordre des événements sur un objet particulier.
> * Utilisez le champ blobType pour comprendre le type d’opération autorisé sur l’objet Blob, et les types de bibliothèque client que vous devez utiliser pour accéder à l’objet Blob. Les valeurs valides sont `BlockBlob` ou `PageBlob`. 
> * Utilisez le champ URL avec les constructeurs `CloudBlockBlob` et `CloudAppendBlob` pour accéder à l’objet Blob.
> * Ignorez les champs que vous ne comprenez pas. Cette pratique vous aidera à prendre en charge les nouvelles fonctionnalités qui peuvent être ajoutées à l’avenir.
> * Si vous souhaitez vous assurer que l’événement **Microsoft.Storage.BlobCreated** n’est déclenché que lorsqu’un objet blob de blocs est entièrement validé, filtrez l’événement pour les appels d’API REST `CopyBlob`, `PutBlob`, `PutBlockList` ou `FlushWithClose`. Ces d’appels d’API ne déclenchent l’événement **Microsoft.Storage.BlobCreated** que lorsque les données sont entièrement validées en un objet blob de blocs. Pour savoir comment créer un filtre, consultez [Filtrer des événements pour Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur Event Grid et essayer les événements de stockage d’objets Blob :

- [À propos d’Event Grid](../../event-grid/overview.md)
- [Acheminer des événements de stockage Blob Azure vers un point de terminaison Web personnalisé ](storage-blob-event-quickstart.md)
