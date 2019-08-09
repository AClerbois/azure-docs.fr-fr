---
title: Sécuriser les API avec l’authentification de certificat client dans la gestion des API - Gestion des API Azure | Microsoft Docs
description: Apprenez à sécuriser l’accès aux API à l’aide des certificats clients
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427353"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Comment sécuriser les API à l'aide d'une authentification par certificat client dans la Gestion des API

La Gestion des API permet de sécuriser l'accès aux API (par ex. client à gestion des API) en utilisant des certificats client. Vous pouvez valider le certificat entrant et en vérifier les propriétés en les comparant aux valeurs souhaitées à l’aide d’expressions de stratégie.

Pour savoir comment sécuriser l’accès au service back-end d’une API à l’aide de certificats clients (par exemple, de la Gestion des API vers le back-end), consultez [Comment sécuriser les services principaux à l’aide d’une authentification par certificat client dans la Gestion des API Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates).

> [!IMPORTANT]
> Pour recevoir et vérifier des certificats clients dans le niveau Consommation, vous devez d’abord activer le paramètre « Demande de certificat client » dans le panneau « Domaines personnalisés », comme indiqué ci-dessous.

![Demander un certificat client](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Vérification de l’émetteur et du sujet

Les stratégies suivantes peuvent être configurées pour vérifier l’émetteur et le sujet d’un certificat client :

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Pour désactiver la vérification de la liste de révocation de certificats, utilisez `context.Request.Certificate.VerifyNoRevocation()` et non `context.Request.Certificate.Verify()`.
> Si le certificat client est auto-signé, le ou les certificats de l’autorité de certification racine (ou intermédiaire) doivent être [chargés](api-management-howto-ca-certificates.md) dans la Gestion des API pour `context.Request.Certificate.Verify()` et `context.Request.Certificate.VerifyNoRevocation()` pour fonctionner.

## <a name="checking-the-thumbprint"></a>Vérification de l’empreinte numérique

Les stratégies suivantes peuvent être configurées pour vérifier l’empreinte d’un certificat client :

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Pour désactiver la vérification de la liste de révocation de certificats, utilisez `context.Request.Certificate.VerifyNoRevocation()` et non `context.Request.Certificate.Verify()`.
> Si le certificat client est auto-signé, le ou les certificats de l’autorité de certification racine (ou intermédiaire) doivent être [chargés](api-management-howto-ca-certificates.md) dans la Gestion des API pour `context.Request.Certificate.Verify()` et `context.Request.Certificate.VerifyNoRevocation()` pour fonctionner.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Vérification d’une empreinte par rapport aux certificats téléchargés dans la gestion des API

L’exemple suivant montre comment vérifier l’empreinte d’un certificat client par rapport aux certificats téléchargés dans la gestion des API :

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Pour désactiver la vérification de la liste de révocation de certificats, utilisez `context.Request.Certificate.VerifyNoRevocation()` et non `context.Request.Certificate.Verify()`.
> Si le certificat client est auto-signé, le ou les certificats de l’autorité de certification racine (ou intermédiaire) doivent être [chargés](api-management-howto-ca-certificates.md) dans la Gestion des API pour `context.Request.Certificate.Verify()` et `context.Request.Certificate.VerifyNoRevocation()` pour fonctionner.

> [!TIP]
> Le problème de blocage de certificat client décrit dans cet [article](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) peut se manifester de différentes manières, notamment par des demandes qui se figent, des demandes qui génèrent un code d’état `403 Forbidden` après une expiration du délai, `context.Request.Certificate` qui a la valeur `null`. Ce problème affecte généralement les demandes `POST` et `PUT` avec une longueur de contenu d’environ 60 Ko ou plus.
> Pour éviter que ce problème se produise, activez le paramètre « Négocier le certificat client  » pour les noms d’hôte souhaités dans le panneau « Domaines personnalisés », comme indiqué ci-dessous. Cette fonctionnalité n’est pas disponible dans le niveau Consommation.

![Négocier le certificat client](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Étapes suivantes

-   [Comment sécuriser les services principaux à l'aide d'une authentification par certificat client](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Comment télécharger des certificats](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
