---
title: Comprendre les quotas Azure IoT Hub et la limitation | Microsoft Docs
description: Guide du développeur - description des quotas qui s’appliquent à IoT Hub et comportement de limitation attendu.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 11cec9621ad72cfeaee45e4cd466430e64b9b836
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42140658"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Référence - Quotas et limitation IoT Hub

## <a name="quotas-and-throttling"></a>Quotas et limitation
Chaque abonnement Azure peut avoir au maximum 50 IoT Hubs et au maximum 1 hub gratuit.

Chaque hub IoT est approvisionné avec un certain nombre d’unités dans un niveau spécifique. Le niveau et le nombre d’unités déterminent le quota quotidien maximal de messages que vous pouvez envoyer. La taille de message utilisée pour calculer le quota quotidien est de 0,5 Ko pour un hub de niveau gratuit et de 4 Ko pour tous les autres niveaux. Pour plus d’informations, consultez [Tarification d’Azure IoT Hub][lnk-pricing].

Le niveau détermine également les limites qu’IoT Hub applique sur toutes les opérations.

## <a name="operation-throttles"></a>Limitations d’opérations
Les limitations d’opérations sont des limites de taux qui sont appliquées par plages de minutes et sont destinées à éviter les abus. IoT Hub essaie d’éviter de retourner des erreurs dans la mesure du possible, mais il commence à retourner des exceptions si la limitation est enfreinte pendant trop longtemps.

À tout moment, vous pouvez augmenter les quotas ou les limites en augmentant le nombre d’unités provisionnées dans un hub IoT.

Le tableau suivant présente les limitations appliquées. Les valeurs font référence à un hub individuel.

| Limitation | Gratuit, B1 et S1 | B2 et S2 | B3 et S3 | 
| -------- | ------- | ------- | ------- |
| Opérations de registre des identités (création, récupération, création de listes, mise à jour, suppression) | 1,67/s/unité (100/min/unité) | 1.67/s/unité (100/min/unité) | 83.33/s/unité (5 000/min/unité) |
| Nouvelles connexions d’appareil (cette limite s’applique à la fréquence à laquelle de _nouvelles connexions_ sont établies, pas au nombre total de connexions) | Plus de 100/s ou 12/s/unité <br/> Par exemple, deux unités S1 sont 2\*12 = 24 nouvelles connexions/s, mais vous avez au moins 100 nouvelles connexions/s dans toutes vos unités. Avec neuf unités S1, vous avez 108 nouvelles connexion/s (9\*12) dans toutes vos unités. | 120 nouvelles connexions/s/unité | 6 000 nouvelles connexions/s/unité |
| Envois appareil-à-cloud | Plus de 100/s ou 12/s/unité <br/> Par exemple, deux unités S1 équivalent à 2\*12 = 24/s, mais vous obtenez au moins 100/s sur vos unités. Avec neuf unités S1, vous obtenez 108/sec (9\*12) sur vos unités. | 120/s/unité | 6 000/s/unité |
| Envois de cloud-à-appareil<sup>1</sup> | 1.67/s/unité (100/min/unité) | 1.67/s/unité (100/min/unité) | 83.33/s/unité (5 000/min/unité) |
| Réceptions de cloud-à-appareil<sup>1</sup> <br/> (uniquement quand l’appareil utilise HTTPS)| 16.67/s/unité (1 000/min/unité) | 16.67/s/unité (1 000/min/unité) | 833.33/s/unité (50 000/min/unité) |
| Chargement de fichiers | 1.67 notifications de téléchargement de fichier/s/unité (100/min/unité) | 1.67 notifications de téléchargement de fichier/s/unité (100/min/unité) | 83.33 notifications de téléchargement de fichier/s/unité (5 000/min/unité) |
| Méthodes directes<sup>1</sup> | 160 Ko/s/unité<sup>2</sup> | 480 Ko/s/unité<sup>2</sup> | 24 Mo/s/unité<sup>2</sup> | 
| Lectures de jumeaux (appareil et module)<sup>1</sup> | 10/s | Plus de 10/s ou 1/s/unité | 50/s/unité |
| Mises à jour de jumeaux (appareil et module)<sup>1</sup> | 10/s | Plus de 10/s ou 1/s/unité | 50/s/unité |
| Opérations de travaux<sup>1</sup> <br/> (créer, mettre à jour, répertorier, supprimer) | 1.67/s/unité (100/min/unité) | 1.67/s/unité (100/min/unité) | 83.33/s/unité (5 000/min/unité) |
| Opérations de l’appareil de travaux<sup>1</sup> <br/> (mettre à jour le jumeau, appeler la méthode directe) | 10/s | Plus de 10/s ou 1/s/unité | 50/s/unité |
| Configurations et déploiements de périphérie<sup>1</sup> <br/> (créer, mettre à jour, répertorier, supprimer) | 0,33/s/unité (20/min/unité) | 0,33/s/unité (20/min/unité) | 0,33/s/unité (20/min/unité) |


<sup>1</sup>Cette fonctionnalité n’est pas disponible dans le niveau de base d’IoT Hub. Pour plus d’informations, consultez [Comment choisir le bon IoT Hub](iot-hub-scaling.md). <br/><sup>2</sup>La taille du compteur de limitation est de 8 Ko.

La limitation des *connexions d’appareil* régit la fréquence à laquelle de nouvelles connexions d’appareil peuvent être établies avec un hub IoT. La limitation des *connexions d’appareils* ne régit pas le nombre maximal d’appareils connectés simultanément. La limitation dépend du nombre d’unités configurées pour l’IoT Hub.

Par exemple, si vous achetez une seule unité S1, vous obtenez une limitation de 100 connexions par seconde. Par conséquent, pour connecter 100 000 appareils, au moins 1 000 secondes (soit environ 16 minutes) sont nécessaires. Toutefois, vous pouvez avoir autant d’appareils connectés simultanément que d’appareils enregistrés dans le registre des identités.

Le billet de blog [IoT Hub throttling and you][lnk-throttle-blog] (Limitation d’IoT Hub et vous) fournit une présentation détaillée du comportement de limitation d’IoT Hub.

> [!IMPORTANT]
> Les opérations de registre des identités sont prévues pour une utilisation au moment de l’exécution dans les scénarios de gestion et d’approvisionnement des appareils. La lecture ou la mise à jour d’un grand nombre d’identités d’appareils est prise en charge par le biais des [travaux d’importation et d’exportation][lnk-importexport].
> 
> 

## <a name="other-limits"></a>Autres limites

IoT Hub impose d’autres limites opérationnelles :

| Opération | Limite |
| --------- | ----- |
| URI de chargement de fichiers | 10 000 URI de SAP peuvent être générés à la fois pour un compte de stockage. <br/> 10 URI de signature d’accès partagé/appareil peuvent être générés à la fois. |
| Travaux<sup>1</sup> | L’historique des travaux est conservé pendant 30 jours maximum. <br/> Le nombre maximal de travaux simultanés est 1 (pour les niveaux gratuit et S1), 5 (pour S2) ou 10 (pour S3). |
| Points de terminaison supplémentaires | Les hubs avec SKU payants peuvent avoir 10 points de terminaison supplémentaires. Les hubs avec SKU gratuits peuvent avoir un point de terminaison supplémentaire. |
| Règles de routage de messages | Les hubs avec SKU payants peuvent avoir 100 règles de routage. Les hubs avec SKU gratuits peuvent avoir cinq règles de routage. |
| Messages d’appareil-à-cloud | Taille maximale des messages 256 Ko |
| Messages de cloud-à-appareil<sup>1</sup> | Taille maximale des messages 64 Ko. Le nombre maximal de messages en attente est 50. |
| Méthode directe<sup>1</sup> | La taille maximale de charge utile de la méthode directe est de 128 Ko. |
| Configurations | 20 configurations par hub. |
| Déploiements de périphérie | 20 déploiements par hub. 20 modules par déploiement. |
| Jumeaux | La taille maximale par section de jumeaux (balises, propriétés souhaitées, propriétés signalées) est de 8 Ko. |

<sup>1</sup>Cette fonctionnalité n’est pas disponible dans le niveau de base d’IoT Hub. Pour plus d’informations, consultez [Comment choisir le bon IoT Hub](iot-hub-scaling.md).

> [!NOTE]
> Actuellement, le nombre maximal d’appareils que vous pouvez connecter à un IoT Hub unique est 500 000. Si vous souhaitez augmenter cette limite, contactez le [support Microsoft](https://azure.microsoft.com/support/options/).

## <a name="latency"></a>Latence
IoT Hub s’efforce de fournir une faible latence pour toutes les opérations. Toutefois, en raison des conditions réseau et d’autres facteurs imprévisibles, il ne peut pas garantir une latence maximale. Lorsque vous concevez votre solution, vous devez :

* Éviter de faire d’hypothèses concernant la latence maximale de toute opération IoT Hub.
* Configurer votre hub IoT dans la région Azure le plus proche de vos appareils.
* Envisager d’utiliser Azure IoT Edge pour effectuer des opérations sensibles à la latence sur l’appareil ou sur une passerelle proche de celui-ci.

Plusieurs unités IoT Hub affectent la limitation comme décrit précédemment, mais ne fournissent pas d’avantages ni de garanties supplémentaires en termes de latence.
Si vous constatez des augmentations inattendues de la latence des opérations, contactez le [Support Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Étapes suivantes
Les autres rubriques de référence de ce Guide du développeur IoT Hub comprennent :

* [Points de terminaison IoT Hub][lnk-devguide-endpoints]
* [Langage de requête IoT Hub les jumeaux d’appareils, les travaux et le routage des messages][lnk-devguide-query]
* [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
