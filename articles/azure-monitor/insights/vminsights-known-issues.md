---
title: Problèmes connus avec Azure Monitor pour machines virtuelles (version préliminaire) | Microsoft Docs
description: Cet article décrit les problèmes connus avec Azure Monitor pour machines virtuelles, une solution dans Azure qui combine l’état d’intégrité, la découverte des dépendances d’application et l’analyse des performances du système d’exploitation de machine virtuelle Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: magoedte
ms.openlocfilehash: 86a56e71b89e7408d1bc8ca0ee1dc8112bea368f
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522147"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problèmes connus avec Azure Monitor pour machines virtuelles (version préliminaire)

Cet article décrit les problèmes connus avec Azure Monitor pour machines virtuelles, une solution dans Azure qui combine l’état d’intégrité, la découverte des composants d’application et l’analyse des performances du système d’exploitation de machine virtuelle Azure. 

## <a name="health"></a>Intégrité 
Voici les problèmes connus de la version actuelle de la fonctionnalité Health (Intégrité) :

- Si une machine virtuelle Azure est retirée ou supprimée, elle reste affichée dans la liste de machines virtuelles pendant un certain temps. Par ailleurs, le fait de cliquer sur l’état d’une machine virtuelle supprimée a pour effet d’ouvrir la vue **Diagnostics d’intégrité**, qui entre ensuite dans une boucle de chargement. Le fait de sélectionner le nom d’une machine virtuelle supprimée a pour effet d’ouvrir un panneau avec un message indiquant que la machine virtuelle a été supprimée.
- La prise en compte des modifications de configuration, comme la mise à jour d’un seuil, peut prendre jusqu'à 30 minutes, même si la mise à jour du portail ou de l’API de supervision de charge de travail est immédiate. 
- L’interface des diagnostics d’intégrité se met à jour plus rapidement que toutes les autres vues. Lorsque vous passez d’une vue à l’autre, il se peut que les informations connaissent un certain retard. 
- Pour les machines virtuelles Linux, le titre de la page répertoriant les critères d’intégrité pour une vue de machine virtuelle porte le nom de domaine entier de celle-ci au lieu du nom défini par l’utilisateur. 
- Lorsque vous désactivez la supervision d’une machine virtuelle à l’aide d’une méthode prise en charge et que vous essayez de la redéployer, le déploiement doit avoir lieu dans le même espace de travail. Si vous utilisez un autre espace de travail et que vous essayez d’afficher l’état d’intégrité pour cette machine virtuelle, il se peut que le comportement soit incohérent.
- Après avoir supprimé les composants de la solution de votre espace de travail, vous pouvez continuer à voir l’état d’intégrité de vos machines virtuelles Azure, plus précisément les données de performances et de mappage lorsque vous accédez à une de ces vues dans le portail. Les données cesseront d’apparaître dans les vues de performances et de mappage après quelques temps. Toutefois la vue de contrôle d’intégrité continue d’afficher l’état de vos machines virtuelles. L’option **Essayer maintenant** est disponible pour effectuer une réintégration à partir des vues de performances et de mappage uniquement.

## <a name="next-steps"></a>Étapes suivantes
Pour comprendre les exigences et les méthodes pour activer l’analyse de vos machines virtuelles, consultez [activer Azure Monitor pour les machines virtuelles](vminsights-enable-overview.md).
