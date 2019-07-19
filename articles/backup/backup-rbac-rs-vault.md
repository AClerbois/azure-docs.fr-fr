---
title: Gérer des sauvegardes avec le contrôle d’accès en fonction du rôle
description: Le contrôle d’accès en fonction du rôle permet de gérer l’accès aux opérations de gestion de sauvegarde dans le coffre Recovery Services.
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: trinadhk
ms.openlocfilehash: 6ca4de7dfb948f49ebf1c91e29cc0537e67a3c21
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436200"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Utiliser le contrôle d’accès en fonction du rôle pour gérer les points de récupération Sauvegarde Azure
Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès pour Azure. Avec le contrôle d’accès en fonction du rôle, vous pouvez séparer les tâches au sein de votre équipe et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail.

> [!IMPORTANT]
> Les rôles fournis par Azure Backup sont limités à des actions qui peuvent être effectuées dans le portail Azure ou via l’API REST, le coffre Recovery Services Powershell, ou les cmdlets d’interface de ligne de commande. Les actions effectuées dans l’interface cliente de l’agent Azure Backup, l’interface System Center Data Protection Manager ou l’interface de serveur de sauvegarde Azure ne sont pas contrôlées par ces rôles.

Azure Backup fournit 3 rôles intégrés pour contrôler les opérations de gestion des sauvegardes. En savoir plus sur les [rôles intégrés Azure RBAC](../role-based-access-control/built-in-roles.md)

