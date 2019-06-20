---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 752c43604349a2361a8f5b26cd6d0bce7b516bc0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177324"
---
### <a name="prerequisites"></a>Prérequis
* Un compte [MailChimp](https://www.MailChimp.com/) 

Pour pouvoir utiliser votre compte MailChimp dans une application logique, vous devez autoriser l’application à se connecter à votre compte. Heureusement, cette opération est très simple à effectuer dans votre application logique sur le portail Azure. 

Pour autoriser votre application logique à se connecter à votre compte MailChimp, procédez comme suit :

1. Pour créer une connexion à MailChimp, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *MailChimp* dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous allez utiliser :  
   ![MailChimp étape 1](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Si vous n’avez créé aucune connexion à MailChimp auparavant, vous êtes invité à indiquer vos informations d’identification MailChimp. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte MailChimp :  
   ![MailChimp étape 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Indiquez votre nom d’utilisateur et votre mot de passe MailChimp pour autoriser votre application logique :  
   ![MailChimp étape 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Notez que la connexion a été créée et que vous pouvez maintenant poursuivre la procédure dans votre application logique :  
   ![MailChimp étape 4](./media/connectors-create-api-mailchimp/mailchimp-4.png)

