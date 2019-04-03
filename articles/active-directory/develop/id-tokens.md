---
title: Référence de jeton d’ID Azure Active Directory | Microsoft Docs
description: Découvrez comment utiliser des jetons d’ID émis par les terminaux v1.0 et v2.0 d’Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms:custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: b00dd8de47422cf849d97e66698be3300b96ff83
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884548"
---
# <a name="id-tokens"></a>Jetons d’ID

`id_tokens` sont envoyés à l’application cliente en tant que partie d’un [OpenID Connect](v1-protocols-openid-connect-code.md) flux. Ils peuvent être envoyés avec ou à la place un jeton d’accès et sont utilisés par le client pour authentifier l’utilisateur.

## <a name="using-the-idtoken"></a>Utilisation du jeton id_token

Les jetons d’ID doivent servir à confirmer qu’un utilisateur est bien celui qu’il prétend être et à obtenir des informations supplémentaires le concernant. Ils ne doivent pas être utilisés pour accorder une autorisation à la place d’un [jeton d’accès](access-tokens.md). Les revendications obtenues peuvent être utilisées pour l’expérience utilisateur au sein de votre application, saisir des informations dans une base de donnée et fournir l’accès à l’application cliente.

## <a name="claims-in-an-idtoken"></a>Revendications dans un jeton id_token

