---
title: Autoriser des comptes de développeurs à l’aide d’OAuth 2.0 dans Gestion des API Azure | Microsoft Docs
description: Apprenez à autoriser les utilisateurs à l'aide d'OAuth 2.0 dans Gestion des API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 1b52c64db29e17a0c52eb63721be81844e856b64
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2018
ms.locfileid: "42141061"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Comment autoriser des comptes de développeurs à l'aide de OAuth 2.0 dans Gestion des API Azure

De nombreuses API prennent en charge [OAuth 2.0](http://oauth.net/2/) pour sécuriser les API et assurer que seuls les utilisateurs valides y ont accès et peuvent accéder uniquement aux ressources pour lesquelles ils y sont autorisés. Pour utiliser la console de développement interactive de Gestion des API Azure avec ces API, le service vous permet de configurer votre instance de service pour travailler avec votre API compatible OAuth 2.0.

## <a name="prerequisites"> </a>Prérequis

Ce guide explique comment configurer votre instance de service Gestion des API pour utiliser l'autorisation OAuth 2.0 dans des comptes de développeur, mais n'explique pas comment configurer un fournisseur OAuth 2.0. La configuration de chaque fournisseur OAuth 2.0 est différente, bien que la procédure soit similaire et que les informations requises pour configurer OAuth 2.0 dans votre instance de service Gestion des API soient identiques. Cette rubrique inclut des exemples d'utilisation de Azure Active Directory en tant que fournisseur OAuth 2.0.

> [!NOTE]
> Pour plus d’informations sur la configuration d’OAuth 2.0 à l’aide d’Azure Active Directory, consultez l’exemple [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet].

## <a name="step1"></a>Configuration du serveur d’autorisation OAuth 2.0 dans Gestion des API

> [!NOTE]
> Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d’une instance de service Gestion des API][Create an API Management service instance].

