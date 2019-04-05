---
title: Utilisation du portail Azure pour configurer le téléchargement du fichier | Microsoft Docs
description: Utilisation du portail Azure pour configurer votre IoT Hub en vue d’activer les transferts de fichiers à partir d’appareils connectés. Comprend des informations sur la configuration du compte de stockage Azure de destination.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047277"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurer les chargements de fichiers IoT Hub à l’aide du portail Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Chargement de fichiers

Pour utiliser la [fonctionnalité de chargement de fichiers dans IoT Hub](iot-hub-devguide-file-upload.md), vous devez commencer par associer un compte de stockage Azure à votre Hub. Sélectionnez **Chargement de fichier** pour afficher une liste de propriétés de chargement de fichiers pour l’IoT Hub en cours de modification.

![Afficher les paramètres de chargement de fichier IoT Hub dans le portail](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Conteneur de stockage** : Utiliser le portail Azure pour sélectionner un conteneur d’objets blob dans un compte de stockage Azure dans votre abonnement Azure actuel à associer à votre IoT Hub. Si nécessaire, vous pouvez créer un compte Azure Storage dans le panneau **Comptes de stockage** et un conteneur d’objets blob dans le panneau **Conteneurs**. IoT Hub génère automatiquement des URI SAS avec des autorisations d’écriture pour ce conteneur d’objets blob pour les appareils à utiliser lorsqu’ils chargent des fichiers.

   ![Afficher les conteneurs de stockage pour le chargement de fichiers dans le portail](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Recevoir des notifications pour les fichiers chargés** : Activer ou désactiver les notifications de téléchargement de fichier via le bouton bascule.

* **Durée de vie de SAS** : ce paramètre est la durée de vie des URI de signature d’accès partagé retournés à l’appareil par IoT Hub. Il est défini par défaut sur une heure, mais il peut être personnalisé avec d’autres valeurs à l’aide du curseur.

* **Durée de vie par défaut des paramètres de notification de fichiers** : durée de vie d’une notification de chargement de fichier avant son expiration. Il est défini par défaut sur un jour, mais il peut être personnalisé avec d’autres valeurs à l’aide du curseur.

* **Nombre maximal de remises de notifications de fichier** : nombre de tentatives de remise d’une notification de chargement de fichier par le hub IoT. Il est défini par défaut sur 10, mais il peut être personnalisé avec d’autres valeurs à l’aide du curseur.

   ![Configurer le chargement de fichier IoT Hub dans le portail](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les fonctionnalités de chargement de fichiers d’IoT Hub, consultez la section [Charger des fichiers à partir d’un appareil](iot-hub-devguide-file-upload.md) dans le guide du développeur IoT Hub.

Suivez ces liens pour en savoir plus sur la gestion de Azure IoT Hub :

* [Gestion en bloc des appareils IoT](iot-hub-bulk-identity-mgmt.md)
* [Métriques IoT Hub](iot-hub-metrics.md)
* [Surveillance des opérations](iot-hub-operations-monitoring.md)

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub](iot-hub-devguide.md)
* [Déploiement d’une IA sur des appareils edge avec Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Sécuriser votre solution IoT de bout en bout des](../iot-fundamentals/iot-security-ground-up.md)
