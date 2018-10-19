---
title: Définir un profil technique OAuth2 dans une stratégie personnalisée dans Azure Active Directory B2C | Microsoft Docs
description: Définir un profil technique OAuth2 dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7f85de79b683ba7b10f5466c4a8042fc0ffdea90
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44382585"
---
# <a name="define-a-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique OAuth2 dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C prend en charge le fournisseur d’identité de protocole OAuth2. Il s’agit du principal protocole pour l’autorisation et l’authentification déléguée. Pour plus d’informations, voir la spécification [RFC 6749 The OAuth 2.0 Authorization Framework](http://tools.ietf.org/html/rfc6749). Avec un profil technique OAuth2, vous pouvez fédérer avec un fournisseur d’identité OAuth2, tel que Facebook ou Live.com, ce qui permet aux utilisateurs de se connecter avec leurs identités sociales ou d’entreprise existantes.

## <a name="protocol"></a>Protocole

L’attribut **Name** de l’élément **Protocol** doit être défini sur `OAuth2`. Par exemple, le protocole pour profil technique **Facebook-OAUTH** est `OAuth2` :

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...    
```

## <a name="input-claims"></a>Revendications d’entrée

Les éléments **InputClaims** et **InputClaimsTransformations** ne sont pas obligatoires. Vous pouvez cependant envoyer des paramètres supplémentaires à votre fournisseur d’identité. L’exemple suivant ajoute le paramètre de chaîne de requête **domain_hint** avec la valeur `contoso.com` à la demande d’autorisation.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Revendications de sortie

L’élément **OutputClaims** contient une liste de revendications retournée par le fournisseur d'identité OAuth2. Il se peut que vous deviez mapper le nom de la revendication définie dans votre stratégie au nom défini dans le fournisseur d'identité. Vous pouvez également inclure des revendications qui ne sont pas retournées par le fournisseur d’identité, pour autant que vous définissiez l’attribut `DefaultValue`.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

L’exemple suivant montre les revendications retournées par le fournisseur d’identité Facebook :

- Revendication **first_name** mappée à la revendication **givenName**.
- Revendication **last_name** mappée à la revendication **surname**.
- Revendication **displayName** sans mappage de nom.
- Revendication **email** sans mappage de nom.

Le profil technique retourne également des revendications qui ne sont pas retournées par le fournisseur d’identité : 

- La revendication **identityProvider** contenant le nom du fournisseur d’identité.
- La revendication **authenticationSource** avec la valeur par défaut **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_id | Oui | Identificateur d’application du fournisseur d’identité. |
| IdTokenAudience | Non  | Audience du jeton id_token. Si la valeur est spécifiée, Azure AD B2C vérifie si le jeton figure dans une revendication retournée par le fournisseur d’identité, et est identique à celui spécifié. |
| authorization_endpoint | Oui | URL du point de terminaison d’autorisation conformément à la norme RFC 6749. |
| AccessTokenEndpoint | Oui | URL du point de terminaison de jeton conformément à la norme RFC 6749. |  
| ClaimsEndpoint | Oui | URL du point de terminaison d’informations utilisateur conformément à la norme RFC 6749. | 
| AccessTokenResponseFormat | Non  | Format de l’appel de point de terminaison du jeton d’accès. Par exemple, Facebook nécessite une méthode HTTP GET, mais la réponse de jeton d’accès est au format JSON. |
| AdditionalRequestQueryParameters | Non  | Paramètres de requête de demande supplémentaire. Par exemple, vous pouvez envoyer des paramètres supplémentaires à votre fournisseur d’identité. Vous pouvez inclure plusieurs paramètres en utilisant un séparateur virgule. | 
| ClaimsEndpointAccessTokenName | Non  | Nom du paramètre de chaîne de requête du jeton accès. Les points de terminaison de revendications de certains fournisseurs d’identité prennent en charge les requêtes HTTP GET. Dans ce cas, le jeton du porteur est envoyé à l’aide d’un paramètre de chaîne de requête au lieu de l’en-tête d’autorisation. |
| ClaimsEndpointFormatName | Non  | Nom du paramètre de chaîne de requête de format. Par exemple, vous pouvez définir le nom en tant que `format` dans ce point de terminaison de revendications LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. | 
| ClaimsEndpointFormat | Non  | Valeur du paramètre de chaîne de requête de format. Par exemple, vous pouvez définir la valeur en tant que `json` dans ce point de terminaison de revendications LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. | 
| ProviderName | Non  | Nom du fournisseur d'identité. |
| response_mode | Non  | Méthode que le fournisseur d’identité utilise pour renvoyer le résultat à Azure AD B2C. Valeurs possibles : `query`, `form_post` (par défaut) ou `fragment`. |
| scope | Non  | Étendue de la demande d’accès définie conformément à la spécification de fournisseur d’identité OAuth2. Par exemple, `openid`, `profile` ou `email`. |
| HttpBinding | Non  | Liaison HTTP attendue aux points de terminaison de jeton d’accès et de jeton de revendications. Valeurs possibles : `GET` ou `POST`.  |
| ResponseErrorCodeParamName | Non  | Nom du paramètre contenant le message d’erreur retourné sur HTTP 200 (OK). |
| ExtraParamsInAccessTokenEndpointResponse | Non  | Contient les paramètres supplémentaires qui peuvent être retournés dans la réponse d’**AccessTokenEndpoint** par certains fournisseurs d’identité. Par exemple, la réponse d’**AccessTokenEndpoint** contient un paramètre supplémentaire tel que `openid`, qui est obligatoire, en plus du jeton d’accès dans une chaîne de requête de demande **ClaimsEndpoint**. S’il y a plusieurs noms de paramètre, ils doivent être échappés et séparés par le délimiteur virgule « , ». |
| ExtraParamsInClaimsEndpointRequest | Non  | Contient les paramètres supplémentaires qui peuvent être retournés dans la demande **ClaimsEndpoint** par certains fournisseurs d’identité. S’il y a plusieurs noms de paramètre, ils doivent être échappés et séparés par le délimiteur virgule « , ». |

## <a name="cryptographic-keys"></a>Clés de chiffrement

L’élément **CryptographicKeys** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_secret | Oui | Clé secrète client de l’application du fournisseur d’identité. La clé de chiffrement est requise uniquement si les métadonnées **response_types** sont définies sur `code`. Dans ce cas, Azure AD B2C émet un autre appel pour échanger le code d’autorisation pour un jeton d’accès. Si les métadonnées sont définies sur `id_token`, vous pouvez omettre la clé de chiffrement.  |  

## <a name="redirect-uri"></a>URI de redirection

Lorsque vous configurez l’URL de redirection de votre fournisseur d’identité, entrez `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Veillez à remplacer **{tenant}** par le nom de votre locataire (par exemple, contosob2c.onmicrosoft.com), et **{policyId}** par l’identificateur de votre stratégie (par exemple, b2c_1_policy). L’URI de redirection doit être en minuscules.

Si vous utilisez le domaine **b2clogin.com** à la place de **login.microsoftonline.com**, veillez à utiliser b2clogin.com au lieu de login.microsoftonline.com.

Exemples :

- [Ajouter Google+ en tant que fournisseur d’identités OAuth2 en utilisant des stratégies personnalisées](active-directory-b2c-custom-setup-goog-idp.md)













