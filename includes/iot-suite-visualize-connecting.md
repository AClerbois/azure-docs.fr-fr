---
title: Fichier Include
description: Fichier Include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 5eca35141544e8ea451dfc7471a83e3e1e980360
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34307615"
---
## <a name="view-device-telemetry"></a>Afficher la télémétrie d’appareil

Vous pouvez afficher les données de télémétrie envoyées à partir de votre périphérique sur la page **Périphériques** dans la solution.

1. Sélectionnez le périphérique que vous avez configuré dans la liste des périphériques sur la page **Périphériques**. Un panneau affiche des informations sur votre périphérique, y compris un tracé de la télémétrie du périphérique :

    ![Consultez les détails du périphérique](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Choisissez **Pression** pour modifier l’affichage des données de télémétrie :

    ![Télémétrie les données de télémétrie de la pression](media/iot-suite-visualize-connecting/devicespressure.png)

1. Pour afficher les informations de diagnostic relatives à votre périphérique, faites défiler la liste jusqu'à **Diagnostics** :

    ![Afficher les diagnostics du périphérique](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agir sur votre périphérique

Pour appeler des méthodes sur vos périphériques, utilisez la page **Périphériques** dans la solution de surveillance à distance. Par exemple, dans la solution de surveillance à distance les périphériques **Condenseur** mettent en œuvre une méthode **FirmwareUpdate**.

1. Choisissez **Périphériques** pour accéder à la page **Périphériques** dans la solution.

1. Sélectionnez le périphérique que vous avez configuré dans la liste des périphériques sur la page **Périphériques** :

    ![Sélectionnez votre périphérique physique](media/iot-suite-visualize-connecting/devicesselect.png)

1. Pour afficher une liste des méthodes que vous pouvez appeler sur votre appareil, choisissez **Travaux**, puis **Run method** (Exécuter une méthode). Pour planifier un travail à exécuter sur plusieurs appareils, vous pouvez sélectionner plusieurs appareils dans la liste. Le volet **Travaux** affiche les types de méthodes communes à tous les appareils que vous avez sélectionnés.

1. Sélectionnez **FirmwareUpdate**, définissez le nom de la tâche par **UpdatePhysicalChiller**. Définissez la **version du microprogramme** sur **2.0.0**, définissez **l’URI du microprogramme** sur **http://contoso.com/updates/firmware.bin**, puis sélectionnez **Appliquer** :

    ![Planifier la mise à jour du microprogramme](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Une série de messages s’affiche dans la console exécutant votre code de périphérique lorsque le périphérique simulé traite la méthode.

1. Lorsque la mise à jour est terminée, la nouvelle version du microprogramme s’affiche sur la page **Périphériques** :

    ![Mise à jour effectuée](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> Pour suivre l’état de la tâche dans la solution, choisissez **Afficher**.

## <a name="next-steps"></a>Étapes suivantes

L’article [Personnaliser l’accélérateur de solution de surveillance à distance](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) décrit quelques méthodes permettant de personnaliser l’accélérateur de solution.