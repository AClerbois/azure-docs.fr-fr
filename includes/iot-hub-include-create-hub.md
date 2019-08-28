---
title: Fichier Include
description: Fichier Include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: bf63a9fb552b2a41ce7b9945faeda362592d8efb
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558832"
---
Cette section décrit comment créer un hub IoT à l’aide du [portail Azure](https://portal.azure.com).

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Choisissez **Créer une ressource**, puis entrez *IoT Hub* dans le champ **Rechercher dans le marketplace**.

1. Sélectionnez **IoT Hub** dans les résultats de la recherche, puis **Créer**.

1. Dans l'onglet **De base**, renseignez les champs comme suit :

   - **Abonnement**: Sélectionnez l’abonnement à utiliser pour votre hub.

   - **Groupe de ressources** : Sélectionnez un groupe de ressources ou créez-en un. Pour créer un nouveau groupe, sélectionnez **Créer** et indiquez le nom que vous voulez utiliser. Pour utiliser un groupe de ressources existant, sélectionnez-le. Pour plus d’informations, consultez [Gérer des groupes de ressources Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   - **Région** : Sélectionnez la région dans laquelle vous voulez placer votre hub. Sélectionnez l’emplacement le plus proche de vous.

   - **Nom du hub IoT** : Entrez un nom pour votre hub. Ce nom doit être globalement unique. Si le nom saisi est disponible, une coche verte s’affiche.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Créer un hub dans le portail Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics-vs2019.png)

1. Sélectionnez **Suivant : taille et échelle** pour poursuivre la création de votre hub.

   ![Définir la taille et la mise à l’échelle d’un nouveau hub avec le portail Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

    Cet écran vous permet de définir les valeurs suivantes :

    - **Tarification et niveau de mise à l’échelle** : Le niveau sélectionné. Vous pouvez choisir à partir de plusieurs niveaux, selon le nombre de fonctionnalités souhaité et le nombre de messages envoyés via votre solution par jour. Le niveau gratuit est destiné aux tests et à l’évaluation. Il permet la connexion de 500 appareils au hub, avec jusqu’à 8 000 messages par jour. Chaque abonnement Azure peut créer un IoT Hub dans le niveau gratuit.

    - **Unités IoT Hub** : Le nombre de messages autorisés par unité par jour dépend du niveau de tarification de votre concentrateur. Par exemple, si vous souhaitez que le hub prenne en charge l’arrivée de 700 000 messages, vous choisissez deux unités de niveau S1.
    Pour plus d’informations sur les autres options de niveau, consultez [Choix du bon niveau IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

    - **Paramètres avancés** > **Partitions appareil-à-cloud** : cette propriété associe les messages appareil-à-cloud au nombre de lecteurs simultanés des messages. La plupart des hubs ont besoin de seulement 4 partitions.

1. Pour ce didacticiel, acceptez les options par défaut, puis sélectionnez **Vérifier + créer** pour valider vos choix. Vous voyez quelque chose de similaire à cet écran.

   ![Vérifier les informations pour la création du hub](./media/iot-hub-include-create-hub/iot-hub-create-review-vs2019.png)

1. Sélectionnez **Créer** pour créer votre hub. La création du hub prend quelques minutes.
