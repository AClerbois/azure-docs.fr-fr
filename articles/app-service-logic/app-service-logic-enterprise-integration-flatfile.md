---
title: "Apprendre à encoder ou à décoder des fichiers plats à l’aide d’Enterprise Integration Pack et des applications logiques | Microsoft Docs"
description: "Utiliser les fonctionnalités d’Enterprise Integration Pack et des applications logiques pour encoder ou décoder des fichiers plats"
services: app-service\logic
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5283c565c29c357fa2f82535dc07891ab42e6704


---
# <a name="enterprise-integration-with-flat-files"></a>Intégration d'entreprise avec des fichiers plats
## <a name="overview"></a>Vue d'ensemble
Par exemple, vous pouvez encoder le contenu XML avant de l’envoyer à un partenaire commercial dans un scénario B2B. Dans une application logique créée par la fonctionnalité Logic Apps d’Azure App Service, vous pouvez utiliser le connecteur d’encodage de fichier plat pour ce faire. L’application logique que vous créez peut obtenir son contenu XML de diverses sources y compris à partir d’un déclencheur de requête HTTP, d’une autre application, voire d’un des nombreux [connecteurs](../connectors/apis-list.md). Pour plus d’informations sur les applications logiques, consultez la [documentation sur les applications logiques](app-service-logic-what-are-logic-apps.md "En savoir plus sur les applications logiques").  

## <a name="how-to-create-the-flat-file-encoding-connector"></a>Comment créer le connecteur d’encodage de fichier plat ?
Suivez ces étapes pour ajouter un connecteur d’encodage de fichier plat à votre application logique.

1. Créez une application logique et [liez-la à votre compte d’intégration](app-service-logic-enterprise-integration-accounts.md "Découvrez comment lier un compte d’intégration à une application logique"). Ce compte contient le schéma que vous allez utiliser pour encoder les données XML.  
2. Ajoutez un déclencheur **Requête - Lors de la réception d’une requête HTTP** à votre application logique.  
   ![Capture d’écran du déclencheur à sélectionner](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)    
3. Ajoutez l’action d’encodage de fichier plat en procédant comme suit :
   
    a. Sélectionnez le signe **plus** .
   
    b. Sélectionnez le lien **Ajouter une action** (qui s’affiche après que vous avez sélectionné le signe plus).
   
    c. Dans la zone de recherche, entrez *Plat* pour filtrer toutes les actions et obtenir celle que vous souhaitez utiliser.
   
    d. Sélectionnez l’option **Encodage du fichier plat** dans la liste.   
   ![Capture d’écran de l’option Encodage du fichier plat](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)   
4. Dans la boîte de dialogue **Encodage du fichier plat**, sélectionnez la zone de texte **Contenu**.  
   ![Capture d’écran de la zone de texte Contenu](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)  
5. Sélectionnez la balise body comme contenu à encoder. La balise body permet de renseigner le champ de contenu.     
   ![Capture d’écran de la balise body](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)  
6. Sélectionnez la zone de liste **Nom du schéma** et choisissez le schéma que vous souhaitez utiliser pour encoder le contenu d’entrée.    
   ![Capture d’écran de la zone de liste Nom du schéma](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)  
7. Enregistrez votre travail.   
   ![Capture d’écran de l’icône Enregistrer](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)  

À ce stade, vous avez terminé de configurer votre connecteur d’encodage de fichier plat. Dans une application réelle, vous souhaiterez peut-être stocker les données encodées dans une application métier, comme Salesforce. Vous pouvez également envoyer ces données encodées à un partenaire commercial. Vous pouvez facilement ajouter une action pour envoyer la sortie de l’action d’encodage à Salesforce ou à votre partenaire commercial en utilisant l’un des autres connecteurs fournis.

Vous pouvez maintenant tester votre connecteur en envoyant une requête au point de terminaison HTTP, en incluant le contenu XML dans le corps de la requête.  

## <a name="how-to-create-the-flat-file-decoding-connector"></a>Comment créer le connecteur de décodage de fichier plat ?
> [!NOTE]
> Pour effectuer ces étapes, vous devez disposer d’un fichier de schéma déjà chargé sur votre compte d’intégration.
> 
> 

1. Ajoutez un déclencheur **Requête - Lors de la réception d’une requête HTTP** à votre application logique.  
   ![Capture d’écran du déclencheur à sélectionner](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)    
2. Ajoutez l’action de décodage de fichier plat en procédant comme suit :
   
    a. Sélectionnez le signe **plus** .
   
    b. Sélectionnez le lien **Ajouter une action** (qui s’affiche après que vous avez sélectionné le signe plus).
   
    c. Dans la zone de recherche, entrez *Plat* pour filtrer toutes les actions et obtenir celle que vous souhaitez utiliser.
   
    d. Sélectionnez l’action **Décodage du fichier plat** dans la liste.   
   ![Capture d’écran de l’option Décodage du fichier plat](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)   
3. Sélectionnez le contrôle **Contenu** . Vous obtenez la liste du contenu des étapes précédentes que vous pouvez utiliser comme contenu à décoder. Notez que le *Corps* de la requête HTTP entrante peut être utilisé comme contenu à décoder. Vous pouvez également entrer le contenu à décoder directement dans le contrôle **Contenu** .     
4. Sélectionnez la balise *Body* . Notez que la balise body apparaît maintenant dans le contrôle **Contenu** .
5. Sélectionnez le nom du schéma que vous souhaitez utiliser pour décoder le contenu. La capture d’écran suivante montre que *OrderFile* est le nom de schéma sélectionné. Ce nom de schéma a été chargé précédemment dans le compte d’intégration.
   
   ![Capture d’écran de la boîte de dialogue Décodage du fichier plat](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. Enregistrez votre travail.  
   ![Capture d’écran de l’icône Enregistrer](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)    

À ce stade, vous avez terminé de configurer votre connecteur de décodage de fichier plat. Dans une application réelle, vous souhaiterez peut-être stocker les données décodées dans une application métier, comme Salesforce. Vous pouvez facilement ajouter une action pour envoyer la sortie de l'action d'encodage à SalesForce.

Vous pouvez maintenant tester votre connecteur en envoyant une demande au point de terminaison HTTP, en incluant le contenu XML à décoder dans le corps de la demande.  

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack").  




<!--HONumber=Nov16_HO3-->


