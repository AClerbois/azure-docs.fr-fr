---
title: Considérations sur Xamarin Android (bibliothèque d’authentification Microsoft pour .NET)
titleSuffix: Microsoft identity platform
description: En savoir plus sur les considérations spécifiques lors de l’utilisation de Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d6af9753887ffa593a44fba9faa3376066417a8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802654"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Considérations spécifiques à Xamarin Android avec MSAL.NET
Cet article traite des considérations spécifiques lors de l’utilisation de Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Définir l’activité parente

Dans Xamarin.Android, vous devez définir l’activité parente afin que le jeton revienne une fois que l’interaction a eu lieu.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
Vous pouvez également définir ceci au niveau PublicClientApplication (dans MSAL 4.2+) à l’aide d’un rappel.

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Il est recommandé d’utiliser le CurrentActivityPlugin [ici](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Votre code de générateur PublicClientApplication ressemble alors à ceci :

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>S’assurer que le contrôle revient à la bibliothèque MSAL une fois la partie interactive du flux d’authentification terminée
Sur Android, vous devez remplacer la méthode `OnActivityResult` de `Activity` et appeler la méthode SetAuthenticationContinuationEventArgs de la classe MSAL AuthenticationContinuationHelper.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
Cette ligne garantit que le contrôle revient à la bibliothèque MSAL une fois la partie interactive du flux d’authentification terminée.

## <a name="update-the-android-manifest"></a>Mettre à jour le manifeste Android
`AndroidManifest.xml` doit contenir les valeurs suivantes :
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>Utilisez la vue web incorporée (facultatif)

Par défaut, MSAL.NET utilise le navigateur web du système, qui vous permet d’obtenir l’authentification unique avec les applications web et d’autres applications. Dans certains cas rares, vous souhaiterez sans doute spécifier que vous voulez utiliser la vue web intégrée. Pour plus d’informations, consultez [MSAL.NET utilise un navigateur web](msal-net-web-browsers.md) et [Navigateur du système Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous créez une application Xamarin.Forms et que vous ajoutez une référence au package NuGet de MSAL.Net, cela fonctionnera.
Toutefois, si vous souhaitez mettre à niveau une application Xamarin.Forms vers MSAL.NET preview 1.1.2 ou version ultérieure, vous rencontrerez peut-être des problèmes de build.

Pour résoudre ces problèmes, vous devez :
- Mettre à jour le package NuGet de MSAL.NET existant vers MSAL.NET preview 1.1.2 ou version ultérieure
- Vérifier que Xamarin.Forms est automatiquement mis à jour vers la version 2.5.0.122203 (si ce n’est pas, mettez à jour vers cette version)
- Vérifier que Xamarin.Android.Support.v4 est automatiquement mis à jour vers la version 25.4.0.2 (si ce n’est pas, mettez à jour vers cette version)
- Tous les packages Xamarin.Android.Support doivent cibler la version 25.4.0.2
- Nettoyer/régénérer
- Essayez de définir la valeur maximale de builds de projet en parallèle sur 1 dans Visual Studio (Options->Projets et Solutions->Générer et exécuter->Nombre maximal de builds de projets en parallèle)
- Vous pouvez également, si vous générez à partir de la ligne de commande, essayer de supprimer /m de votre commande si vous l’utilisez.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Error: Le nom 'AuthenticationContinuationHelper' n’existe pas dans le contexte actuel

Visual Studio n’a sans doute pas mis correctement à jour le fichier Android.csproj*. Parfois le chemin d’accès au fichier **\<HintPath>** contient la valeur netstandard13 au lieu de **monoandroid90**, ce qui est incorrect.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez plus de détails et d’exemples dans le paragraphe [Considérations spécifiques pour Android](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) du fichier readme.md de l’exemple suivant :

| Exemple | Plateforme | Description |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Une application Xamarin Forms simple expliquant comment utiliser MSAL pour authentifier MSA et Azure AD via le point de terminaison AADD v2.0, et comment accéder à Microsoft Graph avec le jeton résultant. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |