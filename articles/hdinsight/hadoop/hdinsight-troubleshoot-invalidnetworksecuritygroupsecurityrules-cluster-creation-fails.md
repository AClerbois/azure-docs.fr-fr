---
title: InvalidNetworkSecurityGroupSecurityRules – Échec de la création d’un cluster dans Azure HDInsight
description: La création de cluster échoue en générant le code d’erreur InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: 35e583fa31ee99c9d6307c01287c2c0fde021280
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700233"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scénario : InvalidNetworkSecurityGroupSecurityRules – Échec de la création d’un cluster dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Vous recevez le code d’erreur `InvalidNetworkSecurityGroupSecurityRules`, accompagné d’une description similaire à ce qui suit : « Les règles de sécurité dans le Groupe de sécurité réseau configurées avec le sous-réseau n’autorisent pas la connectivité entrante et/ou sortante requise. »

## <a name="cause"></a>Cause :

Ce problème est probablement lié aux règles du [groupe de sécurité réseau](../../virtual-network/virtual-network-vnet-plan-design-arm.md) entrant qui sont configurées pour votre cluster.

## <a name="resolution"></a>Résolution :

Accédez au Portail Microsoft Azure et identifiez le groupe de sécurité réseau associé au sous-réseau dans lequel le cluster est en cours de déploiement. Dans la section **Règles de sécurité de trafic entrant**, assurez-vous que les règles autorisent l’accès entrant au port 443 pour les adresses IP indiquées [ici](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une demande de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus dans votre abonnement Microsoft Azure. En outre, le support technique est fourni via l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
