---
title: DNS personnalisé d’Azure SQL Database Managed Instance | Microsoft Docs
description: Cette rubrique détaille les options de configuration d’un DNS personnalisé avec Azure SQL Database Managed Instance.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: d5bb2f2f4b79c4b03e631fc844a712f76fc69109
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258165"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurer un DNS personnalisé pour Azure SQL Database Managed Instance

Une instance Azure SQL Database Managed Instance (préversion) doit être déployée au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure. Quelques scénarios, comme les serveurs liés à d’autres instances SQL dans votre cloud ou environnement hybride, nécessitent des noms d’hôte privés pour être résolus depuis l’instance gérée. Si tel est le cas, vous devez configurer un DNS personnalisé dans Azure. Comme Managed Instance utilise le même DNS pour ces tâches internes, la configuration du DNS du réseau virtuel doit être compatible avec Managed Instance. 

Pour rendre la configuration d’un DNS personnalisé compatible avec Managed Instance, vous devez réaliser les étapes suivantes : 
- Configurer le DNS personnalisé pour envoyer des requêtes au Azure DNS 
- Configurer le DNS personnalisé comme DNS principal et Azure DNS comme DNS secondaire pour le réseau virtuel 
- Enregistrer le DNS personnalisé comme DNS principal et Azure DNS comme DNS secondaire pour le réseau virtuel

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Configurer le DNS personnalisé pour envoyer des requêtes au Azure DNS 

Pour configurer l’envoi du DNS sur Windows Server 2016, suivez ces étapes : 

1. Dans **Gestionnaire de serveurs**, cliquez sur **Outils** puis cliquez sur **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Double-cliquez sur **Redirecteurs**.

   ![Redirecteurs](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Cliquez sur **Modifier**. 

   ![Liste des redirecteurs](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Entrez l’adresse IP des programmes de résolution récursifs d’Azure, telle que 168.63.129.16.

   ![Adresse IP des programmes de résolution récursifs](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Configurer le DNS personnalisé comme DNS principal et Azure DNS comme DNS secondaire 
 
La configuration du DNS sur un réseau virtuel Azure nécessite que vous entriez les adresses IP. Configurez donc la machine virtuelle qui héberge le serveur DNS avec une adresse IP statique en suivant les étapes suivantes : 

1. Dans le portail Azure, ouvrez l’interface réseau de la machine virtuelle du DNS personnalisé.

   ![Interface réseau](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. Dans la section Configurations IP. sélectionner Configuration IP 

   ![Configuration IP](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Définissez l’adresse IP privée comme Statique. Noter l’adresse IP (10.0.1.5 sur cette capture d’écran) 

   ![statique](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Enregistrer le DNS personnalisé comme DNS principal et Azure DNS comme DNS secondaire 

1. Dans le portail Azure, trouvez l’option DNS personnalisé pour votre réseau virtuel.

   ![option DNS personnalisé](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Passez sur Personnalisé et entrez l’adresse IP du serveur de votre DNS personnalisé, ainsi que l’adresse IP des programmes de résolution récursifs d’Azure, telle que 168.63.129.16. 

   ![option DNS personnalisé](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Si vous ne renseignez pas le programme de résolution récursif d’Azure dans la liste DNS, Managed Instance renvoie un statut Défectueux. Pour récupérer de ce statut, vous devez créer une nouvelle instance dans un réseau virtuel avec les stratégies réseau conformes, créer des données de niveau d’instance et restaurer vos bases de données. Consultez l’article [Configuration de réseau virtuel](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md)
- Pour suivre un didacticiel vous expliquant comment créer une option Managed Instance, consultez [Créer une option Managed Instance](sql-database-managed-instance-create-tutorial-portal.md).
- Pour plus d’informations sur la configuration d’un réseau virtuel pour une option Managed Instance, consultez [Configurer un réseau virtuel pour Azure SQL Database Managed Instance](sql-database-managed-instance-vnet-configuration.md).
