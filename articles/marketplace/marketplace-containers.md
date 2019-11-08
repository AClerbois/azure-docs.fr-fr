---
title: Guide de publication d’offres de conteneurs pour la Place de marché Azure
description: Cet article décrit les conditions requises pour publier des conteneurs sur la place de marché
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: ffdc6b22209c1d334ac7b75c7079e755a7405154
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822989"
---
# <a name="containers-offer-publishing-guide"></a>Guide de publication d’offres de conteneurs

Les offres de conteneur vous aident à publier votre image de conteneur sur la Place de marché Azure. Utilisez ce guide pour comprendre les exigences propres à cette offre. 

Il s’agit d’offres de transaction qui sont déployées et facturées via la place de marché. L’appel à l’action qu’un utilisateur voit est « Obtenir maintenant ».

Utilisez le type d’offre Conteneur si votre solution est une image de conteneur Docker approvisionnée en tant que service de conteneur Azure Kubernetes.

>[!NOTE]
>Par exemple, un service de conteneur Azure basé sur Kubernetes tel qu’Azure Kubernetes Service ou Azure Container Instances, le choix des clients Azure pour un runtime de conteneur basé sur Kubernetes.  

Microsoft prend actuellement en charge les modèles de licence BYOL (apportez votre propre licence) et gratuits.

## <a name="containers-offer"></a>Offre de conteneurs

| Prérequis | Détails |  
|:--- |:--- |  
| Facturation et mesure | Prise en charge du modèle de facturation gratuit ou BYOL. |  
| Image créée à partir du fichier Dockerfile | Les images de conteneur doivent être basées sur la spécification d’image Docker et doivent être générées à partir d’un fichier Dockerfile.<ul> <li>Pour plus d'informations sur la création d’images Docker, visitez la section Utilisation à l’adresse [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hébergement dans ACR | Les images de conteneur doivent être hébergées dans un Azure Container Registry (ACR).<ul> <li>Pour plus d’informations sur l’utilisation d’ACR, consultez le guide de démarrage rapide : Créer un registre de conteneurs à l’aide du portail Azure à l’adresse [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Balisage d’images | Les images conteneur doivent contenir au moins 1 étiquette (nombre maximal d’étiquettes : 16).<ul> <li>Pour plus d'informations sur le balisage d’une image, visitez la page de balise Docker à l’adresse [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, 

- [Inscrivez-vous](https://azuremarketplace.microsoft.com/sell) sur la place de marché.

Si vous êtes inscrit et créez une nouvelle offre ou travaillez sur une offre existante,

- [Connectez-vous au portail Cloud Partner](https://cloudpartner.azure.com) pour créer ou terminer votre offre.
- Pour plus d’informations, consultez [Conteneurs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer).
