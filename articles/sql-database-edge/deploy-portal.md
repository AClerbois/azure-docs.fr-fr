---
title: Déployer SQL Database Edge en préversion à l’aide du Portail Azure | Microsoft Docs
description: Découvrez comment déployer Azure SQL Database Edge à l’aide du Portail Azure
keywords: déployer sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da756b702c994d69aae42ecef0e2da4d44eed39
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510670"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Déployer Azure SQL Database Edge en préversion

Azure SQL Database Edge en préversion est un moteur de base de données relationnelle optimisé conçu pour les déploiements IoT et Azure IoT Edge. Il offre des fonctionnalités permettant de créer une couche de traitement et de stockage des données hautes performances pour les solutions et applications IoT. Ce guide de démarrage rapide vous montre comment prendre en main la création d’un module Azure SQL Database Edge via Azure IoT Edge à l’aide du Portail Azure.

## <a name="before-you-begin"></a>Avant de commencer

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).
* Connectez-vous au [Portail Azure](https://portal.azure.com/).
* Envoyez une demande [ici](https://azure.microsoft.com/services/sql-database-edge/#contact)pour que l’abonnement soit activé pour le déploiement de SQL Database Edge.
* Créez une ressource [Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Inscrivez un [appareil IoT Edge à partir du Portail Azure](../iot-edge/how-to-register-device-portal.md).
* Préparez l’appareil IoT Edge pour [déployer un module IoT Edge à partir du Portail Azure](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Pour déployer une machine virtuelle Linux Azure en tant qu’appareil IoT Edge, consultez ce [guide de démarrage rapide](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Déployer le module SQL Database Edge à partir de la Place de marché Azure

La Place de marché Azure est un marché d’applications et de services en ligne qui vous permet de naviguer au milieu d’un large éventail d’applications et de solutions d’entreprise certifiées et optimisées pour s’exécuter sur Azure, notamment les [modules IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure SQL Database Edge peut être déployé sur un appareil de périphérie via la marketplace.

1. Recherchez le module Azure SQL Database Edge sur la Place de marché Azure.<br><br>

   ![SQL Database Edge dans la marketplace](media/deploy-portal/find-offer-marketplace.png)

2. Sélectionnez l’abonnement logiciel qui correspond le mieux à vos besoins, puis cliquez sur **Créer**. <br><br>

   ![Sélectionner l’abonnement logiciel approprié](media/deploy-portal/pick-correct-plan.png)

3. Sur la page des appareils cibles pour le module IoT Edge, spécifiez les informations suivantes, puis cliquez sur **Créer**

   |**Champ**  |**Description**  |
   |---------|---------|
   |Subscription  |  L’abonnement Azure dans lequel l’IoT Hub a été créé |
   |IoT Hub   |  Nom de l’IoT Hub où l’appareil IoT Edge est inscrit. Sélectionnez ensuite l’option « Déployer sur un appareil »|
   |Nom de l’appareil IoT Edge  |  Nom de l’appareil IoT Edge sur lequel SQL Database Edge sera déployé |

4. Sur la page **Définir des modules**, accédez à la section sur les modules de déploiement, puis cliquez sur **Configurer** par rapport au module SQL Database Edge. 

5. Dans le volet **Modules personnalisés IoT Edge**, spécifiez les valeurs souhaitées pour les variables d’environnement et/ou personnalisez les options de création et les propriétés souhaitées pour le module. Pour obtenir la liste complète des variables d’environnement prises en charge, consultez [Configure SQL Server settings with environment variables on Linux](/sql/linux/sql-server-linux-configure-environment-variables/) (Configurer les paramètres SQL Server avec les variables d’environnement sur Linux).

   |**Paramètre**  |**Description**|
   |---------|---------|
   | Nom | Nom du module. |
   |SA_PASSWORD  | Spécifiez un mot de passe fort pour le compte administrateur SQL Database Edge. |
   |MSSQL_LCID   | Définit l’ID de langue à utiliser pour SQL Server. Par exemple, 1036 correspond au français. |
   |MSSQL_COLLATION | Définit le classement par défaut pour SQL Server. Ce paramètre remplace le mappage par défaut de l’ID de langue (LCID) par le classement. |

   > [!NOTE]
   > Ne modifiez pas ou ne mettez pas à jour **l’URI d’image** ou les paramètres **ACCEPT_EULA** sur le module.

6. Dans le volet **Modules personnalisés IoT Edge**, mettez à jour la valeur souhaitée des options de création de conteneur pour **Port hôte** et **Cible** des points de montage. La cible du point de montage est l’emplacement de stockage des fichiers de base de données SQL sur l’appareil IoT Edge hôte.

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. Dans le volet **Modules personnalisés IoT Edge**, mettez à jour *Set module twin’s desired properties* (Définir les propriétés souhaitées du jumeau de module) pour inclure l’emplacement du package SQL et les informations relatives au travail Stream Analytics. Ces deux champs sont facultatifs et doivent être utilisés si vous souhaitez déployer le module SQL Database Edge avec une base de données et un travail de diffusion en continu.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. Dans le volet **Modules personnalisés IoT Edge**, définissez *Stratégie de redémarrage* sur « toujours » et *Statut souhaité* sur « en cours d’exécution ».
9. Dans le volet **Modules personnalisés IoT Edge**, cliquez sur **Enregistrer**.
10. Sur la page **Définir des modules**, cliquez sur **Suivant**.
11. Dans la section **Specify Route (optional)** (Spécifier l’itinéraire (facultatif)) de la page **Définir des modules**, spécifiez les itinéraires pour la communication module-module ou module-IoT Edge Hub ; consultez [Découvrez comment déployer des modules et établir des itinéraires dans IoT Edge](../iot-edge/module-composition.md).
12. Cliquez sur **Suivant**.
13. Cliquez sur **Envoyer**.

Dans ce guide de démarrage rapide, vous avez déployé un module SQL Database Edge sur un appareil IoT Edge.

## <a name="next-steps"></a>Étapes suivantes

- [Machine Learning et intelligence artificielle avec ONNX dans SQL Database Edge](onnx-overview.md).
- Création d’une solution IoT de bout en bout avec SQL Database Edge à l’aide d’IoT Edge.