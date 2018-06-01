---
title: Publier un élément de Place de Marché personnalisé dans Azure Stack (opérateur cloud) | Microsoft Docs
description: En tant qu’opérateur Azure Stack, découvrez comment publier un élément de Place de Marché personnalisé dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 7b1a6020fb8730aee7ed41d8c82358db0945e4ef
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257640"
---
# <a name="the-azure-stack-marketplace-overview"></a>Vue d’ensemble de Place de Marché Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Marketplace est une collection de services, d’applications et de ressources personnalisées pour Azure Stack. Les ressources incluent des réseaux, des machines virtuelles, des stockages et ainsi de suite. Les utilisateurs s’y rendent pour créer des ressources et déployer de nouvelles applications. Il s’agit en quelque sorte d’un catalogue où les utilisateurs peuvent choisir les éléments qu’ils souhaitent utiliser. Pour utiliser un élément de Place de Marché, les utilisateurs doivent s’abonner à une offre qui leur accorde l’accès à l’élément.

En tant qu’opérateur Azure Stack, c’est vous qui décidez des éléments à ajouter (publier) dans Place de Marché. Vous pouvez publier des éléments tels que des bases de données, App Services, et ainsi de suite. La publication les rend visibles par tous les utilisateurs. Vous pouvez publier des éléments personnalisés que vous créez. Vous pouvez également publier des éléments à partir d’une [liste croissante d’éléments de Place de Marché Azure](azure-stack-marketplace-azure-items.md). Quand vous publiez un élément sur Place de Marché, les utilisateurs peuvent le voir dans les cinq minutes qui suivent.

Pour ouvrir la Place de marché, dans la console d’administration, sélectionnez **Nouveau**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Éléments du Marketplace
Un élément Place de Marché Azure Stack est un service, une application ou une ressource que vos utilisateurs peuvent télécharger et utiliser. Tous les éléments Place de Marché Azure Stack sont visibles par tous les utilisateurs, y compris les éléments d’administration comme les plans et les offres. Ces éléments ne nécessitent pas un abonnement pour les voir, mais ils ne fonctionnent pas avec les utilisateurs.

Chaque élément du Marketplace comporte :

* Modèle Azure Resource Manager pour l’approvisionnement de la ressource
* Les métadonnées telles que les chaînes, les icônes et autres supports marketing
* Des informations de mise en forme permettant d’afficher l’élément dans le portail

Chaque élément publié sur Marketplace utilise un format Azure Gallery Package (.azpkg). Ajoutez les ressources de déploiement ou d’exécution (par exemple du code, des fichiers zip avec des logiciels, ou des images de machine virtuelle) à Azure Stack séparément, et non dans le cadre de l’élément de Place de Marché. 

Avec la version 1803 ou plus, Azure Stack convertit les images en fichiers partiellement alloués lors du téléchargement à partir d’Azure ou lorsque vous téléchargez des images personnalisées. Ce processus ajoute du temps lors de l’ajout d’une image, mais économise de l’espace et accélère le déploiement de ces images. La conversion s’applique uniquement aux nouvelles images.  Les images existantes ne sont pas modifiées. 

## <a name="next-steps"></a>Étapes suivantes
[Télécharger des éléments de la Place de marché](azure-stack-download-azure-marketplace-item.md)  
[Créer et publier un élément de Place de Marché](azure-stack-create-and-publish-marketplace-item.md)

