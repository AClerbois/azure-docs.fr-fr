---
title: Messages cloud-à-appareil avec Azure IoT Hub (.NET) | Microsoft Docs
description: Envoi de messages cloud-à-appareil vers un appareil depuis un Azure IoT Hub à l’aide des kits de développement logiciel Azure IoT pour .NET. Vous modifiez une application d’appareil pour recevoir des messages cloud-à-appareil et modifiez une application principale pour envoyer des messages cloud-à-appareil.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: 2dca04cadd4c5b904c752ac8a01af8bcb7c6ae33
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220106"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Envoyer des messages du cloud à votre appareil avec IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduction

Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie d’un appareil à un hub IoT) explique comment créer un IoT Hub, y approvisionner une identité d’appareil et coder une application d’appareil qui envoie des messages d’appareil-à-cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce tutoriel est basé sur le démarrage rapide [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie d’un appareil à un hub IoT). Il vous montre comment effectuer les étapes suivantes :

* À partir du serveur principal de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.

* Recevez des messages cloud-à-appareil sur un appareil.

* À partir du serveur principal de votre application, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez des informations supplémentaires sur les messages cloud-à-appareil dans [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).

À la fin de ce didacticiel, vous exécutez deux applications console .NET :

* **SimulatedDevice**, une version modifiée de l’application créée dans [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie d’un appareil à un hub IoT), qui se connecte à votre hub IoT et reçoit des messages cloud-à-appareil.

* **SendCloudToDevice**, qui envoie un message cloud-à-appareil à l’application d’appareil par le biais d’IoT Hub, puis reçoit son accusé de réception.

> [!NOTE]
> IoT Hub offre la prise en charge de Kits de développement logiciel (SDK) pour plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des [Kits Azure IoT device SDK](iot-hub-devguide-sdks.md). Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel et à Azure IoT Hub de manière générale, consultez le [Guide du développeur IoT Hub](iot-hub-devguide.md).
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2017

* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](http://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

## <a name="receive-messages-in-the-device-app"></a>Recevoir des messages dans l’application d’appareil

Dans cette section, vous allez modifier l’application d’appareil créée dans [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie d’un appareil à un hub IoT) pour recevoir des messages cloud-à-appareil en provenance du hub IoT.

1. Dans Visual Studio, dans le projet **SimulatedDevice**, ajoutez la méthode suivante à la classe **Program**.

   ```csharp   
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

   La méthode `ReceiveAsync` renvoie de façon asynchrone le message reçu au moment où l’appareil le reçoit. Elle renvoie *null* après un délai d’attente pouvant être précisé (dans ce cas, la valeur par défaut est une minute). Lorsque l’application reçoit une valeur *null*, elle doit continuer à attendre de nouveaux messages. C’est pour répondre à cette exigence que la ligne `if (receivedMessage == null) continue` est insérée.
   
    L’appel à `CompleteAsync()` notifie IoT Hub que le message a été traité avec succès. Le message peut être supprimé en toute sécurité de la file d’attente d’appareils. Si l’application d’appareil n’a pas été en mesure de terminer le traitement du message, IoT Hub le remet à nouveau. Il est alors important que la logique de traitement du message de l’application d’appareil soit *idempotente*, afin qu’un message identique reçu plusieurs fois produise le même résultat. 
    
    Une application peut également abandonner temporairement un message. IoT hub en conserve alors le message dans la file d’attente pour un traitement ultérieur. Ou l’application peut rejeter un message, ce qui le supprime définitivement de la file d'attente. Pour plus d’informations sur le cycle de vie des messages cloud-à-appareil, consultez [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).
   
   > [!NOTE]
   > Quand vous utilisez HTTPS comme moyen de transport au lieu de MQTT ou AMQP, la méthode `ReceiveAsync` est immédiatement renvoyée. Le modèle de prise en charge des messages cloud-à-appareil avec HTTPS est représenté par des appareils connectés par intermittence qui vérifient rarement les messages (moins de toutes les 25 minutes). L’émission d’un nombre plus élevé de réceptions HTTPS conduit IoT Hub à limiter les demandes. Pour plus d’informations sur les différences entre la prise en charge de MQTT, d’AMQP et de HTTPS ainsi que la limitation d’IoT Hub, voir le [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).
   > 
   > 
2. Ajoutez la méthode suivante à la méthode **Main** juste avant la ligne `Console.ReadLine()` :
   
   ``` csharp   
   ReceiveC2dAsync();
   ```

## <a name="send-a-cloud-to-device-message"></a>Envoi d’un message cloud vers appareil
Dans cette section, vous écrivez une application console .NET qui envoie des messages cloud-à-appareil à l’application d’appareil.

1. Dans la solution Visual Studio actuelle, créez un projet d’application de bureau Visual C# à l’aide du modèle de projet **d’application de console** . Nommez le projet **SendCloudToDevice**.
   
   ![Nouveau projet dans Visual Studio](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution...**. 
   
    La fenêtre **Gérer les packages NuGet** s’ouvre.

3. Recherchez **Microsoft.Azure.Devices**, cliquez sur **Installer**, puis acceptez les conditions d’utilisation. 
   
    Cette opération lance le téléchargement, l’installation et ajoute une référence au [package Azure IoT - Service SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

4. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

   ``` csharp   
   using Microsoft.Azure.Devices;
   ```

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion du hub IoT issue de la procédure [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie à partir d’un appareil à un hub IoT) :

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Ajoutez la méthode suivante à la classe **Program** :
   
   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new 
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Cette méthode envoie un nouveau message cloud-à-appareil à l’appareil avec l’ID `myFirstDevice`. Modifiez ce paramètre uniquement si vous avez modifié celui utilisé dans [Send telemetry from a device to an IoT hub...](quickstart-send-telemetry-dotnet.md) (Envoyer des données de télémétrie à partir d’un appareil à un hub IoT).

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. Dans Visual Studio, cliquez avec le bouton droit sur votre solution et sélectionnez **Définir les projets de démarrage...**. Sélectionnez **Plusieurs projets de démarrage**, puis l’action **Démarrer** pour les applications **ReadDeviceToCloudMessages**, **SimulatedDevice** et **SendCloudToDevice**.

9. Appuyez sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice**, puis appuyez sur **Entrée**. Vous devez voir le message reçu par l’application d’appareil.
   
   ![Application recevant le message](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Réception des commentaires de remise

Il est possible de demander des accusés de réception (ou d’expiration) à IoT Hub pour chaque message cloud-à-appareil. Cette option permet au serveur principal de solution d’informer facilement d’une nouvelle tentative ou d’une logique de compensation. Pour plus d’informations sur le cycle de vie des messages cloud-à-appareil, consultez [Messagerie D2C et C2D avec IoT Hub](iot-hub-devguide-messaging.md).

Dans cette section, vous modifiez l’application **SendCloudToDevice** pour demander des commentaires et en recevoir du hub IoT.

1. Dans Visual Studio, dans le projet **SendCloudToDevice**, ajoutez la méthode suivante à la classe **Program**.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;
   
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}", 
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();
   
            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Notez que ce modèle de réception est le même que celui utilisé pour recevoir des messages cloud-à-appareil à partir de l’application de l’appareil.

2. Ajoutez la méthode suivante dans la méthode **Main** juste après la ligne `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` :
   
   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. Pour obtenir des commentaires sur la remise de votre message cloud-à-appareil, vous devez spécifier une propriété dans la méthode **SendCloudToDeviceMessageAsync** . Ajoutez la ligne suivante, immédiatement après la ligne `var commandMessage = new Message(...);` :
   
   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Exécutez les applications en appuyant sur **F5**. Les trois applications doivent démarrer. Sélectionnez les fenêtres **SendCloudToDevice**, puis appuyez sur **Entrée**. Vous devez voir le message reçu par l’application d’appareil et, après quelques secondes, le message de commentaires reçu par votre application **SendCloudToDevice**.
   
   ![Application recevant le message](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).
> 

## <a name="next-steps"></a>Étapes suivantes

Dans cette procédure, vous avez appris comment envoyer et recevoir des messages de cloud-à-appareil. 

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Accélérateur de solution de surveillance à distance Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT Hub](iot-hub-devguide.md).