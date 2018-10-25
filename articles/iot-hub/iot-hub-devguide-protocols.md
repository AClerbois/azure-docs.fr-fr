---
title: Ports et protocoles de communication IoT Hub | Microsoft Docs
description: 'Guide du développeur : décrit les protocoles de communication pris en charge pour les communications appareil-à-cloud et cloud-à-appareil et les numéros de port qui doivent être ouverts.'
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 3079b2414c44fc97bc8aff4b207e0943e94c7457
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584334"
---
# <a name="reference---choose-a-communication-protocol"></a>Référence - Choisir un protocole de communication

IoT Hub permet aux appareils d’utiliser les protocoles suivants pour les communications côté appareil :

* [MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT sur WebSockets
* [AMQP](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP sur WebSockets
* HTTPS

Pour plus d’informations sur la façon dont ces protocoles prennent en charge les fonctionnalités spécifiques d’IoT Hub, consultez [Recommandations sur les communications appareil-à-cloud](iot-hub-devguide-d2c-guidance.md) et [Conseils pour les communications cloud-à-appareil](iot-hub-devguide-c2d-guidance.md).

Le tableau suivant fournit des recommandations de haut niveau pour votre choix de protocole :

| Protocole | Quand choisir ce protocole |
| --- | --- |
| MQTT <br> MQTT sur WebSocket |Utilisez sur tous les appareils ne nécessitant pas de connecter plusieurs appareils (chacun avec ses propres informations d’identification par appareil) à l’aide de la même connexion TLS. |
| AMQP <br> AMQP sur WebSocket |Utiliser sur les passerelles de champ et de cloud pour tirer parti du multiplexage de connexion sur les appareils. |
| HTTPS |Utiliser pour les appareils qui ne peuvent pas prendre en charge les autres protocoles. |

Prenez en compte les points suivants lorsque vous choisissez votre protocole pour les communications côté appareil :

* **Modèle Cloud vers appareil**. HTTPS ne dispose pas d’un moyen efficace de mettre en œuvre la transmission des messages par le serveur. Ainsi, quand vous utilisez HTTPS, les appareils interrogent IoT Hub pour rechercher les messages cloud-à-appareil. Cette approche est inefficace pour l’appareil et pour IoT Hub. Conformément aux recommandations actuelles concernant HTTP,S chaque appareil doit interroger la présence de messages toutes les 25 minutes ou plus. AMQP et MQTT prennent en charge les notifications Push sur le serveur lors de la réception de messages cloud-à-appareil. Ils permettent d’obtenir des notifications Push immédiates pour les messages IoT Hub-à-appareil. Si la latence de remise pose problème, MQTT ou AMQP sont les meilleurs protocoles à utiliser. Pour les appareils rarement connectés, HTTPS fonctionne aussi bien.

* **Passerelles de champ**. Quand vous utilisez MQTT et HTTPS, il est impossible de connecter plusieurs appareils (chacun avec ses propres informations d’identification par appareil) à l’aide de la même connexion TLS. Pour les [scénarios de passerelle de champ](iot-hub-devguide-endpoints.md#field-gateways) qui nécessitent une connexion TLS entre la passerelle de champ et IoT Hub pour chaque appareil connecté, ces protocoles ne représentent pas la solution optimale.

* **Appareils faibles en ressources**. Les bibliothèques MQTT et HTTPS sont moins encombrantes que les bibliothèques AMQP. Par conséquent, si l’appareil dispose de ressources limitées (par exemple, moins de 1 Mo de mémoire RAM), ces protocoles sont peut-être les seuls protocoles d’implémentation disponibles.

* **Traversée réseau**. Le protocole AMQP standard utilise le port 5671 tandis que MQTT écoute sur le port 8883. L’utilisation de ces ports peut entraîner des problèmes dans les réseaux fermés aux protocoles autres que HTTPS. Utilisez MQTT sur WebSockets, AMQP sur WebSockets ou HTTPS dans ce scénario.

* **Taille de charge utile**. MQTT et AMQP sont des protocoles binaires qui génèrent des charges utiles plus compactes que HTTPS.

> [!WARNING]
> Quand vous utilisez HTTPS, chaque appareil doit envoyer des interrogations pour les messages cloud-à-appareil toutes les 25 minutes, voire plus. Toutefois, au cours du développement, il est clairement acceptable d’avoir des fréquences d’interrogation plus régulières que toutes les 25 minutes.

## <a name="port-numbers"></a>Numéros de ports

Les appareils peuvent communiquer avec IoT Hub dans Azure à l’aide de divers protocoles. En règle générale, le choix du protocole dépend des exigences spécifiques de la solution. Le tableau suivant répertorie les ports de sortie qui doivent être ouverts pour qu’un appareil puisse utiliser un protocole spécifique :

| Protocole | Port |
| --- | --- |
| MQTT |8883 |
| MQTT sur WebSockets |443 |
| AMQP |5671 |
| AMQP sur WebSockets |443 |
| HTTPS |443 |

Lorsque vous avez créé un hub IoT dans une région Azure, ce hub conserve la même adresse IP pendant sa durée de vie. Toutefois, si Microsoft le fait passer à une autre unité d’échelle pour maintenir la qualité du service, il reçoit une nouvelle adresse IP.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon dont IoT Hub implémente le protocole MQTT, consultez [Communication avec votre IoT Hub à l’aide du protocole MQTT](iot-hub-mqtt-support.md).
