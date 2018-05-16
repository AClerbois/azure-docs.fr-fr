---
title: Notifications Push vers des applications Chrome avec Azure Notification Hubs | Microsoft Docs
description: Apprenez comment utiliser Azure Notification Hubs pour envoyer des notifications push vers une application Chrome.
services: notification-hubs
keywords: notifications push mobiles, notifications push, notification push, notifications push chrome
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 5754a537b8a0bf0a93d6d54ba0ba78e5957ac87f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Didacticiel : Notifications Push vers des applications Chrome avec Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Ce didacticiel explique comment créer un hub de notifications et envoyer des notifications Push à un exemple d’application Google Chrome à l’aide de [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). L’application Chrome s’exécute dans le contexte d’un navigateur Google Chrome et s’inscrit auprès du hub de notifications. 

> [!NOTE]
> Les notifications push d’application Chrome ne sont pas des notifications de navigateur générique : ils sont spécifiques au modèle d’extensibilité de navigateur (voir [vue d’ensemble des applications Chrome] pour plus d’informations). Outre le navigateur de bureau, les applications Chrome s’exécutent sur mobile (Android et iOS) par le biais d’Apache Cordova. Pour plus d’informations, consultez [Applications Chrome sur mobile].

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * [Activation de Google Cloud Messaging](#register)
> * [Configuration de votre hub de notification](#configure-hub)
> * [Connexion de votre application Chrome au hub de notification](#connect-app)
> * [Envoi d’une notification push à votre application Chrome](#send)

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a id="register"></a>Activer Google Cloud Messaging
1. Accédez au site web [Google Cloud Console](https://console.cloud.google.com/cloud-resource-manager) et connectez-vous avec les informations d’identification de votre compte Google.
2. Sélectionnez **Create Project** dans la barre d’outils. 

    ![Bouton Create Project](media/notification-hubs-chrome-get-started/create-project-button.png)
1. Saisissez un nom de projet adéquat sous **Project Name** et cliquez sur le bouton **Create**.
2. Sélectionnez l’icône de notifications (clochette) dans la barre d’outils, puis sélectionnez le message **Create Project**. 

    ![Notification de création du projet](media/notification-hubs-chrome-get-started/project-creation-notification.png)
1. Notez le **Project Number** du projet que vous avez créé, affiché sur la page **Projects**. Vous allez utiliser le numéro du projet comme **GCM Sender ID** dans l’application Chrome pour votre inscription auprès de GCM.
   
    ![Console Google Cloud - numéro de projet](media/notification-hubs-chrome-get-started/gcm-project-number.png)
3. Dans le tableau de bord, sélectionnez **Go to APIs overview**. 

    ![Bouton Go to API overviews](media/notification-hubs-chrome-get-started/go-to-apis-overview-button.png)
1. Dans la page API & Services, sélectionnez **Enable APIs and Services**. 

    ![Enable APIs and Services](media/notification-hubs-chrome-get-started/enable-apis-and-services.png)
1. Lancez une recherche dans la liste avec le mot-clé **cloud messaging**. Sélectionnez **Google Cloud Messaging** dans la liste filtrée. 

    ![API Google Cloud Messaging](media/notification-hubs-chrome-get-started/google-cloud-messaging-api.png)
1. Sur la page **Google Cloud Messaging**, sélectionnez **Enable**.

    ![Activation de GCM](media/notification-hubs-chrome-get-started/enable-gcm.png)
1. Sur la page **API & Services**, basculez sur l’onglet **Credentials**. Sélectionnez **Create credentials**, puis **API key**. 

    ![Bouton Create API key](media/notification-hubs-chrome-get-started/create-api-key-button.png)
1. Dans la boîte de dialogue **API key created**, sélectionnez le bouton de copie pour copier la clé dans le Presse-papiers. Enregistrez-la à l’emplacement de votre choix. Vous allez utiliser cette valeur pour configurer le hub de notifications dans la section suivante afin de lui permettre d’envoyer des notifications push à GCM.

    ![Page de l’API](media/notification-hubs-chrome-get-started/api-created-page.png)
12. Sélectionnez votre clé d’API dans la liste **API keys**. Sur la page des clés d’API, sélectionnez **IP addresses (web servers, cron jobs, etc.)**, entrez **0.0.0.0/0** comme adresse IP, puis cliquez sur Save. 

    ![Activation des adresses IP](media/notification-hubs-chrome-get-started/enable-ip-addresses.png)

## <a id="configure-hub"></a>Création de votre hub de notifications
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

6. Sélectionnez **Google (GCM)** dans la catégorie **NOTIFICATION SETTINGS**, saisissez la **clé d’API** correspondant au projet GCM, puis cliquez sur **Save**.

      ![Azure Notification Hubs - Google (GCM)](media/notification-hubs-chrome-get-started/configure-gcm-api-key.png)

## <a id="connect-app"></a>Connexion de votre application Chrome au hub de notification
Votre hub de notification est à présent configuré pour GCM, et vous disposez des chaînes de connexion nécessaires pour inscrire votre application pour la réception et l’envoi de notifications push.

### <a name="create-a-new-chrome-app"></a>Création d’une application Chrome
L’exemple suivant repose sur un [exemple GCM d’application Chrome] et utilise la méthode recommandée pour créer une application Chrome. Cette section présente les étapes spécifiques à Azure Notification Hubs. 

> [!NOTE]
> Nous vous recommandons de télécharger la source de cette application Chrome depuis l’ [exemple de hub de notification d’application Chrome]. 

L’application Chrome est créée à l’aide de JavaScript et vous pouvez utiliser vos éditeurs favoris pour la création. L’image suivante montre à quoi ressemble l’application Chrome :

![Application Google Chrome][15]

1. Créez un dossier et nommez-le `ChromePushApp`. Le nom est arbitraire : Si vous lui donnez un autre nom, assurez-vous que vous remplacez le chemin d’accès dans les segments de code requis.
2. Téléchargez la [bibliothèque crypto-js] dans le dossier que vous avez créé au cours de la deuxième étape. Ce dossier contient deux sous-dossiers : `components` et `rollups`.
3. Créez un fichier `manifest.json` . Toutes les applications Chrome sont sauvegardées dans un fichier manifeste qui contient les métadonnées de l’application et, plus important encore, toutes les autorisations qui sont accordées à l’application lorsque l’utilisateur l’installe.
   
        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }
   
    Notez l’élément `permissions`, qui spécifie que cette application Chrome peut recevoir des notifications push de GCM. Cet exemple utilise un fichier icône, `gcm_128.png`, que vous trouvez à la source réutilisée à partir de l’exemple GCM d’origine. Vous pouvez le remplacer par une image qui correspond aux [critères d’icône](https://developer.chrome.com/apps/manifest/icons).
4. Créez un fichier appelé `background.js` en utilisant le code suivant :
   
        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }
   
        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.
   
          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);
   
          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }
   
        var registerWindowCreated = false;
   
        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {
   
            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }
   
        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);
   
        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);
   
    Ce fichier affiche le code HTML de la fenêtre de l’application Chrome (**register.html**) et définit également le gestionnaire **messageReceived** pour gérer les notifications push entrantes.
