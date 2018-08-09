---
title: Configurer et gérer des stratégies de réplication pour la réplication VMware sur Azure à l’aide d’Azure Site Recovery | Microsoft Docs
description: Décrit comment configurer les paramètres de réplication en vue d’une réplication VMware sur Azure avec Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: 03197d1f42a17d6fc99b85d3fbc3635468b1e6ae
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423604"
---
# <a name="configure-and-manage-replication-policies-for-vmware-replication"></a>Configurer et gérer des stratégies de réplication pour la réplication VMware
Cet article explique comment configurer une stratégie de réplication pour la réplication d’une machine virtuelle VMware sur Azure avec [Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Création d’une stratégie

1. Sélectionnez **Gérer** > **Infrastructure Site Recovery**.
1. Sélectionnez **Stratégies de réplication** dans **Pour les machines VMware et physiques**. 
1. Cliquez sur **+ Stratégie de réplication**, et spécifiez le nom de la stratégie.
1. Dans le champ **Seuil d’objectif de point de récupération**, spécifiez la limite de l’objectif de point de récupération. Des alertes sont générées lorsque la réplication continue dépasse cette limite.
1. Dans **Rétention des points de récupération**, spécifiez la durée (en heures) de la fenêtre de rétention pour chaque point de récupération. Les machines protégées peuvent être récupérées à tout moment pendant cette fenêtre de rétention. Les machines virtuelles répliquées vers le Stockage Premium peuvent prendre en charge jusqu’à 24 heures de rétention. Le stockage standard prend en charge jusqu’à 72 heures de rétention.
1. Dans **Fréquence des captures instantanées cohérentes au niveau de l’application**, spécifiez la fréquence de création des points de récupération qui contiennent des captures instantanées cohérentes au niveau de l’application (en minutes).
1. Cliquez sur **OK**. La création de la stratégie devrait prendre entre 30 et 60 secondes.

Lorsque vous créez une stratégie de réplication, une stratégie de correspondance est automatiquement créée pour la restauration automatique, avec le suffixe « failback ». Une fois la stratégie créée, vous pouvez la modifier en sélectionnant **Modifier les paramètres**.

## <a name="associate-a-configuration-server"></a>Associer un serveur de configuration 

Associez la stratégie de réplication à votre serveur de configuration local.

1. Cliquez sur **Associer**, puis sélectionnez le serveur de configuration.

    ![Associer un serveur de configuration](./media/vmware-azure-set-up-replication/associate1.png)

1. Cliquez sur **OK**. L’association du serveur de configuration devrait prendre entre une et deux minutes.

    ![Association du serveur de configuration](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Dissocier ou supprimer une stratégie de réplication
1. Choisissez la stratégie de réplication.
    a. Pour dissocier la stratégie du serveur de configuration, veillez à ce qu’aucune machine répliquée n’utilise la stratégie. Ensuite, cliquez sur **Dissocier**.
    b. Pour supprimer la stratégie, veillez à ce qu’elle ne soit associée à aucun serveur de configuration. Ensuite, cliquez sur **Supprimer**. L’opération de suppression dure entre 30 et 60 secondes.
1. Cliquez sur **OK**.
