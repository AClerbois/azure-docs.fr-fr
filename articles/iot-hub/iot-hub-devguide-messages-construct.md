---
title: Présentation du format des messages Azure IoT Hub | Microsoft Docs
description: Le guide du développeur décrit le format et le contenu attendu des messages IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: dd45c68fb7d7a7226d18dd1afc508b3dbf7b770b
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950453"
---
# <a name="create-and-read-iot-hub-messages"></a>Créer et lire des messages IoT Hub

Pour prendre en charge une interopérabilité transparente entre les différents protocoles, IoT Hub définit un format de message commun pour tous les protocoles d’appareil. Ce format de message est utilisé pour les messages [de routage appareil-à-cloud](iot-hub-devguide-messages-d2c.md) et [cloud-à-appareil](iot-hub-devguide-messages-c2d.md). 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implémente les messages appareil-à-cloud à l’aide d’un modèle de messagerie en streaming. Les messages appareil-à-cloud d’IoT Hub s’apparentent plus à des *événements* [Event Hubs](/azure/event-hubs/) qu’à des *messages* [Service Bus](/azure/service-bus-messaging/), dans la mesure où de nombreux événements passant à travers le service peuvent être lus par plusieurs lecteurs.

Un message IoT Hub comprend les éléments suivants :

* Un ensemble prédéfini de *propriétés système*, telles que répertoriées ci-dessous.

* Un ensemble de *propriétés de l’application*. Dictionnaire de propriétés de chaîne que l’application peut définir et auquel elle peut accéder sans avoir à désérialiser le corps du message. IoT Hub ne modifie jamais ces propriétés.

* Un corps binaire opaque.

