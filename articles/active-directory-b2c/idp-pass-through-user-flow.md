---
title: Passer un jeton d’accès par le biais d’un flux utilisateur à une application dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment passer un jeton d’accès pour les fournisseurs d’identité OAuth 2.0 en tant que revendication dans un flux utilisateur dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fd0f8241c07f603089b896dcfc9ece29f1e33d1c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428202"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Passer un jeton d’accès par le biais d’un flux utilisateur à une application dans Azure Active Directory B2C

> [!NOTE]
> Cette fonctionnalité est actuellement disponible en préversion publique.

Un [flux utilisateur](active-directory-b2c-reference-policies.md) dans Azure Active Directory (Azure AD) B2C permet aux utilisateurs d’une application de s’inscrire ou de se connecter à un fournisseur d’identité. Au départ, Azure AD B2C reçoit un [jeton d’accès](active-directory-b2c-reference-tokens.md) du fournisseur d’identité. Azure AD B2C utilise ce jeton pour récupérer des informations sur l’utilisateur. Vous activez une revendication dans votre flux utilisateur pour passer le jeton aux applications que vous inscrivez dans Azure AD B2C.

Azure AD B2C prend en charge la transmission du jeton d'accès des fournisseurs d'identité [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) et [OpenID Connect](active-directory-b2c-reference-oidc.md). Pour tous les autres fournisseurs d’identité, la revendication est retournée vide.

## <a name="prerequisites"></a>Prérequis

- Votre application doit utiliser un [flux utilisateur v2](user-flow-versions.md).
- Votre flux utilisateur est configuré avec un fournisseur d’identité OAuth 2.0 ou Open ID Connect.

## <a name="enable-the-claim"></a>Activer la revendication

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Sélectionnez **Flux d’utilisateur**, puis votre flux utilisateur. Par exemple, **B2C_1_SignupSignIn**.
5. Cliquez sur **Revendications de l’application**.
6. Activez **Jeton d’accès du fournisseur d’identité**.

    ![Revendication d’application](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Cliquez sur **Enregistrer** pour enregistrer le flux utilisateur.

## <a name="test-the-user-flow"></a>Tester le flux utilisateur

Quand vous testez vos applications dans Azure AD B2C, il peut être utile de retourner le jeton Azure AD B2C à `https://jwt.ms` pour passer en revue les revendications qu’il contient.

1. Dans la page Vue d’ensemble du flux utilisateur, sélectionnez **Exécuter le flux d’utilisateur**.
2. Pour **Application**, sélectionnez l’application que vous avez précédemment inscrite. Pour voir le jeton dans l’exemple ci-dessous, l’**URL de réponse** doit indiquer `https://jwt.ms`.
3. Cliquez sur **Exécuter le flux d’utilisateur**, puis connectez-vous avec les informations d’identification de votre compte. Vous devez voir le jeton d’accès du fournisseur d’identité dans la revendication **idp_access_token**.

    Vous devriez voir quelque chose de similaire à l’exemple suivant :

    ![Jeton décodé](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les jetons dans les [informations de référence sur les jetons Azure Active Directory](active-directory-b2c-reference-tokens.md).




