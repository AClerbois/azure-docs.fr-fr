---
title: Problèmes de redémarrage ou de redimensionnement de machines virtuelles dans Azure | Microsoft Docs
description: Résoudre les problèmes de déploiement Resource Manager liés au redémarrage ou au redimensionnement d’une machine virtuelle existante dans Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f510a111a6c8846b300c09f368a3a2a05b2bb7ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719897"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Résoudre les problèmes de déploiement liés au redémarrage ou au redimensionnement d’une machine virtuelle Windows existante dans Azure
Lorsque vous essayez de démarrer une machine virtuelle Azure arrêtée ou de redimensionner une machine virtuelle Azure existante, l’erreur la plus fréquemment rencontrée est un échec d’allocation. Cette erreur se produit lorsque le cluster ou la région n’ont pas de ressources disponibles ou ne prennent pas en charge la taille de machine virtuelle demandée.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Collecte des journaux d’activité
Pour résoudre les problèmes, commencez par collecter les journaux d’activité afin d’identifier l’erreur associée au problème. Les liens suivants contiennent des informations détaillées sur le processus :

[Voir les opérations de déploiement](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Afficher les journaux d’activité pour gérer les ressources Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problème : Erreur lors du démarrage d’une machine virtuelle arrêtée
Vous essayez de démarrer une machine virtuelle arrêtée, mais obtenez un échec d’allocation.

### <a name="cause"></a>Cause :
La demande de démarrage de la machine virtuelle arrêtée doit être exécutée sur le cluster d’origine qui héberge le service cloud. Toutefois, le cluster n’a pas d’espace libre pour répondre à la demande.

### <a name="resolution"></a>Résolution :
* Arrêtez toutes les machines virtuelles du groupe à haute disponibilité, puis redémarrez chacune d’elles.
  
  1. Cliquez sur **Groupes de ressources** > *votre groupe de ressources* > **Ressources** > *votre groupe à haute disponibilité* > **Machines virtuelles** > *votre machine virtuelle* > **Arrêter**.
  2. Après l’arrêt de toutes les machines virtuelles, sélectionnez chacune des machines arrêtées et cliquez sur Démarrer.
* Relancez la demande de redémarrage ultérieurement.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problème : Erreur lors du redimensionnement d’une machine virtuelle existante
Vous essayez de redimensionner une machine virtuelle existante, mais obtenez un échec d’allocation.

### <a name="cause"></a>Cause :
La demande de redimensionnement de la machine virtuelle doit être exécutée sur le cluster d’origine qui héberge le service cloud. Toutefois, le cluster ne prend pas en charge la taille de machine virtuelle demandée.

### <a name="resolution"></a>Résolution :
* Relancez la demande en utilisant une plus petite taille de machine virtuelle.
* Si la taille de la machine virtuelle demandée n’est pas modifiable :
  
  1. Arrêtez toutes les machines virtuelles dans le groupe à haute disponibilité.
     
     * Cliquez sur **Groupes de ressources** > *votre groupe de ressources* > **Ressources** > *votre groupe à haute disponibilité* > **Machines virtuelles** > *votre machine virtuelle* > **Arrêter**.
  2. Après l’arrêt de toutes les machines virtuelles, redimensionnez la machine virtuelle souhaitée à une taille supérieure.
  3. Sélectionnez la machine virtuelle redimensionnée, cliquez sur **Démarrer**, puis démarrez chacune des machines virtuelles arrêtées.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lorsque vous créez une machine virtuelle Windows dans Azure, consultez [Résoudre les problèmes de déploiement liés à la création d’une machine virtuelle Windows dans Azure](../windows/troubleshoot-deployment-new-vm.md).

