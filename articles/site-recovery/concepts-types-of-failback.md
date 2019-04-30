---
title: Restauration automatique pendant la récupération d’urgence avec Azure Site Recovery | Microsoft Docs
description: Cet article donne une vue d’ensemble des différents types de restaurations automatiques et d’avertissements à prendre en compte lors d’une restauration automatique locale avec le service Azure Site Recovery pendant la récupération d’urgence.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 6f178ef1a0aec7f742bce4c2570962b995876026
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124116"
---
# <a name="failback-after-disaster-recovery-of-vmware-vms"></a>Restauration automatique après la récupération d’urgence des machines virtuelles VMware

Une fois que vous avez basculé vers Azure dans le cadre du processus de récupération d’urgence, vous pouvez opérer une restauration automatique sur votre site local. Deux types de restaurations automatiques sont possibles avec Azure Site Recovery : 

- Restauration dans l’emplacement d’origine 
- Restauration dans un autre emplacement

Si vous avez basculé une machine virtuelle VMware, vous pouvez procéder à une restauration automatique sur la même machine virtuelle source si elle existe toujours. Dans ce scénario, seules les modifications sont restaurées automatiquement. Ce scénario est appelé **récupération dans l’emplacement d’origine**. Si la machine virtuelle locale n’existe pas, le scénario est une **récupération dans un autre emplacement**.

> [!NOTE]
> Vous pouvez uniquement restaurer le vCenter d’origine et le serveur de Configuration. Vous ne pouvez pas déployer un nouveau serveur de configuration et procéder à une restauration automatique au moyen de celui-ci. Par ailleurs, vous ne pouvez pas ajouter de nouveau serveur vCenter au serveur de configuration existant, puis procéder à une restauration automatique vers le nouveau serveur vCenter.

## <a name="original-location-recovery-olr"></a>Récupération dans l’emplacement d’origine
Si vous décidez de procéder à une restauration automatique vers la machine virtuelle d’origine, vous devez respecter les conditions suivantes :

* Si la machine virtuelle est gérée par un serveur vCenter, l’ordinateur hôte ESX du serveur maître cible doit avoir accès au magasin de données de la machine virtuelle.
* Si la machine virtuelle se trouve sur un ordinateur hôte ESX, mais n’est pas gérée par vCenter, le disque dur de la machine virtuelle doit se trouver dans un magasin de données auquel l’hôte de la cible principale a accès.
* Si votre machine virtuelle se trouve sur un ordinateur hôte ESX et n’utilise pas vCenter, vous devez exécuter la détection de l’hôte ESX du serveur maître cible avant d’effectuer la reprotection. Cela s’applique si vous restaurez automatiquement les serveurs physiques.
* Vous pouvez effectuer une restauration automatique sur un réseau de stockage virtuel (vSAN) ou un disque RDM (Raw Device Mapping), si les disques existent déjà et sont connectés à la machine virtuelle locale.

> [!IMPORTANT]
> Il est important d'activer disk.enableUUID= TRUE afin que, lors de la restauration automatique, le service Azure Site Recovery puisse identifier le VMDK d’origine sur la machine virtuelle sur laquelle les modifications en attente seront écrites. Si cette valeur n’est pas définie sur TRUE, le service s’efforce d’identifier le VMDK local correspondant. Si le VMDK correct n’est trouvé, un disque supplémentaire est créé, sur lequel les données sont écrites.

## <a name="alternate-location-recovery-alr"></a>Récupération dans un autre emplacement
Si la machine virtuelle locale n’existe pas avant la reprotection, la solution consiste à utiliser un autre emplacement. Cette procédure recrée la machine virtuelle locale. Cela entraîne également le téléchargement complet de données.

* Lorsque vous opérez une restauration automatique dans un autre emplacement, la machine virtuelle est récupérée sur l’hôte ESX où le serveur cible maître est déployé. Le magasin de données utilisé pour créer le disque sera le même que celui sélectionné lors de la reprotection de la machine virtuelle.
* Vous ne pouvez effectuer la restauration automatique que dans une banque de données VMFS (Virtual Machine File System) ou vSAN. Si vous utilisez un disque RDM, la reprotection et la restauration automatique ne fonctionneront pas.
* La reprotection implique un transfert de données initial de grande taille, suivi par les modifications. Ce processus est préconisé lorsque la machine virtuelle n’existe pas sur le site. Toutes les données doivent être répliquées. Cette reprotection prendra également plus de temps que la récupération sur l’emplacement d’origine.
* Vous ne pouvez pas effectuer une restauration automatique sur des disques RDM. Seuls les nouveaux disques de machine virtuelle (VMDK) peuvent être créés dans une banque de données VMFS/vSAN.

> [!NOTE]
> Une machine physique basculée vers Azure ne peut être restaurée automatiquement qu’en tant que machine virtuelle VMware. Le flux de travail est le même que pour une récupération dans un autre emplacement. Veillez à détecter au moins un serveur cible maître et les hôtes ESX/ESXi nécessaires sur lesquels vous devez effectuer une restauration automatique.

## <a name="next-steps"></a>Étapes suivantes

Suivez les étapes pour effectuer l’[opération de restauration automatique](vmware-azure-failback.md).

