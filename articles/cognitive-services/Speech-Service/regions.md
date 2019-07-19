---
title: Régions - Services Speech
titlesuffix: Azure Cognitive Services
description: Informations de référence pour les régions du service Speech.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 1ad618e9eb70fd75f433030584c0f6538532928f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466985"
---
# <a name="speech-service-supported-regions"></a>Régions prises en charge pour le service Speech

Le service Speech permet à votre application de convertir de l’audio en texte, d’effectuer une traduction vocale et de convertir du texte par synthèse vocale. Le service est disponible dans plusieurs régions avec des points de terminaison uniques pour le SDK et les API REST Speech.

Vérifiez que vous utilisez le point de terminaison qui correspond à la région de votre abonnement.

## <a name="speech-sdk"></a>Kit de développement logiciel (SDK) de reconnaissance vocale

Dans le [SDK Speech](speech-sdk.md), les régions sont spécifiées sous forme de chaînes (par exemple, en tant que paramètres de `SpeechConfig.FromSubscription`dans le SDK Speech pour C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Reconnaissance vocale, synthèse vocale et traduction vocale

Le kit de développement logiciel (SDK) Speech est disponible dans les régions suivantes pour la **reconnaissance vocale**, la **synthèse vocale** et la **traduction** :

  Région | Paramètre du SDK Speech | Portail de personnalisation de reconnaissance vocale
 ------|-------|--------
 USA Ouest | `westus` | https://westus.cris.ai
 USA Ouest 2 | `westus2` | https://westus2.cris.ai
 USA Est | `eastus` | https://eastus.cris.ai
 USA Est 2 | `eastus2` | https://eastus2.cris.ai
 USA Centre | `centralus` | https://centralus.cris.ai
 USA Centre Nord | `northcentralus` | https://northcentralus.cris.ai
 USA Centre Sud | `southcentralus` | https://southcentralus.cris.ai
 Inde Centre | `centralindia` | https://centralindia.cris.ai
 Asie Est | `eastasia` | https://eastasia.cris.ai
 Asie Sud-Est | `southeastasia` | https://southeastasia.cris.ai
 Japon Est | `japaneast` | https://japaneast.cris.ai
 Centre de la Corée | `koreacentral` | https://koreacentral.cris.ai
 Australie Est | `australiaeast` | https://australiaeast.cris.ai
 Centre du Canada | `canadacentral` | https://canadacentral.cris.ai
 Europe Nord | `northeurope` | https://northeurope.cris.ai
 Europe Ouest | `westeurope` | https://westeurope.cris.ai
 Sud du Royaume-Uni | `uksouth` | https://uksouth.cris.ai
 France Centre | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Reconnaissance de l’intention

Les régions disponibles pour la **reconnaissance de l'intention** via le kit de développement logiciel (SDK) Speech sont les suivantes :

 Région globale | Région | Paramètre du SDK Speech
 ------|-------|--------
 Asie | Asie Est | `eastasia`
 Asie | Asie Sud-Est | `southeastasia`
 Australie | Australie Est | `australiaeast`
 Europe | Europe Nord | `northeurope`
 Europe | Europe Ouest | `westeurope`
 Amérique du Nord | USA Est | `eastus`
 Amérique du Nord | USA Est 2 | `eastus2`
 Amérique du Nord | USA Centre Sud | `southcentralus`
 Amérique du Nord | USA Centre-Ouest | `westcentralus`
 Amérique du Nord | USA Ouest | `westus`
 Amérique du Nord | USA Ouest 2 | `westus2`
 Amérique du Sud | Brésil Sud | `brazilsouth`

Il s'agit d'un sous-ensemble des régions de publication prises en charge par le [service Language Understanding (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-first-virtual-assistants"></a>Assistants virtuels « voice-first »

Le [Kit de développement logiciel (SDK) Speech](speech-sdk.md) prend en charge les fonctionnalités d’**assistant virtuel basé sur la voix en priorité** dans les régions suivantes :

Région | Paramètre du SDK Speech
-------|---------------------
USA Ouest | `westus`
USA Ouest 2 | `westus2`
USA Est | `eastus`
USA Est 2 | `eastus2`
Europe Ouest | `westeurope`
Europe Nord | `northeurope`
Asie Sud-Est | `southeastasia`

## <a name="rest-apis"></a>API REST

Le service Speech expose également des points de terminaison REST pour les requêtes de reconnaissance vocale et de synthèse vocale.

### <a name="speech-to-text"></a>Reconnaissance vocale

Pour obtenir la documentation de référence relative à la reconnaissance vocale, consultez [API REST de reconnaissance vocale](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Synthèse vocale

Pour obtenir la documentation de référence relative à la synthèse vocale, consultez [API REST de synthèse vocale](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]