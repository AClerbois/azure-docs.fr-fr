---
title: Configurer l’authentification Facebook - Azure App Service
description: Découvrez comment configurer l’authentification Facebook pour votre application App Service.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: fb8497f3b9b887e2fd06b350bcc25ac8faaa7b43
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177006"
---
# <a name="configure-your-app-service-app-to-use-facebook-login"></a>Configurer votre application App Service pour pouvoir utiliser une connexion Facebook

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cet article vous montre comment configurer Azure App Service pour utiliser Facebook comme fournisseur d’authentification.

Pour effectuer la procédure de cet article, vous avez besoin d’un compte Facebook avec une adresse e-mail vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com].

## <a name="register"></a>Inscription de votre application sur Facebook

1. Accédez au site web [Développeurs Facebook] et connectez-vous à l’aide des informations d’identification de votre compte Facebook.

   Si vous n’avez pas de compte Facebook pour les développeurs, sélectionnez **Prise en main** et suivez les étapes d’inscription.
1. Sélectionnez **Mes applications** > **Ajouter une nouvelle application**.
1. Dans le champ **Nom d’affichage** :
   1. Tapez un nom unique pour votre application.
   1. Indiquez votre **E-mail du contact**.
   1. Sélectionnez **Créer un ID d'application**.
   1. Effectuez la vérification de sécurité.

   Le tableau de bord du développeur pour votre nouvelle application Facebook s’ouvre.
1. Sélectionnez **Tableau de bord** > **Connexion Facebook** > **Configurer** > **Web**.
1. Dans le volet de navigation de gauche, sous **Connexion Facebook**, sélectionnez **Paramètres**.
1. Dans le champ **URI de redirection OAuth Valides**, entrez `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`. N’oubliez pas de remplacer `<app-name>` par le nom de votre application Azure App Service.
1. Sélectionnez **Enregistrer les modifications**.
1. Dans le volet gauche, sélectionnez **Paramètres** > **De base**. 
1. Dans le champ **Secret d’application**, sélectionnez **Afficher**. Copiez les valeurs **ID d’application** et **Secret d’application**. Vous les utiliserez plus tard pour configurer votre application App Service dans Azure.

   > [!IMPORTANT]
   > La clé secrète de l'application est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.
   >

1. Le compte Facebook que vous avez utilisé pour inscrire l'application est un administrateur de l'application. À ce stade, seuls les administrateurs peuvent se connecter à cette application.

   Pour authentifier d’autres comptes Facebook, sélectionnez **Révision de l’application** et activez **Rendre public \<nom-de-votre-application** pour activer l’accès public général à l’application à l’aide de l’authentification Facebook.

## <a name="secrets"></a>Ajout des informations Facebook à votre application

1. Connectez-vous au [portail Azure]et accédez à votre application App Service.
1. Sélectionnez **Paramètres** > **Authentification/Autorisation**, puis vérifiez que **l’authentification App Service** est activée, sur **On**.
1. Sélectionnez **Facebook**, puis collez-y les valeurs d’ID d’application et de secret d’application que vous avez obtenues précédemment. Activez toutes les étendues nécessaires à votre application.
1. Sélectionnez **OK**.

   ![Capture d’écran des paramètres Facebook d’application mobile][0]

    Par défaut, App Service fournit une authentification, mais ne restreint pas l’accès autorisé au contenu et aux API de votre site. Vous avez besoin d’autoriser les utilisateurs dans votre code d’application.
1. (Facultatif) Pour restreindre l’accès aux seuls utilisateurs authentifiés par Facebook, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Facebook**. Quand vous définissez cette fonctionnalité, votre application exige que toutes les demandes soient authentifiées. Elle redirige également toutes les demandes non authentifiées vers Facebook à des fins d’authentification.

   > [!CAUTION]
   > Cette manière de restreindre l’accès s’applique à tous les appels à votre application qui peuvent ne pas être souhaitables pour les applications qui ont une page d’accès publique disponible, comme dans de nombreuses applications monopages. Pour de telles applications, préférez **Autoriser les requêtes anonymes (aucune action)** . L’application démarre alors elle-même manuellement l’authentification. Pour plus d’informations, consultez [Flux d’authentification](overview-authentication-authorization.md#authentication-flow).

1. Sélectionnez **Enregistrer**.

Vous êtes maintenant prêt à utiliser Facebook pour l’authentification dans votre application.

## <a name="related-content"> </a>Étapes suivantes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Développeurs Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portail Azure]: https://portal.azure.com/
