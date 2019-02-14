---
title: Informations d’identification de certificat dans Azure AD | Microsoft Docs
description: Cet article traite de l’inscription et de l’utilisation des informations d’identification de certificat pour l’authentification d’application.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: celested
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4ee1ce56723e4a2c9ab80c12456bbc1b66f6d5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162796"
---
# <a name="certificate-credentials-for-application-authentication"></a>Informations d’identification de certificat pour l’authentification d’application

Azure Active Directory (Azure AD) permet à une application d’utiliser ses propres informations d’identification pour l’authentification, par exemple, le flux d’octroi d’informations d’identification du client d’OAuth 2.0 ([v1.0](v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) ou le flux On-Behalf-Of ([v1.0](v1-oauth2-on-behalf-of-flow.md), [v2.0](v2-oauth2-on-behalf-of-flow.md)).

Parmi les types d’informations d’identification qu’une application peut utiliser pour l’authentification figure l’assertion JSON Web Token (JWT) signée avec un certificat dont est propriétaire l’application.

## <a name="assertion-format"></a>Format d’assertion
Pour calculer l’assertion, vous pouvez utiliser l’une des nombreuses bibliothèques [JSON Web Token](https://jwt.ms/) dans la langue de votre choix. Les informations contenues dans le jeton sont les suivantes :

### <a name="header"></a>En-tête

| Paramètre |  Remarque |
| --- | --- |
| `alg` | Doit être **RS256** |
| `typ` | Doit être **JWT** |
| `x5t` | Doit être l’empreinte SHA-1 du certificat X.509 |

### <a name="claims-payload"></a>Revendications (charge utile)

| Paramètre |  Remarques |
| --- | --- |
| `aud` | Audience: Doit être **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | Date d’expiration : date d’expiration du jeton. L’heure est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’à l’expiration du jeton.|
| `iss` | Émetteur : doit être le paramètre client_id (ID de l’application du service client) |
| `jti` | GUID : ID JWT |
| `nbf` | Pas avant : date avant laquelle le jeton ne peut pas être utilisé. L’heure est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment de l’émission du jeton. |
| `sub` | Objet : En ce qui concerne `iss`, doit être le paramètre client_id (ID de l’application du service client) |

### <a name="signature"></a>Signature

La signature est calculée en appliquant le certificat, conformément à la [spécification TFC7519 sur les jetons Web JSON](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Exemple d’une assertion JWT décodée

```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

## <a name="example-of-an-encoded-jwt-assertion"></a>Exemple d’une assertion JWT encodée

La chaîne suivante est un exemple d’assertion encodée. Si vous regardez attentivement, vous remarquerez les trois sections séparées par des points (.) :
* La première section encode l’en-tête.
* La deuxième section encode la charge utile.
* La dernière section est la signature calculée avec les certificats à partir du contenu des deux premières sections.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>Inscrire votre certificat dans Azure AD

Vous pouvez associer les informations d’identification du certificat à l’application cliente dans Azure AD via le portail Azure en utilisant l’une des méthodes suivantes :

### <a name="uploading-the-certificate-file"></a>Chargement du fichier de certificat

Dans l’inscription d’application Azure pour l’application cliente :
1. Sélectionnez **Paramètres > Clés**, puis **Télécharger la clé publique**. 
2. Sélectionnez le fichier de certificat à charger.
3. Sélectionnez **Enregistrer**. 
   
   Une fois enregistré, le certificat est chargé et les valeurs de l’empreinte numérique, de la date de début et d’expiration sont affichées. 

### <a name="updating-the-application-manifest"></a>Mise à jour du manifeste d’application

Sur la base de votre certificat, vous devez calculer :

- `$base64Thumbprint`, qui est l’encodage en base64 du hachage de certificat
- `$base64Value`, qui est l’encodage en base64 des données brutes du certificat

Vous devez également fournir un GUID pour identifier la clé dans le manifeste de l’application (`$keyId`).

Dans l’inscription d’application Azure pour l’application cliente :
1. Ouvrez le manifeste d’application.
2. Remplacez la propriété *keyCredentials* par les nouvelles informations de votre certificat, en utilisant le schéma suivant.

   ```
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Enregistrez les modifications du manifeste de l’application, puis chargez-le dans Azure AD. 

   La propriété `keyCredentials` peut avoir plusieurs valeurs. Vous pouvez donc charger plusieurs certificats pour une gestion plus élaborée des clés.
   
## <a name="code-sample"></a>Exemple de code

L’exemple de code sur l’[authentification auprès d’Azure AD dans les applications de démon avec certificats](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) montre comment une application utilise ses propres informations d’identification pour l’authentification. Il montre comment vous pouvez [créer un certificat auto-signé](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) à l’aide de la commande PowerShell `New-SelfSignedCertificate`. Vous pouvez également utiliser les [scripts de création d’application](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) pour créer les certificats, calculer l’empreinte, etc.
