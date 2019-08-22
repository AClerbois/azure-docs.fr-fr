---
title: Connexion web avec OpenID Connect - Azure Active Directory B2C | Microsoft Docs
description: Créez des applications web à l’aide du protocole d’authentification OpenID Connect dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0e60bedcf1324b443d9b9cd34e8dc695fdb0b372
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931751"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Connexion web avec OpenID Connect dans Azure Active Directory B2C

OpenID Connect est un protocole d’authentification basé sur OAuth 2.0, qui peut être utilisé pour connecter de façon sécurisée des utilisateurs à des applications web. En utilisant l’implémentation d’OpenID Connect d’Azure Active Directory B2C (Azure AD B2C), vous pouvez sous-traiter l’inscription, la connexion et d’autres tâches de gestion des identités de vos applications web à Azure Active Directory. Ce guide explique comment procéder, indépendamment du langage. Il explique comment envoyer et recevoir des messages HTTP sans utiliser l’une de nos bibliothèques open source.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) étend le protocole *d’autorisation* OAuth 2.0 pour l’utiliser en tant que protocole *d’authentification*. Ce protocole d’authentification vous permet d’effectuer une authentification unique. Il introduit le concept de *jeton d’ID*, qui permet au client de vérifier l’identité de l’utilisateur et d’obtenir des informations de base sur son profil.

