---
title: Authentification et autorisation dans Azure App Service pour les applications mobiles | Microsoft Docs
description: Référence et vue d’ensemble conceptuelles de la fonctionnalité d’authentification/autorisation pour Azure App Service, en particulier pour des applications mobiles
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 87bdfcc827155e5dd0a02ffb1640bf7e9cd4e479
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859122"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Authentification et autorisation dans Azure App Service pour les applications mobiles

Cet article décrit comment l’authentification et autorisation fonctionne lorsque vous développez des applications mobiles natives avec un serveur principal App Service. App Service fournit l’authentification intégrée et l’autorisation, pour que vos applications mobiles puissent se connecter aux utilisateurs sans modifier le code dans App Service. Elle propose un moyen simple de protéger votre application et fonctionne avec des données par utilisateur. 

Cet article se concentre sur le développement d’applications mobiles. Si vous souhaitez démarrer rapidement avec l’authentification et l’autorisation App Service pour votre application mobile, consultez un des didacticiels suivants [Ajout de l’authentification à votre application iOS][iOS] (ou [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] ou [Cordova]). 

Pour en savoir plus sur l’authentification et l’autorisation dans App Service, consultez [Authentification et autorisation dans Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Authentification avec un Kit de développement logiciel (SDK) fournisseur

Une fois tout configuré dans App Service, vous pouvez modifier les clients mobiles pour la connexion à App Service. Il existe deux approches :

* Exploitez un Kit de développement logiciel (SDK) publié par un fournisseur d’identité donné pour établir l’identité, puis accédez à App Service.
* Utilisez une seule ligne de code pour que le Kit de développement logiciel (SDK) client Mobile Apps puisse connecter des utilisateurs.

> [!TIP]
> La plupart des applications doivent utiliser un Kit de développement logiciel (SDK) fournisseur pour obtenir une expérience de connexion plus cohérente et tirer parti de la prise en charge de l’actualisation de jetons et obtenir d’autres avantages propres au fournisseur.
> 
> 

Lorsque vous utilisez un Kit de développement logiciel (SDK), l’expérience de connexion s’intègre parfaitement avec le système d’exploitation sur lequel l’application s’exécute. Cette méthode permet également d’obtenir un jeton fournisseur et certaines informations utilisateur sur le client, ce qui facilite beaucoup la consommation d’API graphiques et la personnalisation de l’expérience utilisateur. Dans les blogs et sur les forums, cette opération est de temps en temps appelée « flux client » ou « flux dirigé vers le client » car le code client connecte les utilisateurs et a accès à un jeton fournisseur.

Une fois qu’un jeton de fournisseur est obtenu, il doit être envoyé à App Service à des fins de validation. Lorsque App Service a validé le jeton, App Service crée un jeton App Service, qui est renvoyé au client. Le Kit de développement logiciel (SDK) client Mobile Apps dispose de méthodes auxiliaires visant à gérer cet échange et joindre automatiquement le jeton à toutes les requêtes du back end de l’application. Les développeurs peuvent également conserver une référence au jeton fournisseur.

Pour plus d’informations sur le flux d’authentification, consultez [Flux d’authentification App Service](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Authentification sans Kit de développement logiciel (SDK) fournisseur

Si vous ne souhaitez pas configurer un Kit de développement logiciel (SDK) de fournisseur, vous pouvez autoriser la fonctionnalité Mobile Apps d’Azure App Service à effectuer la connexion à votre place. Le SDK client Mobile Apps ouvre un affichage web du fournisseur de votre choix et connecte l’utilisateur. Dans les blogs et sur les forums, cette opération est occasionnellement appelée « flux serveur » ou « flux dirigé vers le serveur », car le serveur gère le processus de connexion des utilisateurs et le Kit de développement logiciel (SDK) client ne reçoit jamais le jeton fournisseur.

Le code nécessaire pour démarrer ce flux est inclus dans le didacticiel sur l’authentification pour chaque plateforme. À la fin du flux, le Kit de développement logiciel (SDK) client dispose d’un jeton App Service et le jeton est automatiquement joint à toutes les requêtes adressées au back-end de l’application.

Pour plus d’informations sur le flux d’authentification, consultez [Flux d’authentification App Service](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Autres ressources

Les didacticiels suivants expliquent comment ajouter une authentification à vos clients mobiles en utilisant le [flux dirigé vers le serveur](../app-service/overview-authentication-authorization.md#authentication-flow) :

* [Ajout de l'authentification à votre application iOS][iOS]
* [Ajout de l’authentification à votre application Android][Android]
* [Ajout de l’authentification à votre application Windows][Windows]
* [Ajout de l’authentification à votre application Xamarin.iOS][Xamarin.iOS]
* [Ajout de l’authentification à votre application Xamarin.Android][Xamarin.Android]
* [Ajout de l’authentification à votre application Xamarin.Forms][Xamarin.Forms]
* [Ajout de l’authentification à votre application Cordova][Cordova]

Utilisez les ressources suivantes si vous souhaitez exploiter le [flux dirigé vers le client](../app-service/overview-authentication-authorization.md#authentication-flow) pour Azure Active Directory :

* [Bibliothèque Active Directory Authentication Library pour iOS][ADAL-iOS]
* [Bibliothèque Active Directory Authentication Library pour Android][ADAL-Android]
* [Bibliothèque Active Directory Authentication Library pour Windows et Xamarin][ADAL-dotnet]

Utilisez les ressources suivantes si vous souhaitez exploiter le [flux dirigé vers le client](../app-service/overview-authentication-authorization.md#authentication-flow) pour Facebook :

* [Kit de développement logiciel (SDK) Facebook pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Utilisez les ressources suivantes si vous souhaitez exploiter le [flux dirigé vers le client](../app-service/overview-authentication-authorization.md#authentication-flow) pour Twitter :

* [Twitter Fabric pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Utilisez les ressources suivantes si vous souhaitez exploiter le [flux dirigé vers le client](../app-service/overview-authentication-authorization.md#authentication-flow) pour Google :

* [Kit de développement logiciel (SDK) Google Sign-In pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
