---
title: Inscrire une application de service dans Azure Active Directory - API Azure pour FHIR
description: Cet article explique comment inscrire une application de service dans Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 19f730c1d8a0fc0f809e8e16016795e725d80b61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033723"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Inscrire une application cliente de service dans Azure Active Directory

Dans cet article, vous découvrez comment inscrire une application cliente de service dans Azure Active Directory. Les inscriptions d’applications clientes sont des représentations Azure Active Directory des applications qui peuvent être utilisées pour l’authentification et l’obtention de jetons. Un client de service est destiné à être utilisé par une application pour obtenir un jeton d’accès sans authentification interactive d’un utilisateur. Il disposera de certaines permissions d’application et utilisera un secret d’application (mot de passe) pour l’obtention de jetons d’accès.

Suivez les étapes ci-dessous pour créer un client de service.

## <a name="app-registrations-in-azure-portal"></a>Inscriptions d’applications dans le portail Azure

1. Dans le volet de navigation gauche du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.

2. Dans le panneau **Azure Active Directory**, cliquez sur **Inscriptions d’applications (préversion)** :

    ![Portail Azure. Nouvelle inscription d’application.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Cliquez sur **Nouvelle inscription**.

## <a name="service-client-application-details"></a>Informations sur l’application cliente de service

* Le client du service a besoin d’un nom d’affichage, et vous pouvez également fournir une URL de réponse, mais elle ne sera probablement pas utilisée.

    ![Portail Azure. Nouvelle inscription d’application cliente de service.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>Autorisations des API

Vous devez accorder des rôles d’application au client du service. 

1. Ouvrez les **autorisations de l’API** et sélectionnez votre [inscription d’application de ressource d’API FHIR](register-resource-azure-ad-client-app.md) :

    ![Portail Azure. Autorisations d’API du client de service](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Sélectionnez les rôles d’application parmi ceux définis sur l’application de ressource :

    ![Portail Azure. Autorisations d’application cliente de service](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Accordez les autorisations de l’application. Si vous ne disposez pas des autorisations nécessaires, contactez votre administrateur Active Directory Azure :

    ![Portail Azure. Consentement administrateur du client de service](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Secret d’application

Le client de service a besoin d’un secret (mot de passe), que vous utiliserez lors de l’obtention des jetons.

1. Cliquez sur **Certificats &amp; secrets**

2. Cliquez sur **Nouveau secret client**

    ![Portail Azure. Clé secrète client de service](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Indiquez une durée pour le secret.

4. Après avoir été généré, il sera uniquement affiché une fois dans le portail. Notez-le et stockez-le en toute sécurité.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à inscrire une application cliente de service dans Azure Active Directory. Ensuite, déployez une API FHIR dans Azure.
 
>[!div class="nextstepaction"]
>[Déployer le serveur FHIR open source](fhir-oss-powershell-quickstart.md)