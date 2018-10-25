---
title: API d’annulation d’opération | Microsoft Docs
description: 'Annulez des opérations. '
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 18f00391beded0744c80eab73bb1efe1c6ab8dbc
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806045"
---
<a name="cancel-operation"></a>Annuler une opération 
=================

Cette API annule une opération qui est en cours sur l’offre. Utilisez l’[API de récupération d’opérations](./cloud-partner-portal-api-retrieve-operations.md) pour obtenir un `operationId` à transmettre à cette API. L’annulation est généralement une opération synchrone, mais, dans certains scénarios complexes, une nouvelle opération peut être nécessaire pour annuler une opération existante. Dans ce cas, le corps de la réponse HTTP contient l’emplacement de l’opération qui doit être utilisé pour interroger l’état de la requête.

Vous pouvez fournir une liste d’adresses e-mail séparées par des virgules avec la requête, de sorte l’API puisse informer ces adresses de l’avancement de l’opération.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Paramètres URI
--------------

|  **Nom**    |      **Description**                                  |    **Type de données**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherid  |  Identificateur du serveur de publication, par exemple `contoso`         |   Chaîne          |
| offerId      |  Identificateur de l’offre                                     |   Chaîne          |
| api-version  |  Version actuelle de l’API                               |    Date           |
|  |  |  |


<a name="header"></a>En-tête
------

|  **Nom**              |  **Valeur**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Authorization         |  Porteur de VOTRE JETON |
|  |  |


<a name="body-example"></a>Exemple de corps
------------

### <a name="request"></a>Requête

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Propriétés du corps de la requête

|  **Nom**                |  **Description**                                               |
|  --------                |  ---------------                                               |
|  e-mails de notification     | Liste d’ID d’e-mail séparés par des virgules pour informer de la progression de l’opération de publication. |
|  |  |


### <a name="response"></a>response

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>En-tête de réponse

|  **Nom**             |    **Valeur**                       |
|  ---------            |    ----------                      |
| Operation-Location    | URL qui peut être interrogée pour déterminer l’état actuel de l’opération. |
|  |  |


### <a name="response-status-codes"></a>Codes d’état de réponse

| **Code**  |  **Description**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | OK. La requête a été traitée avec succès et l’opération est annulée de manière synchrone. |
|  202      | Accepté. La requête a été traitée avec succès et l’opération est en cours d’annulation. L’emplacement de l’opération d’annulation est renvoyé dans l’en-tête de la réponse. |
|  400      | Requête inappropriée/mal formée. Le corps de la réponse d’erreur pourrait fournir plus d’informations.  |
|  403      | Accès interdit. Le client n’a pas accès à l’espace de noms spécifié dans la requête. |
|  404      | Introuvable. L’entité spécifiée n’existe pas. |
|  |  |
