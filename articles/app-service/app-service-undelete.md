---
title: Restaurer des applications App Service supprimées - Azure App Service
description: Découvrez comment restaurer une application App Service supprimée à l’aide de PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 7dc3934f486b205febd5be3c0b484dfd2c97bb8f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755547"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurer une application App Service supprimée à l’aide de PowerShell

Si vous avez supprimé accidentellement votre application dans Azure App Service, vous pouvez la restaurer à l’aide des commandes du [module PowerShell Az](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Lister les applications supprimées

Pour obtenir la collection des applications supprimées, vous pouvez utiliser `Get-AzDeletedWebApp`.

Pour plus d’informations sur une application supprimée spécifique, vous pouvez utiliser :

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

Les informations détaillées incluent :

- **DeletedSiteId** : Identificateur unique de l’application, utilisé pour les scénarios où plusieurs applications portant le même nom ont été supprimées.
- **SubscriptionID** : Abonnement contenant la ressource supprimée.
- **Emplacement** : Emplacement de l’application d’origine.
- **ResourceGroupName** : Nom du groupe de ressources d’origine.
- **Nom** : Nom de l’application d’origine.
- **Emplacement** : Nom de l’emplacement.
- **Heure de la suppression** : Horodatage de la suppression de l’application.  

## <a name="restore-deleted-app"></a>Restaurer une application supprimée

Une fois que vous avez identifié l’application à restaurer, vous pouvez la restaurer à l’aide de `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Les entrées de la commande sont les suivantes :

- **Groupe de ressources** : Groupe de ressources cible dans lequel l’application sera restaurée.
- **Nom** : Nom de l’application qui doit être globalement unique.
- **TargetAppServicePlanName** : Plan App Service lié à l’application.

Par défaut, `Restore-AzDeletedWebApp` restaure à la fois la configuration de votre application et son contenu. Si vous voulez uniquement restaurer le contenu, utilisez l’indicateur `-RestoreContentOnly` avec cette cmdlet.

> [!NOTE]
> Si l’application a été hébergée, puis supprimée d’un App Service Environment, elle ne peut être restaurée que si les App Service Environment correspondants existent toujours.
>

La référence complète de la cmdlet se trouve ici : [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
