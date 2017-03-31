---
title: "Créer une machine virtuelle Linux Classic à l’aide d’Azure CLI 1.0 | Microsoft Docs"
description: "Apprenez à créer une machine virtuelle Linux avec Azure CLI 1.0, à l’aide du modèle de déploiement Classic."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: c4d29c453d22a2765f0884c11d98d84676b3bf32
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Comment créer une machine virtuelle Linux Classic à l’aide d’Azure CLI 1.0
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour la version de Resource Manager, suivi [ce lien](../../virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Cette rubrique décrit comment créer une machine virtuelle Linux avec Azure CLI 1.0 à l’aide du modèle de déploiement Classic. Nous utilisons une image Linux provenant des **IMAGES** disponibles sur Azure. Les commandes d’Azure CLI 1.0 proposent notamment les choix de configuration suivants :

* Connexion de la machine virtuelle à un réseau virtuel
* Ajout de la machine virtuelle à un service cloud existant
* Ajout de la machine virtuelle à un compte de stockage existant
* Ajout de la machine virtuelle à un groupe à haute disponibilité

> [!IMPORTANT]
> Si vous souhaitez que la machine virtuelle utilise un réseau virtuel pour pouvoir vous y connecter directement par nom d’hôte ou configurer des connexions entre différents locaux, veillez à spécifier le réseau virtuel lors de la création de la machine virtuelle. Vous pouvez configurer une machine virtuelle pour qu'elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Création d’une machine virtuelle Linux à l’aide du modèle de déploiement Classic
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]


