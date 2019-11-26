---
title: 'Architecture : récupération d’urgence VMware/physique sur un site secondaire à l’aide d’Azure Site Recovery'
description: Cet article offre une vue d’ensemble de l’architecture et des composants utilisés lors de la reprise d’activité de machines virtuelles VMware locales ou de serveurs physiques Windows/Linux sur un site secondaire VMware avec Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b0a46dcf8fe298494a53713f122b1bda8ce07e5e
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954571"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Architecture pour la réplication de machines virtuelles VMware ou de serveurs physiques vers un site local secondaire

Cet article décrit l’architecture et les processus utilisés quand vous configurez la réplication et le basculement pour la reprise d’activité de machines virtuelles VMware locales ou de serveurs physiques Windows/Linux vers un site secondaire VMware en utilisant [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Composants architecturaux

**Zone** | **Composant** | **Détails**
--- | --- | ---
**Microsoft Azure** | Vous déployez ce scénario à l’aide d’InMage Scout. | Pour obtenir InMage Scout, vous avez besoin d’un abonnement Azure.<br/><br/> Après avoir créé un coffre Recovery Services, téléchargez InMage Scout et installez les dernières mises à jour pour configurer le déploiement.
**Serveur de traitement** | Situé dans le site principal | Vous déployez le serveur de traitement pour gérer la mise en cache, la compression et l’optimisation des données.<br/><br/> Il gère aussi l’installation Push de l’Agent unifié sur les machines à protéger.
**Serveur de configuration** | Situé sur le site secondaire | Le serveur de configuration gère, configure et surveille votre déploiement via le site web de gestion ou la console vContinuum.
**Serveur vContinuum** | facultatif. Installé au même emplacement que le serveur de configuration. | Il intègre une console qui vous permet de gérer et surveiller votre environnement protégé.
**Serveur cible maître** | Situé sur le site secondaire | Le serveur cible maître stocke les données répliquées. Il reçoit les données du serveur de traitement, crée une machine de réplication sur le site secondaire et stocke les points de rétention des données.<br/><br/> Le nombre de serveurs cibles maîtres dont vous avez besoin dépend du nombre de machines que vous protégez.<br/><br/> Si vous voulez effectuer une restauration sur le site principal, vous avez là aussi besoin d’un serveur cible maître. L’Agent unifié est installé sur ce serveur.
**Serveur VMware ESX/ESXi et vCenter** |  Les machines virtuelles sont hébergées sur des hôtes ESX/ESXi. Les hôtes sont gérés avec un serveur vCenter | Vous avez besoin d’une infrastructure VMware pour répliquer des machines virtuelles VMware.
**Machines virtuelles/serveurs physiques** |  L’Agent unifié installé sur les machines virtuelles VMware et les serveurs physiques que vous souhaitez répliquer. | Il joue le rôle de fournisseur de communication entre tous les composants.

## <a name="replication-process"></a>Processus de réplication

1. Vous configurez les serveurs de composants dans chaque site (configuration, processus, cible maître) et installez l’Agent unifié sur les ordinateurs que vous souhaitez répliquer.
2. Après la réplication initiale, l’agent de chaque machine envoie les modifications de réplication différentielle au serveur de traitement.
3. Le serveur de traitement optimise ces données et les transfère vers le serveur cible maître du site secondaire. Le serveur de configuration gère le processus de réplication.

**Figure 6 : Réplication de VMware vers VMware**

![VMware vers VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Étapes suivantes

[Configurez](vmware-physical-secondary-disaster-recovery.md) la récupération d’urgence des machines virtuelles VMware et des serveurs physiques vers un site secondaire.
