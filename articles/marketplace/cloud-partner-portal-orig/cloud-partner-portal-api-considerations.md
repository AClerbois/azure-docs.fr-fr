---
title: Considérations sur les API | Place de marché Azure
description: Gestion des versions, des erreurs et des problèmes d’autorisation lors de l’utilisation des API de la Place de marché.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 07cdb5e44dde0ca655191111d0a23dbab85b4cb2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819741"
---
# <a name="api-considerations"></a>Considérations sur les API


<a name="api-versioning"></a>Contrôle de version d’API
--------------

Plusieurs versions de l’API peuvent être disponibles simultanément. Les clients doivent indiquer la version qu’ils souhaitent invoquer en fournissant le paramètre `api-version` en tant que partie de la chaîne de requête.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Le code HTTP 400 est émis en cas de réponse à une requête avec une version d’API inconnue ou invalide. Cette erreur renvoie la collection des versions connues de l’API dans le corps de la réponse.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Errors
------

L’API répond aux erreurs avec les codes d’état HTTP correspondants et renvoie, éventuellement, des informations supplémentaires dans la réponse sérialisée au format JSON.
Lorsque vous recevez une erreur, en particulier une erreur de classe 400, ne réexécutez pas la requête avant d’avoir corrigé la cause sous-jacente. Par exemple, dans l’exemple de réponse ci-dessus, corrigez le paramètre de version de l’API avant de renvoyer la requête.

<a name="authorization-header"></a>En-tête d’autorisation.
--------------------

Pour toutes les API abordées dans cette référence, vous devez transmettre l’en-tête d’autorisation ainsi que le jeton du porteur obtenu auprès d’Azure Active Directory (Azure AD). Cet en-tête est nécessaire pour recevoir une réponse valide ; s’il n’est pas fourni, une erreur `401 Unauthorized` est renvoyée. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
