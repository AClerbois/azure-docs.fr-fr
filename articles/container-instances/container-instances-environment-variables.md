---
title: Définir des variables d’environnement dans Azure Container Instances
description: Découvrez comment définir des variables d’environnement dans les conteneurs que vous exécutez dans Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 4a4b19338d96094f28b4f4bedd8042723f67f10a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994773"
---
# <a name="set-environment-variables-in-container-instances"></a>Définir des variables d’environnement dans les instances de conteneur

Définir des variables d’environnement dans vos instances de conteneur vous permet de fournir une configuration dynamique de l’application ou du script exécuté par le conteneur. Cela revient à définir l’argument de ligne de commande `--env` sur `docker run`. 

Pour définir des variables d’environnement dans un conteneur, spécifiez-les au moment de créer l’instance de conteneur. Cet article présente des exemples de définition des variables d’environnement lorsque vous démarrez un conteneur avec la [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)et le [Azure portal](#azure-portal-example). 

Par exemple, si vous exécutez Microsoft [aci-wordcount] [ aci-wordcount] image de conteneur, vous pouvez modifier son comportement en spécifiant les variables d’environnement suivantes :

*NumWords* : Nombre de mots envoyés à STDOUT.

*MinLength* : Nombre minimal de caractères dans un mot pour que celui-ci soit comptabilisé. Cela vous permet d’ignorer les mots communs tels que « de » et « le ».

Si vous devez transmettre des secrets en tant que variables d’environnement, Azure Container Instances prend en charge des [valeurs sécurisées](#secure-values) pour les conteneurs Windows et Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Exemple Azure CLI

Pour afficher la sortie par défaut de la [aci-wordcount] [ aci-wordcount] conteneur, exécutez tout d’abord avec ce [créer de conteneur az] [ az-container-create] commande (non variables d’environnement spécifiées) :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Pour modifier la sortie, démarrez un deuxième conteneur auquel vous avez ajouté l’argument `--environment-variables`, en spécifiant des valeurs pour les variables *NumWords* et *MinLength*. (Cet exemple suppose que vous exécutez l’interface CLI dans un interpréteur de commandes Bash ou dans Azure Cloud Shell. Si vous utilisez l’invite de commandes Windows, spécifiez les variables à l’aide de guillemets, par exemple `--environment-variables "NumWords"="5" "MinLength"="8"`.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Une fois que l’état de ces deux conteneurs est *Terminé* (utilisez [az container show][az-container-show] pour vérifier l’état), affichez leurs journaux d’activité avec [az container logs][az-container-logs] pour consulter leur contenu.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

La sortie des conteneurs indique la façon dont vous avez modifié le comportement du script du deuxième conteneur en définissant les variables d’environnement.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Exemple Azure PowerShell

La définition de variables d’environnement dans PowerShell est similaire à celle effectuée dans l’interface CLI, à ceci près qu’elle utilise l’argument de ligne de commande `-EnvironmentVariable`.

Tout d’abord, lancez le [aci-wordcount] [ aci-wordcount] conteneur dans sa configuration par défaut avec ce [New-AzContainerGroup] [ new-Azcontainergroup] commande :

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Exécutez maintenant la commande suivante [New-AzContainerGroup] [ new-Azcontainergroup] commande. Celle-ci spécifie les variables d’environnement *NumWords* et *MinLength* après le remplissage de la variable tableau `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Une fois que l’état de ces deux conteneurs est *Terminated* (utilisez [Get-AzContainerInstanceLog] [ azure-instance-log] pour vérifier l’état), extraire leurs journaux avec le [ Get-AzContainerInstanceLog] [ azure-instance-log] commande.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

La sortie de chaque conteneur indique la façon dont vous avez modifié le script exécuté par le conteneur en définissant les variables d’environnement.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Exemple du portail Azure

Pour définir les variables d’environnement lorsque vous démarrez un conteneur dans le portail Azure, indiquez-les dans la **avancé** page lorsque vous créez le conteneur.

1. Sur le **avancé** , définissez le **stratégie de redémarrage** à *en cas d’échec*
2. Sous **variables d’environnement**, entrez `NumWords` avec la valeur `5` pour la première variable, puis entrez `MinLength` avec la valeur `8` pour la deuxième variable. 
1. Sélectionnez **révision + créer** pour vérifier, puis déployer le conteneur.

![Page du portail montrant le bouton d’activation et les zones de texte des variables d’environnement][portal-env-vars-01]

Pour afficher les journaux du conteneur, sous **paramètres** sélectionnez **conteneurs**, puis **journaux**. Comme pour la sortie des sections CLI et PowerShell précédentes, vous voyez que le comportement du script a été modifié par les variables d’environnement. Seuls cinq mots sont affichés, chacun avec une longueur minimale de huit caractères.

![Sortie du journal du conteneur dans le portail][portal-env-vars-02]

## <a name="secure-values"></a>Valeurs sécurisées

Les objets avec des valeurs sécurisées sont destinés à contenir des informations sensibles telles que des mots de passe ou des clés pour votre application. L’utilisation de valeurs sécurisées pour les variables d’environnement est plus sûre et plus flexible que d’inclure ces dernières dans l’image de votre conteneur. Une autre option consiste à utiliser des volumes secrets, décrits dans [Monter un volume secret Azure Container Instances](container-instances-volume-secret.md).

Les variables d’environnement avec des valeurs sécurisées ne sont pas visibles dans les propriétés de votre conteneur, leurs valeurs sont accessibles uniquement à partir du conteneur. Par exemple, les propriétés de conteneur affichées dans le portail Azure ou dans Azure CLI n’affichent pas une variable d’environnement sécurisée, ni sa valeur.

Définissez une variable d’environnement sécurisée en spécifiant la propriété `secureValue` au lieu de la valeur `value` standard pour le type de variable. Les deux variables définies dans le YAML suivant illustrent les deux types de variables.

### <a name="yaml-deployment"></a>Déploiement YAML

Créez un fichier `secure-env.yaml` avec l’extrait de code suivant.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Exécutez la commande suivante pour déployer le groupe de conteneurs avec YAML (ajustez le nom du groupe de ressources en fonction des besoins) :

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Vérifier les variables d’environnement

Exécutez la commande [az container show][az-container-show] pour rechercher les variables d’environnement de votre conteneur :

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

La réponse JSON indique à la fois la clé et la valeur de la variable d’environnement non sécurisée, mais n’indique que le nom de la variable d’environnement sécurisée :

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Avec la commande [az container exec][az-container-exec], qui permet l’exécution d’une commande à partir d’un conteneur en cours d’exécution, vous pouvez vérifier que la variable d’environnement sécurisée est définie . Exécutez la commande suivante pour démarrer une session Bash interactive dans le conteneur :

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Une fois que vous avez ouvert un interpréteur de commandes interactif dans le conteneur, vous pouvez accéder à la valeur de la variable `SECRET` :

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Étapes suivantes

Les scénarios basés sur des tâches, telles que le traitement par lots d’un jeu de données volumineux avec plusieurs conteneurs, peuvent bénéficier de l’utilisation de variables d’environnement personnalisées lors de l’exécution. Pour plus d’informations sur l’exécution des conteneurs basés sur des tâches, consultez [exécuter des tâches en conteneur avec les stratégies de redémarrage](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
