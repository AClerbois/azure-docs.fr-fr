---
title: Récupérer l’état de l’offre | Place de marché Azure
description: L’API récupère l’état actuel de l’offre.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5ce546d79497f462f6c262de738036d7e3a30226
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819665"
---
<a name="retrieve-offer-status"></a>Récupérer l’état de l’offre 
=====================

Récupère l’état actuel de l’offre.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>Paramètres URI
--------------

|  **Nom**       |   **Description**                            |  **Type de données** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identificateur du serveur de publication, par exemple `Contoso`  |     Chaîne     |
|  offerId        | GUID qui identifie de façon unique l’offre      |     Chaîne     |
|  api-version    | Dernière version de l’API                        |     Date       |
|  |  |


<a name="header"></a>En-tête
------

|  Nom           |  Valeur               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Authorization  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exemple de corps
------------

### <a name="response"></a>response

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>Propriétés du corps de réponse

|  **Nom**             |    **Description**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | État de l’offre. Pour la liste des valeurs possibles, consultez [État de l’offre](#offer-status) ci-dessous. |
|  Cloud vers appareil             | Tableau des messages associés à l’offre                                                    |
|  steps                | Tableau des étapes par lesquelles l’offre passe au cours de la publication d’une offre                      |
|  estimatedTimeFrame   | Estimation du temps qui serait nécessaire pour effectuer cette étape, dans un format convivial                       |
|  id                   | Identificateur de l’étape                                                                         |
|  stepName             | Nom de l’étape                                                                               |
|  description          | Description de l’étape                                                                        |
|  status               | État de l’étape. Pour la liste des valeurs possibles, consultez [État de l’étape](#step-status) ci-dessous.    |
|  Cloud vers appareil             | Tableau de messages liés à l’étape                                                          |
|  processPercentage    | Pourcentage d’achèvement de l’étape                                                              |
|  previewLinks         | *Actuellement non implémenté*                                                                    |
|  liveLinks            | *Actuellement non implémenté*                                                                    |
|  notificationEmails   | Liste d’adresses e-mail séparées par des virgules, pour informer de la progression de l’opération        |
|  |  |


### <a name="response-status-codes"></a>Codes d’état de réponse

| **Code** |   **Description**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` : la demande a été correctement traitée et l’état actuel de l’offre a été retourné. |
|  400     | `Bad/Malformed request` : le corps de la réponse d’erreur peut contenir plus d’informations.                 |
|  404     | `Not found` : l’entité spécifiée n’existe pas.                                                |
|  |  |


### <a name="offer-status"></a>État de l’offre

|  **Nom**                    |    **Description**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | L’offre n’a jamais été publiée.                          |
|  NotStarted                  | L’offre est nouvelle et n’a pas démarré.                            |
|  WaitingForPublisherReview   | L’offre est en attente d’approbation du serveur de publication.                 |
|  Exécution                     | La soumission de l’offre est en cours de traitement.                     |
|  Succeeded                   | La soumission de l’offre a été traitée.               |
|  Canceled                    | La soumission de l’offre a été annulée.                           |
|  Échec                      | La soumission de l’offre a échoué.                                 |
|  |  |


### <a name="step-status"></a>État de l’étape

|  **Nom**                    |    **Description**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | L’étape n’a pas démarré.                        |
|  InProgress                  | L’étape est en cours d’exécution.                             |
|  WaitingForPublisherReview   | L’étape est en attente d’approbation du serveur de publication.      |
|  WaitingForApproval          | L’étape est en attente d’approbation du processus.        |
|  Bloqué                     | L’utilisateur est bloqué.                             |
|  Rejeté                    | L’étape est rejetée.                            |
|  Complete                    | L’étape est terminée.                            |
|  Canceled                    | L’étape a été annulée.                           |
|  |  |
