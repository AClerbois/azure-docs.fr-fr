---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: d86976ad191ffffa343ad7a94b8171759ad102c3
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338355"
---
La galerie d’images partagées est un service qui vous permet de structurer et d’organiser vos images managées. Les galeries d’images partagées proposent les éléments suivants :

- une réplication mondiale et managée des images ;
- la gestion de versions et le regroupement d’images pour faciliter la gestion ;
- des images hautement disponibles avec des comptes ZRS (Zone Redundant Storage) dans les régions qui prennent en charge les zones de disponibilité Azure (le stockage redondant interzone (ZRS) offre une meilleure résilience en cas de défaillance de zone) ;
- le partage entre différents abonnements, voire entre locataires Active Directory, à l’aide du contrôle d’accès en fonction du rôle (RBAC) ;
- la mise à l’échelle de vos déploiements avec des réplicas d’image dans chaque région.

Celle-ci vous permet de partager vos images avec différents utilisateurs, principaux de service ou groupes Active Directory au sein de votre organisation. Il est possible de répliquer des images partagées dans plusieurs régions, pour une mise à l’échelle plus rapide de vos déploiements.

Une image managée est une copie d’une machine virtuelle complète (y compris tous les disques de données attachés) ou simplement du disque de système d’exploitation, selon la façon dont vous créez l’image. Quand vous créez une machine virtuelle à partir de l’image, une copie des disques durs virtuels dans l’image est utilisée pour créer les disques de la nouvelle machine virtuelle. L’image managée reste dans le stockage et peut servir à créer des machines virtuelles à l’infini.

Si vous avez un grand nombre d’images managées à gérer et que vous voulez qu’elles soient disponibles pour toute l’entreprise, vous pouvez utiliser une galerie d’images partagées en tant que référentiel facilitant la mise à jour et le partage de vos images. 

La fonctionnalité Galerie d’images partagées a plusieurs types de ressources :

| Ressource | Description|
|----------|------------|
| **Image managée** | Il s’agit d’une image de base qui peut être utilisée seule ou pour créer une **version de l’image**  dans une galerie d’images. Les images managées sont créées à partir de machines virtuelles généralisées. Une image managée est un type spécial de disque dur virtuel qui peut être utilisé pour définir plusieurs machines virtuelles et qui peut maintenant être utilisé pour créer des versions d’image partagée. |
| **Galerie d’images** | Tout comme la Place de marché Azure, une **galerie d’images** est un dépôt permettant de gérer et partager des images, mais vous contrôlez les utilisateurs qui y ont accès. |
| **Définition d'image** | Les images sont définies dans une galerie et incluent des informations sur l’image et sur les exigences relatives à son utilisation dans votre organisation. Vous pouvez inclure des informations telles que le type de système d’exploitation associé à l’image (Windows ou Linux), les exigences minimales et maximales et des notes de publication. Il s’agit d’une définition de type d’image. |
| **Version de l’image** | Une **version d’image** est ce qui vous permet de créer une machine virtuelle quand vous utilisez une galerie. Vous pouvez avoir plusieurs versions d’une image en fonction des besoins de votre environnement. Tout comme une image managée, quand vous utilisez une **version d’image** pour créer une machine virtuelle, la version d’image permet de créer des disques pour la machine virtuelle. Les versions d’image peuvent être utilisées plusieurs fois. |

<br>


