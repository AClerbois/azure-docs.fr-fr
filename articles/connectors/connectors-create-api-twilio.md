---
title: Ajouter le connecteur Twilio à vos applications logiques Azure | Microsoft Docs
description: Vue d’ensemble du connecteur Twilio avec les paramètres d’API REST
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 8bcf69a7c8e04cb45d795fd0d6f20d477c15865d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296133"
---
# <a name="get-started-with-the-twilio-connector"></a>Prise en main du connecteur Twilio
Connectez-vous à Twilio pour envoyer et recevoir des SMS, des MMS, et des adresses IP. Avec Twilio, vous pouvez :

* Créer votre flux d'activité en fonction des données que vous obtenez de Twilio. 
* Utiliser des actions pour obtenir un message, répertorier les messages, et plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand vous recevez un nouveau message Twilio, vous pouvez prendre ce message et l’utiliser comme flux de travail Service Bus. 

Commencez par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Créer une connexion à Twilio
Quand vous ajoutez ce connecteur à vos applications logiques, entrez les valeurs Twilio suivantes :

| Propriété | Obligatoire | Description |
| --- | --- | --- |
| ID de compte |OUI |Entrez votre ID de compte Twilio |
| Jeton d'accès |OUI |Entrez votre jeton d’accès Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Si vous n’avez pas un jeton d’accès Twilio, consultez [Identité de l’utilisateur et jetons d’accès](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/twilio/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).