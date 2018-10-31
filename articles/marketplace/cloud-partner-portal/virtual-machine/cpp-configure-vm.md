---
title: Configurer la machine virtuelle hébergée sur Microsoft Azure pour Place de marché Microsoft Azure | Microsoft Docs
description: Cet article explique comment dimensionner, mettre à jour et généraliser une machine virtuelle hébergée sur Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c1db8c99b1bd3f9bbb768572ca1f5f7a4e1e0de4
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639147"
---
# <a name="configure-the-azure-hosted-vm"></a>Configurer la machine virtuelle hébergée sur Azure

Cet article explique comment dimensionner, mettre à jour et généraliser une machine virtuelle hébergée sur Azure.  Cette procédure est requise pour préparer le déploiement de votre machine virtuelle à partir de la plateforme Place de marché Microsoft Azure.


## <a name="sizing-the-vhds"></a>Dimensionnement des disques durs virtuels (VHD)

<!--TD: Check if the following assertion is true. I didn't understand the original content. --> Si vous avez sélectionné l’une des machines virtuelles préconfigurées avec un système d’exploitation (et éventuellement d’autres services), vous avez déjà choisi une taille de machine virtuelle Azure standard, comme décrit dans l’article [Virtual machine SKUs tab](./cpp-skus-tab.md) (Onglet Références de machine virtuelle).  Il s’agit de l’approche recommandée.  Toutefois, si vous installez un système d’exploitation manuellement, vous devez dimensionner votre VHD principal dans votre image de machine virtuelle :

- Pour Windows, le VHD de système d’exploitation doit être créé en tant que VHD au format fixe de 127-128 Go. 
- Pour Linux, ce VHD doit être créé en tant que VHD au format fixe de 30-50 Go.

Si la taille physique est inférieure à 127-128 Go, le VHD doit être fragmenté. Les images Windows et SQL Server de base fournies satisfont déjà les exigences requises alors, ne modifiez pas le format ou la taille du disque dur virtuel obtenu. 

Les disques de données peuvent avoir une taille maximale de 1 To. Lorsque vous choisissez la taille du disque, rappelez-vous que les clients ne peuvent pas redimensionner les disques durs virtuels dans une image lors du déploiement. Les VHD de données doivent être créés en tant que VHD au format fixe. Ils doivent également être fragmentés. À l’origine, les disques de données peuvent être vides ou contenir des données.


## <a name="install-the-most-current-updates"></a>Installer les dernières mises à jour

Les images de base des machines virtuelles de système d’exploitation contiennent les dernières mises à jour jusqu’à leur date de publication. Avant de publier le VHD de système d’exploitation que vous avez créé, veillez à mettre à jour le système d’exploitation et tous les services installés avec l’ensemble des derniers correctifs de sécurité et de maintenance.

