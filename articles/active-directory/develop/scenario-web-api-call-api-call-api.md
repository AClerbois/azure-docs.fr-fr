---
title: API web appelant des API web (API d’appel) - plateforme d’identités Microsoft
description: Apprenez à créer une API web qui appelle des API web en aval (appel d’une API web).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074724"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>API web qui appelle des API web- Appeler une API

Une fois que vous avez un jeton, vous pouvez appeler une API web protégée. Cela est effectué depuis le contrôleur de votre API web ASP.NET/ASP.NET Core.

## <a name="controller-code"></a>Code de contrôle

Voici la continuation de l’exemple de code présenté dans [API web protégées appellent des API web - obtention d’un jeton](scenario-web-api-call-api-acquire-token.md), appelé dans les actions des contrôleurs d’API, appelant une API en aval (nommée todolist).

Une fois que vous avez acquis le jeton, utilisez-le comme un jeton du porteur pour appeler l’API en aval.

```CSharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-web-api-call-api-production.md)