Les valeurs et les noms de propriétés peuvent contenir seulement des caractères alphanumériques ASCII, plus les caractères ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` quand vous envoyez des messages appareil-à-cloud avec le protocole HTTPS ou quand vous envoyez des messages cloud-à-appareil.

La messagerie appareil-à-cloud avec IoT Hub présente les caractéristiques suivantes :

* Les messages appareil-à-cloud sont durables et sont conservés dans le point de terminaison **messages/events** par défaut d’un hub IoT jusqu’à sept jours.

* Les messages appareil-à-cloud ne doivent pas dépasser 256 Ko et peuvent être groupés en lots pour optimiser les envois. Les lots ne peuvent pas dépasser 256 Ko.

* IoT Hub n’autorise pas le partitionnement arbitraire. Les messages appareil-à-cloud sont partitionnés selon leur **deviceId**d’origine.

* Comme expliqué dans [Contrôler l’accès à IoT Hub](iot-hub-devguide-security.md), IoT Hub permet l’authentification et le contrôle d’accès pour chaque appareil.

* Vous pouvez marquer les messages avec des informations qui sont placées dans les propriétés de l’application. Pour plus d’informations, consultez [Enrichissements de messages](iot-hub-message-enrichments-overview.md).

Pour plus d’informations sur l’encodage et le décodage des messages envoyés avec différents protocoles, consultez [SDK Azure IoT](iot-hub-devguide-sdks.md).

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Propriétés système des messages IoT Hub **D2C**

| Propriété | Description  |L’utilisateur est-il définissable ?|Mot clé de la requête de routage|
| --- | --- | --- | --- |
| message-id |Identificateur correspondant au message défini par l’utilisateur utilisé pour les modèles demande-réponse. Format : Une chaîne qui respecte la casse (jusqu’à 128 caractères) de caractères alphanumériques 7 bits ASCII + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  | OUI | MessageId |
| iothub-enqueuedtime |Date et heure de réception du message [Appareil-à-cloud](iot-hub-devguide-d2c-guidance.md) par IoT Hub. | Non | EnqueuedTime |
| user-id |Un ID utilisé pour spécifier l’origine des messages. Lorsque des messages sont générés par IoT Hub, la propriété est définie sur `{iot hub name}`. | OUI | UserId |
| iothub-connection-device-id |Un ID défini par IoT Hub sur les messages appareil vers cloud. Elle contient la propriété **deviceId** de l’appareil qui a envoyé le message. | Non | deviceId |
| iothub-connection-auth-generation-id |Un ID défini par IoT Hub sur les messages appareil vers cloud. Elle contient la propriété **generationId** (conformément aux [Propriétés d’identité des appareils](iot-hub-devguide-identity-registry.md#device-identity-properties)) de l’appareil qui a envoyé le message. | Non |DeviceGenerationId |
| iothub-connection-auth-method |Une méthode d’authentification définie par IoT Hub sur les messages appareil-à-cloud. Cette propriété contient des informations sur la méthode d’authentification utilisée pour authentifier l’appareil qui a envoyé le message.| Non | AuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Propriétés système des messages IoT Hub **C2D**

| Propriété | Description  |L’utilisateur est-il définissable ?|
| --- | --- | --- |
| message-id |Identificateur correspondant au message défini par l’utilisateur utilisé pour les modèles demande-réponse. Format : Une chaîne qui respecte la casse (jusqu’à 128 caractères) de caractères alphanumériques 7 bits ASCII + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  |OUI|
| sequence-number |Un numéro (unique par file d’attente d’appareil) affecté par IoT Hub à chaque message cloud-à-appareil. |Non|
| to |Une destination spécifiée dans les messages [cloud vers appareil](iot-hub-devguide-c2d-guidance.md) . |Non|
| absolute-expiry-time |Date et heure d’expiration du message. |Non|   |
| correlation-id |Une propriété de chaîne d’un message de réponse qui contient généralement l'ID du message de la demande dans les modèles demande-réponse. |OUI|
| user-id |Un ID utilisé pour spécifier l’origine des messages. Lorsque des messages sont générés par IoT Hub, la propriété est définie sur `{iot hub name}`. |OUI|
| iothub-ack |Un générateur de messages de commentaires. Cette propriété est utilisée dans les messages cloud-à-appareil pour demander à IoT Hub de générer des messages de commentaires à la suite de la consommation du message par l’appareil. Valeurs possibles : **none** (par défaut) : aucun message de commentaires n’est généré ; **positive** : recevoir un message de commentaires si le message est achevé ; **negative** : recevoir un message de commentaires si le message a expiré (ou si le nombre maximal de remises a été atteint) sans être achevé par l’appareil, ou **full** : propriétés à la fois positive et négative. |OUI|

## <a name="message-size"></a>Taille des messages

IoT Hub mesure la taille des messages d’une façon indépendante du protocole, en considérant uniquement la charge utile réelle. La taille en octets est calculée comme la somme des éléments suivants :

* La taille du corps en octets.
* La taille en octets de toutes les valeurs des propriétés système du message.
* La taille en octets de tous les noms et valeurs des propriétés des utilisateurs.

Les noms et les valeurs des propriétés sont limités aux caractères ASCII ; ainsi, la longueur des chaînes est égale à la taille en octets.

## <a name="anti-spoofing-properties"></a>Propriétés de détection d’usurpation d’identité

Pour éviter l’usurpation d’appareil dans les messages appareil-à-cloud, IoT Hub marque tous les messages avec les propriétés suivantes :

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

Les deux premières propriétés contiennent le **deviceId** et le **generationId** de l’appareil d’origine, conformément aux [Propriétés d’identité des appareils](iot-hub-devguide-identity-registry.md#device-identity-properties).

La propriété **iothub-connection-auth-method** contient un objet sérialisé JSON avec les propriétés suivantes :

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les limites de taille des messages dans IoT Hub, consultez [Quotas et limitations IoT Hub](iot-hub-devguide-quotas-throttling.md).

* Pour découvrir comment créer et lire des messages IoT Hub dans différents langages de programmation, consultez les [Démarrages rapides](quickstart-send-telemetry-node.md).
