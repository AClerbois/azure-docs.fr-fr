---
title: Exécuter le conteneur Form Recognizer dans Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Déployez le conteneur Form Recognizer sur un Azure Container Instance et testez-le dans un navigateur web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: de81fcb5ee62f9b295c93132b271507c040af46a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512770"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Déployer le conteneur Form Recognizer sur Azure Container Instances

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Découvrez comment déployer le conteneur [Form Recognizer](form-recognizer-container-howto.md) de Cognitive Services sur Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Cette procédure illustre la création d’une ressource Azure Form Recognizer. Puis nous aborderons l’extraction de l’image de conteneur associée. Enfin, nous mettrons en avant la possibilité d’exercer l’orchestration des deux à partir d’un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
