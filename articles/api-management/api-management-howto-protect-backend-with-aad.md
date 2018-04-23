---
title: Protéger une API à l’aide d’OAuth 2.0 avec Azure Active Directory et Gestion des API | Microsoft Docs
description: Découvrez comment protéger un serveur principal d’API web avec Azure Active Directory et Gestion des API.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: f5662a4082487137dfd642cc3264a90f8ab19054
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Guide pratique pour protéger une API à l’aide d’OAuth 2.0 avec Azure Active Directory et Gestion des API

Ce guide montre comment configurer votre instance de Gestion des API pour protéger une API à l’aide du protocole OAuth 2.0 avec Azure Active Directory (Azure AD). 

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans cet article, vous devez avoir :
* Une instance Gestion des API
* Une API publiée qui utilise l’instance Gestion des API
* un locataire Azure AD ;

## <a name="overview"></a>Vue d'ensemble

Voici un petit aperçu des étapes :

1. Inscrire une application (backend-app) dans Azure AD pour représenter l’API.
2. Inscrire une autre application (client-app) dans Azure AD pour représenter une application cliente qui doit appeler l’API.
3. Dans Azure AD, accorder des autorisations pour permettre à client-app d’appeler backend-app.
4. Configurer la console de développeur pour utiliser l’autorisation utilisateur OAuth 2.0.
5. Ajouter une stratégie **validate-jwt** afin de valider le jeton OAuth pour chaque requête entrante.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Inscrire une application dans Azure AD pour représenter l’API

Pour protéger une API avec Azure AD, la première étape consiste à inscrire dans Azure AD une application qui représente l’API. 

1. Accédez à votre locataire Azure AD, puis accédez à **Inscriptions des applications**.

2. Sélectionnez **Nouvelle inscription d’application**. 

3. Spécifiez le nom de l’application. (Pour cet exemple, le nom est `backend-app`.)  

4. Choisissez **Application web/API** comme **Type d’application**. 

5. Pour **URL de connexion**, vous pouvez utiliser `https://localhost` comme espace réservé.

6. Sélectionnez **Créer**.

Une fois l’application créée, prenez note de l’**ID d’application** ; vous en aurez besoin lors d’une étape ultérieure. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Inscrire une autre application dans Azure AD pour représenter une application cliente

Chaque application cliente qui appelle l’API doit également être inscrite en tant qu’application dans Azure AD. Ici, l’exemple d’application cliente est la console de développeur dans le portail des développeurs de Gestion des API. Voici comment inscrire une autre application dans Azure AD pour représenter la console de développeur.

1. Sélectionnez **Nouvelle inscription d’application**. 

2. Spécifiez le nom de l’application. (Pour cet exemple, le nom est `client-app`.)

3. Choisissez **Application web/API** comme **Type d’application**.  

4. Pour **URL de connexion**, vous pouvez utiliser `https://localhost` comme espace réservé ou utiliser l’URL de connexion de votre instance Gestion des API. (Pour cet exemple, l’URL est `https://contoso5.portal.azure-api.net/signin`.)

5. Sélectionnez **Créer**.

Une fois l’application créée, prenez note de l’**ID d’application** ; vous en aurez besoin lors d’une étape ultérieure. 

Maintenant, créez un secret client pour cette application, que nous utiliserons lors d’une étape ultérieure.

1. Sélectionnez **Paramètres** et accédez à **Clés**.

2. Sous **Mots de passe**, fournissez une **Description de la clé**. Choisissez quand la clé doit expirer et sélectionnez **Enregistrer**.

Prenez note de la valeur de la clé. 

## <a name="grant-permissions-in-azure-ad"></a>Accorder des autorisations dans Azure AD

Maintenant que vous avez inscrit deux applications pour représenter l’API et la console de développeur, vous devez accorder des autorisations pour permettre à client-app d’appeler backend-app.  

1. Accédez à **Inscriptions des applications**. 

2. Sélectionnez `client-app` et accédez à **Paramètres**.

3. Sélectionnez **Autorisations requises** > **Ajouter**.

4. Sélectionnez **Sélectionner une API** et recherchez `backend-app`.

5. Sous **Autorisations déléguées**, cochez `Access backend-app`. 

6. Sélectionnez **Sélectionner**, puis sélectionnez **Terminé**. 

> [!NOTE]
> Si **Azure Active Directory** n’est pas listé sous les autorisations d’autres applications, sélectionnez **Ajouter** pour l’ajouter à partir de la liste.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Activer l’autorisation utilisateur OAuth 2.0 dans la console de développeur

À ce stade, vous avez créé vos applications dans Azure AD et vous avez accordé les autorisations appropriées pour que client-app puisse appeler backend-app. 

Dans cet exemple, la console de développeur est l’application cliente. Les étapes suivantes décrivent comment activer l’autorisation utilisateur OAuth 2.0 dans la console de développeur. 

1. Accédez à votre instance Gestion des API.

2. Sélectionnez **OAuth 2.0** > **Ajouter**.

3. Spécifiez un **Nom d’affichage** et une **Description**.

4. Pour **l’URL de la page d’inscription client**, entrez une valeur d’espace réservé telle que `http://localhost`. L’**URL de la page d’enregistrement client** pointe vers la page que les utilisateurs peuvent utiliser pour créer et configurer leurs propres comptes pour les fournisseurs OAuth 2.0 qui prennent en charge cette fonctionnalité. Dans cet exemple, les utilisateurs ne peuvent pas créer et configurer leurs propres comptes. Un espace réservé est donc utilisé à la place.

