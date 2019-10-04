---
title: Ajout de l’authentification à Apache Cordova à l’aide de Mobile Apps | Microsoft Docs
description: Découvrez comment utiliser Mobile Apps dans Azure App Service pour authentifier les utilisateurs de votre application Apache Cordova via divers fournisseurs d’identité, notamment Google, Facebook, Twitter et Microsoft.
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b0634038dbf5771ac1aa0bc00d007e758171b238
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443504"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Ajout de l’authentification à votre application Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center investit dans des services nouveaux et intégrés, essentiels au développement d’applications mobiles. Les développeurs peuvent utiliser les services **Build**, **Test** et **Distribute** pour configurer le pipeline de livraison et d’intégration continues. Une fois l’application déployée, les développeurs peuvent superviser l’état et l’utilisation de leur application à l’aide des services **Analytics** et **Diagnostics**, puis interagir avec les utilisateurs à l’aide du service **Push**. Les développeurs peuvent aussi utiliser **Auth** pour authentifier leurs utilisateurs ainsi que le service **Data** pour conserver et synchroniser les données d’application dans le cloud. Découvrez [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started-users) dès aujourd’hui.
>

## <a name="summary"></a>Résumé
Dans ce didacticiel, vous allez ajouter l’authentification au projet de démarrage rapide todolist sur Apache Cordova en faisant appel à un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [Prise en main de Mobile Apps] , que vous devez effectuer en premier.

## <a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Regarder une vidéo montrant des étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

À présent, vous pouvez vérifier que l’accès anonyme à votre serveur principal a été désactivé. Dans Visual Studio :

* Ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Apps].
* Exécutez votre application dans **l’émulateur Android de Google**.
* Vérifiez qu’un problème inattendu de connexion s’affiche après le démarrage de l’application.

Ensuite, vous mettez à jour l’application pour authentifier les utilisateurs avant de demander des ressources à partir du serveur principal d’applications mobiles.

## <a name="add-authentication"></a>Ajout de l’authentification à l’application
1. Ouvrez votre projet dans **Visual Studio**, puis ouvrez le fichier `www/index.html` pour modification.
2. Localisez la balise META `Content-Security-Policy` dans la section d’en-tête.  Ajoutez l’hôte OAuth à la liste des ressources autorisées.

   | Fournisseur | Nom du fournisseur du Kit de développement logiciel. | Hôte OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Voici un exemple Content-Security-Policy (implémenté pour Azure Active Directory) :

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Remplacez `https://login.microsoftonline.com` par l’hôte OAuth du tableau précédent.  Consultez la [documentation Content-Security-Policy] pour en savoir plus sur la balise meta content-security-policy.

    Certains fournisseurs d’authentification ne requièrent aucune modification Content-Security-Policy avec des appareils mobiles appropriés.  Par exemple, aucune modification de l’approche Content-Security-Policy n’est nécessaire lorsque vous recourez à l’authentification Google sur un appareil Android.

3. Ouvrez le fichier `www/js/index.js` pour modification, recherchez la méthode `onDeviceReady()` et, sous le code de création du client, ajoutez le code suivant :

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Ce code remplace le code existant qui crée la référence de table et actualise l'interface utilisateur.

    La méthode login() lance l'authentification auprès du fournisseur. La méthode login() est une méthode asynchrone qui renvoie une promesse JavaScript.  Le reste de l’initialisation est placé au sein de la réponse de promesse, de manière à ce qu’aucune exécution n’intervienne avant la fin de la méthode login().

4. Dans le code que vous venez d'ajouter, remplacez `SDK_Provider_Name` par le nom de votre fournisseur de connexion. Par exemple, pour Azure Active Directory, utilisez `client.login('aad')`.
5. Exécutez votre projet.  Une fois que le projet a terminé l’initialisation, votre application affiche la page de connexion OAuth du fournisseur d’authentification choisi.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus [À propos de l’authentification] avec Azure App Service.
* Poursuivez le didacticiel en ajoutant les [Notifications Push] à votre application Apache Cordova.

Découvrez comment utiliser les Kits de développement logiciel.

* [Kit de développement logiciel (SDK) Apache Cordova]
* [Kit de développement logiciel du serveur ASP.NET]
* [Kit de développement logiciel du serveur Node.js]

<!-- URLs. -->
[Prise en main de Mobile Apps]: app-service-mobile-cordova-get-started.md
[documentation Content-Security-Policy]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notifications Push]: app-service-mobile-cordova-get-started-push.md
[À propos de l’authentification]: app-service-mobile-auth.md
[Kit de développement logiciel (SDK) Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[Kit de développement logiciel du serveur ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Kit de développement logiciel du serveur Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
