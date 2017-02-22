> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> 
> 

## <a name="introduction"></a>Introduction
Les représentations d’appareil sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). IoT Hub conserve une représentation d’appareil pour chaque appareil que vous y connectez.

Vous pouvez utiliser des représentations d’appareil pour répondre aux besoins suivants :

* Stockez les métadonnées d’appareil à partir de votre serveur principal de solution.
* Signaler les informations d’état actuel, telles que les capacités disponibles et les conditions (par exemple, méthode de connectivité utilisée), à partir de votre application d’appareil
* Synchronisez l’état des workflows de longue durée (par exemple, mises à jour de microprogramme et de configuration) entre l’application d’appareil et une application de serveur principal.
* Interroger les métadonnées, la configuration ou l’état de vos appareils

> [!NOTE]
> Les représentations d’appareil sont conçues pour les synchronisations et pour l’interrogation des configurations et des conditions d’appareil. Pour plus d’informations sur l’utilisation des représentations d’appareils, consultez l’article [Comprendre les représentations d’appareils][lnk-twins].
> 
> 

Les représentations d’appareil sont stockées dans un IoT Hub et contiennent les éléments suivants :

* *Étiquettes* : métadonnées d’appareil uniquement accessibles par le serveur principal de solution ;
* *Propriétés souhaitées* : objets JSON modifiables par le serveur principal de solution et observables par l’application d’appareil ; et
* *Propriétés signalées* : objets JSON modifiables par l’application d’appareil et consultables par le serveur principal de solution. Les étiquettes et les propriétés ne peuvent pas contenir de tableaux, mais les objets peuvent être imbriqués.

![][img-twin]

En outre, le serveur principal de solution peut interroger les représentations d’appareil concernant toutes les données ci-dessus.
Pour plus d’informations sur les représentations d’appareils, consultez l’article [Comprendre les représentations d’appareils][lnk-twins] ; pour plus d’informations sur l’interrogation, consultez l’article de référence [Langage de requête IoT Hub][lnk-query].

> [!NOTE]
> Actuellement, des représentations d’appareil sont accessibles uniquement à partir d’appareils qui se connectent à IoT Hub à l’aide du protocole MQTT. Pour obtenir des instructions sur la conversion d’une application de périphérique existante pour utiliser MQTT, voir [Support MQTT][lnk-devguide-mqtt].
> 
> 

Ce didacticiel vous explique les procédures suivantes :

* Création d’une application principale ajoutant des *étiquettes* à une représentation d’appareil, et création d’une application de périphérique simulé signalant son canal de connectivité sous la forme d’une *propriété signalée* sur la représentation d’appareil.
* Interrogez les appareils à partir de votre application principale à l’aide de filtres sur les étiquettes et les propriétés précédemment créées.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

<!--HONumber=Feb17_HO3-->


