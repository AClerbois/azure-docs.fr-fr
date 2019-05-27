---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140553"
---
1. Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur le projet d’application Microsoft Store. Puis sélectionnez **Store** > **Associer l’application au Store**.

    ![Associer une application au Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. Dans l’Assistant, sélectionnez **Suivant**. Puis connectez-vous avec votre compte Microsoft. Dans la zone **Réserver un nouveau nom d’application**, tapez un nom pour votre application, puis sélectionnez **Réserver**.
3. Une fois l’inscription de l’application créée, sélectionnez le nouveau nom d’application. Sélectionnez **Suivant**, puis **Associer**. Ce processus ajoute les informations d’inscription Microsoft Store requises au manifeste de l’application.
4. Répétez les étapes 1 et 3 pour le projet d’application Windows Phone Store en utilisant la même inscription que celle que vous avez créée précédemment pour l’application Windows Store.  
5. Accédez au [Centre de développement Windows](https://dev.windows.com/en-us/overview), puis connectez-vous avec votre compte Microsoft. Dans **Mes applications**, sélectionnez la nouvelle inscription d’application. Puis développez **Services** > **Notifications Push**.
6. Sur la page **Notifications Push**, sous **Windows Push Notification Services (WNS) et Microsoft Azure Mobile Apps**, sélectionnez **Site des services Microsoft Live**.  Prenez note des valeurs **SID du package** et de la valeur *actuelle* de **Clé secrète d’application**. 

    ![Paramètre d’application dans le centre de développement](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Le secret d’application et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez ces valeurs avec personne et ne les distribuez pas avec votre application.
   >
   >
