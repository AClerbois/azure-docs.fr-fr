---
title: Erreur de démarrage de machine virtuelle Azure
description: Le démarrage de la machine virtuelle a échoué, car celle-ci est entrée dans une console de secours
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: ''
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 14bf7b289f3d03dd1437a18b0b5bd35fdec567ea
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143160"
---
# <a name="vm-boot-error"></a>Erreur de démarrage de machine virtuelle

Nous avons détecté que votre machine virtuelle est entrée dans une console de secours. Ce problème se produit lorsque le noyau de votre machine virtuelle Linux a été modifié (lors d’une mise à niveau du noyau, par exemple), empêchant la machine virtuelle de démarrer correctement en raison d’erreurs qui se produisent au niveau du noyau lors du démarrage. Au cours du processus de démarrage, lorsque le chargeur de démarrage tente de localiser le noyau Linux pour lui transférer le contrôle du démarrage, la machine virtuelle entre dans une console de secours si le transfert échoue.

Si vous constatez que vous ne pouvez plus vous connecter à une machine virtuelle, vous pouvez afficher une capture d’écran de votre machine virtuelle en utilisant le panneau des diagnostics de démarrage dans le portail Azure. Cela peut vous aider à diagnostiquer le problème et à déterminer si une erreur de démarrage similaire en est la cause.

## <a name="recommended-steps"></a>Étapes recommandées

Suivez les étapes d’atténuation ci-dessous en fonction de l’erreur qui s’affiche :

### <a name="error---unknown-filesystem"></a>Erreur : Unknown filesystem (Système de fichiers inconnu)

* Si vous obtenez l’erreur **Système de fichiers inconnu**, cela peut être dû à la corruption du système de fichiers de la partition de démarrage ou à une configuration incorrecte du noyau.

   * En cas de problèmes liés au système de fichiers, suivez les étapes de l’article [Linux Recovery: Cannot SSH to Linux VM due to file system errors (fsck, inodes)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * En cas de problèmes liés au noyau, suivez les étapes de l’article [Linux Recovery: Manually fixing non-boot issues related to Kernel problems](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/) ou [Linux Recovery: Fixing non-boot issues related to Kernel problems using chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Erreur : File not found (Fichier introuvable)

* Si les messages **Error 15: File not found or initial RAM disk** (Fichier ou disque virtuel initial introuvable) ou **initrd/initramfs file not found** (Fichier initrd/initramfs introuvable) s’affichent, suivez les étapes ci-dessous.

    * Si le fichier manquant est `/boot/grub2/grub.cfg` ou `initrd/initramfs`, suivez la procédure ci-dessous :

    1. Vérifiez que `/etc/default/grub` existe et qu’il est paramétré selon vos besoins. Si vous ne connaissez pas les paramètres par défaut, vous pouvez les vérifier sur une machine virtuelle en cours d’exécution.

    2. Ensuite, exécutez la commande suivante pour regénérer sa configuration : `grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Si le fichier manquant est `/boot/grub/menu.lst`, cette erreur concerne les versions de système d’exploitation plus anciennes (**RHEL 6.x**, **Centos 6.x** et **Ubuntu 14.04**). Les commandes peuvent donc être différentes. Vous devrez exécuter un ancien serveur et vérifier à l’aide de tests si les commandes nécessaires sont fournies.

### <a name="error---no-such-partition"></a>Erreur : No such partition (Cette partition n’existe pas)

* Si l’erreur **No such partition** (Cette partition n’existe pas) s’affiche, consultez [Case Scenario : "no such partition" error while trying to start the VM after attempting to extend the OS drive](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Erreur : grub.cfg file not found (Fichier grub.cfg introuvable)

* Si l’erreur **/boot/grub2/grub.cfg file not found** (Fichier /boot/grub2/grub.cfg introuvable) s’affiche, suivez les étapes ci-dessous.

    * Si le fichier manquant est `/boot/grub2/grub.cfg` ou `initrd/initramfs`, suivez la procédure ci-dessous :

    1. Vérifiez que `/etc/default/grub` existe et qu’il est paramétré selon vos besoins. Si vous ne connaissez pas les paramètres par défaut, vous pouvez les vérifier sur une machine virtuelle en cours d’exécution.

    2. Ensuite, exécutez la commande suivante pour regénérer sa configuration : `grub2-mkconfig -o /boot/grub2/grub.cfg`.

   * Si le fichier manquant est `/boot/grub/menu.lst`, cette erreur concerne les versions de système d’exploitation plus anciennes (**RHEL 6.x**, **Centos 6.x** et **Ubuntu 14.04**). Les commandes peuvent donc être différentes. Exécutez un ancien serveur et vérifiez à l’aide de tests si les commandes nécessaires sont fournies.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble d’agent de machine virtuelle Azure](../extensions/agent-windows.md)
* [Extensions et fonctionnalités de machine virtuelle pour Windows](../extensions/features-windows.md)

