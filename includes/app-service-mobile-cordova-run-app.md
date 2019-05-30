---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240226"
---
1. Accédez au fichier de solution dans le projet client (.sln) et ouvrez-le à l’aide de Visual Studio.

2. Dans Visual Studio, choisissez la plateforme de solution (Windows, iOS ou Android) dans la liste déroulante en regard de la flèche de démarrage. Sélectionnez un périphérique de déploiement spécifique ou un émulateur en cliquant sur le menu déroulant sur la flèche verte. Vous pouvez utiliser la plateforme Android par défaut et l’émulateur Ripple. Certains didacticiels plus avancés (les notifications push, par exemple) vous demanderont de sélectionner un émulateur ou un périphérique pris en charge.

3. Ouvrez le fichier `ToDoActivity.java` dans ce dossier - ZUMOAPPNAME/application/src/main/java/com/exemple/zumoappname. Le nom de l’application est `ZUMOAPPNAME`.

4. Accédez à la [Azure portal](https://portal.azure.com/) et accédez à l’application mobile que vous avez créé. Sur le `Overview` panneau, recherchez l’URL qui est le point de terminaison public pour votre application mobile. Exemple - sitename pour mon nom de l’application « test123 » sera https://test123.azurewebsites.net.

5. Accédez à la `index.js` fichier dans ZUMOAPPNAME/www/js/index.js et dans `onDeviceReady()` (méthode), remplacez `ZUMOAPPURL` paramètre avec le point de terminaison public ci-dessus.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    devient
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Pour générer et exécuter votre application Cordova, appuyez sur F5 ou cliquez sur la flèche verte. Si vous voyez apparaître dans l’émulateur une boîte de dialogue de sécurité demandant l’accès au réseau, acceptez.

7. Une fois que l’application est lancée sur le périphérique ou l’émulateur, tapez un texte explicite dans **entrer un nouveau texte**, tel que *suivre le didacticiel* puis cliquez sur le **ajouter** bouton.

Le serveur principal insère les données de la requête dans la table SQL TodoItem dans la base de données SQL et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste.

Vous pouvez répéter les étapes 3 à 5 pour les autres plateformes.