* [Contributeur de sauvegarde](../role-based-access-control/built-in-roles.md#backup-contributor) : ce rôle dispose de toutes les autorisations pour créer et gérer des sauvegardes, à l’exception de la suppression du coffre Recovery Services et de l’octroi d’un accès à d’autres personnes. Imaginez ce rôle comme un administrateur de gestion des sauvegardes qui peut exécuter chaque opération de gestion des sauvegardes.
* [Opérateur de sauvegarde](../role-based-access-control/built-in-roles.md#backup-operator) : ce rôle dispose des autorisations généralement accordées à un contributeur à l’exception de la suppression de sauvegardes et de la gestion des stratégies de sauvegarde. Ce rôle est équivalent au contributeur, mais il ne peut pas effectuer d’opérations destructrices telles que l’arrêt de la sauvegarde avec suppression des données ou la suppression de l’enregistrement de ressources locales.
* [Lecteur de sauvegarde](../role-based-access-control/built-in-roles.md#backup-reader) : ce rôle dispose des autorisations pour afficher toutes les opérations de gestion des sauvegardes. Imaginez ce rôle comme un responsable de surveillance.

Si vous avez besoin de définir vos propres rôles pour un meilleur contrôle, découvrez comment créer [des rôles personnalisés dans RBAC Azure](../role-based-access-control/custom-roles.md).



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mappage des rôles de sauvegarde intégrés pour les actions de gestion des sauvegardes
Le tableau suivant répertorie les actions de gestion des sauvegardes et le rôle RBAC minimum nécessaire correspondant pour effectuer cette opération.

| Opération de gestion | Rôle RBAC minimum nécessaire | Étendue requise |
| --- | --- | --- |
| Créer un coffre Recovery Services | Contributeur de sauvegarde | Groupe de ressources contenant le coffre |
| Activer la sauvegarde des machines virtuelles Azure | Opérateur de sauvegarde | Groupe de ressources contenant le coffre |
| | Contributeur de machine virtuelle | Ressource de machine virtuelle |
| Sauvegarde de machine virtuelle à la demande | Opérateur de sauvegarde | Ressource du coffre |
| Restaurer une machine virtuelle | Opérateur de sauvegarde | Coffre Recovery Services |
| | Contributeur | Groupe de ressources dans lequel la machine virtuelle sera déployée |
| | Contributeur de machine virtuelle | Machine virtuelle source qui a été sauvegardée |
| Restaurer la sauvegarde de machine virtuelle de disques non managés | Opérateur de sauvegarde | Ressource du coffre |
| | Contributeur de machine virtuelle | Machine virtuelle source qui a été sauvegardée |
| | Contributeur de compte de stockage | Ressource de compte de stockage où les disques vont être restaurés |
| Restaurer des disques managés de la sauvegarde de machine virtuelle | Opérateur de sauvegarde | Ressource du coffre |
| | Contributeur de machine virtuelle | Machine virtuelle source qui a été sauvegardée |
| | Contributeur de compte de stockage | Compte de stockage temporaire sélectionné dans le cadre de la restauration pour conserver les données de l’espace de stockage avant de les convertir en disques managés. |
| | Contributeur | Groupe de ressources vers lequel le(s) disque(s) managé(s) sera (seront) restauré(s) |
| Restaurer des fichiers individuels à partir d’une sauvegarde de machine virtuelle | Opérateur de sauvegarde | Ressource du coffre |
| | Contributeur de machine virtuelle | Machine virtuelle source qui a été sauvegardée |
| Créer une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde | Ressource du coffre |
| Modifier une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde | Ressource du coffre |
| Supprimer une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde | Ressource du coffre |
| Arrêt de la sauvegarde (avec conservation ou suppression des données) lors la sauvegarde d’une machine virtuelle | Contributeur de sauvegarde | Ressource du coffre |
| Inscrire un Windows Server/client/SCDPM ou un serveur de sauvegarde Azure | Opérateur de sauvegarde | Ressource du coffre |
| Supprimer un Windows Server/client/SCDPM inscrit ou un serveur de sauvegarde Azure | Contributeur de sauvegarde | Ressource du coffre |

> [!IMPORTANT]
> Si vous spécifiez VM Contributor dans le cadre d’une ressource de machine virtuelle et cliquez sur Backup dans les paramètres de la machine virtuelle, l’écran 'Enable Backup' (Activer la sauvegarde) s’ouvre même si la VM est déjà sauvegardée car l’appel de vérification de l’état de la sauvegarde fonctionne uniquement au niveau abonnement. Pour éviter cela, allez dans le coffre-fort et ouvrez l’affichage des éléments de sauvegarde de la machine virtuelle ou spécifiez le rôle VM Contributor au niveau de l’abonnement.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Exigences de rôle minimum pour la sauvegarde de partage de fichiers Azure
Le tableau suivant répertorie les actions de gestion des sauvegardes et le rôle nécessaire correspondant pour effectuer cette opération de partage Azure File.

| Opération de gestion | Rôle requis | Ressources |
| --- | --- | --- |
| Activer la sauvegarde d’un partage de fichiers Azure | Contributeur de sauvegarde | Coffre Recovery Services |
| | Compte de stockage | Ressource de compte de stockage de contributeur |
| Sauvegarde de machine virtuelle à la demande | Opérateur de sauvegarde | Coffre Recovery Services |
| Restaurer le partage de fichiers | Opérateur de sauvegarde | Coffre Recovery Services |
| | Contributeur de compte de stockage | Ressources du compte de stockage où les partages de fichiers source et cible de restauration sont présents |
| Restaurer des fichiers individuels | Opérateur de sauvegarde | Coffre Recovery Services |
| | Contributeur de compte de stockage |   Ressources du compte de stockage où les partages de fichiers source et cible de restauration sont présents |
| Arrêter la protection | Contributeur de sauvegarde | Coffre Recovery Services |      
| Annuler l’inscription d’un compte de stockage au coffre |   Contributeur de sauvegarde | Coffre Recovery Services |
| | Contributeur de compte de stockage | Ressource du compte de stockage|


## <a name="next-steps"></a>Étapes suivantes
* [Contrôle d’accès en fonction du rôle](../role-based-access-control/role-assignments-portal.md) : Découvrez comment bien démarrer avec le contrôle d’accès en fonction du rôle dans le portail Azure.
* Découvrez comment gérer l’accès avec :
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [interface de ligne de commande Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Résolution des problèmes de contrôle d’accès en fonction du rôle](../role-based-access-control/troubleshooting.md) : obtenez des suggestions pour résoudre les problèmes courants.
