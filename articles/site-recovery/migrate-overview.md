---
title: Présentation de la migration de machines locales et de machines virtuelles Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment migrer des machines locales et des machines virtuelles Azure IaaS sur Azure à l’aide du service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 856d03b1ecc1c7a3bd527eb265061f9a305d8f50
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315631"
---
# <a name="about-migration"></a>À propos de la migration

Lisez cet article pour obtenir une vue d’ensemble rapide de la façon dont le service [Azure Site Recovery](site-recovery-overview.md) vous aide à migrer des ordinateurs. 

Voici ce que vous pouvez migrer avec Site Recovery :

- **Migrer à partir du site local** : migrer des machines virtuelles Hyper-V, des machines virtuelles VMware et des serveurs physiques locaux vers Azure. Après la migration, les charges de travail en cours d’exécution sur les machines locales seront exécutées sur les machines virtuelles Azure. 
- **Migrer au sein d’Azure** : migrer des machines virtuelles Azure entre des régions Azure. 
- **Migrer AWS** : migrer des instances Windows AWS vers des machines virtuelles IaaS Azure. 


## <a name="what-do-we-mean-by-migration"></a>Quelle est notre définition de la migration ?

Outre l’utilisation de Site Recovery pour la récupération d’urgence des machines virtuelles Azure et locales, vous pouvez utiliser le service Site Recovery pour les migrer. Quelle est la différence ?

- Pour la récupération d’urgence, répliquez des machines régulièrement vers Azure. En cas de panne, vous basculez les machines du site principal vers le site Azure secondaire, puis y accédez à partir de ce dernier. Lorsque le site principal est à nouveau disponible, vous pouvez lancer la restauration automatique à partir d’Azure.
- Pour la migration, vous répliquez des machines locales vers Azure ou des machines virtuelles Azure vers une région secondaire. Basculez ensuite la machine virtuelle du site principal vers le site secondaire, puis terminez le processus de migration. Aucune restauration automatique n’est mise en jeu.  


## <a name="migration-scenarios"></a>Scénarios de migration

**Scénario** | **Détails**
--- | ---
**Migrer de l’environnement local vers Azure** | Vous pouvez migrer des machines virtuelles Hyper-V, des machines virtuelles VMware et des serveurs physiques locaux vers Azure. Pour ce faire, effectuez presque les mêmes étapes que pour une récupération d’urgence complète. Vous ne basculez simplement pas les machines virtuelles d’Azure vers le site local.
**Migrer entre des régions Azure** | Vous pouvez migrer des machines virtuelles d’une région Azure vers une autre. Une fois que la migration est terminée, vous pouvez configurer la récupération d’urgence des machines virtuelles Azure dans la région secondaire vers laquelle vous avez migré.
**Migrer AWS vers Azure** | Vous pouvez migrer des instances AWS vers des machines virtuelles Azure. Site Recovery gère les instances AWS de la même façon que les serveurs physiques lors de la migration. 

## <a name="next-steps"></a>Étapes suivantes

- [Migrer des machines locales vers Azure](migrate-tutorial-on-premises-azure.md)
- [Migrer des machines virtuelles d’une région Azure vers une autre](azure-to-azure-tutorial-migrate.md)
- [Migrer AWS vers Azure](migrate-tutorial-aws-azure.md)
