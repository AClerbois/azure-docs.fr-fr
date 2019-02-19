---
title: Activer la journalisation des diagnostics dans Azure Traffic Manager
description: Découvrez comment activer la journalisation des diagnostics pour votre profil Traffic Manager et accéder aux fichiers journaux créés par la suite.
services: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: kumud
ms.openlocfilehash: a7d6893c42028790ec565961f2a2cb54035aefa1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106459"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Activer la journalisation des diagnostics dans Azure Traffic Manager

Cet article décrit comment activer la journalisation des diagnostics et accéder aux données de journal d’un profil Traffic Manager.

Les journaux de diagnostic Azure Traffic Manager peuvent donner un insight du comportement de la ressource de profil Traffic Manager. Par exemple, vous pouvez utiliser les données de journal du profil pour déterminer pourquoi des probes individuels ont expiré par rapport à un point de terminaison.

## <a name="enable-diagnostic-logging"></a>Activer la journalisation des diagnostics

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous pouvez exécuter les commandes qui suivent dans [Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif gratuit. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Si vous exécutez PowerShell depuis votre ordinateur, vous avez besoin du module Azure PowerShell version 1.0.0 ou ultérieure. Vous pouvez exécuter `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Login-AzAccount` pour vous connecter à Azure.

1. **Récupérez le profil Traffic Manager :**

    Pour activer la journalisation des diagnostics, vous avez besoin de l’ID d’un profil Traffic Manager. Récupérez le profil Traffic Manager pour lequel vous voulez activer la journalisation des diagnostics avec [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). La sortie inclut les informations d’ID du profil Traffic Manager.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Activez la journalisation des diagnostics pour le profil Traffic Manager :**

    Activez la journalisation des diagnostics pour le profil Traffic Manager à l’aide de l’ID obtenu à l’étape précédente avec [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). La commande suivante stocke les journaux détaillés du profil Traffic Manager dans un compte de stockage Azure spécifié. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Vérifiez les paramètres de diagnostic :**

      Vérifiez les paramètres de diagnostic pour le profil Traffic Manager à l’aide de [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). La commande suivante affiche les catégories journalisées pour une ressource.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Veillez à ce que toutes les catégories de journaux activées associées à la ressource de profil Traffic Manager s’affichent correctement. Vérifiez aussi que le compte de stockage est correctement configuré.

## <a name="access-log-files"></a>Accéder aux fichiers journaux
1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
1. Accédez à votre compte de stockage Azure dans le portail.
2. Dans la page **Vue d’ensemble** de votre compte de stockage Azure, sous **Services**, sélectionnez **Objets blob**.
3. Pour **Conteneurs**, sélectionnez **insights-logs-probehealthstatusevents**, puis accédez au fichier PT1H.json et cliquez sur **Télécharger** pour télécharger et enregistrer une copie de ce fichier journal.

    ![Accéder aux fichiers journaux de votre profil Traffic Manager à partir d’un stockage d’objets blob](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Schéma des journaux de Traffic Manager

Tous les journaux de diagnostic disponibles via Azure Monitor partagent un schéma commun de niveau supérieur, avec la flexibilité pour chaque service d’émettre des propriétés uniques pour ses propres événements. Pour voir le schéma général des journaux de diagnostic, consultez [Services, schémas et catégories pris en charge pour les journaux de diagnostic Azure](../azure-monitor/platform/tutorial-dashboards.md).

Le tableau suivant inclut le schéma des journaux propre à la ressource de profil Azure Traffic Manager.

|||||
|----|----|---|---|
|**Nom du champ**|**Type de champ**|**Définition**|**Exemple**|
|EndpointName|Chaîne|Nom du point de terminaison Traffic Manager dont l’état d’intégrité est en cours d’enregistrement.|*myPrimaryEndpoint*|
|Statut|Chaîne|État d’intégrité du point de terminaison Traffic Manager qui a été sondé. L’état peut être **Up** (sain) ou **Down** (non opérationnel).|**Haut**|
|||||

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [supervision Traffic Manager](traffic-manager-monitoring.md)

