---
title: Gérer la personnalisation des configurations SSO et de jetons avec des stratégies personnalisées dans Azure Active Directory B2C | Microsoft Docs
description: En savoir plus sur la gestion de la personnalisation des configurations SSO et de jetons avec des stratégies personnalisées dans Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c150c63bfdba70b256e639497c8daaf2a8cc7dbe
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843410"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Gérer la personnalisation des configurations SSO et de jetons avec des stratégies personnalisées dans Azure Active Directory B2C

Cet article explique comment gérer vos configurations de jetons, de session et d’authentification unique (SSO) à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md) dans Azure Active Directory (Azure AD) B2C.

## <a name="token-lifetimes-and-claims-configuration"></a>Configuration des revendications et de la durée de vie des jetons

Pour modifier les paramètres de durée de vie de vos jetons, vous devez ajouter un élément [ClaimsProviders](claimsproviders.md) dans le fichier de partie de confiance de la stratégie que vous souhaitez affecter.  L’élément **ClaimsProviders** est un enfant de l’élément [TrustFrameworkPolicy](trustframeworkpolicy.md). À l’intérieur, vous devez indiquer les informations qui modifient la durée de vie de votre jeton. Le code XML ressemble à cet exemple :

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

Les valeurs suivantes sont définies dans l’exemple précédent :

- **Durée de vie des jetons d’accès** : la durée de vie d’un jeton accès est définie avec l’élément de métadonnées **token_lifetime_secs**. La valeur par défaut est de 3600 secondes (60 minutes).
- **Durée de vie des jetons d’ID** : la durée de vie d’un jeton d’ID est définie avec l’élément de métadonnées **id_token_lifetime_secs**. La valeur par défaut est de 3600 secondes (60 minutes).
- **Durée de vie des jetons d’actualisation** : la durée de vie d’un jeton d’actualisation est définie avec l’élément de métadonnées **refresh_token_lifetime_secs**. La valeur par défaut est de 1209600 secondes (14 jours).
- **Durée de vie de la fenêtre glissante du jeton d’actualisation** : si vous souhaitez définir une durée de vie pour la fenêtre glissante de votre jeton d’actualisation, définissez la valeur de l’élément de métadonnées **rolling_refresh_token_lifetime_secs**. La valeur par défaut est de 7776000 jours (90 jours). Si vous ne souhaitez pas appliquer une durée de vie à la fenêtre glissante, remplacez l’élément par `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Revendication de l’émetteur (iss)** : la revendication de l’émetteur (iss) est définie avec l’élément de métadonnées **IssuanceClaimPattern**. Les valeurs possibles sont `AuthorityAndTenantGuid` et `AuthorityWithTfp`.
- **Paramétrage de l’ID de stratégie représentant la revendication** : les options permettant de définir cette valeur sont `TFP` (Trust Framework Policy) et `ACR` (Authentication Context Reference). `TFP` est la valeur recommandée. Définissez **AuthenticationContextReferenceClaimPattern** avec la valeur de `None`. Dans votre élément **OutputClaims**, ajoutez cet élément :
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Pour l’option ACR, supprimez l’élément **AuthenticationContextReferenceClaimPattern**.

- **Revendication de sujet (sub)** : cette option est définie par défaut sur ObjectID. Si vous souhaitez que cette option soit définie sur `Not Supported`, remplacez cette ligne : 

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```
    
    par cette ligne :
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Comportement de la session et SSO

Pour changer le comportement des sessions et les configurations de SSO, vous devez ajouter un élément **UserJourneyBehaviors** à l’intérieur de l’élément [RelyingParty](relyingparty.md).  L’élément **UserJourneyBehaviors** doit suivre immédiatement l’élément **DefaultUserJourney**. L’intérieur de votre élément **UserJourneyBehaviors** devrait ressembler à l’exemple suivant :

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Les valeurs suivantes sont configurées dans l’exemple précédent :

- **Authentification unique (SSO)** : l’authentification unique est configurée avec l’élément **SingleSignOn**. Les valeurs possibles sont `Tenant`, `Application`, `Policy` et `Suppressed`. 
- **Durée de vie de la session de l’application web (minutes)** : la durée de vie de la session de l’application web est définie avec l’élément **SessionExpiryInSeconds**. La valeur par défaut est de 86400 secondes (1440 minutes).
- **Délai d’expiration de la session de l’application web** : il est défini avec l’élément **SessionExpiryType**. Les valeurs possibles sont `Absolute` et `Rolling`.