5. Sélectionnez **Code d’autorisation** comme **Types d’octroi d’autorisation**.

6. Spécifiez l’**URL de point de terminaison d’autorisation** et l’**URL de point de terminaison de jeton**. Récupérez ces valeurs à partir de la page **Points de terminaison** dans votre locataire Azure AD. Accédez à nouveau à la page **Inscriptions des applications** puis sélectionnez **Points de terminaison**.

7. Copiez la valeur de **Point de terminaison d’autorisation OAuth 2.0** et collez-la dans la zone de texte **URL de point de terminaison d’autorisation**.

8. Copiez la valeur de **Point de terminaison de jeton OAuth 2.0** et collez-la dans la zone de texte **URL de point de terminaison de jeton**. En plus de coller le point de terminaison de jeton, ajoutez un paramètre de corps nommé **ressource**. Comme valeur pour ce paramètre, utilisez l’**ID d’application** de l’application back-end.

9. Ensuite, spécifiez les informations d’identification du client. Il s’agit des informations d’identification de client-app.

10. Pour **ID client**, utilisez l’**ID d’application** de client-app.

11. Pour **Secret client**, utilisez la clé que vous avez créée pour client-app. 

12. Juste après le secret client figure la valeur **redirect_url** pour le type d’octroi du code d’autorisation. Notez cette URL.

13. Sélectionnez **Créer**.

14. Revenez à la page **Paramètres** de votre client-app.

15. Sélectionnez l’**URL de réponse** et collez la valeur **redirect_url** sur la première ligne. Dans cet exemple, vous avez remplacé `https://localhost` par l’URL sur la première ligne.  

Maintenant que vous avez configuré un serveur d’autorisation OAuth 2.0, la console de développeur peut obtenir des jetons d’accès à partir d’Azure AD. 

L’étape suivante consiste à activer l’autorisation utilisateur OAuth 2.0 pour votre API. Cela permet à la console de développeur de savoir qu’elle doit obtenir un jeton d’accès pour l’utilisateur avant d’effectuer des appels vers votre API.

1. Accédez à votre instance Gestion des API,puis accédez à **API**.

2. Sélectionnez l’API que vous souhaitez protéger. Dans cet exemple, vous utilisez `Echo API`.

3. Accédez à **Settings**.

4. Sous **Sécurité**, choisissez **OAuth 2.0** et sélectionnez le serveur OAuth 2.0 que vous avez configuré précédemment. 

5. Sélectionnez **Enregistrer**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Appel de l’API à partir du portail des développeurs

Maintenant que l’autorisation utilisateur OAuth 2.0 est activée sur `Echo API`, la console de développeur obtient un jeton d’accès pour le compte de l’utilisateur avant d’appeler l’API.

1. Accédez à l’opération de votre choix sous `Echo API` dans le portail des développeurs puis sélectionnez **Essayez-la**. Cela vous amène à la console de développeur.

2. Observez le nouvel élément dans la section **Autorisation** correspondant au serveur d’autorisation que vous venez d’ajouter.

3. Sélectionnez **Code d’autorisation** dans la liste déroulante d’autorisations. Vous êtes alors invité à vous connecter au locataire Azure AD. Si vous êtes déjà connecté avec ce compte, il est possible qu’aucune invite ne s’affiche.

4. Une fois la connexion établie, un en-tête `Authorization` est ajouté à la requête avec un jeton d’accès d’Azure AD. Voici un exemple de jeton (encodage Base64) :

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Sélectionnez **Envoyer**, et vous pouvez appeler l’API avec succès.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configuration d’une stratégie de validation JWT pour autoriser des demandes

À ce stade, lorsqu’un utilisateur tente d’effectuer un appel à partir de la console de développeur, l’utilisateur est invité à se connecter. La console de développeur obtient un jeton d’accès pour le compte de l’utilisateur.

Toutefois, que se passe-t-il si quelqu’un appelle votre API sans jeton ou avec un jeton non valide ? Par exemple, vous pouvez toujours appeler l’API même si vous supprimez l’en-tête`Authorization`. Cela est dû au fait que Gestion des API ne valide pas le jeton d’accès à ce stade. Elle transfert simplement l’en-tête `Authorization` à l’API principale.

Vous pouvez utiliser la stratégie [Validate JWT](api-management-access-restriction-policies.md#ValidateJWT) pour pré-autoriser les requêtes dans Gestion des API en validant les jetons d’accès de chaque requête entrante. Si une requête n’a pas de jeton valide, Gestion des API la bloque. Par exemple, vous pouvez ajouter la stratégie ci-dessous à la section de stratégie `<inbound>` de `Echo API`. Il vérifie la revendication d’audience dans un jeton d’accès et retourne un message d’erreur si le jeton n’est pas valide. Pour plus d’informations sur la façon de configurer des stratégies, consultez [Définir ou modifier des stratégies](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>Générer une application pour appeler l’API

Dans ce guide, vous avez utilisé la console de développeur dans Gestion des API comme exemple d’application cliente pour appeler le `Echo API` protégé par OAuth 2.0. Pour en savoir plus sur la façon de générer une application et d’implémenter OAuth 2.0, consultez [exemples de code Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur [Azure Active Directory et OAuth2.0](../active-directory/develop/active-directory-authentication-scenarios.md).
* Découvrez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur Gestion des API.
* Pour les autres méthodes permettant de sécuriser votre service principal, consultez [Authentification mutuelle des certificats](api-management-howto-mutual-certificates.md).

* [Création d’une instance du service Gestion des API](get-started-create-service-instance.md).

* [Gérer votre première API](import-and-publish.md).