![Graphique montrant comment vous pouvez avoir plusieurs versions d’une image dans la galerie](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Définitions d’image

Une définition d’image est un regroupement logique des versions d’une image. La définition d’image conserve des informations sur la raison pour laquelle l’image a été créée, le système d’exploitation concerné et l’utilisation de l’image. Une définition d’image est similaire à un plan, qui inclut l’ensemble des détails concernant la création d’une image spécifique. Vous ne déployez pas une machine virtuelle à partir d’une définition d’image, mais à partir de la version de l’image créée à partir de la définition.


Il existe trois paramètres pour chaque définition d’image, qui sont utilisés les uns avec les autres : **Publisher**, **Offre** et **SKU**. Ils permettent de rechercher une définition d’image spécifique. Des versions d'image peuvent partager une ou deux de ces valeurs, mais pas les trois.  Par exemple, voici trois définitions d'image et leurs valeurs :

|Définition de l’image|Publisher|Offre|Sku|
|---|---|---|---|
|myImage1|Contoso|Finances|Backend|
|myImage2|Contoso|Finances|Serveur frontal|
|myImage3|Test|Finances|Serveur frontal|

Ces trois définitions présentent des ensembles de valeurs uniques. Le format ressemble à celui qui est utilisé pour spécifier un éditeur (publisher), une offre et une SKU pour des [images Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) dans Azure PowerShell, afin d’obtenir la toute dernière version d’une image d’une Place de marché Microsoft Azure. Chaque définition d’image doit disposer d’un ensemble unique de ces valeurs.

Voici d’autres paramètres qui peuvent être configurés sur votre définition d’image de sorte à faciliter le suivi de vos ressources :

* État de système d’exploitation : vous pouvez définir l’état du système d’exploitation sur la valeur Généralisée ou Spécialisée, mais la première est la seule prise en charge pour l’instant. Les images doivent être créées à partir de machines virtuelles qui ont été généralisées via Sysprep pour Windows ou `waagent -deprovision` pour Linux.
* Système d’exploitation : Windows ou Linux.
* Description : elle vous permet de fournir des informations plus détaillées sur la raison pour laquelle la définition d’image a été créée. Par exemple, vous avez peut-être besoin d’une définition d’image pour le serveur principal sur lequel l’application est préinstallée.
* CLUF : il peut être utilisé pour pointer vers un contrat de licence utilisateur final spécifique à la définition d’image.
* Notes de publication et déclaration de confidentialité : stockez des notes de publication et des déclarations de confidentialité dans le stockage Azure et fournissez un URI permettant d’y accéder dans la définition d’image.
* Date de fin de vie : indiquez une date de fin de vie pour la définition d’image, afin de pouvoir planifier la suppression automatique des définitions les plus anciennes.
* Étiquette : vous pouvez ajouter des étiquettes lorsque vous créez votre définition d’image. Pour en savoir plus sur les étiquettes, voir [Organisation des ressources Azure à l’aide d’étiquettes](../articles/azure-resource-manager/resource-group-using-tags.md).
* Suggestions concernant la quantité maximale et minimale de processeurs virtuels et de mémoire : si votre image est associée à ces types de recommandation, vous pouvez indiquer ces informations dans votre définition d’image.
* Types de disque non autorisés : vous pouvez fournir des informations sur les besoins de votre machine virtuelle en termes de stockage. Par exemple, si l’image n’est pas adaptée aux disques durs standard, vous pouvez les ajouter à la liste de disques non autorisés.


## <a name="regional-support"></a>Prise en charge régionale

Les régions sources sont répertoriées dans la table ci-dessous. Tout les régions publiques peuvent être choisies comme cibles. Cependant, pour que vous puissiez effectuer la réplication dans les régions Australie Centre et Australie Centre 2, votre abonnement doit figurer sur la liste verte. Pour demander la mise en liste verte, accédez à : https://azure.microsoft.com/global-infrastructure/australia/contact/


| Régions sources |
|---------------------|-----------------|------------------|-----------------|
| Centre de l’Australie   | EUAP USA Centre | Centre de la Corée    | Centre-USA Ouest |
| Centre de l’Australie 2 | Asie Est       | Corée du Sud      | Europe Ouest     |
| Australie Est      | USA Est         | Centre-Nord des États-Unis | Inde Ouest      |
| Sud-Australie Est | USA Est 2       | Europe Nord     | USA Ouest         |
| Brésil Sud        | USA Est 2 (EUAP)  | États-Unis - partie centrale méridionale | USA Ouest 2       |
| Centre du Canada      | France Centre  | Inde Sud      | Chine orientale      |
| Est du Canada         | France Sud    | Asie Sud-Est   | Chine orientale 2    |
| Inde centrale       | Japon Est      | Sud du Royaume-Uni         | Chine du Nord     |
| USA Centre          | OuJapon Est      | Ouest du Royaume-Uni          | Chine Nord 2   |



## <a name="limits"></a>limites 

Certaines limites par abonnement ont été définies pour le déploiement de ressources à l’aide des galeries d’images partagées :
- 100 galeries d’images partagées par abonnement et par région
- 1 000 définitions d’images par abonnement et par région
- 10 000 versions d’image par abonnement et par région

Pour en savoir plus, consultez les exemples figurant dans la section [Vérifier l’utilisation des ressources par rapport aux limites](https://docs.microsoft.com/azure/networking/check-usage-against-limits), qui indiquent comment vérifier l’utilisation actuelle.
 

## <a name="scaling"></a>Mise à l'échelle
La galerie d’images partagées vous permet de spécifier le nombre de réplicas qu’Azure doit conserver pour les images. De cette façon, dans les scénarios de déploiement multimachines virtuelles, les déploiements de machines virtuelles peuvent être répartis sur différents réplicas pour réduire le risque de limitation du traitement de création d’instances liée à la surcharge d’un seul réplica.


Avec la galerie d’images partagées, vous pouvez désormais déployer jusqu’à 1 000 instances de machines virtuelles dans un groupe de machines virtuelles identiques (à partir de 600 avec des images managées). Le réplicas d’images permettent d’optimiser les performances d’un déploiement, sa fiabilité et sa cohérence.  Vous pouvez définir un nombre de réplicas différents dans chaque région cible, selon les besoins de mise à l’échelle de la région. Comme chaque réplica est une copie complète de votre image, cela vous permet de mettre à l’échelle vos déploiements de façon linéaire, en fonction de chaque réplica supplémentaire. Nous le savons bien, les images ou régions ne sont jamais les mêmes, mais nous vous invitons à suivre les règles suivantes sur la création de réplicas dans une région :

- Pour les déploiements non-VMSS (Virtual Machine Scale Sets) : pour chaque lot de 20 machines virtuelles que vous créez simultanément, nous vous recommandons de ne garder qu’un réplica. Par exemple, si vous créez 120 machines virtuelles simultanément à l’aide de la même image dans une région, nous vous conseillons de conserver au moins 6 réplicas de votre image. 
- Pour les déploiements VMSS (Virtual Machine Scale Sets) - Pour chaque déploiement de groupe identique avec un maximum de 600 instances, nous vous recommandons de garder au moins un réplica. Ainsi, si vous créez 5 groupes identiques simultanément, chacun doté de 600 instances de VM utilisant la même image dans une seule région, nous vous suggérons de conserver au moins 5 réplicas de votre image. 

De plus, nous recommandons toujours de surapprovisionner le nombre de réplicas, à cause de facteurs tels que la taille des images, le contenu et le type de système d’exploitation.


![Graphique montrant comment mettre à l’échelle des images](./media/shared-image-galleries/scaling.png)



## <a name="make-your-images-highly-available"></a>Rendre votre image hautement disponible

[Azure Zone Redundant Storage (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) fournit une résilience élevée contre une défaillance de la Zone de disponibilité dans la région. Avec la disponibilité générale de la galerie d’images partagées, vous pouvez choisir de stocker vos images dans les comptes ZRS au sein de régions avec des Zones de disponibilité. 

Vous pouvez également choisir le type de compte de chaque région cible. Le type de compte de stockage par défaut est Standard_LRS, mais vous pouvez choisir Standard_ZRS pour les régions avec des Zones de disponibilité. Vérifiez la disponibilité régionale de ZRS [ici](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Graphique affiche ZRS](./media/shared-image-galleries/zrs.png)


## <a name="replication"></a>Réplication
La galerie d’images partagées vous permet aussi de répliquer vos images sur d’autres régions Azure automatiquement. Chaque version de l’image partagée peut être répliquée sur différentes régions en fonction des besoins de votre organisation. Par exemple, vous pouvez répliquer la dernière image dans plusieurs régions tout en gardant les versions plus anciennes disponibles dans une seule région. De cette façon vous économisez sur les coûts de stockage des versions d’image partagée. 

Les régions sur lesquelles est répliquée une version d’image partagée peuvent être mises à jour après la création. La durée de réplication sur différentes régions dépend de la quantité de données copiées et du nombre de régions concernées. La réplication peut prendre plusieurs heures dans certains cas. Pendant le processus, vous pouvez voir l’état de la réplication par région. Une fois la réplication d’image effectuée dans une région, vous pouvez ensuite déployer une machine virtuelle ou un groupe identique à partir de cette version d’image dans la région.

![Graphique montrant comment répliquer des images](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access

Tout comme la Galerie d’images partagées, la définition d’image et la version d’image sont des ressources, qui peuvent être partagées à l’aide des contrôle d’accès en fonction du rôle (RBAC) Azure natifs intégrés. Le contrôle d’accès en fonction du rôle (RBAC) vous permet de partager ces ressources avec d’autres utilisateurs, principaux de service et groupes. Vous pouvez même partager l’accès avec des personnes en dehors du locataire au sein duquel ils ont été créés. Un utilisateur disposant d’un accès à la version d’image partagée peut déployer une machine virtuelle ou un groupe de machines virtuelles identiques.  La matrice de partage suivante vous aide à comprendre les éléments auxquels l’utilisateur a accès :

| Partagé avec l’utilisateur     | Galerie d’images partagées | Définition de l’image | Version d’image |
|----------------------|----------------------|--------------|----------------------|
| Galerie d’images partagées | OUI                  | OUI          | OUI                  |
| Définition de l’image     | Non                   | OUI          | OUI                  |

Nous vous recommandons de partager les images au niveau de la galerie, afin de proposer une expérience optimale. Nous vous déconseillons de partager des versions d’images individuelles. Pour en savoir plus sur le contrôle d’accès en fonction du rôle (RBAC), consultez la section relative à la [gestion de l’accès aux ressources Azure à l’aide du contrôle d’accès en fonction du rôle (RBAC)](../articles/role-based-access-control/role-assignments-portal.md).

Les images peuvent également être partagées à grande échelle, même entre les locataires, via l’inscription d’une application multilocataire. Pour en savoir plus sur le partage d’images entre les locataires, voir [Partager des images de machine virtuelle de la galerie sur les clients Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Facturation
L’utilisation du service Galerie d’images partagées n’engendre aucuns frais supplémentaires. Vous êtes facturé pour les ressources suivantes :
- Coûts de stockage des versions d’Image partagée. Ils dépendent du nombre de réplicas de la version d’image et du nombre de régions dans lesquelles la version est répliquée. Par exemple, si vous avez 2 images et qu’elles sont toutes deux répliquées dans 3 régions, vous serez facturé pour 6 disques managés, en fonction de leur taille. Pour plus d’informations, consultez [Tarification de la fonctionnalité Disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).
- Coûts de sortie de réseau pour la réplication de la première version d’image de la région source vers les régions répliquées. Les réplicas suivants sont gérés au sein de la région, donc aucun coût supplémentaire n’est prévu. 

## <a name="updating-resources"></a>Mise à jour des ressources

Une fois qu’elles sont créées, vous pouvez apporter des modifications aux ressources de la galerie d’images. Ceux-ci sont limités aux éléments suivants :
 
Galerie d’images partagées :
- Description

Définition d’image :
- Processeurs virtuels recommandés
- Mémoire recommandée
- Description
- Date de fin de vie

Version d’image :
- Nombre de réplicas régionaux
- Régions cibles
- Exclure de la plus récente
- Date de fin de vie


## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)

Les SDK suivants prennent en charge la création de galeries d’images partagées :

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.JS](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Modèles

Vous pouvez créer la ressource de galerie d’images partagées à l’aide de modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Créer une définition d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Créer une version d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Créer une machine virtuelle à partir d’une version d’image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 

**Q.** Comment lister toutes les ressources de galerie d’images partagées de différents abonnements ? 
 
 R. Pour lister toutes les ressources de galerie d’images partagées de différents abonnements auxquels vous avez accès sur le portail Azure, suivez les étapes ci-dessous :

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Accédez à **Toutes les ressources**.
1. Sélectionnez tous les abonnements pour lesquels vous voulez lister toutes les ressources.
1. Recherchez des ressources de type **Galerie privée**.
 
   Pour voir les définitions d’image et les versions d’image, vous devez aussi sélectionner **Afficher les types masqués**.
 
   Pour lister toutes les ressources de galerie d’images partagées des différents abonnements sur lesquels vous avez des autorisations, utilisez la commande suivante dans Azure CLI :

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**Q.** Puis-je déplacer mon image existante vers la galerie d’images partagées ?
 
 R. Oui. Il existe 3 scénarios basés sur les types d’images.

 Scénario 1 : Si vous avez une image managée, vous pouvez créer une définition et une version de cette image.

 Scénario 2 : Si vous avez une image généralisée non managée, vous pouvez créer une image managée de l’image, puis créer une définition et une version de cette image. 

 Scénario 3 : Si vous avez un VHD dans votre système de fichiers local, vous devez le charger, puis créer une image managée pour pouvoir ensuite créer une définition et une version de cette image.
- Si le VHD vient d’une machine virtuelle Windows, consultez [Charger un disque dur virtuel généralisé](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Si le VHD est pour une machine virtuelle Linux, consultez [Charger un disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**Q.** Puis-je créer une version d’image à partir d’un disque spécialisé ?

 R. Non, nous ne prenons pas encore en charge les disques spécialisés sous forme d’images. Si vous avez un disque spécialisé, vous devez [créer une machine virtuelle à partir du disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) en attachant le disque spécialisé à une nouvelle machine virtuelle. Une fois la machine virtuelle en cours d’exécution, suivez les instructions pour créer une image managée à partir de la [machine virtuelle Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) ou [machine virtuelle Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Dès que vous avez une image managée généralisée, vous pouvez démarrer le processus de création d’une description et d’une version de l’image partagée.

 
**Q.** Une fois la ressource de galerie d’images partagées créée, puis-je la déplacer vers un autre abonnement ?

 R. Non, vous ne pouvez pas déplacer la ressource de galerie d’images partagées vers un autre abonnement. Toutefois, vous pouvez répliquer les versions d’image dans la galerie vers d’autres régions selon vos besoins.

**Q.** Puis-je répliquer mes versions d’image entre clouds (Azure China 21Vianet, Azure Allemagne et Azure Government Cloud) ? 

 R. Non, vous ne pouvez pas répliquer les versions d’image entre clouds.

**Q.** Puis-je répliquer mes versions d’image entre abonnements ? 

 R. Non, vous pouvez répliquer les versions d’image entre régions dans un abonnement et les utiliser dans d’autres abonnements au moyen de RBAC.

**Q.** Puis-je partager des versions d’image entre locataires Azure AD ? 

 R. Oui, vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) pour partager des images avec des individus se trouvant sur différents locataires. Si vous voulez effectuer un partage à grande échelle, consultez la section relative au partage d’images de galerie sur plusieurs locataires Azure via [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) ou la [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).


**Q.** Combien de temps faut-il pour répliquer des versions d’image sur les régions cibles ?

 R. Le temps de réplication des versions d’image dépend entièrement de la taille de l’image et du nombre de régions cibles. Toutefois, une bonne pratique est de limiter la taille de l’image et de rapprocher les régions source et cible pour obtenir de meilleurs résultats. Vous pouvez vérifier l’état de la réplication à l’aide de l’indicateur -ReplicationStatus.


**Q.** Quelle est la différence entre la région source et la région cible ?

 R. La région source est la région dans laquelle votre version d’image est créée et les régions cibles sont les régions dans lesquelles vous stockez une copie de votre version d’image. Pour chaque version d’image, vous pouvez avoir seulement une région source. Par ailleurs, vérifiez que vous passez l’emplacement de la région source comme l’une des régions cibles quand vous créez une version d’image.  


**Q.** Comment spécifier la région source pendant la création de la version d’image ?

 R. Quand vous créez une version d’image, vous pouvez utiliser l’étiquette **--location** dans l’interface CLI et **-Location** dans PowerShell pour spécifier la région source. Vérifiez que l’image managée que vous utilisez comme image de base pour créer la version d’image est dans le même emplacement que celui dans lequel vous voulez créer la version d’image. Par ailleurs, vérifiez que vous passez l’emplacement de la région source comme l’une des régions cibles quand vous créez une version d’image.  


**Q.** Comment spécifier le nombre de réplicas de version d’image à créer dans chaque région ?

 R. Deux méthodes vous permettent de spécifier le nombre de réplicas de version d’image à créer dans chaque région :
 
1. Le nombre de réplicas régionaux, qui spécifie le nombre de réplicas que vous voulez créer par région. 
2. Le nombre de réplicas communs, qui est le nombre par défaut par région si le nombre de réplicas régionaux n’est pas spécifié. 

Pour spécifier le nombre de réplicas régionaux, passez l’emplacement avec le nombre de réplicas à créer dans cette région : « USA Centre Sud=2». 

Si le nombre de réplicas régionaux n’est pas spécifié avec chaque emplacement, le nombre de réplicas par défaut est le nombre de réplicas communs que vous avez spécifié. 

Pour spécifier le nombre de réplicas communs dans l’interface CLI, utilisez l’argument **--replica-count** dans la commande `az sig image-version create`.


**Q.** Puis-je créer la galerie d’images partagées dans un autre emplacement que celui où je veux créer la définition d’image et la version d’image ?

 R. Oui, vous pouvez. Cependant, nous vous encourageons à conserver au même endroit le groupe de ressources, la galerie d’images partagées, la définition d’image et la version d’image.


**Q.** Quel est le coût d’utilisation de la galerie d’images partagées ?

 R. L’utilisation du service Galerie d’images partagées n’engendre aucun coût, à l’exception des coûts de stockage des versions d’image et des coûts de sortie de réseau pour la réplication des versions d’image de la région source vers les régions cibles.

**Q.** Quelle version d’API utiliser pour créer une galerie d’images partagées, une définition d’image, une version d’image et une machine virtuelle/un groupe de machines virtuelles identiques à partir de la version d’image ?

 R. Pour déployer une machine virtuelle ou un groupe de machines virtuelles identiques à partir d’une version d’image, nous vous recommandons d’utiliser la version d’API 2018-04-01 ou une version ultérieure. Pour utiliser des galeries d’images partagées, des définitions d’image et des versions d’image, nous vous recommandons d’utiliser la version d’API 2018-06-01. ZRS requiert la version 2019-03-01 ou plus.
