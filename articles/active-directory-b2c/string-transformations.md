---
title: Exemples de transformations de revendications de chaînes pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C | Microsoft Docs
description: Exemples de transformations de revendications de chaînes pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83379cc194f23ebff977babc7124a7bc90f4bc60
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063457"
---
# <a name="string-claims-transformations"></a>Transformations de revendications de chaînes

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications de chaîne du schéma Identity Experience Framework dans Azure Active Directory B2C (Azure AD B2C). Pour plus d’informations, voir [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Compare deux revendications et lève une exception si elles ne sont pas égales en fonction des éléments de comparaison inputClaim1, inputClaim2 et stringComparison spécifiés.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Type de la première revendication qui doit être comparée. |
| inputClaim | inputClaim2 | string | Type de la deuxième revendication qui doit être comparée. |
| InputParameter | stringComparison | string | comparaison de chaînes, une des valeurs suivantes : Ordinal, OrdinalIgnoreCase. |

La transformation de revendication **AssertStringClaimsAreEqual** est toujours exécutée à partir d’un [profil technique de validation](validation-technical-profile.md) appelé par un [profil technique autodéclaré](self-asserted-technical-profile.md). Les métadonnées de profil technique autodéclaré **UserMessageIfClaimsTransformationStringsAreNotEqual** contrôlent le message d’erreur présenté à l’utilisateur.

![Exécution d’AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Vous pouvez utiliser cette transformation de revendication pour vérifier que deux ClaimTypes ont la même valeur. Si ce n’est pas le cas, un message d’erreur est levé. L’exemple suivant vérifie que le ClaimType **strongAuthenticationEmailAddress** est égal au ClaimType **email**. Si ce n’est pas le cas, un message d’erreur est levé.

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


Le profil technique de validation **login-NonInteractive** appelle la transformation de revendication **AssertEmailAndStrongAuthenticationEmailAddressAreEqual**.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Le profil technique autodéclaré appelle le profil technique de validation **login-NonInteractive**.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
  - **inputClaim1** : someone@contoso.com
  - **inputClaim2** : someone@outlook.com
    - Paramètres d’entrée :
  - **stringComparison** : ordinalIgnoreCase
- Résultat : Erreur levée

## <a name="changecase"></a>ChangeCase

Modifie la casse de la revendication fournie (minuscules ou majuscules) en fonction de l’opérateur.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | ClaimType à modifier. |
| InputParameter | toCase | string | L’une des valeurs suivantes : `LOWER` ou `UPPER`. |
| OutputClaim | outputClaim | string | ClaimType généré après que cette transformation de revendication a été appelée. |

Utilisez cette transformation de revendication pour mettre un ClaimType en majuscules ou en minuscules.

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
  - **email** : SomeOne@contoso.com
- Paramètres d’entrée :
    - **toCase** : LOWER
- Revendications de sortie :
  - **email** : someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Crée une revendication de chaîne à partir du paramètre d’entrée fourni dans la stratégie.

| Item | TransformationClaimType | Type de données | Notes |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | string | Chaîne à définir |
| OutputClaim | createdClaim | string | ClaimType généré après que cette transformation de revendication a été appelée, avec la valeur spécifiée dans le paramètre d’entrée. |

Utilisez cette transformation de revendication pour définir une chaîne de valeur ClaimType.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Paramètre d’entrée :
    - **value** : Contoso terms of service...
- Revendications de sortie :
    - **createdClaim** : le ClaimType TOS contient la valeur « Contoso terms of service... ».

## <a name="compareclaims"></a>CompareClaims

Détermine si une revendication de chaîne est égale à une autre. Le résultat est un nouveau ClaimType booléen avec la valeur `true` ou `false`.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Premier type de revendication à comparer. |
| inputClaim | inputClaim2 | string | Deuxième type de revendication à comparer. |
| InputParameter | operator | string | Valeurs possibles : `EQUAL` ou `NOT EQUAL`. |
| InputParameter | ignoreCase | boolean | Spécifie si cette comparaison doit ignorer la casse des chaînes comparées. |
| OutputClaim | outputClaim | boolean | ClaimType généré après que cette transformation de revendication a été appelée. |

Utilisez cette transformation de revendication pour vérifier si une revendication est égale à une autre. Par exemple, la transformation de revendication suivante vérifie si la valeur de la revendication **email** est égale à la revendication **Verified.Email**.

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
  - **inputClaim1** : someone@contoso.com
  - **inputClaim2** : someone@outlook.com
- Paramètres d’entrée :
    - **operator** :  NON ÉGAL À
    - **ignoreCase** : true
- Revendications de sortie :
    - **outputClaim** : true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Détermine si une valeur de revendication est égale à la valeur du paramètre d’entrée.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Type de la revendication à comparer. |
| InputParameter | operator | string | Valeurs possibles : `EQUAL` ou `NOT EQUAL`. |
| InputParameter | compareTo | string | comparaison de chaînes, une des valeurs suivantes : Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Spécifie si cette comparaison doit ignorer la casse des chaînes comparées. |
| OutputClaim | outputClaim | boolean | ClaimType généré après que cette transformation de revendication a été appelée. |

Vous pouvez utiliser cette transformation de revendication pour vérifier si une revendication est égale à une valeur que vous avez spécifiée. Par exemple, la transformation de revendication suivante vérifie si la valeur de la revendication **termsOfUseConsentVersion** est égale à `v1`.

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples
- Revendications d’entrée :
    - **inputClaim1** : v1
- Paramètres d’entrée :
    - **compareTo** : V1
    - **operator** : EQUAL
    - **ignoreCase** : true
- Revendications de sortie :
    - **outputClaim** : true

## <a name="createrandomstring"></a>CreateRandomString

Crée une chaîne aléatoire à l’aide du générateur de nombres aléatoires. Si le générateur de nombres aléatoires est de type `integer`, vous pouvez éventuellement spécifier un paramètre de départ et un nombre maximal. Un paramètre de format de chaîne facultatif permet de mettre en forme la sortie, et un paramètre base64 facultatif spécifie si la sortie est outputClaim (string) randomGeneratorType [guid, entier] encodée en base64.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | string | Spécifie la valeur aléatoire à générer, `GUID` (ID global unique) ou `INTEGER` (nombre). |
| InputParameter | stringFormat | string | [Facultatif] Mettre en forme la valeur aléatoire. |
| InputParameter | base64 | boolean | [Facultatif] Convertir la valeur aléatoire en base64. Si la mise en forme de la chaîne est appliquée, la valeur après la mise en forme de la chaîne est encodée en base64. |
| InputParameter | maximumNumber | int | [Facultatif] Pour randomGeneratorType `INTEGER` uniquement. Spécifiez le nombre maximal. |
| InputParameter | seed  | int | [Facultatif] Pour randomGeneratorType `INTEGER` uniquement. Spécifiez la valeur de départ pour la valeur aléatoire. Remarque : la même valeur de départ génère la même séquence de nombres aléatoires. |
| OutputClaim | outputClaim | string | ClaimType généré après que cette transformation de revendication a été appelée. Valeur aléatoire. |

L’exemple suivant génère un ID unique global. Cette transformation de revendication permet de créer l’UPN (nom d’utilisateur principal) aléatoire.

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemples

- Paramètres d’entrée :
    - **randomGeneratorType** : GUID
- Revendications de sortie :
    - **outputClaim** : bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

L’exemple suivant génère une valeur entière aléatoire comprise entre 0 et 1000. La valeur est mise en forme au format OTP_ {valeur aléatoire}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Paramètres d’entrée :
    - **randomGeneratorType** : INTEGER
    - **maximumNumber** : 1 000
    - **stringFormat** : OTP_{0}
    - **base64** : false
- Revendications de sortie :
    - **outputClaim** : OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Met en forme une revendication en fonction de la chaîne de format fournie. Cette transformation utilise la méthode C# `String.Format`.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string |ClaimType qui agit en tant que paramètre {0} de format de chaîne. |
| InputParameter | stringFormat | string | Format de chaîne, y compris le paramètre {0}. |
| OutputClaim | outputClaim | string | ClaimType généré après que cette transformation de revendication a été appelée. |

Utilisez cette transformation de revendication pour mettre en forme une chaîne avec un paramètre {0}. L’exemple suivant crée un **userPrincipalName**. Tous les profils techniques de fournisseurs d’identité sociale, tels que `Facebook-OAUTH` appellent **CreateUserPrincipalName** pour générer un **userPrincipalName**.

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputClaim** : 5164db16-3eee-4629-bfda-dcc3326790e9
- Paramètres d’entrée :
    - **stringFormat** :  cpim_{0}@{RelyingPartyTenantId}
- Revendications de sortie :
  - **outputClaim** : cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Met en forme deux revendications en fonction de la chaîne de format fournie. Cette transformation utilise la méthode C# **String.Format**.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string | ClaimType qui agit en tant que paramètre {0} de format de chaîne. |
| InputClaim | inputClaim | string | ClaimType qui agit en tant que paramètre {1} de format de chaîne. |
| InputParameter | stringFormat | string | Format de chaîne, y compris les paramètres {0} et {1}. |
| OutputClaim | outputClaim | string | ClaimType généré après que cette transformation de revendication a été appelée. |

Utilisez cette transformation de revendication pour mettre en forme une chaîne avec deux paramètres, {0} et {1}. L’exemple suivant crée un **displayName** au format spécifié :

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputClaim1** : Joe
    - **inputClaim2** : Fernando
- Paramètres d’entrée :
    - **stringFormat** : {0} {1}
- Revendications de sortie :
    - **outputClaim** : Joe Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Recherche un élément dans une collection de revendications **Restriction**.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | string | Revendication qui contient le texte à rechercher dans les revendications **restrictionValueClaim** avec la collection **Restriction**.  |
| OutputClaim | restrictionValueClaim | string | Revendication qui contient la collection **Restriction**. Une fois la transformation de revendications appelée, la valeur de cette revendication contient la valeur de l’élément sélectionné. |

L’exemple suivant recherche la description de message d’erreur en fonction de la clé de l’erreur. La revendication **responseMsg** contient une collection de messages d’erreur à présenter à l’utilisateur final ou à envoyer à la partie de confiance.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
La transformation de revendication recherche le texte de l’élément et retourne sa valeur. Si la restriction est localisée à l’aide de `<LocalizedCollection>`, la transformation de revendication retourne la valeur localisée.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **mapFromClaim** : B2C_V1_90001
- Revendications de sortie :
    - **restrictionValueClaim** : You cant sign in because you are a minor.

## <a name="lookupvalue"></a>LookupValue

Recherche une valeur de revendication dans une liste de valeurs en fonction de la valeur d’une autre revendication.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | Revendication qui contient la valeur de recherche |
| InputParameter | |string | Collection d’inputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Contrôle si une erreur est retournée en l’absence de correspondance. |
| OutputClaim | inputParameterId | string | ClaimType généré après que cette transformation de revendication a été appelée. Valeur de l’ID correspondant. |

L’exemple suivant recherche le nom de domaine dans l’une des collections inputParameters. La transformation de revendication recherche le nom de domaine dans l’identificateur et retourne sa valeur (un ID d’application).

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputParameterId** : test.com
- Paramètres d’entrée :
    - **contoso.com** : 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com** : 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com** : c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup** : false
- Revendications de sortie :
    - **outputClaim** : c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Nettoie la valeur d’une revendication donnée.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | Revendication dont la valeur doit être NULL. |

Utilisez cette transformation de revendication pour supprimer des données inutiles dans le jeu de propriétés de revendications. Le cookie de session sera alors plus petit. L’exemple suivant supprime la valeur du type de revendication `TermsOfService`.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Revendications d’entrée :
    - **outputClaim** : Welcome to Contoso App. If you continue to browse and use this website, you are agreeing to comply with and be bound by the following terms and conditions...
- Revendications de sortie :
    - **outputClaim** : NULL

## <a name="parsedomain"></a>ParseDomain

Obtient la partie domaine d’une adresse e-mail.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | string | ClaimType qui contient l’adresse e-mail. |
| OutputClaim | domaine | string | ClaimType généré après que cette transformation de revendication a été appelée (le domaine). |

Utilisez cette transformation de revendication pour analyser le nom de domaine de l’utilisateur après le symbole @. Cela peut être utile lors de la suppression des informations d’identification personnelle (PII) dans les données d’audit. La transformation de revendication suivante montre comment analyser le nom de domaine d’une revendication **e-mail**.

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
  - **emailAddress** : joe@outlook.com
- Revendications de sortie :
    - **domain** : outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Vérifie qu’une revendication de chaîne et un paramètre d’entrée `matchTo` sont égaux, et définit les revendications de sortie avec la valeur présente dans les paramètres d’entrée `stringMatchMsg` et `stringMatchMsgCode`, ainsi que la revendication de sortie de résultat de comparaison, qui est définie sur `true` ou `false` en fonction du résultat de la comparaison.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | Type de revendication à comparer. |
| InputParameter | matchTo | string | Chaîne à comparer à `inputClaim`. |
| InputParameter | stringComparison | string | Valeurs possibles : `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | string | Première valeur à définir si les chaînes sont égales. |
| InputParameter | stringMatchMsgCode | string | Deuxième valeur à définir si les chaînes sont égales. |
| OutputClaim | outputClaim1 | string | Si les chaînes sont égales, cette revendication de sortie contient la valeur du paramètre d’entrée `stringMatchMsg`. |
| OutputClaim | outputClaim2 | string | Si les chaînes sont égales, cette revendication de sortie contient la valeur du paramètre d’entrée `stringMatchMsgCode`. |
| OutputClaim | stringCompareResultClaim | boolean | Type de revendication de la sortie de résultat de comparaison, qui doit être défini sur `true` ou `false` en fonction du résultat de la comparaison. |

Vous pouvez utiliser cette transformation de revendication pour vérifier si une revendication est égale à la valeur que vous avez spécifiée. Par exemple, la transformation de revendication suivante vérifie si la valeur de la revendication **termsOfUseConsentVersion** est égale à `v1`. Si c’est le cas, elle remplace la valeur par `v2`.

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputClaim** : v1
- Paramètres d’entrée :
    - **matchTo** : V1
    - **stringComparison** : ordinalIgnoreCase
    - **stringMatchMsg** :  B2C_V1_90005
    - **stringMatchMsgCode** :  The TOS is upgraded to v2
- Revendications de sortie :
    - **outputClaim1** : B2C_V1_90005
    - **outputClaim2** : The TOS is upgraded to v2
    - **stringCompareResultClaim** : true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Vérifie qu’une revendication de chaîne et un paramètre d’entrée `matchTo` sont égaux, et définit les revendications de sortie avec la valeur présente dans le paramètre d’entrée `outputClaimIfMatched`, ainsi que la revendication de sortie de résultat de comparaison, qui est définie sur `true` ou `false` en fonction du résultat de la comparaison.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | string | Type de revendication à comparer. |
| InputParameter | matchTo | string | Chaîne à comparer à inputClaim. |
| InputParameter | stringComparison | string | Valeurs possibles : `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | string | Valeur à définir si les chaînes sont égales. |
| OutputClaim | outputClaim | string | Si les chaînes sont égales, cette revendication de sortie contient la valeur du paramètre d’entrée `outputClaimIfMatched`. Ou null, si les chaînes ne sont pas mises en correspondance. |
| OutputClaim | stringCompareResultClaim | boolean | Type de revendication de la sortie de résultat de comparaison, qui doit être défini sur `true` ou `false` en fonction du résultat de la comparaison. |

Par exemple, la transformation de revendication suivante vérifie si la valeur de la revendication **ageGroup** est égale à `Minor`. Si c’est le cas, elle retourne la valeur `B2C_V1_90001`.

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **claimToMatch** : Secondaire
- Paramètres d’entrée :
    - **matchTo** : Secondaire
    - **stringComparison** : ordinalIgnoreCase
    - **outputClaimIfMatched** :  B2C_V1_90001
- Revendications de sortie :
    - **isMinorResponseCode** : B2C_V1_90001
    - **isMinor** : true

