---
title: 'Sauvegarde Azure : Surveiller les charges de travail protégées de Sauvegarde Azure'
description: Dans cet article, découvrez les fonctionnalités de surveillance et de notification des charges de travail de Sauvegarde Azure à l’aide du Portail Azure.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Sauvegarde Azure ; alertes ;
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: dacurwin
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: cd9e4e14771744b6275bb1dfe0a96bbef475a123
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090962"
---
# <a name="monitoring-azure-backup-workloads"></a>Surveillance des charges de travail de Sauvegarde Azure

Le service Sauvegarde Azure fournit plusieurs solutions de sauvegarde basées sur l’exigence en matière de sauvegarde et la topologie d’infrastructure (locale ou Azure). N’importe quel administrateur ou utilisateur de sauvegarde doit voir ce qui se passe au niveau de toutes les solutions et s’attendre à être informé dans les scénarios importants. Cet article décrit en détail les fonctionnalités de surveillance et de notification fournies par le service Sauvegarde Azure.

## <a name="backup-jobs-in-recovery-services-vault"></a>Travaux de sauvegarde dans le coffre Recovery Services

Le service Sauvegarde Azure offre des fonctionnalités de surveillance et d’alerte intégrées pour les charges de travail protégées par Sauvegarde Azure. Dans les paramètres du coffre Recovery Services, la section **Surveillance** fournit des travaux intégrés et des alertes.

![Surveillance intégrée du coffre Recovery Services](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Les travaux sont générés lorsque des opérations telles que la configuration de la sauvegarde, la sauvegarde, la restauration, la suppression de la sauvegarde, etc. sont effectuées.

Les travaux provenant des solutions Sauvegarde Azure suivantes sont indiqués ici :

- Sauvegarde des machines virtuelles Azure
- Sauvegarde de fichiers Azure
- Sauvegarde de charge de travail Azure comme SQL
- Azure Backup Agent (MAB)

Les travaux provenant de System Center Data Protection Manager (SC-DPM) et du Serveur Sauvegarde Microsoft Azure NE sont PAS affichés.

> [!NOTE]
> Les charges de travail Azure telles que les sauvegardes SQL au sein de machines virtuelles Azure présentent un très grand nombre de travaux de sauvegarde. Par exemple, les sauvegardes de fichier journal peuvent s’exécuter toutes les 15 minutes. Par conséquent, pour ces charges de travail de base de données, seules les opérations déclenchées par l’utilisateur sont affichées. Les opérations de sauvegarde planifiées ne sont PAS affichées.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alertes de sauvegarde dans le coffre Recovery Services

Les alertes sont principalement des scénarios dans le cadre desquels les utilisateurs reçoivent des notifications leur permettant de prendre les mesures nécessaires. La section **Alertes de sauvegarde** affiche les alertes générées par le service Sauvegarde Azure. Ces alertes sont définies par le service et l’utilisateur ne peut pas créer d’alerte de façon personnalisée.

### <a name="alert-scenarios"></a>Scénarios d’alerte

Les scénarios suivants sont définis par le service en tant que scénarios pouvant donner lieu à des alertes.

- Échecs de sauvegarde/restauration
- Sauvegarde réussie avec des avertissements pour Azure Backup Agent (MAB)
- Arrêt de la protection avec données conservées/Arrêt de la protection avec données supprimées

### <a name="exceptions-when-an-alert-is-not-raised"></a>Cas exceptionnels dans lesquels une alerte n’est pas déclenchée

Dans quelques cas exceptionnels, une alerte n’est pas déclenchée en cas d’échec :

- L’utilisateur a explicitement annulé le travail en cours d’exécution.
- Le travail échoue, car un autre travail de sauvegarde est en cours d’exécution (aucune intervention n’est nécessaire dans ce cas, dans la mesure où il nous faut simplement attendre que la tâche précédente se termine).
- Le travail de sauvegarde de machine virtuelle échoue, car la machine virtuelle Azure sauvegardée n’existe plus.

Dans le cadre des exceptions ci-dessus, il est entendu que le résultat de ces opérations (principalement déclenchées par l’utilisateur) s’affiche immédiatement sur le portail et dans les clients PS et CLI. Par conséquent, l’utilisateur en est informé immédiatement et n’a pas besoin de notification.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Les alertes provenant des solutions Sauvegarde Azure suivantes sont indiqués ici :

- Sauvegardes de machines virtuelles Azure
- Sauvegardes de fichiers Azure
- Sauvegardes de charge de travail Azure comme SQL
- Azure Backup Agent (MAB)

> [!NOTE]
> Les alertes provenant de System Center Data Protection Manager (SC-DPM) et du Serveur Sauvegarde Microsoft Azure NE sont PAS affichées ici.

### <a name="alert-types"></a>Types d’alertes

Les alertes peuvent être définies selon trois types, en fonction de leur gravité :

- **Critique** : En principe, tout échec de sauvegarde ou de récupération (planifiée ou déclenchée par l’utilisateur) conduit à la génération d’une alerte et s’affiche en tant qu’alerte critique, de même que les opérations destructrices telles que la suppression de sauvegarde.
- **Avertissement** : Si l’opération de sauvegarde réussit, mais avec quelques avertissements, ces derniers sont répertoriés sous forme d’alertes d’avertissement.
- **Information** : À ce jour, aucune alerte d’information n’est générée par le service Sauvegarde Azure.

## <a name="notification-for-backup-alerts"></a>Notification pour les alertes de sauvegarde

> [!NOTE]
> La configuration de la notification peut être effectuée uniquement via le Portail Azure. La prise en charge de l’interface CLI/de PS/de l’API REST/du modèle Azure Resource Manager n’est pas assurée.

Lorsqu’une alerte est déclenchée, les utilisateurs en sont informés. Le service Sauvegarde Azure fournit un mécanisme intégré de notification par e-mail. Vous pouvez spécifier des adresses e-mail individuelles ou des listes de distribution pour être informé de la génération d’une alerte. Vous pouvez également choisir d’être informé à chaque alerte ou de les grouper dans une synthèse horaire avant d’être informé.

![Notification par e-mail intégrée du coffre Recovery Services](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

> [!NOTE]
> Les alertes correspondant aux sauvegardes SQL seront consolidées et l’e-mail est envoyé uniquement pour la première occurrence. Toutefois, si l’alerte est désactivée par l’utilisateur, l’occurrence suivante déclenche un autre e-mail.

Lorsque la notification est configurée, vous recevez un e-mail de bienvenue ou d’introduction. Il confirme que le service Sauvegarde Azure peut envoyer des e-mails à ces adresses lorsqu’une alerte est déclenchée.<br>

Si la fréquence était définie sur une synthèse horaire et qu’une alerte a été déclenchée et résolue dans l’heure, elle ne fera pas partie de la synthèse horaire à venir.

> [!NOTE]
>
> - Si une opération destructive comme **l’arrêt de la protection avec suppression des données** est effectuée, une alerte est déclenchée et un e-mail est envoyé aux administrateurs, coadministrateurs et propriétaires de l’abonnement, même si les notifications ne sont PAS configurées pour le coffre Recovery Services.
> - Pour configurer la notification pour les travaux réussis, utilisez [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Désactivation des alertes

Pour désactiver/résoudre une alerte active, vous pouvez cliquer sur l’élément de liste correspondant à l’alerte que vous voulez désactiver. Cette opération ouvre un écran qui affiche des informations détaillées sur l’alerte, avec un bouton « Désactiver » en haut. Le fait de cliquer sur ce bouton change l’état de l’alerte en « Inactif ». Vous pouvez également désactiver une alerte en cliquant avec le bouton droit sur l’élément de liste correspondant à cette alerte et en sélectionnant « Désactiver ».

![Désactivation d’alerte de coffre Recovery Services](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Étapes suivantes

[Monitor Azure backup workloads using Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) (Surveiller les charges de travail de Sauvegarde Azure avec Azure Monitor)
