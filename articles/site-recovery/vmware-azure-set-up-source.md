---
title: Configurer l’environnement source pour la réplication VMware sur Azure à l’aide de Azure Site Recovery | Microsoft Docs
description: Cet article décrit la procédure de configuration de votre environnement local de manière à répliquer des machines virtuelles VMware dans Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 536b7ed21e7dd54fcbea97951330b08925961713
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884963"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Configurer l’environnement source pour la réplication VMware vers Azure

Cet article décrit la procédure de configuration de votre environnement local de manière à répliquer des machines virtuelles VMware vers Azure. Il comprend des étapes pour la sélection de votre scénario de réplication, la configuration d’un ordinateur local en tant que le serveur de configuration Site Recovery et la détection automatique des machines virtuelles locales. 

## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous avez déjà effectué les opérations suivantes :

- Vous avez planifié votre déploiement à l’aide du [Planificateur de déploiement Azure Site Recovery](site-recovery-deployment-planner.md). Cela vous permet d’allouer suffisamment de bande passante, en fonction de votre taux de modification de données par jour, pour atteindre votre objectif de point de récupération (RPO) souhaité.
- [Configurer les ressources](tutorial-prepare-azure.md) dans le [portail Azure](https://portal.azure.com).
- [Installer localement VMware](vmware-azure-tutorial-prepare-on-premises.md), y compris un compte dédié pour la détection automatique.

## <a name="choose-your-protection-goals"></a>Sélectionner vos objectifs en matière de protection

1. Dans **Archivages de Recovery Services**, sélectionnez le nom de l’archivage. Nous utilisons **ContosoVMVault** pour ce scénario.
2. Dans **Prise en main**, sélectionnez Site Recovery. Sélectionnez ensuite **Préparer l’infrastructure**.
3. Dans **Objectif de protection** > **Où se trouvent vos machines**, sélectionnez **Local**.
4. Dans **Où voulez-vous répliquer vos machines**, sélectionnez **Dans Azure**.
5. Dans **Vos machines sont-elles virtualisées**, sélectionnez **Oui, avec l’hyperviseur VMware vSphere**. Sélectionnez ensuite **OK**.

## <a name="set-up-the-configuration-server"></a>Configurer le serveur de configuration

Vous pouvez configurer le serveur de configuration comme une machine virtuelle VMware locale par le biais d’un modèle OVA (Open Virtualization Application). [En savoir plus](concepts-vmware-to-azure-architecture.md) sur les composants qui vont être installés sur la machine virtuelle VMware.

1. Prenez connaissance des [conditions préalables](vmware-azure-deploy-configuration-server.md#prerequisites) pour le déploiement du serveur de configuration.
2. [Vérifiez les chiffres de capacité](vmware-azure-deploy-configuration-server.md#capacity-planning) pour le déploiement.
3. [Téléchargez](vmware-azure-deploy-configuration-server.md#download-the-template) et [importez](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) le modèle OVA pour configurer une machine virtuelle VMware locale qui exécute le serveur de configuration. La licence fournie avec le modèle est une licence d’évaluation et est valide pendant 180 jours. Après cette période, client doit activer les fenêtres avec une licence approvisionnée.
4. Activez la machine virtuelle VMware et [inscrivez-la](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) dans le coffre Recovery Services.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Exclusions de dossiers Azure Site Recovery du programme antivirus

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Si un logiciel antivirus est actif sur l’ordinateur source

Si l’ordinateur source est doté d’un logiciel antivirus actif, le dossier d’installation doit être exclu. Par conséquent, excluez le dossier *C:\ProgramData\ASR\agent* pour une réplication sans heurts.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Si un logiciel antivirus est actif sur le serveur de configuration

Excluez les dossiers suivants du logiciel antivirus pour une réplication sans heurts et afin d’éviter les problèmes de connectivité

- C:\Program Files\Microsoft Azure Recovery Services Agent.
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Répertoire d'installation du serveur ASR. Par exemple :  E:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Si un logiciel antivirus est actif sur le serveur de processus/la cible maître scale-out

Excluez les dossiers suivants du logiciel antivirus

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Répertoire d’installation du serveur de processus à charge équilibrée ASR, par exemple : C:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>Étapes suivantes
[Configurer l’environnement cible](./vmware-azure-set-up-target.md) 
