---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133996"
---
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Parcourir tout** > **App Services**. Puis sélectionnez votre serveur principal Mobile Apps. Sous **Paramètres**, sélectionnez **App Service Push** (Notification Push App Service). Ensuite, sélectionnez le nom de votre Hub de notification.
2. Accédez à **Windows (WNS)**. Ensuite, entrez la **Clé de sécurité** (clé secrète client) et le **SID du package** que vous avez obtenus à partir du site des services Microsoft Live. Puis sélectionnez **Enregistrer**.

    ![Définition de la clé WNS dans le portail](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Votre serveur principal est désormais configuré pour utiliser WNS afin d’envoyer des notifications Push.
