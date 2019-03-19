---
title: Redimensionner le pool de capacités ou un volume pour Azure NetApp Files | Microsoft Docs
description: Décrit comment modifier la taille d’un pool de capacités ou d’un volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: b-juche
ms.openlocfilehash: f08eaee038ad18c600826dea6fe0fd85935de59a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761971"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Redimensionner un pool de capacités ou un volume
Vous pouvez modifier la taille d’un pool de capacités ou d’un volume selon les besoins. 

## <a name="resize-the-capacity-pool"></a>Redimensionner le pool de capacités 

Vous pouvez modifier la taille du pool de capacités par incréments ou décréments de 4 Tio. Le redimensionnement du pool de capacités change la capacité Azure NetApp Files achetée.

1. Dans le panneau Gérer le compte NetApp, cliquez sur le pool de capacités que vous voulez redimensionner. 
2. Cliquez avec le bouton droit sur le nom du pool de capacités ou cliquez sur l’icône « ... » à la fin de la ligne correspondant au pool de capacités pour afficher le menu contextuel. 
3. Utilisez les options du menu contextuel pour redimensionner ou pour supprimer le pool de capacités.

## <a name="resize-a-volume"></a>Redimensionner un volume

Vous pouvez changer la taille d’un volume selon les besoins. La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool.

1. Dans le panneau Gérer le compte NetApp, cliquez sur **Volumes**. 
2. Cliquez avec le bouton droit sur le nom du volume ou cliquez sur l’icône « ... » à la fin de la ligne correspondant au volume pour afficher le menu contextuel.
3. Utilisez les options du menu contextuel pour redimensionner ou pour supprimer le volume.

