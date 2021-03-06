---
title: Configurer le comportement de session en utilisant des politiques personnalisées – Azure Active Directory B2C | Microsoft Docs
description: Configurez la modification du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2f20a4521efe2806c4bc66e4612b99caf84382a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385261"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la modification du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C

La gestion de [session d’authentification unique (SSO)](session-overview.md) dans Azure Active Directory B2C (Azure AD B2C) permet à un administrateur de contrôler l’interaction avec un utilisateur une fois celui-ci authentifié. Par exemple, l’administrateur peut contrôler si la sélection des fournisseurs d’identité s’affiche ou si des détails de compte doivent être entrés à nouveau. Cet article décrit comment configurer les paramètres d’authentification unique pour Azure AD B2C.

## <a name="session-behavior-properties"></a>Propriétés de comportement de session

Vous pouvez utiliser les propriétés suivantes pour gérer les sessions d’application web :

- **Durée de vie de session d’application web (minutes)** : la durée de vie du cookie de session Azure AD B2C stocké dans le navigateur de l’utilisateur après une authentification réussie.
    - Valeur par défaut = 86400 secondes (1440 minutes).
    - Minimum (inclusif) = 900 secondes (15 minutes).
    - Maximum (inclusif) = 86400 secondes (1440 minutes).
- **Délai d’expiration de session d’application web** : le [type d’expiration de session](session-overview.md#session-expiry-type), *Rolling* (Propagé) ou *Absolute* (Absolu). 
- **Configuration de l’authentification unique** : l’[étendue de session](session-overview.md#session-scope) du comportement de l’authentification unique (SSO) sur plusieurs applications et flux d’utilisateurs dans votre client Azure AD B2C. 

## <a name="configure-the-properties"></a>Configurer les propriétés

Pour changer le comportement des sessions et les configurations de SSO, vous devez ajouter un élément **UserJourneyBehaviors** à l’intérieur de l’élément [RelyingParty](relyingparty.md).  L’élément **UserJourneyBehaviors** doit suivre immédiatement l’élément **DefaultUserJourney**. Votre élément **UserJourneyBehaviors** devrait ressembler à l’exemple suivant :

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="single-sign-out"></a>Déconnexion unique

### <a name="configure-the-applications"></a>Configurez les applications

Lorsque vous redirigez l’utilisateur vers le point de terminaison de déconnexion Azure AD B2C (pour les protocoles OAuth2 et SAML), Azure AD B2C efface la session de l’utilisateur du navigateur.  Pour autoriser la [déconnexion unique](session-overview.md#single-sign-out), définissez le `LogoutUrl` de l’application à partir du portail Azure :

1. Accédez au [portail Azure](https://portal.azure.com).
1. Sélectionnez votre client Azure AD B2C en cliquant sur votre compte en haut à droite de la page.
1. Dans le menu de gauche, choisissez **Azure AD B2C**, **Inscriptions d’applications**, puis sélectionnez votre application.
1. Sélectionnez **Paramètres**, **Propriétés**, puis recherchez la zone de texte **URL de déconnexion**. 

### <a name="configure-the-token-issuer"></a>Configurez le jeton émetteur 

Pour prendre en charge la déconnexion unique, les profils techniques de l’émetteur de jetons pour JWT et SAML doivent spécifier les éléments suivants :

- Le nom du protocole, tel que `<Protocol Name="OpenIdConnect" />`
- La référence au profil technique de session, tel que `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />`.

L’exemple suivant illustre les émetteurs de jetons JWT et SAML avec la fonctionnalité de déconnexion unique :

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur une [session Azure AD B2C](session-overview.md).