Pour Windows Server 2016, exécutez la commande **Rechercher les mises à jour**.  Dans le cas des versions antérieures de Windows, consultez l’article [Procédure d’obtention d’une mise à jour via Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows Update installe automatiquement les dernières mises à jour de sécurité critiques et importantes.

Pour les distributions Linux, les mises à jour sont couramment téléchargées et installées par le biais d’un outil en ligne de commande ou d’un utilitaire graphique.  Par exemple, Ubuntu Linux fournit la commande [apt-get](http://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) et l’outil [Update Manager](http://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) pour la mise à jour du système d’exploitation.


## <a name="perform-additional-security-checks"></a>Effectuer des vérifications de sécurité supplémentaires

Vous devez maintenir un niveau élevé de sécurité pour vos images de solution dans Place de marché Microsoft Azure.  Pour vous aider à atteindre cet objectif, l’article ci-après vous fournit une check-list de configurations et de procédures de sécurité : [Recommandations de sécurité pour les images Place de marché Microsoft Azure](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Certaines de ces recommandations sont propres aux images basées sur Linux, mais la plupart s’appliquent à n’importe quelle image de machine virtuelle. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Exécuter des tâches planifiées de configuration personnalisée

Si d’autres étapes de configuration sont requises, l’approche recommandée consiste à utiliser une tâche planifiée qui s’exécute au démarrage pour apporter des modifications finales à la machine virtuelle après le déploiement de cette dernière.  Tenez également compte des suggestions suivantes :
- Dans le cas d’une tâche à exécution unique, il est recommandé de faire en sorte que la tâche se supprime elle-même dès qu’elle s’est exécutée.
- Les configurations ne doivent pas utiliser de lecteurs autres que C ou D, car seuls ces deux lecteurs existent à coup sûr. Le lecteur C est le disque du système d’exploitation et le lecteur D est le disque local temporaire.

Pour plus d’informations sur les personnalisations Linux, consultez l’article [Extensions et fonctionnalités de machine virtuelle pour Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Généraliser l’image

Toutes les images dans Azure Marketplace doivent être réutilisables de façon générale. Pour autoriser cette réutilisation, le VHD de système d’exploitation doit être *généralisé* : cette opération consiste à supprimer d’une machine virtuelle tous les pilotes logiciels et identificateurs propres à une instance.

### <a name="windows"></a>Windows

Les disques de système d’exploitation Windows sont généralisés à l’aide de [l’outil sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Si vous mettez à jour ou reconfigurez le système d’exploitation par la suite, vous devrez réexécuter sysprep. 

> [!WARNING]
>  Étant donné que les mises à jour peuvent s’exécuter automatiquement, une fois que vous exécutez sysprep, vous devez éteindre la machine virtuelle jusqu’à son déploiement.  Cet arrêt évite que des mises à jour ultérieures apportent des modifications propres à une instance au système d’exploitation du VHD ou aux services installés.

Pour plus d’informations sur l’exécution de sysprep, consultez la section [Étapes de généralisation d’un disque dur virtuel] (https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#steps-to-generalize-a-vhd).

### <a name="linux"></a>Linux

Ce processus en deux étapes généralise une machine virtuelle Linux et la redéploie sous la forme d’une machine virtuelle distincte.  Pour plus d’informations, consultez l’article [Créer une image d’une machine virtuelle ou d’un disque dur virtuel](../../../virtual-machines/linux/capture-image.md). 

#### <a name="remove-the-azure-linux-agent"></a>Supprimer l’agent Linux Azure
1.  Connectez-vous à votre machine virtuelle Linux en utilisant un client SSH.
2.  Dans la fenêtre SSH, tapez la commande suivante : <br/>
    `sudo waagent -deprovision+user`
3.  Tapez `y` pour continuer. (Vous pouvez ajouter le paramètre `-force` à la commande précédente pour éviter cette étape de confirmation.)
4.  Une fois la commande exécutée, tapez `exit` pour fermer le client SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Capturer l’image
1.  Accédez au Portail Azure, sélectionnez votre groupe de ressources et libérez la machine virtuelle.
2.  Votre VHD est désormais généralisé, et vous pouvez créer une autre machine virtuelle à l’aide de ce VHD.


## <a name="create-one-or-more-copies"></a>Créer une ou plusieurs copies

La création de copies de machine virtuelle est souvent utile pour les tâches de sauvegarde, de test, de basculement personnalisé ou d’équilibrage de charge, pour offrir différentes configurations d’une solution, et ainsi de suite. Pour plus d’informations sur la duplication et le téléchargement d’un VHD principal afin de créer un clone non géré, consultez les articles suivants :

- Machine virtuelle Linux : [Télécharger un disque VHD Linux à partir d’Azure](../../../virtual-machines/linux/download-vhd.md)
- Machine virtuelle Windows : [Télécharger un VHD Windows à partir d’Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Étapes suivantes

Une fois votre machine virtuelle configurée, vous êtes en mesure de [déployer une machine virtuelle à partir d’un disque dur virtuel](./cpp-deploy-vm-vhd.md).
