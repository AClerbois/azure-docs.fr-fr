---
title: Configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters Azure HDInsight
description: Découvrez comment configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a97a03f7ef20ae56cec04341fe76b79ee657547b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748482"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux

> [!IMPORTANT]
> Les images Ubuntu sont accessibles pour la création d’un nouveau cluster Azure HDInsight dans les trois mois qui suivent leur publication. Depuis janvier 2019, plus aucun correctif automatique n’est disponible pour les clusters en cours. Les clients doivent utiliser des actions de script ou d'autres mécanismes pour corriger un cluster en cours d'exécution. Les clusters nouvellement créés disposeront toujours des mises à jour et des correctifs de sécurité les plus récents.

HDInsight prend en charge l’exécution des tâches courantes sur votre cluster, telles que l’installation des correctifs du système d’exploitation, les mises à jour de sécurité et le redémarrage des nœuds. Ces tâches sont accomplies à l’aide des deux scripts suivants qui peuvent être exécutés en tant qu’[actions de script](hdinsight-hadoop-customize-cluster-linux.md) et configurés avec les paramètres :

- `schedule-reboots.sh` : effectuez un redémarrage immédiat ou planifiez un redémarrage sur les nœuds de cluster.
- `install-updates-schedule-reboots.sh` : installez toutes les mises à jour, les mises à jour du noyau et de la sécurité ou les mises à jour du noyau uniquement.

> [!NOTE]  
> Ces action de script n’appliquent pas automatiquement les mises à jour pour tous les cycles de mise à jour ultérieurs. Exécutez les scripts chaque fois que de nouvelles mises à jour doivent être appliquées pour installer les mises à jour et redémarrez la machine virtuelle.

## <a name="restart-nodes"></a>Redémarrer des nœuds
  
Le script [schedule-reboots](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh) définit le type de redémarrage qui sera effectué sur les ordinateurs du cluster. Lors de l’envoi de l’action de script, configurez-la pour qu’elle s’applique aux trois types de nœuds : nœud principal, nœud Worker et Zookeeper. Si le script n’est pas appliqué à un type de nœud, les machines virtuelles de ce type de nœud ne seront pas mises à jour ni redémarrées.

Le `schedule-reboots script` accepte un paramètre numérique :

| Paramètre | Valeurs acceptées | Définition |
| --- | --- | --- |
| Type de redémarrage à effectuer | 1 ou 2 | La valeur 1 active le redémarrage planifié (entre 12 et 24 heures). La valeur 2 active le redémarrage immédiat (dans les 5 minutes). Si aucun paramètre n’est fourni, la valeur par défaut est 1. |  

## <a name="install-updates-and-restart-nodes"></a>Installer des mises à jour et redémarrer des nœuds

Le script [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) fournit des options d’installation de différents types de mises à jour et de redémarrage de la machine virtuelle.

Le script `install-updates-schedule-reboots` accepte deux paramètres numériques, comme décrit dans le tableau suivant :

| Paramètre | Valeurs acceptées | Définition |
| --- | --- | --- |
| Type de mises à jour à installer | 0, 1 ou 2 | La valeur 0 installe uniquement les mises à jour du noyau. La valeur 1 installe toutes les mises à jour et la valeur 2 installe uniquement les mise à jour du noyau et les mises à jour de sécurité. Si aucun paramètre n’est fourni, la valeur par défaut est 0. |
| Type de redémarrage à effectuer | 0, 1 ou 2 | La valeur 0 désactive le redémarrage. La valeur 1 active le redémarrage planifié et la valeur 2 active le redémarrage immédiat. Si aucun paramètre n’est fourni, la valeur par défaut est 0. L’utilisateur doit modifier le paramètre 1 pour entrer le paramètre 2. |

> [!NOTE]
> Vous devez marquer un script comme persistant lorsque vous l’appliquez à un cluster existant. Sinon, les nœuds créés lors d’opérations de mise à l’échelle utilisent la planification de mise à jour corrective par défaut. Si vous appliquez le script dans le cadre du processus de création du cluster, il est automatiquement défini comme persistant.

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les étapes spécifiques à l’utilisation de l’action de script, consultez les sections suivantes de l’article [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) :

* [Utiliser une action de script lors de la création du cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Appliquer une action de script sur un cluster en cours d’exécution](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
