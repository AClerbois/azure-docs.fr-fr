---
title: Comment utiliser un référentiel Helm privé dans Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Utilisez un référentiel Helm privé dans un espace Azure Dev.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, conteneurs, Helm
manager: gwallace
ms.openlocfilehash: d0f2cb739a502d614ac329eef1fc57b2fb15cb38
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014276"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Utiliser un référentiel Helm privé dans Azure Dev Spaces

[Helm][helm] est un gestionnaire de packages pour Kubernetes. Helm utilise un format [chart][helm-chart] pour empaqueter les dépendances. Les graphiques Helm sont stockés dans un référentiel, qui peut être public ou privé. Azure Dev Spaces récupère uniquement les graphiques Helm auprès des référentiels publics lors de l’exécution de votre application. Dans les cas où le référentiel Helm est privé ou Azure Dev Spaces ne peut pas y accéder, vous pouvez ajouter un graphique à partir de ce référentiel directement à votre application. L’ajout du graphique permet à Azure Dev Spaces d’exécuter votre application sans avoir à accéder au référentiel Helm privé.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Ajouter le référentiel Helm privé à votre ordinateur local

Utilisez [helm repo add][helm-repo-add] et [helm repo update][helm-repo-update] pour accéder au référentiel Helm privé à partir de votre ordinateur local.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Ajouter le graphique à votre application

Accédez au répertoire de votre projet, puis exécutez `azds prep`.

```cmd
azds prep --public
```

Créez un fichier [requirements.yaml][helm-requirements] avec votre graphique dans le répertoire des graphiques de votre application. Par exemple, si votre application est nommée *app1*, vous devez créer *charts/app1/requirements.yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Naviguez jusqu’au répertoire des graphiques de votre application et utilisez [helm dependency update][helm-dependency-update] pour mettre à jour les dépendances Helm pour votre application et télécharger le graphique depuis le référentiel privé.

```cmd
helm dependency update
```

Vérifiez qu’un sous-répertoire *charts* contenant un fichier *tgz* a été ajouté dans le répertoire des graphiques de votre application. Par exemple, *charts/app1/charts/mychart-0.1.0.tgz*.

Le graphique de votre référentiel Helm privé a été téléchargé et ajouté à votre projet. Supprimez le fichier *requirements.yaml* afin que Dev Spaces n’essaie pas de mettre à jour cette dépendance.

## <a name="run-your-application"></a>Exécuter votre application

Naviguez jusqu’au répertoire racine de votre projet et exécutez `azds up` pour vérifier que votre application fonctionne correctement dans votre espace de développement.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [Helm et son fonctionnement][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/developing_charts/
[helm-dependency-update]: https://helm.sh/docs/helm/#helm-dependency-update
[helm-repo-add]: https://helm.sh/docs/helm/#helm-repo-add
[helm-repo-update]: https://helm.sh/docs/helm/#helm-repo-update
[helm-requirements]: https://helm.sh/docs/developing_charts/#chart-dependencies