`id_tokens` pour Microsoft identity sont [Jwt](https://tools.ietf.org/html/rfc7519), ce qui signifie qu’il s’agit d’une partie de l’en-tête, de charge utile et de signature. Vous pouvez utiliser l’en-tête et la signature pour vérifier l’authenticité du jeton, tandis que la charge utile contient les informations concernant l’utilisateur qui sont demandées par votre client. Sauf indication contraire, toutes les revendications répertoriées ici apparaissent dans les jetons v1.0 et v2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Afficher cet exemple de jeton v1.0 dans [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Afficher cet exemple de jeton v2.0 dans [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Revendications de l’en-tête

|Revendication | Format | Description |
|-----|--------|-------------|
|`typ` | Chaîne : toujours « JWT » | Indique que le jeton est un JWT.|
|`alg` | Chaîne | Indique l’algorithme utilisé pour signer le jeton. Exemple : "RS256" |
|`kid` | Chaîne | Empreinte de la clé publique utilisée pour signer ce jeton. Émise dans les jetons `id_tokens` v1.0 et v2.0. |
|`x5t` | Chaîne | Identique (en utilisation et en valeur) à `kid`. Il s'agit toutefois d'une ancienne revendication émise uniquement dans les jetons `id_tokens` v1.0 à des fins de compatibilité. |

### <a name="payload-claims"></a>Revendications de la charge utile

|Revendication | Format | Description |
|-----|--------|-------------|
|`aud` |  Chaîne, URI ID d’application | Identifie le destinataire du jeton. Dans les jetons `id_tokens`, l'audience est l'ID attribué à votre application dans le portail Azure. Votre application doit valider cette valeur et rejeter le jeton si la valeur ne correspond pas. |
|`iss` |  Chaîne, URI STS | Identifie le service d’émission de jeton de sécurité (STS) qui construit et retourne le jeton, ainsi que le client Azure AD dans lequel l’utilisateur a été authentifié. Si le jeton a été émis par le terminal v2.0, l’URI se termine par `/v2.0`.  La partie GUID qui indique que l’utilisateur est un utilisateur consommateur d’un compte Microsoft est `9188040d-6c67-4c5b-b112-36a304b66dad`. Votre application doit utiliser la partie GUID de la revendication pour restreindre l’ensemble des clients qui peuvent se connecter à l’application, le cas échéant. |
|`iat` |  int, horodatage UNIX | « Issued At » (Délivré le) indique quand l'authentification de ce jeton a eu lieu.  |
|`idp`|Chaîne, généralement un URI STS | Enregistre le fournisseur d’identité qui a authentifié le sujet du jeton. Cette valeur est identique à la valeur de la revendication de l’émetteur sauf si le compte d’utilisateur n’est pas dans le même locataire que l’émetteur (invités par exemple). Si la revendication n’est pas présente, cela signifie que la valeur `iss` peut être utilisée à la place.  Pour les comptes personnels utilisés dans un contexte organisationnel (par exemple, un compte personnel invité dans un locataire Azure AD), la revendication `idp` peut être « live.com » ou un URI STS contenant le locataire de compte Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, horodatage UNIX | La revendication « nbf » (pas avant) indique le délai avant lequel le JWT ne doit PAS être accepté pour être traité.|
|`exp` |  int, horodatage UNIX | La revendication « exp » (délai d'expiration) indique le délai d'expiration à partir duquel le JWT ne doit PAS être accepté pour être traité.  Il est important de remarquer qu'une ressource peut également rejeter le jeton avant ce délai, par exemple lorsqu'un changement d'authentification est requis ou qu'une révocation de jeton a été détectée. |
| `c_hash`| Chaîne |Le hachage de code n’est inclus dans un jeton d’ID que si ce dernier est émis avec un code d’autorisation OAuth 2.0. Il peut servir à valider l’authenticité d’un code d’autorisation. Pour plus d’informations sur l’exécution de cette validation, consultez la [spécification OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) . |
|`at_hash`| Chaîne |Le hachage de jeton d’accès n’est inclus dans un jeton d’ID que si ce dernier est émis avec un jeton d’accès OAuth 2.0. Il peut servir à valider l’authenticité d’un jeton d’accès. Pour plus d’informations sur l’exécution de cette validation, consultez la [spécification OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) . |
|`aio` | Chaîne opaque | Revendication interne utilisée par Azure AD pour enregistrer des données afin de réutiliser les jetons. Cette valeur doit être ignorée.|
|`preferred_username` | Chaîne | Nom d’utilisateur principal qui représente l’utilisateur. Il peut s’agir d’une adresse e-mail, d’un numéro de téléphone ou d’un nom d’utilisateur générique sans format spécifié. Sa valeur est mutable et peut changer au fil du temps. Dans la mesure où elle est mutable, cette valeur ne doit pas utilisée pour prendre des décisions d’autorisation. L’étendue `profile` est requise afin de recevoir cette revendication.|
|`email` | Chaîne | La revendication `email` est présente par défaut pour les comptes invités qui disposent d’une adresse de messagerie.  Votre application peut demander la revendication de courrier électronique pour les utilisateurs gérés (ceux provenant du même locataire que la ressource) à l’aide de la `email` [revendication facultative](active-directory-optional-claims.md).  Sur le point de terminaison v2.0, votre application peut également demander l’étendue OpenID Connect `email` : vous n’avez pas besoin de demander la revendication facultative et l’étendue pour obtenir la revendication.  La revendication de courrier électronique prend uniquement en charge la messagerie adressable provenant des informations du profil de l’utilisateur. |
|`name` | Chaîne | La revendication `name` fournit une valeur explicite qui identifie le sujet du jeton. Il n’est pas certain que cette valeur soit unique. Elle est mutable et conçue pour être utilisée uniquement à des fins d’affichage. L’étendue `profile` est requise afin de recevoir cette revendication. |
|`nonce`| Chaîne | La valeur à usage unique correspond au paramètre inclus dans la requête /authorize d’origine au point de distribution d’émission. Si ce n’est pas le cas, votre application doit rejeter le jeton. |
|`oid` | Chaîne, GUID | Identificateur immuable pour un objet dans le système d’identité Microsoft, dans cet exemple, un compte d’utilisateur. Cet ID identifie de manière unique l’utilisateur entre les applications ; deux applications différentes se connectant au même utilisateur auront la même valeur dans la revendication `oid`. Microsoft Graph renverra cet ID en tant que propriété `id` pour un compte d’utilisateur donné. `oid` permettant à plusieurs applications de faire correspondre des utilisateurs, l’étendue `profile` est requise afin de recevoir cette revendication. Notez que si un utilisateur existe dans plusieurs locataires, l’utilisateur contient un ID d’objet différent dans chaque locataire. Ils sont considérés comme des comptes différents, même si l’utilisateur se connecte à chaque compte avec les mêmes informations d’identification. |
|`roles`| Tableau de chaînes | L’ensemble de rôles qui ont été affectées à l’utilisateur qui se connecte. |
|`rh` | Chaîne opaque |Revendication interne utilisée par Azure pour revalider des jetons. Cette valeur doit être ignorée. |
|`sub` | Chaîne, GUID | Principal sur lequel portent les assertions d’informations du jeton, comme l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réattribuée ou réutilisée. L’objet est un identificateur par paire ; il est spécifique à un ID d’application donné. Par conséquent, si un utilisateur se connecte à deux applications différentes à l’aide de deux ID clients différents, ces applications reçoivent deux valeurs différentes pour la revendication de l’objet. Ceci peut être souhaitable ou non en fonction de vos besoins d’architecture et de confidentialité. |
|`tid` | Chaîne, GUID | GUID représentant le client Azure AD d’où provient l’utilisateur. Pour les comptes professionnels et scolaires, le GUID correspond à l’ID de client immuable de l’organisation à laquelle appartient l’utilisateur. Pour les comptes personnels, la valeur est `9188040d-6c67-4c5b-b112-36a304b66dad`. L’étendue `profile` est requise afin de recevoir cette revendication. |
|`unique_name` | Chaîne | Fournit une valeur contrôlable de visu qui identifie le sujet du jeton. Il n’est pas certain que cette valeur soit unique au sein d’un client. Elle doit être utilisée uniquement à des fins d’affichage. Émise uniquement dans les jetons `id_tokens` v1.0. |
|`uti` | Chaîne opaque | Revendication interne utilisée par Azure pour revalider des jetons. Cette valeur doit être ignorée. |
|`ver` | Chaîne, 1.0 ou 2.0 | Indique la version du jeton id_token. |

## <a name="validating-an-idtoken"></a>Valider un jeton id_token

Le processus de validation d’un `id_token` est très similaire à la première étape de [validation d’un jeton d’accès](access-tokens.md#validating-tokens). Votre client doit confirmer que le bon émetteur a renvoyé le jeton et qu’il n’a pas été falsifié. Étant donné que les jetons `id_tokens` sont toujours au format JWT, de nombreuses bibliothèques servent à valider ces jetons. Nous vous recommandons d’en utiliser une plutôt que de le faire vous-même. 

Pour valider manuellement le jeton, consultez les étapes détaillées dans la section [Validation d’un jeton d’accès](access-tokens.md#validating-tokens). Après avoir validé la signature du jeton, les revendications suivantes doivent être validées dans le jeton id_token (cette opération peut également être effectuée par votre bibliothèque de validation de jetons) :

* Horodatages : les horodatages `iat`, `nbf` et `exp` doivent tous correspondre à un moment avant ou après l’heure actuelle, selon ce qui convient. 
* Audience : la revendication `aud` doit correspondre à l’ID de votre application.
* Valeur à usage unique : la revendication `nonce` dans la charge utile doit correspondre au paramètre de valeur à usage unique transmis au point de terminaison /authorize lors de la demande initiale.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [les jetons d’accès Azure AD](access-tokens.md)