5. Créez un fichier appelé `register.html` qui définit l’interface utilisateur de l’application Chrome. 
   
   > [!NOTE]
   > Cet exemple utilise **CryptoJS v3.1.2**. Si vous avez téléchargé une autre version de la bibliothèque, assurez-vous que vous remplacez correctement la version dans le chemin d’accès `src` .
   > 
   > 
   
        <html>
   
        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>
   
        <body>
   
        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>
   
        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>
   
        <br/>
   
        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
   
        <br/>
        <br/>
   
        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>
   
        </body>
   
        </html>
6. Créez un fichier appelé `register.js` avec le code de cette étape. Ce fichier spécifie le script derrière `register.html`. Les applications Chrome n’autorisent pas l’exécution inline. Vous devez donc créer un script de sauvegarde distinct pour votre interface utilisateur.
   
        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
   
        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }
   
        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }
   
          document.getElementById("console").innerHTML = currentStatus  + status;
        }
   
        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);
   
          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }
   
        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;
   
          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }
   
          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;
   
          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }
   
        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }
   
        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";
   
          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });
   
          originalUri = endpoint + hubName;
        }
   
        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration
   
          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;
   
          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);
   
          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }
   
        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";
   
          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
   
          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();
   
          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };
   
          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }
   
          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");
   
          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }
   
    Le script comporte les points suivants :
   
   * **window.onload** définit les événements de clic des deux boutons de l’interface utilisateur. Le premier gestionnaire d’événements avec clic de bouton permet l’inscription sur GCM, l’autre utilise l’ID d’inscription renvoyé après l’inscription sur GCM pour étendre l’inscription à Azure Notification Hubs.
   * **updateLog** correspond à la fonction qui permet les informations du journal de code. 
   * **registerWithGCM** est le premier gestionnaire avec clic de bouton qui effectue l’appel `chrome.gcm.register` vers GCM afin d’enregistrer cette instance d’application Chrome.
   * **registerCallback** est la fonction de rappel appelée lors du renvoi de l’appel d’inscription GCM.
   * **registerWithNH** est le deuxième gestionnaire de clic de bouton qui s’enregistre avec Notification Hubs. Il obtient `hubName` et `connectionString` (définis par l’utilisateur) et crée l’appel d’API REST de l’inscription à Notification Hubs.
   * **splitConnectionString** et **generateSaSToken** sont des assistances qui représentent l’implémentation JavaScript du processus de création d’un jeton SaS qui doit être utilisé dans tous les appels d’API REST. Pour plus d’informations, consultez la page [Concepts courants](http://msdn.microsoft.com/library/dn495627.aspx).
   * **sendNHRegistrationRequest** est la fonction qui effectue un appel REST HTTP vers Azure Notification Hubs.
   * **registrationPayload** définit la charge utile XML d’enregistrement. Pour plus d’informations, voir [Création de l’API REST NH d’inscription]. Mettez à jour l’ID d’inscription avec la valeur reçue de GCM.
   * **client** est une instance de **XMLHttpRequest** dont se sert l’application pour exécuter la requête HTTP POST. Mettez à jour l’en-tête `Authorization` avec `sasToken`. La réussite de cet appel enregistre cette instance de l’application Chrome auprès d’Azure Notification Hubs.

        La structure générale de dossier pour ce projet doit ressembler à ce qui suit : ![Application Google Chrome - structure de dossier][21]

### <a name="set-up-and-test-your-chrome-app"></a>Installation et test de votre application Chrome
1. Ouvrez votre navigateur Chrome. Ouvrez la page **Extensions** de Chrome et activez **Mode développeur**.
   
    ![Google Chrome - activer le Mode développeur][16]
2. Cliquez sur **Load unpacked extension** et accédez au dossier où vous avez créé les fichiers. Vous pouvez également utiliser **Chrome Apps & Extensions Developer Tool**. Cet outil est lui-même une application Chrome (installée à partir de Chrome Web Store) et fournit des fonctionnalités avancées de débogage pour votre développement d’applications Chrome.
   
    ![Google Chrome - charger l’extension décompressée][17]
3. Si votre application Chrome est créée sans erreur, elle s’affiche.
   
    ![Google Chrome - affichage de l’application Chrome][18]
4. Entrez le **Project Number** obtenu précédemment depuis la **Google Cloud Console** dans le champ Sender ID et cliquez sur **Register with GCM**. Vous devriez obtenir le message vous indiquant que l’ **inscription auprès de GCM a réussi.**
   
    ![Google Chrome - personnalisation de l’application Chrome][19]
5. Entrez le nom de votre hub de notification dans le champ **Notification Hub Name** et la valeur **DefaultListenSharedAccessSignature** obtenue précédemment depuis le portail, puis cliquez sur **Register with Azure Notification Hub**. Vous devriez obtenir le message vous indiquant que l’ **inscription à Notification Hub a réussi** , ainsi que les détails de la réponse à l’inscription qui contient l’ID d’inscription à Azure Notification Hubs.
   
    ![Google Chrome - spécifier les détails de Notification Hub][20]  

## <a name="send"></a>Envoi d’une notification à votre application Chrome
À des fins de test, envoyez des notifications push Chrome à l’aide d’une application de console .NET. 

> [!NOTE]
> Vous pouvez envoyer des notifications push en utilisant Notification Hubs à partir d’un serveur principal utilisant l’<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a> publique. Découvrez le [portail de documentation](https://azure.microsoft.com/documentation/services/notification-hubs/) pour plus d’exemples interplateformes.
> 
> 

1. Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet**. Sous **Visual C#**, cliquez sur **Windows** et **Application Console**, puis cliquez sur **OK**.  Dans cette étape, un projet d’application console est créé.
2. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**. La console du gestionnaire de package s’affiche dans la fenêtre du bas.
3. Dans la fenêtre de console, exécutez la commande suivante :
   
        Install-Package Microsoft.Azure.NotificationHubs
   
   Une référence au SDK Azure Service Bus avec le <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">package NuGet WindowsAzure.ServiceBus est automatiquement ajoutée au projet.</a>
4. Ouvrez `Program.cs` et ajoutez l’instruction `using` suivante :
   
        using Microsoft.Azure.NotificationHubs;
5. Dans la classe `Program`, ajoutez la méthode suivante :
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
    Assurez-vous de remplacer l’espace réservé `<hub name>` par le nom du concentrateur de notification qui s’affiche sur le [portail](https://portal.azure.com) sur votre page Notification Hub. Remplacez également l’espace réservé de la chaîne de connexion appelée `DefaultFullSharedAccessSignature` que vous avez obtenue dans la section de configuration de votre concentrateur de notification.
   
    > [!NOTE]
    > Veillez à utiliser la chaîne de connexion avec un accès **Total**, et non un accès **Écouter**. La clé de connexion d’ **écoute** n’accorde pas de notification d’envoi de notifications push.
6. Ajoutez les appels suivants à la méthode `Main` :
   
         SendNotificationAsync();
         Console.ReadLine();
7. Assurez-vous que Chrome fonctionne bien et exécutez l’application de console.
8. Vous devriez voir le message de notification suivant sur votre bureau.
   
    ![Google Chrome - notification][13]
9. Vous pouvez également voir toutes vos notifications à l’aide de la fenêtre de notifications de Chrome sur la barre des tâches (dans Windows) lorsque Chrome est en cours d’exécution.
   
    ![Google Chrome - liste de notification](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> Vous n’avez pas besoin d’exécuter l’application Chrome ou de l’ouvrir dans le navigateur (bien que le navigateur Chrome lui-même doive être en cours d’exécution). Vous obtenez également une vue consolidée de toutes vos notifications dans la fenêtre notifications de Chrome.
> 
> 

## <a name="next-steps"> </a>Étapes suivantes
Dans ce didacticiel, vous avez envoyé des notifications de diffusion à tous vos clients inscrits auprès du serveur principal. Pour savoir comment envoyer des notifications Push à des appareils spécifiques, passez au didacticiel suivant : 

> [!div class="nextstepaction"]
>[Notifications Push vers des appareils spécifiques](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[exemple de hub de notification d’application Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Notification Hubs Overview]: notification-hubs-push-notification-overview.md
[vue d’ensemble des applications Chrome]: https://developer.chrome.com/apps/about_apps
[exemple GCM d’application Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Applications Chrome sur mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Création de l’API REST NH d’inscription]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[bibliothèque crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Notify Users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
