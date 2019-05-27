---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114563"
---
Les modifications apportées au partage de fichiers Azure avec le portail Azure ou SMB ne sont pas immédiatement détectées et répliquées comme le sont des modifications apportées au point de terminaison de serveur. Azure Files n’a pas encore de notifications ou journalisation des modifications. Il n’existe donc aucun moyen de lancer automatiquement une session de synchronisation lorsque des fichiers sont modifiés. Sur Windows Server, Azure File Sync utilise la [journalisation du nombre de séquences de mise à jour de Windows](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) pour lancer automatiquement une session de synchronisation quand des fichiers changent.<br /><br /> Pour détecter les modifications apportées au partage de fichiers Azure, Azure File Sync a une tâche planifiée appelée *tâche de détection des modifications*. Une tâche de détection des modifications énumère tous les fichiers inclus dans le partage de fichiers, puis les compare à la version de synchronisation de ces fichiers. Lorsque la tâche de détection des modifications détermine que des fichiers ont changé, Azure File Sync lance une session de synchronisation. La tâche de détection des modifications est lancée toutes les 24 heures. Étant donné que la tâche de détection des modifications fonctionne en énumérant chaque fichier dans le partage de fichiers Azure, elle prend plus de temps dans les espaces de noms de grande taille que dans les plus petits. Pour des espaces de noms de grande taille, plus de 24 heures peuvent être nécessaires pour déterminer les fichiers qui ont été modifiés.<br /><br />
Notez que les modifications apportées à un partage de fichiers Azure avec REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles en tant que modifications par synchronisation. <br /><br />
Nous étudions la possibilité d’ajouter la détection des modifications pour un partage de fichiers Azure de la même façon que le nombre de séquences de mise à jour (USN) pour les volumes sur Windows Server. Aidez-nous à faire passer le développement de cette fonctionnalité en priorité en votant pour celle-ci via la page [UserVoice d’Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files).
