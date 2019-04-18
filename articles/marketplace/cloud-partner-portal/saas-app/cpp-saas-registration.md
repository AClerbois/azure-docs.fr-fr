---
title: Inscrire une application SaaS - place de marché Azure | Microsoft Docs
description: Explique comment inscrire une application SaaS à l’aide du portail Azure.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pbutlerm
ms.openlocfilehash: fbc542ea2ed76d99d551d668b00bad1fb3719a9f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012934"
---
# <a name="register-a-saas-application"></a>Inscrire une application SaaS

Cet article explique comment inscrire une application SaaS à l’aide de Microsoft [Azure portal](https://portal.azure.com/).  Après une inscription réussie, vous recevrez un jeton de sécurité Azure Active Directory (Azure AD) que vous pouvez utiliser pour accéder aux API de traitement des commandes de SaaS.  Pour plus d’informations sur Azure AD, consultez [qu’est l’authentification ?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Flux d’authentification de service à service

Le diagramme suivant montre le flux d’abonnement d’un nouveau client et le moment où ces API sont utilisées :

![Flux de l’API de l’offre SaaS](./media/saas-offer-publish-api-flow-v1.png)

Azure n’impose aucune contrainte sur l’authentification que le service SaaS expose à ses utilisateurs finaux. Toutefois, l’authentification avec les API de traitement des commandes SaaS est effectuée avec un jeton de sécurité Azure AD, généralement obtenu en enregistrant l’application SaaS via le portail Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Inscrire une application sécurisées par AD Azure

Toute application qui souhaite utiliser les fonctionnalités d’Azure AD doit d’abord être enregistrée dans un locataire Azure AD. Le processus d’enregistrement implique de fournir à Azure AD des informations sur votre application, notamment l’URL où elle est située, l’URL à laquelle envoyer une réponse après avoir authentifié un utilisateur, l’URI qui identifie l’application, et ainsi de suite.  Pour inscrire une nouvelle application à l’aide du portail Azure, procédez comme suit :

1.  Connectez-vous au [Portail Azure](https://portal.azure.com/).
2.  Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.
3.  Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis cliquez sur **Nouvelle inscription d’application**.

    ![Inscriptions des applications SaaS](./media/saas-offer-app-registration-v1.png)

4.  Sur la page, saisissez les informations d\'inscription de votre application :
    -   **Nom** : saisissez un nom d’application explicite
    -   **Type d’application** : 
        - Sélectionnez **Native** pour les [applications clientes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) installées localement sur un appareil. Ce paramètre est utilisé pour les [clients natifs](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) publics OAuth.
        - Sélectionnez **Application Web / API** pour les [applications clientes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) et les [ressources/applications API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) installées sur un serveur sécurisé. Ce paramètre est utilisé pour les [clients web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) confidentiels OAuth et les [clients basés sur un agent utilisateur](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client) publics.
        La même application peut également exposer un client et une ressource/API.
    -   **URL de connexion** : pour des applications web app/API, indiquez l’URL de base de votre application. Par exemple, **http://localhost:31544** peut être l’URL pour une application web en cours d’exécution sur votre ordinateur local. Les utilisateurs peuvent alors utiliser cette URL pour se connecter à une application web cliente.
    -   **URI de redirection** : pour des applications natives, indiquez l’URI utilisé par Azure AD pour retourner les réponses de jeton. Saisissez une valeur spécifique à votre application, par exemple **http://MyFirstAADApp**.

        ![Inscriptions des applications SaaS](./media/saas-offer-app-registration-v1-2.png)

        Pour obtenir des exemples spécifiques pour les applications web ou des applications natives, consultez le Guide de démarrage rapide guidée par les configurations qui sont disponibles dans le *prise en main* section de la [Guide du développeur Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Lorsque vous avez terminé, cliquez sur **Créer**. Azure AD affecte un unique *ID d’Application* à votre application et vous\'re dirigé vers votre application\'page d’inscription principale de s. Selon que votre application est une application native ou web, différentes options sont disponibles afin d’ajouter des fonctionnalités supplémentaires à votre application.

>[!Note]
>Par défaut, l’application nouvellement inscrite est configurée pour autoriser uniquement les utilisateurs à partir du même locataire à se connecter à votre application.


## <a name="using-the-azure-ad-security-token"></a>En utilisant le jeton de sécurité Azure AD

Une fois que vous avez inscrit votre application, vous pouvez demander par programmation un jeton de sécurité Azure AD.  Le serveur de publication doit utiliser ce jeton et effectuer une requête pour y remédier.  Lorsque vous utilisez les API de traitement des commandes différentes, le paramètre de requête de jeton est dans l’URL lorsque l’utilisateur est redirigé vers le site Web de SaaS à partir d’Azure.  Ce jeton est uniquement valid pendant une heure.  En outre, vous devez les URL de décoder la valeur du jeton à partir du navigateur avant de l’utiliser.

Pour plus d’informations sur ces jetons, consultez [jetons d’accès Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obtenir un jeton basé sur l’application Azure AD

Méthode HTTP

`GET`

*URL de requête*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*Paramètre URI*

|  **Nom du paramètre**  | **Obligatoire**  | **Description**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | ID de client de l’application AAD inscrite   |
|  |  |  |


*En-tête de requête*

|  **Nom de l’en-tête**  | **Obligatoire** |  **Description**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Type de contenu associé à la requête. La valeur par défaut est `application/x-www-form-urlencoded`.  |
|  |  |  |


*Corps de la demande*

| **Nom de la propriété**   | **Obligatoire** |  **Description**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Type d’autorisation. La valeur par défaut est `client_credentials`.                    |
|  Client_id          | True         |  Identificateur du client/de l’application associé à l’application Azure AD.                  |
|  client_secret      | True         |  Mot de passe associé à l’application Azure AD.                               |
|  Ressource           | True         |  Ressource cible pour laquelle le jeton est demandé. La valeur par défaut est `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Réponse*

|  **Nom**  | **Type**       |  **Description**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Requête réussie   |
|  |  |  |

*TokenResponse*

Voici un exemple de token de réponse :

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Étapes suivantes

Votre application sécurisées par AD Azure permettre désormais utiliser le [SaaS Fulfillment API Version 2](./cpp-saas-fulfillment-api-v2.md).
