---
title: Bien démarrer avec la gestion des appareils Azure IoT Hub (Python) | Microsoft Docs
description: Découvrez comment utiliser la gestion des appareils IoT Hub pour lancer un redémarrage d’appareil à distance. Vous utilisez le SDK Azure IoT pour Python afin d’implémenter une application d’appareil simulé qui inclut une méthode directe et une application de service qui appelle la méthode directe.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 04fc1da04d9da715acfed8ca9d26e9c325afb403
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441795"
---
# <a name="get-started-with-device-management-python"></a>Prise en main de la gestion d’appareils (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ce didacticiel vous explique les procédures suivantes :

* Utiliser le portail Azure pour créer un IoT Hub et une identité d’appareil dans celui-ci.

* Créer une application d’appareil simulé disposant d’une méthode directe permettant le redémarrage de cet appareil. Les méthodes directes sont appelées à partir du cloud.

* Créer une application console Python qui appelle une méthode directe de redémarrage sur l’application d’appareil simulé via votre IoT Hub.

À la fin de ce didacticiel, vous disposerez de deux applications console Python :

* **dmpatterns_getstarted_device.py**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment, reçoit une méthode directe de redémarrage, simule un redémarrage physique et indique l’heure du dernier redémarrage.

* **dmpatterns_getstarted_service.py**, qui appelle une méthode directe sur l’application de l’appareil simulé, affiche la réponse et affiche les propriétés signalées pour la mise à jour.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* [Python 2.x ou 3.x](https://www.python.org/downloads/). Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. Si vous utilisez Python 2.x, vous devrez peut-être [installer ou mettre à niveau *pip*, le système de gestion des packages Python](https://pip.pypa.io/en/stable/installing/).

* Installer le [azure-iothub-device-client](https://pypi.org/project/azure-iothub-device-client/) du package, à l’aide de la commande       `pip install azure-iothub-device-client`

* Installer le [azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) du package, à l’aide de la commande       `pip install azure-iothub-service-client`

* Si vous utilisez le système d’exploitation Windows, utilisez le [package redistribuable Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) pour autoriser l’utilisation de DLL natives de Python.

* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Récupérer la chaîne de connexion pour le hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé

Dans cette section, vous allez :

* Créer une application console Python qui répond à une méthode directe appelée par le cloud

* Simuler un redémarrage d’appareil

* Utiliser les propriétés signalées pour activer les requêtes sur le jumeau d’appareil afin d’identifier les appareils et l’heure de leur dernier redémarrage

1. À l’aide d’un éditeur de texte, créez un fichier **dmpatterns_getstarted_device.py**.

2. Ajoutez les instructions `import` suivantes au début du fichier **dmpatterns_getstarted_device.py**.

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

3. Ajoutez des variables, y compris une variable **CONNECTION_STRING** et l’initialisation du client.  Remplacez la chaîne de connexion par la chaîne de connexion de votre appareil.  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

4. Ajoutez les rappels de fonction suivants pour implémenter la méthode directe sur l’appareil.

    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS

        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS

        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
            time.sleep(20)

            print ( "Device rebooted." )

            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            print ( "Updating device twins: rebootTime" )

        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200

        return device_method_return_value
    ```

5. Démarrez l’écouteur de méthode directe et attendez.

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

6. Enregistrez et fermez le fichier **dmpatterns_getstarted_device.py**.

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Déclencher un redémarrage à distance sur l’appareil à l’aide d’une méthode directe

Dans cette section, vous créez une application console Python qui lance un redémarrage à distance sur un appareil avec une méthode directe. L’application utilise des requêtes du jumeau d’appareil pour déterminer l’heure du dernier redémarrage de cet appareil.

1. À l’aide d’un éditeur de texte, créez un fichier **dmpatterns_getstarted_service.py**.

2. Ajoutez les instructions `import` suivantes au début du fichier **dmpatterns_getstarted_service.py**.

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

3. Ajoutez les déclarations de variable suivantes. Remplacez uniquement les valeurs d’espace réservé de _IoTHubConnectionString_ et _deviceId_.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

4. Ajoutez la fonction suivante pour appeler la méthode d’appareil afin de redémarrer l’appareil cible, puis interrogez les jumeaux de l’appareil et obtenez l’heure du dernier redémarrage.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

5. Enregistrez et fermez le fichier **dmpatterns_getstarted_service.py**.

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, exécutez la commande suivante pour commencer à écouter la méthode directe de redémarrage.

    ```
    python dmpatterns_getstarted_device.py
    ```

2. À l’autre invite de commandes, exécutez la commande suivante pour déclencher le redémarrage à distance et interroger la représentation d’appareil pour déterminer le moment du dernier redémarrage.

    ```
    python dmpatterns_getstarted_service.py
    ```

3. La réponse de l’appareil à la méthode directe s’affiche dans la console.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]