---
title: Régions dans lesquelles Video Indexer est disponible - Azure
titlesuffix: Azure Media Services
description: Cet article traite des régions Azure dans lesquelles Video Indexer est disponible.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 8c1d51c2bb6062f1360c831549e523a5821ff14b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532169"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Régions Azure dans lesquelles Video Indexer est présent

Les API Video Indexer contiennent un paramètre **location** que vous devez définir sur la région Azure vers laquelle l’appel doit être routé. Il doit s’agir d’une [région Azure dans laquelle Video Indexer est disponible](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Emplacements

Le nom de code de la région Azure doit être affecté comme valeur au paramètre **location**. Si vous utilisez Video Indexer en mode Aperçu, vous devez indiquer *« trial »* comme valeur. Sinon, pour obtenir le nom de code de la région Azure dans laquelle se trouve votre compte et vers laquelle votre appel doit être routé, vous pouvez exécuter la ligne suivante dans [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) :

```bash
az account list-locations
```

Une fois que vous avez exécuté la ligne ci-dessus, vous obtenez une liste de toutes les régions Azure. Accédez à la région Azure qui présente la valeur *displayName* que vous recherchez et utilisez sa valeur *name* pour le paramètre **location**.

Par exemple, pour la région Azure West US 2 (USA Ouest 2) affichée ci-dessous, vous allez utiliser « westus2 » pour le paramètre **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Étapes suivantes

- [Personnaliser le modèle de langue à l’aide des API](customize-language-model-with-api.md)
- [Personnaliser le modèle de marques à l’aide des API](customize-brands-model-with-api.md)
- [Personnaliser le modèle de personne à l’aide des API](customize-person-model-with-api.md)
