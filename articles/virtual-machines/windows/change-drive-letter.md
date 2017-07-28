---
title: "Définition du lecteur D: d’une machine virtuelle comme lecteur de données | Microsoft Docs"
description: "Décrit comment modifier les lettres de lecteurs pour une machine virtuelle Windows, afin que vous puissiez utiliser le lecteur D: comme lecteur de données."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 7667175c01be2421bfc3badd83b1d8aaeb29bfde
ms.contentlocale: fr-fr
ms.lasthandoff: 06/01/2017


---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Utilisation du lecteur D comme lecteur de données sur une machine virtuelle Windows
Si votre application doit utiliser le lecteur D pour stocker des données, suivez ces instructions pour utiliser une autre lettre de lecteur pour le disque temporaire. N'utilisez jamais le disque temporaire pour stocker des données à conserver.

Si vous procédez au redimensionnement ou à l’ **arrêt (désallocation)** d’une machine virtuelle, il est possible que cela déclenche le placement de la machine virtuelle sur un nouvel hyperviseur. Ce placement peut également être déclenché par un événement de maintenance planifié ou non planifié. Dans ce scénario, le disque temporaire est réaffecté à la première lettre de lecteur disponible. Si vous avez une application qui nécessite spécifiquement le lecteur D:, vous devez suivre ces étapes pour déplacer temporairement pagefile.sys, associer un nouveau disque de données et lui attribuer la lettre D, puis replacer pagefile.sys sur le disque temporaire. Lorsque vous avez terminé, Azure ne reprend pas le lecteur D: si la machine virtuelle est déplacée vers un autre hyperviseur.

Pour plus d’informations sur l’utilisation du disque temporaire par Azure, consultez [Présentation du lecteur temporaire sur Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Association du disque de données
Tout d'abord, vous devrez attacher le disque de données à la machine virtuelle. Pour ce faire en utilisant le portail, consultez [Attacher un disque de données géré dans le Portail Azure](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Déplacement temporaire du fichier pagefile.sys vers le lecteur C
1. Connectez-vous à la machine virtuelle. 
2. Avec le bouton droit, cliquez sur le menu **Démarrer** et sélectionnez **Système**.
3. Dans le menu de gauche, sélectionnez **Paramètres système avancés**.
4. Dans la section **Performances**, sélectionnez **Paramètres**.
5. Sélectionnez l'onglet **Avancé** .
6. Dans la section **Mémoire virtuelle**, sélectionnez **Modifier**.
7. Sélectionnez le lecteur **C**, puis cliquez sur **Taille gérée par le système** et sur **Définir**.
8. Sélectionnez le lecteur **D**, puis cliquez sur **Aucun fichier d’échange** et sur **Définir**.
9. Cliquez sur Appliquer. Vous serez averti que l'ordinateur doit être redémarré pour que les modifications prennent effet.
10. Redémarrez la machine virtuelle.

## <a name="change-the-drive-letters"></a>Modification des lettres de lecteurs
1. Lorsque la machine virtuelle a redémarré, connectez-vous de nouveau.
2. Cliquez sur le menu **Démarrer**, tapez **diskmgmt.msc** et appuyez sur Entrée. La Gestion des disques démarre.
3. Avec le bouton droit, cliquez sur **D**, le disque de stockage temporaire, puis sélectionnez **Modifier la lettre du lecteur et les chemins d’accès**.
4. Sous la lettre du lecteur, sélectionnez un nouveau lecteur (**T**, par exemple), puis cliquez sur **OK**. 
5. Avec le bouton droit, cliquez sur le disque de données, puis sélectionnez **Modifier la lettre de lecteur et les chemins d'accès**.
6. Sous la lettre de lecteur, sélectionnez le lecteur **D**, puis cliquez sur **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Déplacement de pagefile.sys vers le disque de stockage temporaire
1. Avec le bouton droit, cliquez sur le menu **Démarrer** et sélectionnez **Système**.
2. Dans le menu de gauche, sélectionnez **Paramètres système avancés**.
3. Dans la section **Performances**, sélectionnez **Paramètres**.
4. Sélectionnez l'onglet **Avancé** .
5. Dans la section **Mémoire virtuelle**, sélectionnez **Modifier**.
6. Sélectionnez le disque du système d’exploitation **C**, puis cliquez sur **Aucun fichier d’échange** et sur **Définir**.
7. Sélectionnez le disque de stockage temporaire **T**, puis cliquez sur **Taille gérée par le système** et sur **Définir**.
8. Cliquez sur **Apply**. Vous serez averti que l'ordinateur doit être redémarré pour que les modifications prennent effet.
9. Redémarrez la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez augmenter le stockage disponible pour votre machine virtuelle en y [attachant un disque de données supplémentaire](attach-managed-disk-portal.md).


