---
title: Exemples de transformations de revendications JSON pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C | Microsoft Docs
description: Exemples de transformations de revendications JSON pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d712286cb4ea5e67474ec11d56d99eaf2cabec3e
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433070"
---
# <a name="json-claims-transformations"></a>Transformations de revendications JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications JSON du schéma Infrastructure d’expérience d’identité dans Azure Active Directory (Azure AD) B2C. Pour plus d’informations, consultez [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Obtient un élément spécifié à partir de données JSON.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | chaîne | ClaimTypes qui sont utilisés par la transformation de revendication pour obtenir l’élément. |
| InputParameter | claimToExtract | chaîne | Nom de l’élément JSON à extraire. |
| OutputClaim | extractedClaim | chaîne | ClaimType généré après l’appel de cette transformation de revendication, la valeur de l’élément spécifiée dans le paramètre d’entrée _claimToExtract_. |

Dans l’exemple suivant, la transformation de revendication a extrait l’élément `emailAddress` à partir des données JSON : `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
    - **inputJson** : {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Paramètre d’entrée :
    - **claimToExtract** : emailAddress
- Revendications de sortie : 
    - **extractedClaim** : someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Obtenir une liste d’éléments spécifiés à partir de données Json.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | chaîne | ClaimTypes utilisés par la transformation de revendication pour obtenir les revendications. |
| InputParameter | errorOnMissingClaims | booléenne | Spécifie s’il faut lever une erreur si l’une des revendications est manquante. |
| InputParameter | includeEmptyClaims | chaîne | Spécifiez s’il faut inclure les revendications vides. |
| InputParameter | jsonSourceKeyName | chaîne | Nom de clé d’élément |
| InputParameter | jsonSourceValueName | chaîne | Nom de valeur d’élément |
| OutputClaim | Collection | string, int, boolean et datetime |Liste de revendications à extraire. Le nom de la revendication doit être égal à celui spécifié dans la revendication d’entrée _jsonSourceClaim_. |

Dans l’exemple suivant, la transformation de revendication extrait les revendications suivantes : email (string), displayName (string), membershipNum (int), active (boolean) et birthdate (datetime) à partir des données JSON.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- Revendications d’entrée :
    - **jsonSourceClaim** : [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- Paramètres d’entrée :
    - **errorOnMissingClaims** : false
    - **includeEmptyClaims** : false
    - **jsonSourceKeyName** : key
    - **jsonSourceValueName** : value
- Revendications de sortie :
    - **email** : "someone@example.com"
    - **displayName** : "Someone"
    - **membershipNum** : 6353399
    - **active** : true
    - **birthdate** : 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Obtient un élément numérique (long) spécifié à partir de données JSON.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | chaîne | ClaimTypes utilisés par la transformation de revendication pour obtenir les revendications. |
| InputParameter | claimToExtract | chaîne | Nom de l’élément JSON à extraire. |
| OutputClaim | extractedClaim | long | ClaimType généré après l’appel de cette ClaimsTransformation, la valeur de l’élément spécifiée dans les paramètres d’entrée _claimToExtract_. |

Dans l’exemple suivant, la transformation de revendication extrait l’élément `id` à partir des données JSON.

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputJson** : {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- Paramètres d'entrée
    - **claimToExtract** :  id
- Revendications de sortie : 
    - **extractedClaim** : 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Obtient le premier élément à partir d’un tableau de données JSON.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | chaîne | ClaimTypes qui sont utilisés par la transformation de revendication pour obtenir l’élément à partir du tableau JSON. |
| OutputClaim | extractedClaim | chaîne | ClaimType généré après l’appel de cette ClaimsTransformation, le premier élément du tableau JSON. |

Dans l’exemple suivant, la transformation de revendication extrait le premier élément (adresse e-mail) à partir du tableau JSON `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputJsonClaim** : ["someone@example.com", "Someone", 6353399]
- Revendications de sortie : 
    - **extractedClaim** : someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Convertit des données XML au format JSON.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | chaîne | ClaimTypes qui sont utilisés par la transformation de revendication pour convertir les données XML au format JSON. |
| OutputClaim | json | chaîne | ClaimType généré après l’appel de cette ClaimsTransformation, les données au format JSON. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

Dans l’exemple suivant, la transformation de revendication convertit les données XML suivantes au format JSON.

#### <a name="example"></a>Exemples
Revendication d’entrée :

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Revendication de sortie :

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

