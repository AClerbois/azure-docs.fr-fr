---
title: Azure AD B2C (Bibliothèque d’authentification Microsoft pour .NET) |
description: Découvrez les considérations particulières à prendre en compte lors de l’utilisation d’Azure AD B2C avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
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
ms.openlocfilehash: 7444ecfd7a59224d0f08390385c508e4ecc40ddd
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532718"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Utiliser MSAL.NET pour connecter les utilisateurs avec des identités sociales

Vous pouvez utiliser MSAL.NET pour connecter des utilisateurs au moyen d’identités sociales avec [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C s’appuie sur la notion de stratégies. Dans MSAL.NET, la spécification d’une stratégie se traduit par la fourniture d’une autorité.

- Lorsque vous instanciez l’application cliente publique, vous devez préciser la stratégie faisant autorité.
- Lorsque vous souhaitez appliquer une stratégie, vous devez appeler un remplacement de `AcquireTokenInteractive` contenant un paramètre `authority`.

Cette page est destinée à MSAL 3.x. Si vous êtes intéressé par MSAL 2.x, consultez [Spécificités d’Azure AD B2C dans MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autorité pour une stratégie et un locataire d’Azure AD B2C

L’autorité à utiliser est `https://login.microsoftonline.com/tfp/{tenant}/{policyName}`, dans laquelle :

- `tenant` est le nom de votre locataire Azure AD B2C ; 
- `policyName`, le nom de la stratégie à appliquer (par exemple « b2c_1_susi » pour les connexions/inscriptions).

La recommandation actuelle à partir d’Azure AD B2C est d’utiliser `b2clogin.com` comme autorité. Par exemple : `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Pour plus d’informations, consultez [cette documentation](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Instanciation de l’application

Lors de la génération de l’application, vous devez fournir l’autorité.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Acquérir un jeton pour appliquer une stratégie

L’acquisition d’un jeton pour une API protégée par Azure AD B2C dans une application cliente publique vous oblige à utiliser les remplacements avec une autorité :

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

par :

- `policy` étant une des chaînes précédentes (par exemple `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` est une méthode qui trouve un compte pour une stratégie donnée. Par exemple :

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

L’application d’une stratégie (par exemple, laisser l’utilisateur final modifier son profil ou réinitialiser son mot de passe) est actuellement effectuée en appelant `AcquireTokenInteractive`. Dans le cas de ces deux stratégies, vous n’utilisez pas le résultat de jeton / d’authentification retourné.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Cas spécial des stratégies EditProfile et ResetPassword

Lorsque vous souhaitez offrir une expérience où vos utilisateurs finaux se connectent avec une identité sociale et modifient ensuite leur profil, vous décidez d’appliquer la stratégie EditProfile d’Azure AD B2C. Pour ce faire, appelez `AcquireTokenInteractive` avec l’autorité spécifique pour cette stratégie, et une invite définie sur `Prompt.NoPrompt` afin d’éviter la boîte de dialogue de sélection de compte qui doit s’afficher (car l’utilisateur est déjà connecté)

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Informations d’identification de mot de passe du propriétaire de ressource (ROPC) avec Azure AD B2C
Pour des informations plus détaillées sur le flux ROPC, consultez cette [documentation](v2-oauth-ropc.md).

Ce flux est **déconseillé**, car votre application demandant à un utilisateur son mot de passe n’est pas sécurisée. Pour plus d’informations sur ce problème, consultez [cet article](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

En utilisant le nom d’utilisateur/mot de passe, vous abandonnez un certain nombre de choses :
- Les locataires principaux de l’identité moderne : les mots de passe sont récupérés, relus. Du fait de ce concept d’un secret partagé qui peut être intercepté. C’est incompatible avec l’option sans mot de passe.
- Les utilisateurs devant utiliser l’authentification multifacteur (MFA) ne pourront pas se connecter (en l’absence de toute interaction).
- Les utilisateurs ne pourront pas utiliser l’authentification unique.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configurer le flux ROPC dans Azure AD B2C
Dans votre locataire Azure AD B2C, créez un flux d’utilisateur et sélectionnez **Se connecter à l’aide de ROPC**. La stratégie ROPC est ainsi activée pour votre locataire. Consultez [Configurer le flux des informations d’identification par mot de passe du propriétaire de ressource](/azure/active-directory-b2c/configure-ropc) pour plus de détails.

`IPublicClientApplication` contient une méthode :
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Cette méthode accepte comme paramètres :
- Les *scopes*, étendues pour lesquelles demander un jeton d’accès.
- Un nom d'utilisateur : *username*.
- Un mot de passe (*password*) SecureString pour l’utilisateur.

Pensez à utiliser l’autorité qui contient la stratégie ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Limitations du flux ROPC
 - Le flux ROPC **fonctionne uniquement pour les comptes locaux** (où vous vous inscrivez auprès d’Azure AD B2C au moyen d’un e-mail ou d’un nom d’utilisateur). Ce flux ne fonctionne pas s’il y a fédération à un des fournisseurs d’identité pris en charge par Azure AD B2C (Facebook, Google, etc.).
 - Actuellement, il n’existe **aucun id_token retourné à partir d’Azure AD B2C** lors de l’implémentation du flux ROPC depuis MSAL. Cela signifie qu’un objet de compte ne peut pas être créé, et donc que dans le cache il y aura ni compte ni utilisateur. Le flux AcquireTokenSilent ne fonctionne pas dans ce scénario. Toutefois, ROPC n’affiche pas d’interface utilisateur, par conséquent, il n’y aura aucun impact sur l’expérience utilisateur.

## <a name="google-auth-and-embedded-webview"></a>Authentification Google et affichage web incorporé

Si vous êtes un développeur Azure AD B2C qui utilise Google comme fournisseur d’identité, nous vous conseillons d’utiliser le navigateur du système, car Google n’autorise pas [l’authentification à partir d’affichages web incorporés](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). À l’heure actuelle, `login.microsoftonline.com` est une autorité approuvée auprès de Google. L’utilisation de cette autorité fonctionnera avec l’affichage web incorporé. En revanche, l’utilisation de `b2clogin.com` ne constitue pas une autorité approuvée auprès de Google, les utilisateurs ne seront donc pas en mesure de s’authentifier.

Nous fournirons une mise à jour sur le site wiki de ce [problème](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) en cas d’évolution.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Mise en cache avec Azure AD B2C dans MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Problème connu avec Azure AD B2C

MSAL.Net prend en charge un [cache de jeton](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). La clé de la mise en cache de jeton est basée sur les revendications retournées par le fournisseur d’identité. Actuellement, MSAL.Net a besoin de deux revendications pour générer une clé de cache de jeton :  
- `tid`, qui est l’ID de locataire Azure AD et 
- `preferred_username` 

Ces deux revendications sont absentes dans la plupart des scénarios Azure AD B2C. 

L’impact sur le client se traduit lorsque vous tentez d’afficher le champ du nom d’utilisateur, vous pouvez obtenir « Absent de la réponse de jeton » comme valeur. Si tel est le cas, cela est dû au fait qu’Azure AD B2C ne retourne pas de valeur dans IdToken pour preferred_username, en raison des limitations des comptes sociaux et des fournisseurs d’identité externes (IdPs). Azure AD retourne une valeur pour preferred_username, car il sait qui est l’utilisateur, par contre, pour Azure AD B2C, étant donné que l’utilisateur peut se connecter avec un compte local, Facebook, Google, GitHub, etc., il n’y a pas de valeur constante utilisable par Azure AD B2C pour preferred_username. Pour ne pas bloquer MSAL dans son déploiement de la compatibilité du cache avec ADAL, nous avons décidé d’utiliser de notre côté « Absent de la réponse de jeton » lors de la gestion des comptes Azure AD B2C, quand IdToken ne retournera rien pour preferred_username. MSAL doit retourner une valeur pour preferred_username, afin de maintenir la compatibilité du cache entre les bibliothèques.

### <a name="workarounds"></a>Solutions de contournement

#### <a name="mitigation-for-the-missing-tenant-id"></a>Atténuation des risques pour l’ID de locataire manquant

La solution de contournement suggérée consiste à utiliser la [Mise en cache par stratégie](#acquire-a-token-to-apply-a-policy)

Vous pouvez également utiliser la revendication `tid`, si vous utilisez les [stratégies personnalisées B2C](https://aka.ms/ief), car la possibilité est ainsi donnée de retourner des revendications supplémentaires à l’application. Pour en savoir plus sur [Transformation des revendications](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Atténuation pour « Absent de la réponse de jeton »
Une option consiste à utiliser la revendication « name » (nom) comme nom d’utilisateur par défaut. Le processus est mentionné dans cette [documentation B2C](../../active-directory-b2c/active-directory-b2c-reference-policies.md) -> « Dans la colonne Revendication de retour, choisissez les revendications à renvoyer à votre application dans les jetons d’authentification, après une expérience de modification de profil réussie. Par exemple, sélectionnez Nom d’affichage, Code postal ».

## <a name="next-steps"></a>Étapes suivantes 

D’autres informations détaillées sur l’acquisition de jetons de manière interactive avec MSAL.NET pour les applications Azure AD B2C sont fournies dans l’exemple suivant.

| Exemple | Plateforme | Description|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Une application Xamarin Forms simple expliquant comment utiliser MSAL.NET pour authentifier les utilisateurs via Azure AD B2C, et accéder à une API web avec les jetons générés.|
