---
title: Présentation des modules Azure IoT Edge | Microsoft Docs
description: Découvrir les modules Azure IoT Edge et les modes de configuration
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9064e0da6dde6c4b30235adf771f06a4f25d709a
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2018
ms.locfileid: "42140648"
---
# <a name="understand-azure-iot-edge-modules"></a>Présentation des modules Azure IoT Edge

Azure IoT Edge vous permet de déployer et de gérer la logique métier sur la périphérie sous la forme de *modules*. Les modules Azure IoT Edge sont la plus petite unité de calcul gérée par IoT Edge. Ils peuvent contenir des services Azure (par exemple Azure Stream Analytics) ou votre propre code spécifique à la solution. Pour comprendre comment les modules sont développés, déployés et gérés, considérez les quatre éléments conceptuels qui composent un module :

* Une **image de module** est un package contenant le logiciel qui définit un module.
* Une **instance de module** est l’unité de calcul spécifique exécutant l’image de module sur un appareil IoT Edge. L’instance de module est démarrée par le runtime IoT Edge.
* Une **identité de module** est une information (notamment des informations d’identification de sécurité) stockée dans IoT Hub et associée à chaque instance de module.
* Un **jumeau de module** est un document JSON stocké dans IoT Hub qui contient les informations d’état relatives à une instance de module, notamment des métadonnées, des configurations et des conditions. 

## <a name="module-images-and-instances"></a>Instances et images de modules

Les images de modules IoT Edge contiennent des applications qui tirent parti de la gestion, de la sécurité et des fonctionnalités de communication du runtime IoT Edge. Vous pouvez développer vos propres images de modules ou en exporter une à partir d’un service Azure pris en charge, tel qu’Azure Stream Analytics.
Les images existent dans le cloud et peuvent être mises à jour, modifiées et déployées dans différentes solutions. Par exemple, un module qui utilise l’apprentissage automatique pour prédire la sortie d’une ligne de production existe en tant qu’image distincte d’un module qui utilise la vision informatique pour contrôler un drone. 

Chaque fois qu’une image de module est déployée sur un appareil et démarrée par le runtime IoT Edge, une nouvelle instance de ce module est créée. Deux appareils dans différentes parties du monde peuvent utiliser la même image de module. Toutefois, chacun aurait sa propre instance du module lors du démarrage du module sur l’appareil. 

![Images de modules dans le cloud - instances de modules sur les appareils][1]

Dans l’implémentation, les images de modules existent en tant qu’images de conteneurs dans un référentiel tandis que les instances de modules sont des conteneurs sur des appareils. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Identités de modules

Quand une nouvelle instance de module est créée par le runtime IoT Edge, elle est associée à une identité de module correspondante. L’identité de module est stockée dans IoT Hub et employée comme étendue d’adressage et de sécurité pour toutes les communications cloud et locales pour cette instance de module spécifique.
L’identité associée à une instance de module dépend de l’identité de l’appareil sur lequel l’instance s’exécute et du nom que vous donnez à ce module dans votre solution. Par exemple, si vous nommez `insight` un module qui utilise Azure Stream Analytics et que vous le déployez sur un appareil nommé `Hannover01`, le runtime IoT Edge crée une identité de module correspondante nommée `/devices/Hannover01/modules/insight`.

Il est clair que dans les scénarios où vous avez besoin de déployer une image de module plusieurs fois sur le même appareil, vous pouvez déployer la même image plusieurs fois avec des noms différents.

![Les identités de modules sont uniques][2]

## <a name="module-twins"></a>Jumeaux de module

Chaque instance de module a aussi un jumeau de module correspondant que vous pouvez utiliser pour configurer l’instance de module. L’instance et le jumeau sont associés par le biais de l’identité de module. 

Un jumeau de module est un document JSON qui stocke des propriétés de configuration et des informations sur un module. Ce concept s’apparente au concept de [jumeau d’appareil][lnk-device-twin] dans IoT Hub. La structure d’un jumeau de module est exactement identique à celle d’un jumeau d’appareil. Les API utilisées pour interagir avec les deux types de jumeaux sont identiques. La seule différence entre les deux est l’identité utilisée pour instancier le SDK client. 

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings); 
await client.OpenAsync(); 
 
// Get the module twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>Fonctionnalités hors ligne

Azure IoT Edge prend en charge les opérations hors connexion sur vos appareils IoT Edge. Ces fonctionnalités étant limitées pour le moment, des scénarios supplémentaires sont en cours de développement. 

Les modules IoT Edge peuvent être hors connexion pendant de longues périodes, sous réserve que les conditions suivantes soient remplies : 

* **La durée de vie (TTL) des messages n’est pas arrivée à expiration**. La valeur par défaut de la durée de vie des messages est de deux heures, mais elle peut être augmentée ou diminuée dans la configuration du Store et des transferts dans les paramètres du hub IoT Edge. 
* **Les modules n’ont pas besoin de se réauthentifier auprès du hub IoT Edge en mode hors connexion**. Les modules ne peuvent s’authentifier qu’auprès des hubs Edge qui ont une connexion active à un hub IoT. Les modules doivent se réauthentifier s’ils sont redémarrés pour une raison quelconque. Les modules peuvent toujours envoyer des messages au hub Edge après l’expiration de leur jeton SAP. Quand la connectivité est rétablie, le hub Edge demande un nouveau jeton au module et le valide auprès du hub IoT. En cas de réussite, le hub Edge transfère les messages de module qu’il a stockés, même les messages qui ont été envoyés alors que le jeton du module avait expiré. 
* **Le module qui a envoyé les messages en mode hors connexion est toujours opérationnel quand la connectivité est rétablie**. Au moment de la reconnexion à IoT Hub, le hub Edge doit valider un nouveau jeton de module (si le précédent est arrivé à expiration) pour pouvoir transférer les messages de module. Si le module n’est pas en mesure de fournir un nouveau jeton, le hub Edge ne peut pas agir sur les messages stockés du module. 
* **Le hub Edge dispose d’un espace disque pour stocker les messages**. Par défaut, les messages sont stockés dans le système de fichiers du conteneur du hub Edge. Si vous préférez, vous pouvez utiliser une option de configuration qui permet de spécifier un volume monté sur lequel stocker les messages. Dans les deux cas, un espace doit être disponible pour stocker les messages en vue de leur remise différée à IoT Hub.  

## <a name="next-steps"></a>Étapes suivantes
 - [Présentation du runtime Azure IoT Edge et de son architecture][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md
