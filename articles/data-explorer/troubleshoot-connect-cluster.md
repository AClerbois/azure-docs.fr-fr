---
title: Résoudre les échecs de connexion de cluster de l’Explorateur de données Azure
description: Cet article décrit les étapes de dépannage pour la connexion à un cluster dans l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827034"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Résolution de problèmes : Échec de connexion à un cluster dans l’Explorateur de données Azure

Si vous n’êtes pas en mesure de vous connecter à un cluster dans l’Explorateur de données Azure, effectuez les étapes suivantes.

1. Vérifiez que la chaîne de connexion est correcte. Elle doit se présenter sous la forme `https://<ClusterName>.<Region>.kusto.windows.net`, comme dans l’exemple suivant : `https://docscluster.westus.kusto.windows.net`.

1. Veillez à disposer des autorisations appropriées. Dans le cas contraire, vous obtiendrez la réponse *non autorisé*.

    Pour plus d’informations sur les autorisations, consultez [Gérer des autorisations de base de données](manage-database-permissions.md). Si nécessaire, demandez à l’administrateur de votre cluster de vous ajouter au rôle approprié.

1. Vérifiez que le cluster n’a pas été supprimé : passez en revue le journal d’activité dans votre abonnement.

1. Consultez le [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/). Recherchez l’état de l’Explorateur de données Azure dans la région où vous tentez de vous connecter à un cluster.

    Si l’état n’est pas **correct** (coche verte), essayez de vous connecter au cluster une fois que l’état s’est amélioré.

1. Si vous avez toujours besoin d’aide pour résoudre votre problème, ouvrez une demande de support dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).