---
title: Limites et quotas de la préversion d’IoT Plug-and-Play | Microsoft Docs
description: Comprenez les limites, les quotas et les limitations qui s’appliquent lorsque vous utilisez la préversion d’IoT Plug-and-Play.
author: miagdp
ms.author: miag
ms.date: 08/01/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2df8a8820422a22b0512e24c4b052377cb0e61e0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878925"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limites, quotas et limitations de la préversion d’IoT Plug-and-Play

Cet article explique les limites, quotas et limitations spécifiques à la préversion publique d’IoT Plug-and-Play. Il existe des [quotas et des limitations IOT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) qui s’appliquent également.

## <a name="iot-hub"></a>IoT Hub

Pour la préversion publique, les limites et quotas suivants s’appliquent à un hub IoT :

| Limites, restrictions et limitations | Valeur | Notes |
|-----|-----|-----|
| Nombre de modèles de capacité de l’appareil ou d’interfaces pouvant être inscrits par hub | 1 500 ||
| Nombre maximal d’interfaces pouvant être inscrites par appareil | 40 ||
| Nombre maximal de modèles de capacité de l’appareil pouvant être inscrits par appareil | 1 ||
| Taille maximale du fichier interface/modèle de capacité de l’appareil | 512 caractères ||
| Taille maximale du nom d’une interface | 256 caractères ||
| Taille maximale du nom d’une propriété  | 64 octets, 7 niveaux en profondeur (le premier niveau étant réservé pour `$iotin`) | Caractères autorisés : a-z, A-Z, 0-9 (pas comme premier caractère) et trait de soulignement. |
| Taille maximale d’une valeur de propriété | 512 octets ||
| Taille maximale d’un nom de commande | 100 octets ||
| Taille de jumeau d’appareil | Identique à [Limites IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Appels de l’API de résolution sur la référence SKU (quelles que soient les unités) | 100 requêtes/seconde ||

## <a name="model-repository"></a>Référentiel de modèles

Pour la préversion publique, les limites et quotas suivants s’appliquent à un référentiel de modèles :

| Limites, restrictions et limitations| Valeur |
|-----|-----|
| Nombre de référentiels de modèles d’entreprise par locataire Azure Active Directory | 1 |
| Nombre de clés d’autorisation par référentiel de modèle | 10  |
| Nombre de modèles (modèles de capacités de l’appareil ou interfaces) par référentiel de modèle d’entreprise| 1 500  |
| Nombre de modèles (modèles de capacités de l’appareil ou interfaces) dans le référentiel de modèles public par locataire Azure Active Directory| 1 500  |
| Nombre de modèles de capacités de l’appareil ou d’interfaces en cours de suppression dans un référentiel de modèles d’entreprise | 10 requêtes par seconde|
| Nombre de référentiels de modèles créés/mis à jour par un locataire| 1 requête par seconde |
| Nombre de clés d’autorisation créées/mises à jour/supprimées dans un référentiel de modèles | 1 requête par seconde|
| Nombre de modèles de capacités de l’appareil en cours de suppression dans un référentiel de modèles d’entreprise | 10 RPS |
| Nombre d’interfaces en cours de suppression dans un référentiel de modèles d’entreprise | 10 RPS|
| Nombre de modèles de capacités de l’appareil en cours de suppression dans un référentiel de modèles public | 10 RPS|
| Nombre de modèles de capacités de l’appareil en cours de suppression dans un référentiel de modèles public | 10 RPS|

## <a name="parser-library"></a>Bibliothèque de l’analyseur

La bibliothèque de l’analyseur suit les limites qui s'appliquent au [langage de définition du jumeau numérique](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Étapes suivantes

Une étape suivante suggérée consiste à découvrir comment [Se connecter à et agir avec un appareil IoT Plug-and-Play](./howto-develop-solution.md).