Comme il étend OAuth 2.0, il permet aux applications d’acquérir de façon sécurisée des *jetons d’accès*. Vous pouvez utiliser des jetons d’accès pour accéder aux ressources qui sont sécurisées par un [serveur d’autorisation](active-directory-b2c-reference-protocols.md). OpenID Connect est recommandé si vous créez une application web hébergée sur un serveur et accessible via un navigateur. Si vous souhaitez ajouter la gestion des identités à votre application mobile ou à votre application de bureau à l’aide d’Azure AD B2C, vous devez utiliser [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) à la place d’OpenID Connect. Pour plus d’informations sur les jetons, consultez [Vue d’ensemble des jetons dans Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

Azure AD B2C étend le protocole OpenID Connect standard pour proposer plus qu’une simple authentification et une simple autorisation. Il introduit le [paramètre de flux utilisateur](active-directory-b2c-reference-policies.md), grâce auquel vous pouvez utiliser OpenID Connect pour ajouter des expériences utilisateur à votre application, telles que l’inscription, la connexion et la gestion des profils.

## <a name="send-authentication-requests"></a>Envoi de demandes d’authentification

Lorsque votre application web a besoin d’authentifier l’utilisateur et d’exécuter un flux utilisateur, elle peut diriger l’utilisateur vers le point de terminaison `/authorize`. L’utilisateur prend des mesures en fonction du flux utilisateur.

Dans cette demande, le client indique les autorisations qu’il a besoin d’acquérir de l’utilisateur dans le paramètre `scope` et le flux utilisateur à exécuter dans le paramètre `p`. Trois exemples sont fournis dans les sections suivantes (avec des sauts de ligne pour une meilleure lisibilité), chacun utilisant un flux utilisateur différent. Pour avoir une idée du fonctionnement de chaque demande, essayez de coller la demande dans un navigateur et exécutez-la. Vous pouvez remplacer `fabrikamb2c` par le nom de votre locataire si vous en avez un et que vous avez créé un flux utilisateur. Vous devrez également remplacer `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`. Remplacez cet ID client par l’ID d’application de l’inscription d’application que vous avez créée. Remplacez également le nom de la stratégie `b2c_1_sign_in` par le nom de la stratégie que vous avez dans votre locataire. 

#### <a name="use-a-sign-in-user-flow"></a>Utilisation d’un flux d’utilisateur de connexion
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>Utilisation d’un flux d’utilisateur d’inscription
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>Utilisation d’un flux d’utilisateur de modification de profil
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Paramètre | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_id | OUI | ID d’application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| nonce | OUI | Valeur incluse dans la demande (générée par l’application) qui est incluse dans le jeton d’ID résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique aléatoire qui peut être utilisée pour identifier l’origine de la demande. |
| p | OUI | Flux utilisateur qui est exécuté. Il s’agit du nom d’un flux utilisateur créé dans votre locataire Azure AD B2C. Le nom du flux utilisateur doit commencer par `b2c\_1\_`. |
| response_type | OUI | Doit inclure un jeton d’ID pour OpenID Connect. Si votre application web a également besoin de jetons pour appeler une API web, vous pouvez utiliser `code+id_token`. |
| scope | OUI | Une liste d’étendues séparées par des espaces. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de jetons d’ID. L’étendue `offline_access` est facultative pour les applications web. Elle indique que votre application a besoin d’un *jeton d’actualisation* pour un accès étendu aux ressources. |
| prompt | Non | Type d’interaction utilisateur demandée. La seule valeur valide pour l’instant est `login`, qui oblige l’utilisateur à saisir ses informations d’identification sur cette demande. |
| redirect_uri | Non | Paramètre `redirect_uri` de votre application, où les réponses d’authentification peuvent être envoyées et reçues par votre application. Il doit correspondre exactement à un des paramètres `redirect_uri` que vous avez inscrits dans le portail Azure, si ce n’est qu’il doit être codé dans une URL. |
| response_mode | Non | Méthode utilisée pour renvoyer le code d’autorisation résultant à votre application. Il peut s’agir de `query`, `form_post` ou `fragment`.  Le mode de réponse `form_post` est recommandé pour une sécurité optimale. |
| state | Non | Valeur incluse dans la demande qui est également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification, comme la page sur laquelle il était positionné. |

À ce stade, il est demandé à l’utilisateur de terminer le flux de travail. L’utilisateur peut avoir à entrer son nom d’utilisateur et son mot de passe, à se connecter avec une identité sociale ou à s’inscrire à l’annuaire. Il peut y avoir un nombre quelconque d’étapes supplémentaires en fonction de la façon dont le flux utilisateur est défini.

Une fois que l’utilisateur a terminé le flux utilisateur, une réponse est renvoyée à votre application dans le paramètre `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`. La réponse est la même pour chacun des cas ci-dessus, indépendamment du flux utilisateur.

Une réponse réussie utilisant `response_mode=fragment` se présenterait ainsi :

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| --------- | ----------- |
| id_token | Jeton d'ID que l’application a demandé. Vous pouvez utiliser le jeton d'ID pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. |
| code | Code d’autorisation que l’application a demandé, si vous avez utilisé `response_type=code+id_token`. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour une ressource cible. Les codes d’autorisation expirent généralement au bout d’environ 10 minutes. |
| state | Si un paramètre `state` est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs `state` de la demande et de la réponse sont identiques. |

Les réponses d’erreur peuvent également être envoyées au paramètre `redirect_uri` pour que l’application puisse les traiter de façon appropriée :

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| --------- | ----------- |
| error | Code pouvant être utilisé pour classer les types d’erreurs qui se produisent. |
| error_description | Message d’erreur spécifique qui peut aider à identifier la cause racine d’une erreur d’authentification. |
| state | Si un paramètre `state` est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs `state` de la demande et de la réponse sont identiques. |

## <a name="validate-the-id-token"></a>Validation du jeton d’ID

La réception d’un jeton d’ID à elle seule n’est pas suffisante pour authentifier l’utilisateur. Validez la signature du jeton d’ID et vérifiez les revendications figurant dans le jeton par rapport aux exigences de votre application. Azure AD B2C utilise les [jetons Web JSON (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité. Il existe de nombreuses bibliothèques open source pour valider les jetons JWT en fonction de votre langage préféré. Nous vous recommandons d’explorer ces options plutôt que d’implémenter votre propre logique de validation. 

Azure AD B2C présente un point de terminaison de métadonnées OpenID Connect, qui permet à une application d’obtenir des informations sur Azure AD B2C au moment de l’exécution. Ces informations incluent les points de terminaison, le contenu des jetons et les clés de signature de jetons. Il existe un document de métadonnées JSON pour chaque flux utilisateur dans votre locataire B2C. Par exemple, le document de métadonnées pour le flux utilisateur `b2c_1_sign_in` dans `fabrikamb2c.onmicrosoft.com` se trouve à l’emplacement suivant :

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Une des propriétés de ce document de configuration est `jwks_uri`, dont la valeur pour le même flux utilisateur serait :

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Pour déterminer quel flux utilisateur a été utilisé pour la signature d’un jeton d’ID (et d’où obtenir les métadonnées), deux options sont possibles. Tout d’abord, le nom du flux utilisateur est inclus dans la revendication `acr` du jeton d’ID. L’autre option consiste à coder le flux utilisateur dans la valeur du paramètre `state` lors de l’émission de la requête, puis à la décoder pour déterminer le flux utilisateur qui a été utilisé. Les 2 méthodes sont valides.

Après avoir acquis le document de métadonnées à partir du point de terminaison de métadonnées OpenID Connect, vous pouvez utiliser les clés publiques RSA 256 pour valider la signature du jeton d’ID. Il peut y avoir plusieurs clés répertoriées sur ce point de terminaison, chacune étant identifiée par une revendication `kid`. L’en-tête de ce jeton d’ID contient également une revendication `kid`, qui indique quelle clé a été utilisée pour signer le jeton d’ID.

Pour vérifier les jetons Azure AD B2C, vous devez générer la clé publique à l’aide de l’exposant (e) et du modulo (n). Vous devez déterminer comment procéder en conséquence dans votre langage de programmation respectif. La documentation officielle sur la génération de la clé publique en utilisant le protocole RSA est disponible ici : https://tools.ietf.org/html/rfc3447#section-3.1

Après avoir validé la signature du jeton d’ID, vous devez vérifier plusieurs revendications. Exemple :

- Validez la revendication `nonce` afin d’empêcher les attaques par relecture de jetons. Sa valeur doit correspondre à ce que vous avez spécifié dans la requête de connexion.
- Validez la revendication `aud` pour vérifier que le jeton d’ID a été émis pour votre application. Sa valeur doit correspondre à l’ID d’application de votre application.
- Validez les revendications `iat` et `exp` pour vérifier que le jeton d’ID n’a pas expiré.

Vous devez également effectuer plusieurs autres validations. Ces validations sont décrites en détail dans les [Spécifications principales d’OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

- Vérifier que l’utilisateur/l’organisation s’est inscrit pour l’application.
- Vérifier que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
- S’assurer de l’utilisation d’une force certaine d’authentification, comme Azure Multi-Factor Authentication.

Une fois que vous avez validé le jeton d’ID, vous pouvez commencer une session avec l’utilisateur. Vous pouvez utiliser les revendications figurant dans le jeton d’ID pour obtenir des informations sur l’utilisateur dans votre application. Les utilisations de ces informations sont notamment l’affichage, les enregistrements et les autorisations.

## <a name="get-a-token"></a>Obtention d’un jeton

Si votre application web doit seulement exécuter des flux utilisateur, vous pouvez ignorer les quelques sections suivantes. Ces sections s’appliquent seulement aux applications web qui doivent effectuer des appels authentifiés à une API web et qui sont également protégées par Azure AD B2C.

Vous pouvez échanger le code d’autorisation que vous avez acquis (en utilisant `response_type=code+id_token`) contre un jeton sur la ressource souhaitée en envoyant une demande `POST` au point de terminaison `/token`. Dans Azure AD B2C, vous pouvez [demander des jetons d’accès pour les autres API](active-directory-b2c-access-tokens.md#request-a-token) comme d’habitude en spécifiant leurs étendues dans la demande.

Vous pouvez également demander un jeton d’accès pour l’API web de back-end de votre application par convention visant à utiliser l’ID client de l’application comme étendue demandée (ce qui génère un jeton d’accès avec cet ID client comme « public ») :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paramètre | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_id | OUI | ID d’application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| client_secret | OUI | Secret d'application qui a été généré dans le [portail Azure](https://portal.azure.com/). Ce secret d’application est un artefact de sécurité important. Vous devez le stocker sur votre serveur de manière sécurisée. Renouvelez régulièrement ce secret client. |
| code | OUI | Code d’autorisation que vous avez acquis au début du flux utilisateur. |
| grant_type | OUI | Le type d’octroi, qui doit être `authorization_code` pour le flux de code d’autorisation. |
| p | OUI | Le flux utilisateur qui a été utilisé pour obtenir le code d’autorisation. Vous ne pouvez pas utiliser un autre flux utilisateur dans cette demande. Ajoutez ce paramètre à la chaîne de requête, et non pas au corps POST. |
| redirect_uri | OUI | Le paramètre `redirect_uri` de l’application où vous avez reçu le code d’autorisation. |
| scope | Non | Une liste d’étendues séparées par des espaces. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de paramètres id_token. Elle peut être utilisée afin d’obtenir des jetons pour l’API web de back-end de votre application, qui est représentée par le même ID d’application que le client. L’étendue `offline_access` indique que votre application a besoin d’un jeton d’actualisation pour l’accès étendu aux ressources. |

Un jeton de réponse de réussite se présente ainsi :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| --------- | ----------- |
| not_before | Heure à laquelle le jeton est considéré comme valide, en heure epoch. |
| token_type | Valeur du type de jeton. `Bearer` est le seul type pris en charge. |
| access_token | Le jeton JWT signé que vous avez demandé. |
| scope | Étendues pour lesquelles le jeton est valide. |
| expires_in | Durée de validité du jeton d’accès (en secondes). |
| refresh_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel. Les jetons d’actualisation peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. L’étendue `offline_access` doit avoir été utilisée dans les demandes d’autorisation et de jeton pour recevoir un jeton d’actualisation. |

Les réponses d’erreur se présentent comme ceci :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| --------- | ----------- |
| error | Code pouvant être utilisé pour classer les types d’erreurs qui se produisent. |
| error_description | Message qui peut aider à identifier la cause racine d’une erreur d’authentification. |

## <a name="use-the-token"></a>Utilisation du jeton

Un jeton d’accès étant acquis, vous pouvez maintenant l’utiliser dans les demandes effectuées à vos API web principales en l’incluant dans l’en-tête `Authorization` :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Actualisation du jeton

Les jetons d’ID expirent après un court délai. Actualisez les jetons après leur expiration pour continuer à accéder aux ressources. Vous pouvez actualiser un jeton en envoyant une nouvelle demande `POST` au point de terminaison `/token`. Cette fois-ci, spécifiez le paramètre `refresh_token` au lieu du paramètre `code` :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paramètre | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_id | OUI | ID d’application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| client_secret | OUI | Secret d'application qui a été généré dans le [portail Azure](https://portal.azure.com/). Ce secret d’application est un artefact de sécurité important. Vous devez le stocker sur votre serveur de manière sécurisée. Renouvelez régulièrement ce secret client. |
| grant_type | OUI | Type d’octroi, qui doit être un jeton d’actualisation pour cette partie du flux de code d’autorisation. |
| refresh_token | OUI | Jeton d’actualisation d’origine qui a été acquis dans la seconde partie du flux. L’étendue `offline_access` doit être utilisée dans les demandes d’autorisation et de jeton pour recevoir un jeton d’actualisation. |
| p | OUI | Flux utilisateur utilisé pour obtenir le jeton d’actualisation d’origine. Vous ne pouvez pas utiliser un autre flux utilisateur dans cette demande. Ajoutez ce paramètre à la chaîne de requête, et non pas au corps POST. |
| redirect_uri | Non | Le paramètre `redirect_uri` de l’application où vous avez reçu le code d’autorisation. |
| scope | Non | Une liste d’étendues séparées par des espaces. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de jetons d’ID. Elle peut être utilisée afin d’envoyer des jetons à l’API web de back-end de votre application, qui est représentée par le même ID d’application que le client. L’étendue `offline_access` indique que votre application a besoin d’un jeton d’actualisation pour l’accès étendu aux ressources. |

Un jeton de réponse de réussite se présente ainsi :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| --------- | ----------- |
| not_before | Heure à laquelle le jeton est considéré comme valide, en heure epoch. |
| token_type | Valeur du type de jeton. `Bearer` est le seul type pris en charge. |
| access_token | Jeton JWT signé qui a été demandé. |
| scope | Étendue pour laquelle le jeton est valide. |
| expires_in | Durée de validité du jeton d’accès (en secondes). |
| refresh_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel. Les jetons d’actualisation peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. |

Les réponses d’erreur se présentent comme ceci :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| --------- | ----------- |
| error | Code pouvant être utilisé pour classer les types d’erreurs qui se produisent. |
| error_description | Message qui peut aider à identifier la cause racine d’une erreur d’authentification. |

## <a name="send-a-sign-out-request"></a>Envoi d’une demande de déconnexion

Quand vous souhaitez déconnecter l’utilisateur de l’application, il ne suffit pas de supprimer les cookies de l’application ou d’arrêter la session de l’utilisateur. Redirigez l’utilisateur vers Azure AD B2C pour le déconnecter. Si vous n’y parvenez pas, l’utilisateur peut être en mesure de se réauthentifier auprès de votre application, sans entrer à nouveau ses informations d’identification.

Vous pouvez simplement rediriger l’utilisateur vers le point de terminaison `end_session` qui est répertorié dans le document de métadonnées OpenID Connect précédemment décrit :

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paramètre | Obligatoire | Description |
| --------- | -------- | ----------- |
| p | OUI | Flux utilisateur que vous voulez utiliser pour déconnecter l’utilisateur de votre application. |
| post_logout_redirect_uri | Non | URL vers laquelle l’utilisateur doit être redirigé après la déconnexion. Si elle n’est pas incluse, Azure AD B2C affiche un message générique à l’utilisateur. |

La redirection de l’utilisateur vers le point de terminaison `end_session` efface une partie de l’état d’authentification unique de l’utilisateur auprès d’Azure AD B2C, mais elle ne déconnecte pas l’utilisateur de sa session auprès du fournisseur d’identité sociale. Si l’utilisateur sélectionne le même IDP lors d’une connexion ultérieure, il est réauthentifié sans entrer ses informations d’identification. Si un utilisateur veut se déconnecter de l’application, cela ne signifie pas nécessairement qu’il souhaite se déconnecter de son compte Facebook. Toutefois, si les comptes locaux sont utilisés, la session de l’utilisateur se termine correctement.

