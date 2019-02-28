---
title: Prise en main d'Azure Mobile Apps pour les applications Xamarin.Android
description: Suivez ce didacticiel pour commencer à utiliser Azure Mobile Apps pour le développement d'applications Xamarin Android.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 16f67f55b752e8602d43066cc1ce503ce9e5c1e2
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879201"
---
# <a name="create-a-xamarinandroid-app"></a>Création d'une application Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble
Ce didacticiel montre comment ajouter un service backend cloud à une application Xamarin Android. Pour plus d’informations, consultez [Que sont les applications Mobile Apps ?](app-service-mobile-value-prop.md).

Voici une capture d’écran de l’application terminée :

![][0]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Apps pour les applications Xamarin.Android.

## <a name="prerequisites"></a>Prérequis
Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un compte Azure actif. Si vous n’avez pas de compte, inscrivez-vous pour obtenir une version d’évaluation Azure et jusqu’à 10 applications mobiles gratuites. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio avec Xamarin. Pour obtenir des instructions, consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .

## <a name="create-an-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure
Suivez ces étapes pour créer un serveur principal d’application mobile.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant configuré un serveur principal d’application mobile Azure qui peut être utilisé par vos applications clientes mobiles. Téléchargez ensuite un projet de serveur pour un serveur principal « todo list » simple et le publier dans Azure.

## <a name="configure-the-server-project"></a>Configurer le projet de serveur
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Télécharger et exécuter l'application Xamarin.Android
1. Sous **Télécharger et exécuter votre projet Xamarin.Android**, cliquez sur le bouton **Télécharger**.

      Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.
2. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application.
3. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel* , puis cliquez sur l’icône **Ajouter**.

    ![][10]

    Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le serveur principal d'applications mobiles et les données apparaissent dans la liste.

   > [!NOTE]
   > Vous pouvez vérifier le code qui se trouve dans le fichier C# ToDoActivity.cs et permet d'accéder à votre backend d'application mobile pour exécuter une requête et insérer des données.
   >
   >

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous avez des difficultés à générer la solution, exécutez le gestionnaire de package NuGet et mettez à jour les packages de support `Xamarin.Android`. Les projets de démarrage rapide n’incluent pas toujours les dernières versions.

Veuillez noter que tous les packages de support référencés dans le projet doivent avoir la même version. Le [package NuGet Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) comporte une dépendance `Xamarin.Android.Support.CustomTabs` à la plateforme Android. Par conséquent, si votre projet utilise des packages de support plus récents, vous devez installer directement ce package avec la version requise pour éviter les conflits.

## <a name="next-steps"></a>Étapes suivantes
* [Ajout de la synchronisation hors connexion à votre application](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [Ajout de l'authentification à votre application](app-service-mobile-xamarin-android-get-started-users.md)
* [Ajouter des notifications Push à votre application](app-service-mobile-xamarin-android-get-started-push.md)
* [Utilisation du client géré pour Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
