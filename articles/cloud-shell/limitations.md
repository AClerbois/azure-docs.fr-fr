---
title: "Limitations d’Azure Cloud Shell (version préliminaire) | Microsoft Docs"
description: "Vue d’ensemble des limites d’Azure Cloud Shell"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 92c8e4c205043f6c5c2925d9197270fb720969a3
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2017
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitations d’Azure Cloud Shell

Azure Cloud Shell a les limitations connues suivantes :

## <a name="general-limitations"></a>Limitations générales

### <a name="system-state-and-persistence"></a>État du système et persistance

La machine qui fournit votre session Cloud Shell est temporaire. En effet, elle est recyclée lorsque votre session reste inactive pendant 20 minutes. Cloud Shell requiert qu’un partage de fichiers soit monté. Par conséquent, votre abonnement doit être en mesure de configurer des ressources de stockage pour accéder à Cloud Shell. Autres éléments à prendre en compte :

* Avec le stockage monté, seules les modifications apportées à votre répertoire `clouddrive` sont conservées. Dans Bash, votre répertoire `$Home` est également conservé.
* Les partages de fichiers peuvent être montés uniquement depuis votre [région affectée](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Dans Bash, exécutez `env` pour rechercher votre région définie en tant que `ACC_LOCATION`.
* Azure Files prend uniquement en charge les comptes de stockage localement redondant et les comptes de stockage géoredondant.

### <a name="browser-support"></a>Prise en charge des navigateurs

Cloud Shell prend en charge les dernières versions de Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox et Apple Safari. Safari en mode privé n’est pas pris en charge.

### <a name="copy-and-paste"></a>Copier et coller

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Pour un utilisateur donné, un seul interpréteur de commandes peut être actif

Les utilisateurs peuvent lancer uniquement un seul type d’interpréteur de commandes à la fois, soit **Bash** soit **PowerShell**. Toutefois, plusieurs instances de Bash ou de PowerShell peuvent s’exécuter simultanément. Le fait de basculer de Bash à PowerShell entraîne le redémarrage de Cloud Shell, ce qui met fin aux sessions existantes.

### <a name="usage-limits"></a>Limites d’utilisation

Cloud Shell est destiné aux cas d’usage interactif. De fait, les sessions non interactives longues se terminent sans avertissement.

## <a name="bash-limitations"></a>Limites de Bash

### <a name="user-permissions"></a>Autorisations utilisateur

Les autorisations sont définies en tant qu’utilisateurs standards sans accès sudo. Les installations en dehors du répertoire `$Home` ne sont pas conservées.
Même si certaines commandes situées dans le répertoire `clouddrive` (par exemple, `git clone`) ne disposent pas des autorisations nécessaires, votre répertoire `$Home`, lui, dispose de ces autorisations.

### <a name="editing-bashrc"></a>Modifier .bashrc

Faites attention lorsque vous modifiez .bashrc : cela peut entraîner des erreurs inattendues dans Cloud Shell.

### <a name="bashhistory"></a>.bash_history

Votre historique de commandes de l’interpréteur de commandes risque d’être incohérent en raison d’interruptions de sessions Cloud Shell ou de sessions simultanées.

## <a name="powershell-limitations"></a>Limites de PowerShell

### <a name="slow-startup-time"></a>Temps de démarrage lent

L’initialisation de PowerShell dans Azure Cloud Shell peut prendre jusqu’à 60 secondes pendant l’aperçu.

### <a name="no-home-directory-persistence"></a>Aucune persistance du répertoire $Home

Les données écrites sur `$Home` par n’importe quelle application (telle que git, vim, etc.) ne sont pas conservées entre les sessions PowerShell. Consultez [ici](troubleshooting.md#powershell-resolutions) la solution de contournement.

## <a name="next-steps"></a>Étapes suivantes

[Dépannage de Cloud Shell](troubleshooting.md) <br>
[Démarrage rapide de Bash](quickstart.md) <br>
[Démarrage rapide de PowerShell](quickstart-powershell.md)
