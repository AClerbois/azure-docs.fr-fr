---
title: Monter un volume emptyDir dans Azure Container Instances
description: Découvrez comment monter un volume emptyDir pour partager des données entre les conteneurs d’un groupe de conteneurs dans Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 98a72123a05fa7d8dc16be7ddb787f2a2cf7e4d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563118"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Monter un volume emptyDir dans Azure Container Instances

Découvrez comment monter un volume *emptyDir* pour partager des données entre les conteneurs d’un groupe de conteneurs dans Azure Container Instances.

> [!NOTE]
> Le montage d’un volume *emptyDir* est actuellement limité aux conteneurs Linux. Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans [Disponibilité des régions et quotas pour Azure Container Instances](container-instances-quotas.md).

## <a name="emptydir-volume"></a>Volume emptyDir

Le volume *emptyDir* fournit un répertoire accessible en écriture à chaque conteneur d’un groupe de conteneurs. Les conteneurs du groupe peuvent lire et écrire les mêmes fichiers dans le volume qui peut être monté en utilisant des chemins d’accès identiques ou différents dans chaque conteneur.

Exemples d’utilisation d’un volume *emptyDir*:

* Espace scratch
* Points de contrôle au cours de tâches longues
* Stocker des données extraites par un conteneur sidecar et servies par un conteneur d’applications

Les données figurant dans un volume *emptyDir* sont conservées au fil des pannes de conteneur. Toutefois, il n’est pas garanti que les conteneurs redémarrés conservent les données figurant dans un volume *emptyDir*.

## <a name="mount-an-emptydir-volume"></a>Monter un volume emptyDir

Pour monter un volume emptyDir dans une instance de conteneur, vous devez opérer le déploiement à l’aide d’un [modèle Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Tout d’abord, remplissez le tableau `volumes` dans la section `properties` du groupe de conteneurs du modèle. Ensuite, pour chaque conteneur du groupe de conteneurs dans lequel vous souhaitez monter le volume *emptyDir*, remplissez le tableau `volumeMounts` dans la section `properties` de la définition de conteneur.

Par exemple, le modèle Resource Manager suivant crée un groupe de conteneurs consistant en deux conteneurs montant chacun le volume *emptyDir* :

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Pour voir un exemple de déploiement d’instance de conteneur avec un modèle Azure Resource Manager, consultez [Déployer des groupes de plusieurs conteneurs dans Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment monter d’autres types de volumes dans Azure Container Instances :

* [Monter un partage de fichiers Azure dans Azure Container Instances](container-instances-volume-azure-files.md)
* [Monter un volume gitRepo dans Azure Container Instances](container-instances-volume-gitrepo.md)
* [Monter un volume secret dans Azure Container Instances](container-instances-volume-secret.md)