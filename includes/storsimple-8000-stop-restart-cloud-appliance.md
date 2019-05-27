---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c10482029e6cfce7063d205161fed54030919c48
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159689"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Pour arrêter et démarrer une appliance cloud

1. Pour arrêter une applicance cloud, accédez à la machine virtuelle de votre appliance cloud.
    ![Machine virtuelle StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. Dans la barre de commandes, cliquez sur **Arrêter**.

    ![Machine virtuelle StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Cliquez sur **Oui**lorsque vous êtes invité à confirmer l’opération.

    ![Machine virtuelle StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Lorsque vous arrêtez une machine virtuelle, celle-ci est désallouée. Lorsque de l’arrêt de l’appliance cloud, son état est **Désallocation**. Une fois l’appliance cloud arrêtée, son état devient **Arrêté (désalloué)**.

    ![Machine virtuelle StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Une fois la machine virtuelle arrêtée, cliquez sur **Démarrer** (le bouton devient disponible) pour la démarrer. Une fois l’appliance cloud démarrée, son état devient **Démarré**.

    ![Machine virtuelle StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Utilisez les applets de commande suivantes pour arrêter et démarrer une appliance cloud.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>Pour redémarrer une appliance cloud

Pour redémarrer une appliance cloud, accédez à la machine virtuelle de votre appliance cloud. Dans la barre de commandes, cliquez sur **Redémarrer**. Quand vous y êtes invité, confirmez le redémarrage. Lorsque l’appliance cloud est prête à être utilisée, son état devient **En cours d’exécution**.

![Machine virtuelle StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Utilisez les applets de commande suivantes pour redémarrer une appliance cloud.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

