---
title: Didacticiel - Accéder aux objets blob de stockage à l’aide d’un domaine personnalisé Azure CDN via HTTPS | Microsoft Docs
description: ''
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 7aaf4be23c806dda621430c4d1b0c142f41feb1f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090380"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Didacticiel : Accéder aux objets blob de stockage à l’aide d’un domaine personnalisé Azure CDN via HTTPS

Une fois que vous avez intégré votre compte de stockage Azure à Azure Content Delivery Network (CDN), vous pouvez ajouter un domaine personnalisé et activer le protocole HTTPS sur ce domaine pour le point de terminaison de votre stockage d’objets blob personnalisé. 

## <a name="prerequisites"></a>Prérequis

Avant de suivre la procédure indiquée dans ce didacticiel, vous devez intégrer votre compte de stockage Azure à Azure CDN. Pour plus d’informations, consultez le guide de démarrage rapide [Intégrer un compte de stockage Azure à Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Ajouter un domaine personnalisé
Lorsque vous créez un point de terminaison CDN dans votre profil, le nom du point de terminaison, qui est un sous-domaine de azureedge.net, est inclus dans l’URL de diffusion de contenu CDN par défaut. Vous avez également la possibilité d’associer un domaine personnalisé à un point de terminaison CDN. Grâce à cette option, vous distribuez votre contenu avec un domaine personnalisé dans votre URL au lieu d’un nom de point de terminaison. Pour ajouter un domaine personnalisé à votre point de terminaison, suivez les instructions du didacticiel : [Ajouter un domaine personnalisé à votre point de terminaison Azure CDN](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>Configurer le protocole HTTPS
En utilisant le protocole HTTPS sur votre domaine personnalisé, vous vous assurez que vos données sont distribuées sur internet en toute sécurité via le chiffrement TLS/SSL. Lorsque votre navigateur web est connecté à un site web via HTTPS, ce protocole valide le certificat de sécurité du site et vérifie qu’il est fourni par une autorité de certification légitime. Pour configurer le protocole HTTPS sur votre domaine personnalisé, suivez les instructions du didacticiel : [Configurer le protocole HTTPS sur un domaine personnalisé Azure CDN](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Les signatures d’accès partagé
Si le point de terminaison de votre stockage d’objets blob est configuré de sorte que l’accès en lecture anonyme est interdit, vous devez fournir un jeton [Signature d’accès partagé (SAP)](cdn-sas-storage-support.md) pour chaque requête effectuée auprès de votre domaine personnalisé. Par défaut, les points de terminaison de stockage d’objets blob interdisent l’accès en lecture anonyme. Pour en savoir plus sur SAP, consultez la section [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../storage/blobs/storage-manage-access-to-resources.md).

Azure CDN ignore les restrictions ajoutées au jeton SAP. Par exemple, tous les jetons SAP disposent d’un délai d’expiration. Ainsi, le contenu reste accessible avec une SAP qui a expiré tant que le contenu n’est pas vidé des serveurs de point de présence (POP) du CDN. Vous pouvez contrôler la durée de mise en cache sur Azure CDN en définissant l’en-tête de réponse du cache. Pour en savoir plus, consultez la rubrique [Gérer l’expiration des objets blob de stockage Azure CDN](cdn-manage-expiration-of-blob-content.md).

Si vous créez plusieurs URL de SAP pour le même point de terminaison d’objets blob, il convient d’activer la mise en cache de la chaîne de requête. Ce paramètre vous permet de garantir que chaque URL est traitée en tant qu’entité unique. Pour plus d’informations, consultez l’article [Contrôler le comportement de mise en cache d’Azure CDN avec des chaînes de requête](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Redirection HTTP vers HTTPS
Vous avez la possibilité de rediriger le trafic HTTP vers HTTPS en créant une [règle de redirection d’URL](cdn-rules-engine-reference-features.md#url-redirect) à l’aide du [moteur de règles Azure CDN](cdn-rules-engine.md). Pour bénéficier de cette option, vous devez disposer d’un profil **Azure CDN Premium de Verizon**. 

![Règle de redirection d’URL](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

Dans cette règle, *Cdn-endpoint-name* fait référence au nom que vous avez configuré pour le point de terminaison du CDN. Vous pouvez le sélectionner dans la liste déroulante. La valeur du paramètre *origin-path* désigne, au sein de votre compte de stockage d’origine, le chemin où est hébergé votre contenu statique. Si vous hébergez tout le contenu statique dans un seul conteneur, remplacez *origin-path* par le nom de ce conteneur.

## <a name="pricing-and-billing"></a>Tarification et facturation
Lorsque vous accédez à des objets blob via Azure CDN, vous payez [le prix du stockage d’objets Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) pour le trafic entre les serveurs POP et l’origine (stockage d’objets Blob), et [la tarification Azure CDN](https://azure.microsoft.com/pricing/details/cdn/) pour l’accès aux données à partir des serveurs POP.

Par exemple, si vous disposez d’un compte de stockage aux États-Unis, accessible à l’aide d’Azure CDN, et qu’une personne en Europe tente d’accéder à l’un des objets blob dans ce compte de stockage via Azure CDN, Azure CDN vérifie d’abord le serveur POP européen le plus proche pour cet objet blob. S’il le trouve, Azure CDN accède à cette copie de l’objet blob et utilise la tarification CDN, car l’accès se fait via Azure CDN. Dans le cas contraire, Azure CDN copie l’objet blob vers le serveur POP, ce qui entraîne des frais de sortie et de transaction tels que spécifiés dans la tarification du stockage d’objets Blob, et accède ensuite au fichier sur le serveur POP, ce qui entraîne la facturation Azure CDN.

## <a name="next-steps"></a>Étapes suivantes
[Tutoriel : Définir des règles de mise en cache d’Azure CDN](cdn-caching-rules-tutorial.md)




