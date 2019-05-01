---
title: Passer un jeton d’accès par le biais d’une stratégie personnalisée à une application dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment passer à votre application, par le biais d’une stratégie personnalisée, un jeton d’accès pour les fournisseurs d’identité OAuth 2.0 en tant que revendication dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d752af1860d76e59df045907c11d16b4e1d20b0c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702947"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Passer un jeton d’accès par le biais d’une stratégie personnalisée à une application dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Une [stratégie personnalisée](active-directory-b2c-get-started-custom.md) dans Azure Active Directory (Azure AD) B2C permet aux utilisateurs d’une application de s’inscrire ou de se connecter à un fournisseur d’identité. Quand cela se produit, Azure AD B2C reçoit un [jeton d’accès](active-directory-b2c-reference-tokens.md) du fournisseur d’identité. Azure AD B2C utilise ce jeton pour récupérer des informations sur l’utilisateur. Vous ajoutez un type de revendication et une revendication de sortie à votre stratégie personnalisée pour passer le jeton aux applications que vous inscrivez dans Azure AD B2C. 

Azure AD B2C prend en charge la transmission du jeton d'accès des fournisseurs d'identité [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) et [OpenID Connect](active-directory-b2c-reference-oidc.md). Pour tous les autres fournisseurs d’identité, la revendication est retournée vide.

## <a name="prerequisites"></a>Conditions préalables

- Votre stratégie personnalisée est configurée avec un fournisseur d’identité OAuth 2.0 ou Open ID Connect.

## <a name="add-the-claim-elements"></a>Ajouter les éléments de la revendication 

1. Ouvrez votre fichier *TrustframeworkExtensions.xml* et ajoutez l’élément **ClaimType** avec l’identificateur `identityProviderAccessToken` à l’élément **ClaimsSchema** :

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Ajoutez l’élément **OutputClaim** à l’élément **TechnicalProfile** pour chaque fournisseur d’identité OAuth 2.0 dont vous souhaitez avoir le jeton d’accès. L’exemple suivant montre l’élément ajouté au profil technique de Facebook :

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Enregistrez le fichier *TrustframeworkExtensions.xml*.
4. Ouvrez le fichier de stratégie de votre partie de confiance, par exemple *SignUpOrSignIn.xml*, puis ajoutez l’élément **OutputClaim** à **TechnicalProfile** :

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Enregistrez le fichier de stratégie.

## <a name="test-your-policy"></a>Tester votre stratégie

Quand vous testez vos applications dans Azure AD B2C, il peut être utile de retourner le jeton Azure AD B2C à `https://jwt.ms` pour passer en revue les revendications qu’il contient.

### <a name="upload-the-files"></a>Téléchargement des fichiers

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Infrastructure d’expérience d’identité**.
5. Dans la page Stratégies personnalisées, cliquez sur **Charger une stratégie**.
6. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustframeworkExtensions.xml*.
7. Cliquez sur **Télécharger**.
8. Répétez les étapes 5 à 7 pour le fichier de la partie de confiance (par exemple, *SignUpOrSignIn.xml*).

### <a name="run-the-policy"></a>Exécuter la stratégie

1. Ouvrez la stratégie que vous avez changée. Par exemple, *B2C_1A_signup_signin*.
2. Pour **Application**, sélectionnez l’application que vous avez précédemment inscrite. Pour voir le jeton dans l’exemple ci-dessous, l’**URL de réponse** doit indiquer `https://jwt.ms`.
3. Cliquez sur **Exécuter maintenant**.

    Vous devriez voir quelque chose de similaire à l’exemple suivant :

    ![Jeton décodé](./media/idp-pass-through-custom/idp-pass-through-custom-token.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les jetons dans les [informations de référence sur les jetons Azure Active Directory](active-directory-b2c-reference-tokens.md).





