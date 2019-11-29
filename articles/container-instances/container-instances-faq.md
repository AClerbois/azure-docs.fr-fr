---
title: Questions fréquentes (FAQ)
description: Réponses aux questions fréquemment posées sur le service Azure Container Instances
author: dkkapur
ms.topic: article
ms.date: 4/25/2019
ms.openlocfilehash: b5888efe210ab0f3794895d350c5647b6f685880
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484133"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Forum aux questions sur Azure Container Instances

Cet article aborde les questions fréquemment posées sur Azure Container Instances.

## <a name="deployment"></a>Déploiement

### <a name="how-large-can-my-container-image-be"></a>Quelle peut être la taille maximale d’une image de conteneur ?

La taille maximale d’une image de conteneur pouvant être déployée sur Azure Container Instances est de 15 Go. Vous devriez être en mesure de déployer des images de grande taille en fonction de la disponibilité exacte au moment du déploiement, mais cela n’est pas garanti.

La taille de votre image de conteneur a un impact sur la durée du déploiement. En règle générale, essayez de faire en sorte que vos images de conteneur soient aussi petites que possible.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Comment accélérer le déploiement de mon conteneur ?

Étant donné qu’un des principaux facteurs déterminants pour la durée du déploiement est la taille de l’image, essayez par tous les moyens de réduire sa taille. Supprimez les calques dont vous n’avez besoin ou réduisez la taille des calques de l’image (en choisissant une image de système d’exploitation de base plus légère). Par exemple, si vous exécutez des conteneurs Linux, envisagez d’utiliser Alpine comme image de base au lieu d’un serveur Ubuntu complet. De même, pour les conteneurs Windows, utilisez une image de base Nano Server si possible. 

Vous devez également vérifier la liste des images préalablement mises en cache dans Azure Container Images, disponible via l’[API répertoriant les images mises en cache](/rest/api/container-instances/listcachedimages). Vous pouvez éventuellement retirer un calque de l’une des images préalablement mises en cache. 

Retrouvez des [conseils détaillés](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) sur la réduction du temps de démarrage des conteneurs.

### <a name="what-windows-base-os-images-are-supported"></a>Quelles images de système d’exploitation de base Windows sont prises en charge ?

#### <a name="windows-server-2016-base-images"></a>Images de base Windows Server 2016

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver) : `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore) : `ltsc2016`, `10.0.14393.x`

> [!NOTE]
> Les images de Windows basées sur canal semi-annuel version 1709 ou 1803 ne sont pas prises en charge.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Images de base Windows Server 2019 et client (préversion)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver) : `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore) : `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows) : `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Quels calques d’image .NET ou .NET Core dois-je utiliser dans mon conteneur ? 

Utiliser l’image la plus petite qui répond à vos besoins. Pour Linux, vous pouvez utiliser une image .NET Core *runtime-alpine*, prise en charge depuis la version 2.1 de .NET Core. Pour Windows, si vous utilisez le .NET Framework complet, vous devez utiliser une image Windows Server Core (image runtime, comme *4.7.2-windowsservercore-ltsc2016*). Les images runtime sont plus petites, mais ne prennent pas en charge les charges de travail qui nécessitent le SDK .NET.

## <a name="availability-and-quotas"></a>Disponibilité et quotas

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Combien cœurs et de mémoire dois-je allouer à mes conteneurs ou au groupe de conteneurs ?

Cela dépend vraiment de votre charge de travail. Commencez petit et testez les performances pour voir comment vos conteneurs évoluent. [Surveillez l’utilisation des ressources d’UC et de mémoire](container-instances-monitor.md), puis ajoutez des cœurs ou de la mémoire en fonction du type de processus que vous déployez dans le conteneur. 

Veillez également à vérifier la [disponibilité des ressources](container-instances-region-availability.md#availability---general) pour la région dans laquelle vous effectuez le déploiement, ainsi que les limites supérieures sur les cœurs d’UC et la mémoire disponibles par groupe de conteneurs. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Sur quelle infrastructure sous-jacente ACI s’exécute-t-il ?

Azure Container Instances est conçu comme un service de conteneurs à la demande serverless, afin que vous puissiez vous concentrer sur le développement de vos conteneurs sans vous inquiéter de l’infrastructure ! Si vous êtes curieux ou souhaitez comparer les performances, ACI s’exécute sur les ensembles de machines virtuelles Azure de différentes références SKU, principalement des gammes F et D. Cela va probablement changer à l’avenir, car nous continuons à développer et à optimiser le service. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Je souhaite déployer des milliers de cœurs sur ACI. Puis-je augmenter le quota ?
 
Oui, sous certaines conditions. Consultez l’article sur [les quotas et les limites](container-instances-quotas.md) pour connaître les quotas actuels et les limites qui peuvent être augmentées par demande.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Puis-je effectuer un déploiement avec plus de 4 cœurs et 16 Go de RAM ?

Pas encore. Actuellement, ce sont les valeurs maximales pour un groupe de conteneurs. Contactez le support Azure si vous avez des exigences ou des demandes spécifiques. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quand ACI sera-t-il disponible dans une région spécifique ?

La disponibilité actuelle dans les différentes régions est publiée [ici](container-instances-region-availability.md#availability---general). Si vous en avez besoin dans une région spécifique, contactez le support Azure.

## <a name="features-and-scenarios"></a>Fonctionnalités et scénarios

### <a name="how-do-i-scale-a-container-group"></a>Comment pour mettre à l’échelle un groupe de conteneurs ?

Actuellement, la mise à l’échelle n’est pas disponible pour les conteneurs ou les groupes de conteneurs. Si vous avez besoin d’exécuter plus d’instances, utilisez notre API pour automatiser et créer plus de demandes de création de groupe conteneurs pour le service. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Quelles sont les fonctionnalités disponibles pour les instances en cours d’exécution dans un réseau virtuel personnalisé ?

Vous pouvez déployer des groupes de conteneurs dans un réseau virtuel Azure de votre choix et déléguer des adresses IP privées aux groupes de conteneurs pour acheminer le trafic au sein du réseau virtuel dans vos ressources Azure. Le déploiement d’un groupe de conteneurs dans un réseau virtuel est actuellement préversion, et certains aspects de cette fonctionnalité peuvent changer avant qu’elle soit mise à disposition de tous. Consultez les [limitations de la préversion](container-instances-vnet.md#preview-limitations) pour obtenir des informations mises à jour.

## <a name="pricing"></a>Tarifs

### <a name="when-does-the-meter-start-running"></a>Quand le compteur commence-t-il à tourner ?

La durée du groupe de conteneurs est calculée à partir du moment où nous commencerons à extraire l’image de votre premier conteneur (pour un nouveau déploiement) ou du moment où votre groupe de conteneurs est redémarré (s’il est déjà déployé), jusqu’à l’arrêt du groupe de conteneurs. Retrouvez plus de détails dans la section [Tarification Container Instances](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>La facturation cesse-t-elle lorsque mes conteneurs sont arrêtés ?

Les compteurs s’arrêtent dès que votre groupe de conteneurs est entièrement arrêté. Tant qu’un conteneur de votre groupe de conteneurs est en cours d’exécution, nous maintenons les ressources au cas où vous souhaiteriez démarrer à nouveau les conteneurs. 

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus](container-instances-overview.md) sur Azure Container Instances.
* [Résoudre les problèmes courants](container-instances-troubleshooting.md) dans Azure Container Instances.