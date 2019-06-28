---
title: Connecter un appareil Windows IoT Core à votre application Azure IoT Central | Microsoft Docs
description: En tant que développeur d’appareils, apprenez à connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e8d4ab46c598580a3a87f4344202f2700926bf5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510322"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Connecter un appareil Windows IoT Core à votre application Azure IoT Central

Cet article vous explique comment, en tant que développeur d’appareils, connecter un appareil Windows IoT Cor à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

- Une application Azure IoT Central créée à partir du modèle d’application **Exemples de Devkits**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).

- Un appareil exécutant le système d’exploitation Windows 10 IoT Core. Pour plus d’informations, consultez procédure de [configuration de votre appareil Windows 10 IoT Standard](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Une machine de développement où [Node.js](https://nodejs.org/) version 8.0.0 ou ultérieure est installée. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Node.js est disponible pour un large éventail de systèmes d’exploitation.

## <a name="the-sample-devkits-application"></a>Exemple d’application Devkits

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Windows IoT Core** avec les caractéristiques suivantes :

- Mesures de télémétrie de l’appareil : **humidité**, **température** et **pression**.
- Paramètre pour contrôler la **vitesse du ventilateur**.
- Propriété d’appareil **Numéro gravé** et propriété cloud **Emplacement**.

Pour plus d’informations sur la configuration du modèle d’appareil, voir [Détails de modèle d’appareil Windows IoT Standard](#device-template-details).

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, utilisez la page **Explorateur d’appareils** pour ajouter un appareil réel à partir du modèle d’appareil **Windows 10 IoT Standard**. Notez les détails de connexion de l’appareil : **ID de l’étendue**, **ID de l’appareil** et **Clé primaire**. Pour plus d’informations, voir [Obtenir des informations de connexion](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Préparer l’appareil

Pour se connecter à IoT Central, l’appareil a besoin d’une chaîne de connexion.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Pour que le code de l’appareil accède à la chaîne de connexion, enregistrez-le dans un fichier appelé **connection.string.iothub** dans le dossier `C:\Data\Users\DefaultAccount\Documents\` sur votre appareil Windows 10 IoT Standard.

Pour copier le fichier **connection.string.iothub** de votre ordinateur de bureau vers le dossier `C:\Data\Users\DefaultAccount\Documents\` sur votre appareil, vous pouvez utiliser le [portail d’appareil Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) :

1. Utilisez votre navigateur web pour accéder au portail d’appareil Windows sur votre appareil.
1. Pour parcourir les fichiers sur votre appareil, choisissez **Applications > Explorateur de fichiers**.
1. Accédez à **User Folders\Documents**. Ensuite, chargez le fichier **connection.string.iothub** :

    ![Charger une chaîne de connexion](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Déployer et exécuter

Pour déployer et exécuter l’exemple d’application sur votre appareil, vous pouvez utiliser le [portail d’appareil Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) :

1. Utilisez votre navigateur web pour accéder au portail d’appareil Windows sur votre appareil.
1. Pour déployer et exécuter l’application **Client Azure IoT Hub**, choisissez **Applications > Exemples de démarrage rapide**. Choisissez **Client Azure IoT Hub**.
1. Puis choisissez **Déployer et exécuter**.

    ![Déployer et exécuter](media/howto-connect-windowsiotcore/quick-run.png)

Après quelques minutes, vous pouvez afficher les données de télémétrie de votre appareil dans votre application IoT Central.

Le [portail d’appareil Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) inclut des outils permettant de résoudre les problèmes de votre appareil :

- La page **Gestionnaire d’applications** vous permet de contrôler les applications en cours d’exécution sur votre appareil.
- Si aucun moniteur n’est connecté à votre appareil, vous pouvez utiliser la page **Paramètres de l’appareil** pour créer les captures d’écran à partir de votre appareil. Par exemple :

    ![Capture d’écran de l’application](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Télécharger le code source

Si vous voulez explorer et modifier le code source de l’application cliente, vous pouvez le télécharger depuis le [référentiel GitHub Windows-iotcore-samples](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Détails du modèle d’appareil

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Windows IoT Core** avec les caractéristiques suivantes :

### <a name="telemetry-measurements"></a>Mesures de télémétrie

| Nom du champ     | Units  | Minimale | Maximale | Nombre de décimales |
| -------------- | ------ | ------- | ------- | -------------- |
| humidité       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pression       | hPa    | 260     | 1 260    | 0              |

### <a name="settings"></a>Paramètres

Paramètres numériques

| Nom complet | Nom du champ | Units | Nombre de décimales | Minimale | Maximale | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Vitesse du ventilateur    | fanSpeed   | TR/MIN   | 0              | 0       | 1 000    | 0       |

### <a name="properties"></a>properties

| Type            | Nom complet | Nom du champ | Type de données |
| --------------- | ------------ | ---------- | --------- |
| Propriété d’appareil | Numéro gravé   | dieNumber  | number    |
| Texte            | Lieu     | location   | N/A       |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment connecter un appareil Windows IoT Core à votre application Azure IoT Central, l’étape suivante suggérée est de découvrir comment [définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
