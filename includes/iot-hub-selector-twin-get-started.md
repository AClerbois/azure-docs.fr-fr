---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 72ccad94301e053d8103ca949d41202e58d9f5bb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177170"
---
> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Les représentations d’appareil sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). IoT Hub conserve une représentation d’appareil pour chaque appareil que vous y connectez.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Vous pouvez utiliser des jumeaux d’appareil pour répondre aux besoins suivants :

* Stockez les métadonnées d’appareil à partir de votre serveur principal de solution.

* Signaler les informations d’état actuel, telles que les capacités disponibles et les conditions (par exemple, méthode de connectivité utilisée), à partir de votre application d’appareil

* Synchronisez l’état des workflows de longue durée (par exemple, mises à jour de microprogramme et de configuration) entre l’application d’appareil et une application de serveur principal.

* Interroger les métadonnées, la configuration ou l’état de vos appareils

Les représentations d’appareil sont conçues pour les synchronisations et pour l’interrogation des configurations et des conditions d’appareil. Pour plus d’informations sur l’utilisation des jumeaux d’appareil, consultez [Comprendre les jumeaux d’appareil](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Les représentations d’appareil sont stockées dans un IoT Hub et contiennent les éléments suivants :

* *Étiquettes* : métadonnées d’appareil uniquement accessibles par le serveur principal de solution ;

* *Propriétés souhaitées* : objets JSON modifiables par le serveur principal de solution et observables par l’application d’appareil ; et

* *Propriétés signalées* : objets JSON modifiables par l’application d’appareil et consultables par le serveur principal de solution. Les étiquettes et les propriétés ne peuvent pas contenir de tableaux, mais les objets peuvent être imbriqués.

![image de jumeau d’appareil affichant la fonctionnalité](./media/iot-hub-selector-twin-get-started/twin.png)

En outre, le serveur principal de solution peut interroger les représentations d’appareil concernant toutes les données ci-dessus.
Pour plus d’informations sur les jumeaux d’appareil, consultez l’article [Comprendre les jumeaux d’appareil](../articles/iot-hub/iot-hub-devguide-device-twins.md). Pour plus d’informations sur l’interrogation, consultez l’article de référence [Langage de requête IoT Hub](../articles/iot-hub/iot-hub-devguide-query-language.md).


Ce didacticiel vous explique les procédures suivantes :

* Création d’une application principale ajoutant des *étiquettes* à une représentation d’appareil, et création d’une application de périphérique simulé signalant son canal de connectivité sous la forme d’une *propriété signalée* sur la représentation d’appareil.

* Interrogez les appareils à partir de votre application principale à l’aide de filtres sur les étiquettes et les propriétés précédemment créées.
