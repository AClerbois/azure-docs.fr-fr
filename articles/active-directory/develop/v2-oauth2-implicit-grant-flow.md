---
title: Sécuriser les applications à page unique à l’aide du flux implicite de la plateforme d’identités Microsoft | Azure
description: Création d’applications web à l’aide de l’implémentation du flux implicite sur la plateforme d’identités Microsoft pour les applications à page unique.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e6ac72a91ae14b6f9c513c84da6f1f06508caef
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482220"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Plateforme d’identités Microsoft et flux d’octroi implicite

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Le point de terminaison de la plateforme d’identités Microsoft vous permet de connecter des utilisateurs dans vos applications à page unique avec des comptes personnels et professionnels ou scolaires de Microsoft. Les applications à page unique et autres applications JavaScript qui s’exécutent principalement dans un navigateur présentent des problématiques spécifiques liées à l’authentification :

* Les caractéristiques de sécurité de ces applications sont considérablement différentes de celles des applications web traditionnelles basées sur serveur.
* De nombreux serveurs d’autorisation et fournisseurs d’identité ne prennent pas en charge les demandes CORS.
* Chacune des redirections à partir de l’application du navigateur plein écran perturbe de manière assez importante l’expérience utilisateur.

Pour ces applications (AngularJS, Ember.js, React.js, etc.), la plateforme d’identités Microsoft prend en charge le flux d’octroi implicite OAuth 2.0. Le flux implicite est décrit dans la [spécification OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Il permet notamment à l’application d’obtenir des jetons de la plateforme d’identités Microsoft sans échanger les informations d’identification du serveur principal. L’application peut alors connecter l’utilisateur, maintenir la session et obtenir des jetons pour d’autres API web, le tout dans le code JavaScript client. Quelques points de sécurité importants sont à prendre en compte lorsque vous utilisez le flux implicite, particulièrement en ce qui concerne l’emprunt d’identité du [client](https://tools.ietf.org/html/rfc6749#section-10.3) et de [l’utilisateur](https://tools.ietf.org/html/rfc6749#section-10.3).

Si vous souhaitez recourir au flux implicite et à la plateforme d’identités Microsoft pour ajouter l’authentification à votre application JavaScript, nous vous recommandons d’utiliser la bibliothèque JavaScript open source [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

Toutefois, si vous préférez ne pas utiliser de bibliothèque dans votre application à page unique et envoyer vous-même des messages de protocole, suivez la procédure générale ci-dessous.

> [!NOTE]
> Le point de terminaison de la plateforme d’identités Microsoft ne prend pas en charge l’intégralité des scénarios et fonctionnalités d’Azure AD (Azure Active Directory). Pour déterminer si vous devez utiliser le point de terminaison de la plateforme d’identités Microsoft, consultez les [limitations de la plateforme d’identités Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Schéma de protocole

Le diagramme suivant montre à quoi ressemble le flux implicite de connexion complet. Les sections qui suivent décrivent chaque étape plus en détail.

![Diagramme montrant le flux de connexion implicite](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Envoyer la requête de connexion

Pour connecter une première fois l’utilisateur à votre application, vous pouvez envoyer une demande d’authentification [OpenID Connect](v2-protocols-oidc.md) et obtenir un jeton `id_token` à partir du point de terminaison de la plateforme d’identités Microsoft.

> [!IMPORTANT]
> Pour demander un jeton d’ID avec succès, le flux d’octroi implicite de l’inscription d’application sur la page [Inscriptions d’applications du portail Azure](https://go.microsoft.com/fwlink/?linkid=2083908) doit être correctement activé. Pour ce faire, sélectionnez **Jetons d’accès** et **Jetons d’ID** sous la section **Octroi implicite**. S’il n’est pas activé, une erreur `unsupported_response` est retournée : **La valeur fournie pour le paramètre d’entrée 'response_type' n’est pas autorisée pour ce client. La valeur attendue est ’code’**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Pour tester la connexion à l’aide du flux implicite, cliquez sur <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Une fois que vous êtes connecté, votre navigateur doit être redirigé vers `https://localhost/myapp/` avec une valeur `id_token` dans la barre d’adresse.
>

| Paramètre |  | Description |
| --- | --- | --- |
| `tenant` | required |La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client. Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints). |
| `client_id` | required | L’ID (client) d’application attribué à votre application par la page [Inscriptions d’applications du portail Azure](https://go.microsoft.com/fwlink/?linkid=2083908). |
| `response_type` | required |Doit inclure `id_token` pour la connexion à OpenID Connect. Il peut également inclure l’élément response_type `token`. L’utilisation de l’élément `token` permet ici à votre application de recevoir immédiatement un jeton d’accès à partir du point de terminaison d’autorisation sans avoir à exécuter une deuxième requête sur le point de terminaison d’autorisation. Si vous utilisez l’élément response_type `token`, le paramètre `scope` doit contenir une étendue indiquant la ressource pour laquelle le jeton doit être émis. |
| `redirect_uri` | recommandé |L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| `scope` | required |Une liste d’[étendues](v2-permissions-and-consent.md) séparées par des espaces. Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion dans l’interface utilisateur de consentement. Vous pouvez également inclure les étendues `email` ou `profile` permettant d’accéder à des données utilisateur supplémentaires. Vous pouvez aussi inclure d’autres étendues dans cette requête pour solliciter le consentement sur diverses ressources. |
| `response_mode` | facultatif |Spécifie la méthode à utiliser pour envoyer le jeton résultant à votre application. Par défaut demande un jeton d’accès, mais fragmente si la requête inclut un jeton id_token. |
| `state` | recommandé |Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les falsifications de requête intersite](https://tools.ietf.org/html/rfc6749#section-10.12). La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| `nonce` | required |Une valeur incluse dans la requête, générée par l’application, qui sera intégrée dans le jeton id_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. Nécessaire uniquement lorsqu’un jeton id_token est demandé. |
| `prompt` | facultatif |Indique le type d’interaction utilisateur requis. Les seules valeurs valides pour l’instant sont « login », « none », « select_account » et « consent ». `prompt=login` oblige l'utilisateur à saisir ses informations d'identification lors de cette requête, annulant de fait l'authentification unique. Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu’aucune invite interactive d’aucune sorte n’est présentée à l’utilisateur. Si la demande ne peut pas être exécutée en mode silencieux au moyen d’une authentification unique, le point de terminaison de la plateforme d’identités Microsoft renvoie une erreur. `prompt=select_account` envoie l’utilisateur vers un sélecteur de compte dans lequel tous les comptes mémorisés dans la session seront affichés. `prompt=consent` déclenche l’affichage de la boîte de dialogue de consentement OAuth après la connexion de l’utilisateur, afin de lui demander d’octroyer des autorisations à l’application. |
| `login_hint`  |facultatif |Peut être utilisé pour remplir au préalable le champ réservé au nom d’utilisateur/à l’adresse électronique de la page de connexion de l’utilisateur si vous connaissez déjà son nom d’utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, après avoir extrait le nom d’utilisateur à partir d’une connexion précédente à l’aide de la revendication `preferred_username` .|
| `domain_hint` | facultatif |Peut être `consumers` ou `organizations`. S’il est inclus, ce paramètre ignore le processus de découverte par e-mail auquel l’utilisateur doit se soumettre sur la page de connexion, ce qui améliore légèrement l’expérience utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, en extrayant la revendication `tid` du jeton id_token. Si la valeur de revendication `tid` est `9188040d-6c67-4c5b-b112-36a304b66dad` (le client consommateur de compte Microsoft), vous devez utiliser `domain_hint=consumers`. Sinon, vous pouvez utiliser `domain_hint=organizations` au cours de la réauthentification. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison de la plateforme d’identités Microsoft s’assure également que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope`. Si l’utilisateur n’a accepté **aucune** de ces autorisations, il lui demande de corriger ce manquement. Pour plus d’informations, consultez [Autorisations, consentement et applications multi-locataires](v2-permissions-and-consent.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison de la plateforme d’identités Microsoft renvoie une réponse à votre application à l’URI `redirect_uri` indiqué à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

#### <a name="successful-response"></a>Réponse correcte

Une réponse correcte utilisant `response_mode=fragment` et `response_type=id_token+token` ressemble à ceci (des sauts de ligne sont ici insérés pour une meilleure lisibilité) :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paramètre | Description |
| --- | --- |
| `access_token` |Inclus si `response_type` inclut `token`. Jeton d’accès demandé par l’application, dans ce cas Microsoft Graph. Le jeton d’accès ne doit pas être décodé ou inspecté ; il doit être traité comme une chaîne opaque. |
| `token_type` |Inclus si `response_type` inclut `token`. Sera toujours `Bearer`. |
| `expires_in`|Inclus si `response_type` inclut `token`. Indique, en secondes, la durée de validité du jeton pour la mise en cache. |
| `scope` |Inclus si `response_type` inclut `token`. Indique la ou les étendues pour lesquelles le jeton access_token sera valide. Peut ne pas inclure toutes les étendues demandées, si elles n’étaient pas applicables à l’utilisateur (dans le cas des étendues spécifiques à Azure AD demandées au moment de la connexion avec un compte personnel). |
| `id_token` | Un jeton Web JSON signé (JWT). L’application peut décoder les segments de ce jeton, afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais elle ne peut pas les utiliser pour les limites d’autorisation ou de sécurité. Pour en savoir plus sur id_tokens, consultez [`id_token reference`](id-tokens.md). <br> **Remarque :** Fourni uniquement si l’étendue `openid` a été demandée. |
| `state` |Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

#### <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée :

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| --- | --- |
| `error` |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| `error_description` |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## <a name="validate-the-idtoken"></a>Valider le jeton id_token

La réception du jeton id_token ne suffit pas à authentifier l’utilisateur. Vous devez également valider la signature du jeton id_token et vérifier la conformité des revendications du jeton par rapport aux exigences de votre application. Le point de terminaison de la plateforme d’identités Microsoft utilise les [jetons web JSON (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité.

Vous pouvez décider de valider l’élément `id_token` dans le code du client, mais une pratique courante consiste à envoyer l’élément `id_token` vers un serveur principal, afin d’y appliquer la validation. Une fois que vous avez validé la signature du jeton id_token, il vous faudra vérifier quelques revendications. Pour plus d’informations, consultez la page de [`id_token`référence](id-tokens.md), notamment les sections [Validation des jetons](id-tokens.md#validating-an-id_token) et [Informations importantes sur la substitution des clés de signature](active-directory-signing-key-rollover.md). Nous vous recommandons d’utiliser une bibliothèque pour analyser et valider les jetons. Il en existe au moins une pour la plupart des langages et plateformes.

En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

* S’assurer que l’utilisateur/l’organisation s’est inscrit pour l’application.
* S’assurer que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
* S’assurer de l’utilisation d’une force certaine d’authentification, comme une authentification multifacteur.

Une fois que vous avez validé le jeton id_token, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton id_token pour récupérer les informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, la personnalisation, etc.

## <a name="get-access-tokens"></a>Obtenir des jetons d’accès

Maintenant que vous avez connecté l’utilisateur dans votre application à page unique, vous pouvez obtenir des jetons d’accès destinés à appeler des API web sécurisées à l’aide de la plateforme d’identités Microsoft, comme [Microsoft Graph](https://developer.microsoft.com/graph). Même si vous avez déjà reçu un jeton utilisant l’élément response_type `token`, vous pouvez utiliser cette méthode pour acquérir des jetons vers des ressources supplémentaires sans avoir à demander à l’utilisateur de se reconnecter.

Dans le flux normal OpenID Connect/OAuth, il vous faut transmettre une demande au point de terminaison `/token` de la plateforme d’identités Microsoft. Toutefois, le point de terminaison de la plateforme d’identités Microsoft ne prend pas en charge les demandes CORS. Il est donc hors de question d’effectuer des appels AJAX afin d’obtenir et d’actualiser des jetons. Au lieu de cela, vous pouvez utiliser le flux implicite d’un iFrame masqué afin d’obtenir de nouveaux jetons pour d’autres API web : 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Pour plus d’informations sur les paramètres de requête dans l’URL, consultez [Envoyer la requête de connexion](#send-the-sign-in-request).

> [!TIP]
> Essayez de copier et coller la requête ci-dessous dans un onglet de navigateur ! (N'oubliez pas de remplacer les valeurs `login_hint` par la valeur correcte pour votre utilisateur)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Grâce au paramètre `prompt=none` , cette requête va immédiatement réussir ou échouer, avant de revenir vers votre application. Une réponse correcte sera envoyée à votre application, à l’`redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

#### <a name="successful-response"></a>Réponse correcte

Une réponse correcte utilisant `response_mode=fragment` se présente ainsi :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Paramètre | Description |
| --- | --- |
| `access_token` |Inclus si `response_type` inclut `token`. Jeton d’accès demandé par l’application, dans ce cas Microsoft Graph. Le jeton d’accès ne doit pas être décodé ou inspecté ; il doit être traité comme une chaîne opaque. |
| `token_type` | Sera toujours `Bearer`. |
| `expires_in` | Indique, en secondes, la durée de validité du jeton pour la mise en cache. |
| `scope` | Indique la ou les étendues pour lesquelles le jeton access_token sera valide. Peut ne pas inclure toutes les étendues demandées, si elles n’étaient pas applicables à l’utilisateur (dans le cas des étendues spécifiques à Azure AD demandées au moment de la connexion avec un compte personnel). |
| `id_token` | Un jeton Web JSON signé (JWT). Inclus si `response_type` inclut `id_token`. L’application peut décoder les segments de ce jeton, afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais elle ne peut pas les utiliser pour les limites d’autorisation ou de sécurité. Pour en savoir plus sur id_tokens, consultez le document de [`id_token`référence](id-tokens.md). <br> **Remarque :** Fourni uniquement si l’étendue `openid` a été demandée. |
| `state` |Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

#### <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée. Dans le cas de `prompt=none`, une erreur se présentera généralement ainsi :

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paramètre | Description |
| --- | --- |
| `error` |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| `error_description` |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

Si vous recevez cette erreur dans la requête iFrame, l’utilisateur doit se connecter de nouveau de manière interactive, ceci pour récupérer un nouveau jeton. Vous êtes invité à gérer ce cas de la manière la plus appropriée pour votre application.

## <a name="validating-access-tokens"></a>Validation des jetons d’accès

Quand vous recevez un access_token, veillez à valider la signature du jeton, ainsi que les réclamations suivantes. En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires.

* Revendication **audience** : il s’agit de vérifier que le jeton était bien destiné à votre application
* Revendication **émetteur** : il s’agit de vérifier que le jeton a été émis vers votre application par le point de terminaison de la plateforme d’identités Microsoft
* Revendications **pas avant** et **heure d’expiration** : il s’agit de vérifier que le jeton n’est pas arrivé à expiration

Pour plus d’informations sur les revendications se trouvant dans le jeton d’accès, consultez l’article [Informations de référence sur les jetons d’accès](access-tokens.md)

## <a name="refreshing-tokens"></a>Actualisation des jetons

L’octroi implicite ne fournit pas de jetons d’actualisation. Les `id_token` et les `access_token` expirant après une courte période, votre application doit être préparée à les actualiser de manière régulière. Pour actualiser chaque type de jeton, vous pouvez exécuter la demande iFrame masquée ci-dessus à l’aide du paramètre `prompt=none` afin de contrôler le comportement de la plateforme d’identités. Si vous souhaitez recevoir un nouvel élément `id_token`, veillez à utiliser `response_type=id_token` et `scope=openid`, ainsi qu’un paramètre `nonce`.

## <a name="send-a-sign-out-request"></a>Envoi d’une demande de déconnexion

L’élément `end_session_endpoint` OpenID Connect permet à votre application d’envoyer une demande au point de terminaison de la plateforme d’identités Microsoft pour mettre fin à la session d’un utilisateur et effacer les cookies définis par le point de terminaison de la plateforme d’identités Microsoft. Pour déconnecter complètement un utilisateur d’une application web, votre application doit mettre fin à sa propre session avec l’utilisateur (généralement en vidant un cache de jeton ou en supprimant les cookies), puis rediriger le navigateur vers :

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Paramètre |  | Description |
| --- | --- | --- |
| `tenant` |required |La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client. Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | recommandé | URL vers laquelle l’utilisateur doit être redirigé après la déconnexion. Cette valeur doit correspondre à l’un des URI de redirection inscrits pour l’application. Si elle n’est pas incluse, le point de terminaison de la plateforme d’identités Microsoft affiche un message générique. |

## <a name="next-steps"></a>Étapes suivantes

* Consultez les [exemples JS MSAL](sample-v2-code.md) pour commencer à coder.
