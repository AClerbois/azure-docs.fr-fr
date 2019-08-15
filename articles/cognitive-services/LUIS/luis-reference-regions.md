---
title: Points de terminaison et régions de publication – LUIS
titleSuffix: Azure Cognitive Services
description: 3 régions de création et leurs portails prennent en charge les nombreuses régions de publication. La région dans laquelle vous publiez votre application LUIS correspond à la région ou à l’emplacement que vous spécifiez dans le portail Azure lorsque vous créez une clé de point de terminaison Azure LUIS. Quand vous publiez une application, LUIS génère automatiquement une URL de point de terminaison pour la région associée à la clé.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: f859047caf64d27ef63783ecd42f12fc9b79b4fd
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933344"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Création et la publication de régions et des clés associées

Trois régions création sont prises en charge par les portails LUIS correspondants. Pour publier une application LUIS dans plusieurs régions, vous avez besoin d’au moins une clé par région. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Régions de création LUIS
Il existe trois portails de création LUIS, en fonction de la région. Vous devez créer et publier dans la même région. 

|LUIS|Région de création|Nom de la région Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai]|Données<br>pas l’Europe<br>pas l’Australie| `westus`|
|[au.luis.ai][au.luis.ai]|Australie| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Europe|`westeurope`|

Les régions de création ont [des régions de basculement jumelées](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Régions de publication et ressources Azure
L’application est publiée dans toutes les régions associées aux ressources LUIS ajoutés dans le portail de LUIS. Par exemple, pour une application créée sur [www.luis.ai][www.luis.ai], si vous créez une ressource LUIS ou une ressource Cognitive Service dans **westus** et que vous [l’ajoutez à l’application en tant que ressource](luis-how-to-azure-subscription.md), l’application est publiée dans cette région. 

## <a name="public-apps"></a>Applications publiques
Une application publique est publiée dans toutes les régions. Ainsi, un utilisateur ayant une clé de ressource LUIS basée sur une région peut accéder à l’application dans toute région associée à sa clé de ressource.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Les régions de publication sont liées aux régions de création.

L’application de la région de création peut uniquement être publiée dans une région de publication correspondante. Si votre application est actuellement dans la mauvaise région de création, exportez l’application et importez-la dans la région de création correcte pour votre région de publication. 

Les applications LUIS créées sur https://www.luis.ai peuvent être publiées sur tous les points de terminaison à l’exception des régions [européenne](#publishing-to-europe) et [australienne](#publishing-to-australia). 

## <a name="publishing-to-europe"></a>Publication en Europe

Pour publier dans les régions européennes, vous créez des applications LUIS sur https://eu.luis.ai uniquement. Si vous essayez de publier n’importe où ailleurs à l’aide d’une clé dans la région Europe, LUIS affiche un message d’avertissement. Utilisez plutôt https://eu.luis.ai. Les applications LUIS créées sur [https://eu.luis.ai][eu.luis.ai] ne migrent pas automatiquement vers d’autres régions. Exportez et importez l’application LUIS pour la faire migrer.

## <a name="europe-publishing-regions"></a>Régions de publication en Europe

 Région globale | Région de l’API de création et de création de site Web| Région de publication et d’interrogation<br>`API region name`   |  Format de l’URL de point de terminaison   |
|-----|------|------|------|
| [Europe](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| France Centre<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europe](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europe Nord<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europe](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europe Ouest<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europe](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Sud du Royaume-Uni<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publication en Australie

Pour publier dans les régions australiennes, vous créez des applications LUIS sur https://au.luis.ai uniquement. Si vous essayez de publier n’importe où ailleurs à l’aide d’une clé dans la région Australie, LUIS affiche un message d’avertissement. Utilisez plutôt https://au.luis.ai. Les applications LUIS créées sur [https://au.luis.ai][au.luis.ai] ne migrent pas automatiquement vers d’autres régions. Exportez et importez l’application LUIS pour la faire migrer.

## <a name="australia-publishing-regions"></a>Régions de publication en Australie

 Région globale | Région de l’API de création et de création de site Web| Région de publication et d’interrogation<br>`API region name`   |  Format de l’URL de point de terminaison   |
|-----|------|------|------|
| [Australie](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australie Est<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publication dans d’autres régions

Pour publier dans les autres régions, vous créez des applications LUIS sur [https://www.luis.ai](https://www.luis.ai) uniquement. 

## <a name="other-publishing-regions"></a>Autres régions de publication

 Région globale | Région de l’API de création et de création de site Web| Région de publication et d’interrogation<br>`API region name`   |  Format de l’URL de point de terminaison   |
|-----|------|------|------|
| Afrique | `westus`<br>[www.luis.ai][www.luis.ai]| Afrique du Sud Nord<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Inde Centre<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Asie Est<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japon Est<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Japon Ouest<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Centre de la Corée<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asie | `westus`<br>[www.luis.ai][www.luis.ai]| Asie Sud-Est<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Centre du Canada<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord |`westus`<br>[www.luis.ai][www.luis.ai] | USA Centre<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord | `westus`<br>[www.luis.ai][www.luis.ai] | USA Est 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord | `westus`<br>[www.luis.ai][www.luis.ai] | USA Centre Nord<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Amérique du Nord | `westus`<br>[www.luis.ai][www.luis.ai] | USA Centre Sud<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Amérique du Nord |`westus`<br>[www.luis.ai][www.luis.ai] | USA Centre-Ouest<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Amérique du Nord | `westus`<br>[www.luis.ai][www.luis.ai] | USA Ouest<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Amérique du Nord |`westus`<br>[www.luis.ai][www.luis.ai] | USA Ouest 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Amérique du Sud | `westus`<br>[www.luis.ai][www.luis.ai] | Brésil Sud<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Points de terminaison

LUIS dispose actuellement de 2 points de terminaison : un pour la création et l’autre pour l’analyse de prédiction de requêtes.

|Objectif|URL|
|--|--|
|Création|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Analyse de texte (prédiction de requête)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

Le tableau suivant décrit les paramètres qui apparaissent entre accolades `{}` dans le tableau précédent.

|Paramètre|Objectif|
|--|--|
|region|Région Azure - Les régions de création et de publication sont différentes|
|appID|ID d’application LUIS utilisé dans l’itinéraire d’URL et figurant dans le tableau de bord de l’application|
|q|Texte de l’énoncé envoyé à partir de l’application cliente en tant que bot de conversation|

## <a name="failover-regions"></a>Régions de basculement

Chaque région a une région secondaire vers laquelle basculer. L’Europe bascule à l’intérieur de l’Europe et l’Australie à l’intérieur de l’Australie.

Les régions de création ont [des régions de basculement jumelées](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Informations de référence sur les entités prédéfinies](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
