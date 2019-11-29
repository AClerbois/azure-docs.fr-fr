---
title: Vue d’ensemble d’ACR Tasks
description: Introduction à ACR Tasks, une suite de fonctionnalités d’Azure Container Registry qui permet la création ; la gestion et la mise à jour corrective d’images de conteneur sécurisées et automatisées dans le cloud.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: b4710591dfd78f0633d5071c78d80e300349f498
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456161"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatiser la création et la maintenance des images de conteneur avec ACR Tasks

Les conteneurs fournissent de nouveaux niveaux de virtualisation en isolant les dépendances d’application et de développement des spécifications opérationnelles et d’infrastructure. Ce qui demeure, toutefois, est la nécessité de gérer les mises à jour correctives et la gestion de cette virtualisation d’application sur le cycle de vie du conteneur.

## <a name="what-is-acr-tasks"></a>Qu’est-ce qu’ACR Tasks ?

**ACR Tasks** est une suite de fonctionnalités d’Azure Container Registry. Elle fournit la création d’image conteneur informatique pour les [plateformes](#image-platforms), notamment Linux, Windows et ARM, et peut automatiser la [mise à jour corrective du framework et du système d’exploitation](#automate-os-and-framework-patching) pour vos conteneurs Docker. Non seulement ACR Tasks étend votre cycle de développement « en boucle interne » au cloud avec des builds d’image de conteneur à la demande, mais il permet également de créer des builds automatisées déclenchés par les mises à jour du code source, les mises à jour de l’image de base d’un conteneur ou les minuteurs. Par exemple, avec les déclencheurs de mise à jour d’image de base, vous pouvez automatiser votre flux de travail de mise à jour corrective du système d’exploitation et du framework d’application tout en garantissant la sécurité de l’environnement et en respectant les critères fondamentaux des conteneurs immuables.

## <a name="task-scenarios"></a>Scénarios de tâches

ACR Tasks prend en charge plusieurs scénarios pour créer et gérer des images de conteneur et d’autres artefacts. Pour plus d’informations, consultez les sections suivantes de cet article.

* **[Tâche rapide](#quick-task)** : générer et envoyer (push) une seule image de conteneur vers un registre de conteneurs à la demande dans Azure, sans avoir besoin d’une installation locale de Docker Engine. Pensez `docker build`, `docker push` dans le cloud.
* **Tâches déclenchées automatiquement** : activez un ou plusieurs *déclencheurs* pour générer une image :
  * **[Déclencher lors de la mise à jour du code source](#trigger-task-on-source-code-update)** 
  * **[Déclencher lors de la mise à jour de l’image de base](#automate-os-and-framework-patching)** 
  * **[Déclencher selon une planification](#schedule-a-task)** 
* **[Tâches multiétapes](#multi-step-tasks)** : renforcent la capacité d’ACR Tasks à compiler et envoyer des images en une fois grâce à des flux de travail à plusieurs étapes avec plusieurs conteneurs. 

Chaque tâche ACR Tasks a un [contexte de code source](#context-locations) associé : l’emplacement d’un ensemble de fichiers sources utilisés pour générer une image conteneur ou un autre artefact. Les exemples de contextes incluent un référentiel Git ou un système de fichiers local.

Les tâches peuvent également tirer parti des [variables d’exécution](container-registry-tasks-reference-yaml.md#run-variables), de sorte que vous pouvez réutiliser les définitions de tâches et normaliser les balises pour les images et les artefacts.

## <a name="quick-task"></a>Tâche rapide

Le cycle de développement « en boucle interne », c’est-à-dire le processus itératif d’écriture de code, de génération de build et de test de votre application avant de valider le code source, correspond au début de la gestion du cycle de vie des conteneurs.

Avant de valider votre première ligne de code, les fonctionnalité [tâche rapide](container-registry-tutorial-quick-task.md) d’ACR Tasks peut fournir un environnement de développement intégré en déchargeant vos builds d’image de conteneur dans Azure. Avec les tâches rapides, vous pouvez vérifier vos définitions de build automatisées et identifier les problèmes potentiels avant de valider votre code.

En utilisant le format `docker build` bien connu, la commande [az acr build][az-acr-build] dans Azure CLI prend un [contexte](#context-locations) (ensemble de fichiers à générer), l’envoie à ACR Tasks et, par défaut, envoie (push) l’image générée à son registre lors de son achèvement.

Pour une introduction, consultez le démarrage rapide pour [générer et exécuter une image conteneur](container-registry-quickstart-task-cli.md) dans Azure Container Registry.  

ACR Tasks est conçu comme un précurseur du cycle de vie de conteneurs. Par exemple, intégrez ACR Tasks dans votre solution CI/CD. En exécutant [az login][az-login] avec un [principal de service][az-login-service-principal], votre solution CI/CD pourra alors émettre des commandes [az acr build][az-acr-build] pour lancer des générations d’image.

Découvrez comment utiliser les tâches rapides dans le premier didacticiel d’ACR Tasks, [Générer des images de conteneur dans le cloud avec Azure Container Registry Tasks](container-registry-tutorial-quick-task.md).

> [!TIP]
> Si vous souhaitez créer et envoyer (push) une image directement à partir du code source, sans fichier Dockerfile, Azure Container Registry fournit la commande [az acr pack build][az-acr-pack-build] (préversion). Cet outil crée et envoie (push) une image à partir du code source de l’application à l’aide de [Cloud Native Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Déclencher la tâche lors de la mise à jour du code source

Déclenchez la création d’une image conteneur ou une tâche multiétapes lors de la validation du code, ou lorsqu’une requête de tirage (pull) est effectuée ou mise à jour, sur un référentiel Git dans GitHub ou Azure DevOps. Par exemple, configurez une tâche de création avec la commande Azure CLI [az acr task create][az-acr-task-create] en spécifiant un référentiel Git et éventuellement une branche et un Dockerfile. Lorsque votre équipe met à jour du code dans le référentiel, un webhook créé par ACR Tasks déclenche la génération de l’image de conteneur définie dans le référentiel. 

ACR Tasks prend en charge les déclencheurs suivants lorsque vous définissez un référentiel Git comme contexte de la tâche :

| Déclencheur | Activée par défaut |
| ------- | ------------------ |
| Validation | OUI |
| Demande de tirage (pull request) | Non |

Pour configurer le déclencheur, vous fournissez à la tâche un jeton d’accès personnel (PAT) pour définir le webhook dans le référentiel GitHub ou Azure DevOps.

Découvrez comment déclencher la génération de builds lors de la validation du code source dans le deuxième didacticiel d’ACR Tasks, [Automatiser les générations d’image de conteneur avec Azure Container Registry Tasks](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatiser les mises à jour correctives du système d’exploitation et du framework

L’aptitude d’ACR Tasks à améliorer réellement votre pipeline de génération de conteneur provient de sa capacité à détecter la mise à jour d’une image de base. Quand l’image de base mise à jour est envoyée à votre registre, ou qu’une image de base est mise à dans un référentiel public comme dans Docker Hub, ACR Tasks peut générer automatiquement des images d’application basées sur elle.

Les images de conteneur appartiennent en gros à deux catégories : les images de *base* et les images d’*application*. En règle générale, vos images de base incluent les frameworks d’application et de système d’exploitation sur lesquels votre application est générée, ainsi que d’autres personnalisations. Ces images de base sont elles-mêmes généralement basées sur des images publiques en amont, par exemple : [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet] ou [Node.js][base-node]. Plusieurs de vos images d’application peuvent partager une image de base commune.

Quand une image de framework d’application ou de système d’exploitation est mise à jour par le chargé de maintenance en amont, par exemple avec un correctif de sécurité critique du système d’exploitation, vous devez également mettre à jour vos images de base pour qu’elles contiennent ce correctif critique. Chaque image d’application doit ensuite être également recréée pour inclure ces correctifs en amont maintenant inclus dans votre image de base.

Comme ACR Tasks détecte de manière dynamique les dépendances de l’image de base quand il génère une image de conteneur, il peut savoir quand l’image de base d’une image d’application est mise à jour. Avec une [tâche de build](container-registry-tutorial-base-image-update.md#create-a-task) préconfigurée, ACR Tasks **regénère automatiquement chaque image d’application** pour vous. Grâce à ces détection et regénération automatiques, ACR Tasks vous permet d’économiser le temps et les efforts normalement nécessaires au suivi et à la mise à jour manuels de chaque image d’application faisant référence à votre image de base mise à jour.

Pour les créations d’images à partir d’un Dockerfile, une tâche ACR effectue le suivi d’une mise à jour d’image de base lorsque l’image de base se trouve dans l’un des emplacements suivants :

* Le registre de conteneurs Azure dans lequel s’exécute la tâche
* Un autre registre de conteneurs Azure dans la même région 
* Un référentiel public dans Docker Hub
* Un référentiel public dans Microsoft Container Registry

> [!NOTE]
> * Le déclencheur de mise à jour d’image de base est activé par défaut dans une tâche ACR. 
> * Actuellement, ACR Tasks effectue uniquement le suivi des mises à jour des images de base pour les images d’application (*runtime*). ACR Tasks n’effectue pas le suivi des mises à jour des images de base pour les images intermédiaires (*au moment de la génération*) utilisées dans des Dockerfiles multiétapes. 

Pour en savoir plus sur la mise à jour corrective du système d’exploitation et du framework, consultez le troisième didacticiel d’ACR Tasks, [Automatiser la génération des images en fonction de la mise à jour d’une image de base avec Azure Container Registry Tasks](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>Planifier une tâche

Planifiez éventuellement une tâche en définissant un ou plusieurs *déclencheurs de minuteur* lorsque vous créez ou mettez à jour la tâche. La planification d’une tâche est utile pour exécuter des charges de travail de conteneur selon une planification définie, ou pour exécuter des opérations de maintenance ou des tests sur des images transmises régulièrement à votre registre. Pour plus d’informations, consultez [Exécuter une tâche ACR selon une planification définie](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Tâches multiétapes

Les tâches multiétapes permettent la définition et l’exécution basées sur une tâche pour la génération, le test et la mise à jour corrective d’images conteneur dans le cloud. Les étapes de tâche définies dans un [fichier YAML](container-registry-tasks-reference-yaml.md) spécifient les opérations de création et Push individuelles pour les images conteneur ou d’autres artefacts. Elles permettent également de définir l’exécution d’un ou plusieurs conteneurs, en utilisant à chaque étape le conteneur comme son environnement d’exécution.

Par exemple, vous pouvez créer une tâche à plusieurs étapes qui automatise les opérations suivantes :

1. Générer (build) une image de l’application web
1. Exécuter le conteneur d’application web
1. Compiler une image test d’application web
1. Exécuter le conteneur de test de l’application web qui effectue des tests sur le conteneur d’application en cours d’exécution
1. Si les tests réussissent, compilez un package d’archivage du graphique Helm
1. Exécutez la commande `helm upgrade` à l’aide du nouveau package d’archivage du graphique Helm

Les tâches à plusieurs étapes vous permettent de fractionner la génération, l’exécution et le test d’une image en étapes plus composables, avec prise en charge des dépendances entre chacune d’elles. Grâce aux tâches à plusieurs étapes d’ACR Tasks, vous contrôlez plus finement la génération d’images, le test la mise à jour corrective du système d’exploitation et du framework.

Pour en savoir plus sur les tâches à plusieurs étapes, consultez [Run multi-step build, test, and patch tasks in ACR Tasks (Exécuter des tâches à plusieurs étapes de génération, de test et de correction dans ACR Tasks)](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Emplacements de contexte

Le tableau suivant présente quelques exemples d’emplacements de contexte pris en charge pour ACR Tasks :

| Emplacement du contexte | Description | Exemples |
| ---------------- | ----------- | ------- |
| Système de fichiers local | Fichiers dans un répertoire sur le système de fichiers local. | `/home/user/projects/myapp` |
| Branche principale GitHub | Fichiers dans la branche maître (ou autre branche par défaut) d’un référentiel GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Branche GitHub | Branche spécifique d’un référentiel GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Sous-dossier de GitHub | Fichiers dans un sous-dossier d’un référentiel GitHub. L’exemple affiche la combinaison de spécifications de branche et de sous-dossier. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Sous-dossier Azure DevOps | Fichiers figurant dans un sous-dossier d’un référentiel Azure. L’exemple montre la combinaison de spécifications de branche et de sous-dossier. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Tarball distant | Fichiers dans une archive compressée sur un serveur Web à distance. | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>Plateformes d’images

Par défaut, ACR Tasks génère des images pour le système d’exploitation Linux et l’architecture amd64. Spécifiez l’étiquette `--platform` pour créer des images Windows ou des images Linux pour d’autres architectures. Spécifiez le système d’exploitation et éventuellement une architecture prise en charge au format système d’exploitation/architecture (par exemple, `--platform Linux/arm`). Pour les architectures ARM, spécifiez éventuellement une variante au format système d’exploitation/architecture/variante (par exemple, `--platform Linux/arm64/v8`) :

| OS | Architecture|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>Afficher les journaux d’activité de tâches

Chaque exécution de tâche génère une sortie de journal que vous pouvez inspecter pour déterminer si les étapes de la tâche ont été exécutées avec succès. Si vous utilisez la commande [az acr build](/cli/azure/acr#az-acr-build), [acr az run](/cli/azure/acr#az-acr-run), ou [az acr task run](/cli/azure/acr/task#az-acr-task-run) pour déclencher la tâche, la sortie de journal pour l’exécution de la tâche est diffusée vers la console et également stockée pour une utilisation ultérieure. Lorsqu’une tâche est déclenchée automatiquement, par exemple par une validation de code source ou une mise à jour d’image de base, les journaux des tâches sont uniquement stockés. Affichez les journaux pour une tâche exécutée dans le portail Azure, ou utilisez la commande [az acr task logs](/cli/azure/acr/task#az-acr-task-logs).

Par défaut, les données et journaux pour les exécutions de tâches dans un registre sont conservées pendant 30 jours puis vidées automatiquement. Si vous souhaitez archiver les données pour une exécution de tâche, activez l’archivage à l’aide de la commande [az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run). L’exemple suivant active l’archivage pour l’exécution de la tâche *cf11* dans le registre *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous êtes prêt à automatiser les builds et la maintenance des images conteneur dans le cloud, consultez la [série de tutoriels sur ACR Tasks](container-registry-tutorial-quick-task.md).

Si vous le souhaitez, vous pouvez installer l’[extension Docker pour Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) et l’extension [Compte Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) pour utiliser vos registres de conteneurs Azure. Dans Visual Studio Code, vous pouvez tirer (pull) et envoyer (push) des images vers un registre de conteneurs Azure, et exécuter ACR Tasks.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