1. Cliquez sur l’onglet OAuth 2.0 dans le menu de gauche, puis cliquez sur **+Ajouter**.

    ![Menu OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Entrez un nom et une description facultative dans les champs **Nom** et **Description**.

    > [!NOTE]
    > Ces champs sont utilisés pour identifier le serveur d'autorisation OAuth 2.0 dans l'instance de service Gestion des API actuelle, et leurs valeurs ne proviennent pas du serveur OAuth 2.0.

3. Entrez l' **URL de la page d'enregistrement client**. Cette page est l'endroit où les utilisateurs créent et gèrent leurs comptes ; elle varie en fonction du fournisseur OAuth 2.0 utilisé. L’ **URL de la page d’enregistrement client** pointe vers la page que les utilisateurs peuvent utiliser pour créer et configurer leurs propres comptes pour les fournisseurs OAuth 2.0 qui prennent en charge la gestion des comptes utilisateur. Certaines organisations ne configurent pas cette fonctionnalité ou ne l’utilisent pas, même si le fournisseur OAuth 2.0 la prend en charge. Si la gestion des utilisateurs de comptes de votre fournisseur OAuth 2.0 n’a pas été configurée, entrez un espace réservé d’URL ici, par exemple l’URL de votre société ou une URL comme `https://placeholder.contoso.com`.

    ![Nouveau serveur OAuth 2.0](./media/api-management-howto-oauth2/oauth-02.png)

4. La section suivante du formulaire inclut les paramètres **Types d’accès accordé aux codes d’autorisation**, **URL de point de terminaison d’autorisation** et **Méthode de demande d’autorisation**.

    Spécifiez le paramètre **Types d'accès accordé aux codes d'autorisation** en sélectionnant les types souhaités. **code d'autorisation** est spécifié par défaut.

    Entrez l' **URL de point de terminaison d'autorisation**. Pour Azure Active Directory, cette URL est similaire à l’URL suivante, où `<client_id>` est remplacé par l’ID client qui identifie votre application sur le serveur OAuth 2.0.

    `https://login.microsoftonline.com/<client_id>/oauth2/authorize`

    La **Méthode de demande d’autorisation** spécifie comment la demande d'autorisation est envoyée au serveur OAuth 2.0. Par défaut, la méthode **GET** est sélectionnée.

5. Vous devez ensuite spécifier les paramètres **URL de point de terminaison de jeton**, **Méthodes d’authentification du client**, **Méthode d’envoi des jetons d’accès** et **Étendue par défaut**.

    ![Nouveau serveur OAuth 2.0](./media/api-management-howto-oauth2/oauth-03.png)

    Pour un serveur OAuth 2.0 Azure Active Directory, l’**URL de point de terminaison de jeton** a le format suivant, où `<APPID>` a le format `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<APPID>/oauth2/token`

    Le paramètre par défaut pour **Méthodes d’authentification du client** est **De base**, et le paramètre par défaut pour **Méthode d’envoi des jetons d’accès** est **En-tête d’autorisation**. Ces valeurs sont configurées dans cette section du formulaire, ainsi que le paramètre **Étendue par défaut**.

6. La section **Informations d’identification du client** inclut les paramètres **ID client** et **Clé secrète client**, qui sont obtenus lors du processus de création et de configuration de votre serveur OAuth 2.0. Une fois que les paramètres **ID client** et **Clé secrète client** ont été spécifiés, le **redirect_uri** pour le **code d’autorisation** est généré. Cette URI est utilisée pour configurer l'URL de réponse dans la configuration de votre serveur OAuth 2.0.

    ![Nouveau serveur OAuth 2.0](./media/api-management-howto-oauth2/oauth-04.png)

    Si le paramètre **Types d’accès accordés aux codes d’autorisation** est défini sur **Mot de passe du propriétaire des ressources**, la section **Informations d’identification du mot de passe du propriétaire des ressources** permet de spécifier ces informations d’identification ; sinon, vous pouvez la laisser vide.

    Une fois le formulaire complété, cliquez sur **Créer** pour enregistrer la configuration du serveur d’autorisation OAuth 2.0 de Gestion des API. Après l'enregistrement de la configuration du serveur, vous pouvez configurer les API pour utiliser cette configuration, tel qu'expliqué dans la section suivante.

## <a name="step2"></a>Configuration d’une API pour utiliser l’autorisation utilisateur OAuth 2.0

1. Cliquez sur **API** dans le menu **Gestion des API** à gauche.

    ![API OAuth 2.0](./media/api-management-howto-oauth2/oauth-05.png)

2. Cliquez sur le nom de l’API souhaitée, puis sur **Paramètres**. Faites défiler l’écran jusqu’à la section **Sécurité**, puis cochez la case **OAuth 2.0**.

    ![Paramètres OAuth 2.0](./media/api-management-howto-oauth2/oauth-06.png)

3. Sélectionnez le **Serveur d’autorisation** souhaité dans la liste déroulante, puis cliquez sur **Enregistrer**.

    ![Paramètres OAuth 2.0](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"></a>Tests de l’autorisation utilisateur OAuth 2.0 dans le portail de développement

Une fois que vous avez configuré votre serveur d'autorisation OAuth 2.0 et votre API pour utiliser ce serveur, vous pouvez le tester en accédant au portail de développement et en appelant une API.  Cliquez sur **Portail des développeurs** dans le menu supérieur de la page **Vue d’ensemble** de votre instance Gestion des API Azure.

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur et sélectionnez **API Echo**.

![API Echo][api-management-apis-echo-api]

> [!NOTE]
> Si vous n'avez qu'une API configurée ou visible dans votre compte, cliquez sur des API pour accéder directement aux opérations associées.

Sélectionnez l’opération **Ressource GET**, cliquez sur **Ouvrir la console**, puis sélectionnez **Code d’autorisation** dans la liste déroulante.

![Open console][api-management-open-console]

Lorsque **Code d'autorisation** est sélectionné, une fenêtre contextuelle s'affiche, avec le formulaire d'inscription du fournisseur OAuth 2.0. Dans cet exemple, le formulaire d'inscription est fourni par Azure Active Directory.

> [!NOTE]
> Si l'utilisation des fenêtres contextuelles est désactivée, vous êtes invité à l'activer par le navigateur. Après l'activation, sélectionnez à nouveau **Code d'autorisation** , et le formulaire d'inscription s'affiche.

![Se connecter][api-management-oauth2-signin]

Une fois que vous êtes connecté, les **En-têtes de demandes`Authorization : Bearer` sont renseignés avec un en-tête** qui autorise la demande.

![Jeton d'en-tête de demande][api-management-request-header-token]

À présent, vous pouvez configurer les valeurs souhaitées pour les paramètres restants, puis soumettre la demande.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’OAuth 2.0 et la gestion des API, regardez la vidéo suivante et l’article qui l’ [accompagne](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

