---
title: Azure App Service - synchroniser la configuration de réseau | Microsoft Docs
description: Cet article explique comment synchroniser la configuration de votre réseau pour le plan d’hébergement Azure App Service.
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: ea8f4aae0324e201def6b9b6cd33b0e79042ebbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647967"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchroniser la configuration de mise en réseau pour le plan d’hébergement Azure App Service

Il peut arriver que, bien que [votre application soit intégrée à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md), vous ne pouvez pas établir de connexion vers une Instance gérée. Vous pouvez par exemple essayer d’actualiser la configuration de mise en réseau pour votre plan de service. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synchroniser la configuration de réseau pour le plan d’hébergement Azure App Service

Pour ce faire, procédez comme suit :  

1. Accédez à votre plan App Service des applications Web.
 
   ![plan app service](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Cliquez sur **Mise en réseau** puis cliquez sur **Cliquez ici pour gérer**.
 
   ![gérer le plan de service](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Sélectionnez votre **Réseau virtuel** et cliquez sur **Synchroniser le réseau**. 
 
   ![synchroniser le réseau](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Attendez la fin de la synchronisation.
  
   ![synchronisation terminée](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Vous êtes maintenant prêt à tenter de rétablir votre connexion vers votre Instance gérée.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la configuration de votre réseau virtuel pour une Instance gérée, consultez [Configuration du réseau virtuel de l’Instance gérée](sql-database-managed-instance-vnet-configuration.md).
