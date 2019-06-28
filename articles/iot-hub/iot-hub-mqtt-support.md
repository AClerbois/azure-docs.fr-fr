---
title: Présentation de la prise en charge de MQTT au niveau d’Azure IoT Hub | Microsoft Docs
description: 'Guide du développeur : prise en charge des appareils se connectant à un point de terminaison IoT Hub côté appareil en utilisant le protocole MQTT. Inclut des informations sur la prise en charge intégrée de MQTT dans les Azure IoT device SDK.'
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: rezas
ms.openlocfilehash: 1a0b6cf8ce272733c259283fdec9c215ac2b0fd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61442560"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Communication avec votre IoT Hub à l’aide du protocole MQTT

Grâce à IoT Hub, les appareils peuvent communiquer avec les points de terminaison d’appareil IoT Hub à l’aide de :

* [MQTT v3.1.1](https://mqtt.org/) sur le port 8883
* MQTT v3.1.1 sur WebSocket sur le port 443.

IoT Hub n’est pas un répartiteur MQTT complet et ne prend pas en charge tous les comportements spécifiés dans la norme MQTT v3.1.1. Cet article décrit comment les appareils peuvent utiliser des comportements MQTT pris en charge pour communiquer avec IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Toutes les communications des appareils avec IoT Hub doivent être sécurisées à l’aide de TLS/SSL. Par conséquent, IoT Hub ne prend pas en charge les connexions non sécurisées sur le port 1883.

## <a name="connecting-to-iot-hub"></a>Connexion à IoT Hub

Un appareil peut utiliser le protocole MQTT pour se connecter à un hub IoT en utilisant une des options suivantes.

* Bibliothèques des [SDK Azure IoT](https://github.com/Azure/azure-iot-sdks).
* Protocole MQTT directement.

## <a name="using-the-device-sdks"></a>Utilisation des Kits device SDK

Les [SDK d’appareil](https://github.com/Azure/azure-iot-sdks) qui prennent en charge le protocole MQTT sont disponibles pour Java, Node.js, C, C# et Python. Les Kits device SDK utilisent la chaîne de connexion IoT Hub standard pour établir une connexion à un IoT Hub. Pour utiliser le protocole MQTT, le paramètre de protocole du client doit être défini sur **MQTT**. Par défaut, les Kits device SDK se connectent à un IoT Hub avec l’indicateur **CleanSession** défini sur **0**, et utilisent **QoS 1** pour l’échange de messages avec l’IoT Hub.

Quand un appareil est connecté à un hub IoT, les SDK d’appareils fournissent des méthodes qui permettent à l’appareil d’échanger des messages avec un hub IoT.

Le tableau suivant contient des liens vers des exemples de code pour chaque langage pris en charge et spécifie les paramètres à utiliser pour établir une connexion à IoT Hub à l’aide du protocole MQTT.

| Langage | Paramètre de protocole |
| --- | --- |
| [Node.JS](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |azure-iot-device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol.MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType.Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples) |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migration d’une application d’appareil d’AMQP vers MQTT

Si vous utilisez les [SDK d’appareil](https://github.com/Azure/azure-iot-sdks), le passage du protocole AMQP au protocole MQTT nécessite de modifier le paramètre de protocole dans l’initialisation du client comme indiqué précédemment.

Lors de cette opération, vérifiez les éléments suivants :

* AMQP retourne des erreurs pour nombreuses conditions, tandis que MQTT met fin à la connexion. Par conséquent, votre logique de gestion des exceptions peut nécessiter certaines modifications.

* MQTT ne prend pas en charge les opérations *reject* lors de la réception de [messages cloud-à-appareil](iot-hub-devguide-messaging.md). Si votre application de back-end doit recevoir une réponse de l’application pour appareil, envisagez d’utiliser des [méthodes directes](iot-hub-devguide-direct-methods.md).

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Utilisation directe du protocole MQTT (en tant qu’appareil)

Si un appareil ne peut pas utiliser les Kits device SDK, il peut toujours se connecter aux points de terminaison d’appareil publics à l’aide du protocole MQTT sur le port 8883. Dans le paquet **CONNECT**, l’appareil doit utiliser les valeurs suivantes :

* Pour le champ **ClientId**, utilisez le **deviceId**.

* Dans le champ **Username**, utilisez `{iothubhostname}/{device_id}/?api-version=2018-06-30`, où `{iothubhostname}` est l’enregistrement CName complet du hub IoT.

    Par exemple, si le nom de votre hub IoT est **contoso.azure-devices.net** et si le nom de votre appareil est **MyDevice01**, le champ **Username** complet doit contenir :

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Dans le champ **Password**, utilisez un jeton SAP. Le format du jeton SAP est identique pour les protocoles HTTPS et AMQP :

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Si vous utilisez l’authentification par certificat X.509, les mots de passe de jeton SAS ne sont pas obligatoires. Pour plus d’informations, consultez [Configurer la sécurité X.509 dans Azure IoT Hub](iot-hub-security-x509-get-started.md)

  Pour plus d’informations sur la génération de jetons de signature d’accès partagé, consultez la section consacrée aux appareils de [Utilisation de jetons de sécurité IoT Hub](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Lors du test, vous pouvez également utiliser les [Outils IoT Azure pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) multiplateforme ou l’outil [Explorateur d’appareils](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) afin de générer rapidement un jeton de signature d’accès partagé que vous pouvez copier et coller dans votre propre code :

Pour les Outils IoT Azure :

1. Déroulez l’onglet **APPAREILS AZURE IOT HUB** dans le coin inférieur gauche de Visual Studio Code.
  
2. Faites un clic droit sur votre appareil et sélectionnez **Générer un jeton SAP pour l’appareil**.
  
3. Définissez un **Délai d’expiration** et appuyez sur « ENTRÉE ».
  
4. Le jeton SAP est créé et copié dans le Presse-papiers.

Pour Device Explorer :

1. Accédez à l’onglet **Management** (Gestion) de **l’Explorateur d’appareils**.

2. Cliquez sur **SAS Token** (Jeton SAP) en haut à droite.

3. Dans **SASTokenForm**, sélectionnez votre appareil dans la liste déroulante **DeviceID**. Définissez votre **TTL**(Durée de vie).

4. Cliquez sur **Generate** (Générer) pour créer votre jeton.

   Le jeton SAP généré présente la structure suivante :

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   La partie de ce jeton à utiliser dans le champ **Password** (Mot de passe) pour la connexion avec le protocole MQTT est :

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Pour les paquets de connexion et de déconnexion MQTT, IoT Hub émet un événement sur le canal **Surveillance des opérations** . Cet événement comporte des informations supplémentaires qui peuvent vous aider à résoudre les problèmes de connectivité.

L’application de l’appareil peut spécifier un message **Will** dans le paquet **CONNECTER**. L’application de l’appareil doit utiliser `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` comme nom de rubrique **Will** pour définir des messages **Will** à transmettre en tant que message de télémétrie. Dans ce cas, si la connexion réseau est fermée, mais qu’un paquet **DÉCONNECTER** n’a pas été préalablement reçu à partir de l’appareil, IoT Hub envoie le message **Will** fourni dans le paquet **CONNECTER** au canal de télémétrie. Le canal de télémétrie peut être soit le point de terminaison **Événements** par défaut, soit un point de terminaison personnalisé défini par le routage d’IoT Hub. Le message a la propriété **iothub-MessageType**, à laquelle une valeur de **Will** est affectée.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Utilisation directe du protocole MQTT (en tant que module)

La connexion à IoT Hub via MQTT à l’aide d’une identité de module est similaire à celle de l’appareil (décrite [ci-dessus](#using-the-mqtt-protocol-directly-as-a-device)) mais vous devez utiliser ce qui suit :

* Définissez l’ID de client sur `{device_id}/{module_id}`.

* Si vous vous authentifiez avec un nom d’utilisateur et un mot de passe, définissez le nom d’utilisateur sur `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` et utilisez le jeton SAS associé à l’identité du module comme mot de passe.

* Utilisez `devices/{device_id}/modules/{module_id}/messages/events/` comme rubrique pour la publication des données de télémétrie.

* Utilisez `devices/{device_id}/modules/{module_id}/messages/events/` comme rubrique WILL.

* Les rubriques GET et PATCH sont identiques pour les modules et les appareils.

* Les rubriques GET et PATCH sont identiques pour les modules et les appareils.

### <a name="tlsssl-configuration"></a>Configuration TLS/SSL

Pour utiliser le protocole MQTT directement, votre client *doit* se connecter via le protocole TLS/SSL. Sans cette étape, des erreurs de connexion se produiront.

Afin d’établir une connexion TLS, vous devrez peut-être télécharger et référencer le certificat racine Baltimore DigiCert. Ce certificat est celui utilisé par Azure pour sécuriser la connexion. Il se trouve dans le dépôt [Azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c). Pour plus d’informations sur ces certificats, consultez le [site web de Digicert](https://www.digicert.com/digicert-root-certificates.htm).

Voici un exemple d’implémentation de cette connexion utilisant la version de Python de la [bibliothèque Paho MQTT](https://pypi.python.org/pypi/paho-mqtt) fournie par la fondation Eclipse.

Tout d’abord, installez la bibliothèque Paho à partir de votre environnement de ligne de commande :

```cmd/sh
pip install paho-mqtt
```

Ensuite, implémentez le client dans un script Python. Remplacez les espaces réservés comme suit :

* `<local path to digicert.cer>` est le chemin d’un fichier local qui contient le certificat racine DigiCert Baltimore. Vous pouvez créer ce fichier en copiant les informations de certificat à partir de [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) dans le SDK Azure IoT pour C. Incluez les lignes `-----BEGIN CERTIFICATE-----` et `-----END CERTIFICATE-----`, supprimez les marques `"` au début et à la fin de chaque ligne, et supprimez les caractères `\r\n` à la fin de chaque ligne.

* `<device id from device registry>` est l’ID d’un appareil que vous avez ajouté à votre hub IoT.

* `<generated SAS token>` est un jeton SAP pour l’appareil créé comme décrit précédemment dans cet article.

* `<iot hub name>` est le nom de votre hub IoT.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>Envoi de messages appareil-à-cloud

Après avoir correctement établi une connexion, un appareil peut envoyer des messages à IoT Hub en utilisant `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` en tant que **Nom de la rubrique**. L’élément `{property_bag}` permet à l’appareil d’envoyer des messages avec des propriétés supplémentaires dans un format codé URL. Par exemple :

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Cet élément `{property_bag}` utilise le même encodage que celui des chaînes de requête dans le protocole HTTPS.

Voici une liste de comportements spécifiques à l’implémentation d’IoT Hub :

* IoT Hub ne prend pas en charge les messages QoS 2. Si un client d’appareil publie un message avec **QoS 2**, IoT Hub interrompt la connexion réseau.

* IoT Hub ne conserve pas les messages Retain. Si un appareil envoie un message avec l’indicateur **RETAIN** défini sur 1, IoT Hub ajoute la propriété d’application **x-opt-retain** au message. Dans ce cas, IoT Hub ne conserve pas le message, mais le transmet à l’application principale.

* IoT Hub ne prend en charge qu’une seule connexion MQTT active par appareil. Toute nouvelle connexion MQTT au nom du même ID d’appareil entraîne l’interruption de la connexion existante par IoT Hub.

Pour plus d’informations, consultez le [Guide du développeur - Messages](iot-hub-devguide-messaging.md).

### <a name="receiving-cloud-to-device-messages"></a>Réception de messages cloud-à-appareil

Pour recevoir des messages d’IoT Hub, l’appareil doit s’abonner en utilisant un `devices/{device_id}/messages/devicebound/#` en tant que **Filtre de rubrique**. Le caractère générique à plusieurs niveaux `#` dans le Filtre de rubrique est utilisé uniquement pour autoriser l’appareil à recevoir des propriétés supplémentaires dans le nom de la rubrique. IoT Hub n’autorise pas l’utilisation des caractères génériques `#` ou `?` pour filtrer les sous-rubriques. IoT Hub n’étant pas un broker de messagerie pub-sub à usage général, il prend uniquement en charge les noms de rubriques et les filtres de rubriques documentés.

L’appareil ne reçoit aucun message d’IoT Hub tant qu’il ne s’est pas abonné à son point de terminaison spécifique d’appareil, représenté par le filtre de rubrique `devices/{device_id}/messages/devicebound/#`. Une fois qu’un abonnement a été établi, l’appareil reçoit les messages cloud-à-appareil qui lui ont été envoyés après l’abonnement. Si l’appareil se connecte avec l’indicateur **CleanSession** défini sur **0**, l’abonnement est rendu persistant entre les différentes sessions. Dans ce cas, la prochaine fois que l’appareil se connecte avec **CleanSession 0**, il reçoit les messages en attente qui lui ont été envoyés quand il était déconnecté. Si l’appareil utilise l’indicateur **CleanSession** défini sur **1**, il ne reçoit pas les messages à partir d’IoT Hub jusqu’à ce qu’il s’abonne à son point de terminaison d’appareil.

IoT Hub remet les messages avec le **Nom de la rubrique** `devices/{device_id}/messages/devicebound/`, ou `devices/{device_id}/messages/devicebound/{property_bag}` quand il y a des propriétés de message. `{property_bag}` contient des paires clé/valeur codées URL de propriétés de message. Seules les propriétés d’application et les propriétés système définissables par l’utilisateur (comme **messageId** ou **correlationId**) sont incluses dans le jeu de propriétés. Les noms de propriété système ont le préfixe **$** , tandis que les noms de propriété d’application ne sont précédés d’aucun préfixe.

Quand une application d’appareil s’abonne à une rubrique avec **QoS 2**, IoT Hub accorde le niveau QoS 1 maximal dans le paquet **SUBACK**. Après cela, IoT Hub remet les messages à l’appareil à l’aide de QoS 1.

### <a name="retrieving-a-device-twins-properties"></a>Récupération des propriétés d’un jumeau d’appareil

Tout d’abord, un appareil s’abonne à `$iothub/twin/res/#` pour recevoir les réponses de l’opération. Ensuite, il envoie un message vide à la rubrique `$iothub/twin/GET/?$rid={request id}`, avec une valeur propagée pour **l’ID de la requête**. Le service envoie alors un message de réponse contenant les données de jumeau d’appareil sur la rubrique `$iothub/twin/res/{status}/?$rid={request id}`, en utilisant le même **ID de requête** que la requête.

L’ID de la requête peut avoir n’importe quelle valeur valide pour une propriété de message, conformément au [Guide du développeur - Messages IoT Hub](iot-hub-devguide-messaging.md), et l’état est validé comme entier.

Le corps de la réponse contient la section des propriétés du jumeau d’appareil, comme illustré dans l’exemple de réponse suivant :

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

Les codes d’état possibles sont :

|Statut | Description |
| ----- | ----------- |
| 204 | Réussite (aucun contenu n’est retourné) |
| 429 | Trop de demandes (limité), selon la [Limitation d’IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erreurs de serveur |

Pour plus d’informations, consultez le [Guide du développeur - Jumeaux d’appareil](iot-hub-devguide-device-twins.md).

### <a name="update-device-twins-reported-properties"></a>Mettre à jour les propriétés signalées du jumeau d’appareil

Pour mettre à jour les propriétés signalées, l’appareil émet une demande à destination d’IoT Hub via une publication sur une rubrique MQTT désignée. Après avoir traité la demande, IoT Hub répond en indiquant l’état de réussite ou d’échec de l’opération de mise à jour via une publication sur une autre rubrique. L’appareil peut souscrire à cette rubrique pour être informé du résultat de sa demande de mise à jour dans le jumeau. Pour implémenter ce type d’interaction demande/réponse dans MQTT, nous tirons parti de la notion d’ID de demande (`$rid`) fournie initialement par l’appareil dans sa demande de mise à jour. Cet ID de demande est également inclus dans la réponse d’IoT Hub pour autoriser l’appareil à mettre en corrélation la réponse avec sa demande antérieure particulière.

La séquence suivante décrit comment un appareil met à jour les propriétés déclarées dans le jumeau d’appareil IoT Hub :

1. Un appareil doit tout d’abord s’abonner à la rubrique `$iothub/twin/res/#` pour recevoir des réponses d’opération de IoT Hub.

2. Un appareil envoie un message qui contient la mise à jour de jumeau d’appareil pour la rubrique `$iothub/twin/PATCH/properties/reported/?$rid={request id}`. Ce message contient une valeur **ID de requête**.

3. Le service envoie ensuite un message de réponse qui contient la nouvelle valeur ETag de la collection de propriétés déclarées dans la rubrique `$iothub/twin/res/{status}/?$rid={request id}`. Ce message de réponse utilise le même **ID de requête** que la requête.

Le corps du message de requête contient un document JSON qui contient de nouvelles valeurs pour les propriétés signalées. Chaque membre du document JSON met à jour ou ajoute le membre correspondant dans le document du jumeau d’appareil. Un membre défini sur `null` supprime le membre de l’objet conteneur. Par exemple :

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Les codes d’état possibles sont :

|Statut | Description |
| ----- | ----------- |
| 200 | Succès |
| 400 | Demande incorrecte. JSON incorrect |
| 429 | Trop de demandes (limité), selon la [Limitation d’IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erreurs de serveur |

L’extrait de code python ci-dessous illustre le processus de mise à jour des propriétés signalées du jumeau via MQTT (à l’aide du client Paho MQTT) :

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" + rid, twin_reported_property_patch, qos=0)
```

En cas de réussite de l’opération de mise à jour des propriétés signalées du jumeau ci-dessus, le message de la publication à partir d’IoT Hub comprend la rubrique suivante : `$iothub/twin/res/204/?$rid=1&$version=6`, où `204` est le code d’état indiquant la réussite, `$rid=1` correspond à l’ID de demande fourni par l’appareil dans le code et `$version` désigne la version de la section des propriétés signalées des jumeaux d’appareil après la mise à jour.

Pour plus d’informations, consultez le [Guide du développeur - Jumeaux d’appareil](iot-hub-devguide-device-twins.md).

### <a name="receiving-desired-properties-update-notifications"></a>Réception de notifications de mise à jour des propriétés souhaitées

Lorsqu’un appareil est connecté, IoT Hub envoie des notifications à la rubrique `$iothub/twin/PATCH/properties/desired/?$version={new version}`, qui contient le contenu de la mise à jour effectuée par le serveur principal de la solution. Par exemple :

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Comme pour les mises à jour de propriétés, les valeurs `null` signifient que le membre de l’objet JSON est en cours de suppression. Notez également que `$version` indique la nouvelle version de la section des propriétés souhaitées du jumeau.

> [!IMPORTANT]
> IoT Hub génère des notifications de modification uniquement lorsque les appareils sont connectés. Veillez à implémenter le [flux de reconnexion des appareils](iot-hub-devguide-device-twins.md#device-reconnection-flow) pour maintenir la synchronisation des propriétés souhaitées entre IoT Hub et l’application pour appareil.

Pour plus d’informations, consultez le [Guide du développeur - Jumeaux d’appareil](iot-hub-devguide-device-twins.md).

### <a name="respond-to-a-direct-method"></a>Répondre à une méthode directe

Tout d’abord, un appareil doit s’abonner à `$iothub/methods/POST/#`. IoT Hub envoie des requêtes de méthodes à la rubrique `$iothub/methods/POST/{method name}/?$rid={request id}`, avec un corps vide ou un code JSON valide.

Pour répondre, l’appareil envoie un message avec un corps vide ou JSON valide à la rubrique `$iothub/methods/res/{status}/?$rid={request id}`. Dans ce message, **request ID** doit correspondre à celui du message de requête, et **status** doit être un entier.

Pour plus d’informations, consultez le [Guide du développeur - Méthode directe](iot-hub-devguide-direct-methods.md).

### <a name="additional-considerations"></a>Considérations supplémentaires

Enfin, si vous avez besoin de personnaliser le comportement du protocole MQTT côté cloud, examinez la [passerelle de protocole Azure IoT](iot-hub-protocol-gateway.md). Ce logiciel vous permet de déployer une passerelle de protocole personnalisé à hautes performances qui communique directement avec IoT Hub. La passerelle de protocole Azure IoT vous permet de personnaliser le protocole de l’appareil pour prendre en charge des déploiements MQTT de type « brownfield » ou autres protocoles personnalisés. Toutefois, cette approche nécessite l’exécution et l’utilisation d’une passerelle de protocole personnalisée.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le protocole MQTT, consultez la [documentation de MQTT](https://mqtt.org/documentation).

Pour en savoir plus sur la planification de votre déploiement IoT Hub, consultez :

* [Catalogue d’appareils certifiés Azure pour l’IoT](https://catalog.azureiotsolutions.com/)
* [Prendre en charge des protocoles supplémentaires](iot-hub-protocol-gateway.md)
* [Comparer à Event Hubs](iot-hub-compare-event-hubs.md)
* [Mise à l’échelle, haute disponibilité et reprise d’activité](iot-hub-scaling.md)

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur d’IoT Hub](iot-hub-devguide.md)
* [Déploiement d’une IA sur des appareils de périmètre avec Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
