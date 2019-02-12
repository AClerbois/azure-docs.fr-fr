---
title: Meilleures pratiques de l’opérateur - Sécurité du cluster dans Azure Kubernetes Service (AKS)
description: Découvrir les meilleures pratiques de l’opérateur relatives à la gestion des mises à jour et de la sécurité du cluster dans Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: d9ce2661fbdca0a28f917e27e27a3e3f954a9999
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488377"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Meilleures pratiques relatives aux mises à jour et à la sécurité du cluster dans Azure Kubernetes Service (AKS)

Quand vous gérez des clusters dans Azure Kubernetes Service (AKS), la sécurité de vos charges de travail et données est un point important. Et ce tout particulièrement lorsque vous exécutez des clusters avec plusieurs locataires à l’aide de l’isolation logique ; l’accès aux ressources et charges de travail doit être sécurisé. Pour réduire le risque d’attaque, vous devez également vous assurer que vous appliquez les dernières mises à jour de sécurité de système d’exploitation de nœud et Kubernetes.

Cet article est dédié à la sécurisation de votre cluster AKS. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Utiliser Azure Active Directory et les contrôles d’accès en fonction du rôle pour sécuriser l’accès au serveur d’API
> * Sécuriser l’accès du conteneur aux ressources de nœud
> * Mettre à niveau un cluster AKS avec la dernière version de Kubernetes
> * Maintenir les nœuds à jour et appliquer automatiquement des correctifs de sécurité

Vous pouvez également consulter les meilleures pratiques relatives à la [gestion des images conteneur][best-practices-container-image-management] et à la [sécurité du pod][best-practices-pod-security].

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Sécuriser l’accès aux nœuds de cluster et au serveur d’API

**Meilleures pratiques** - La sécurisation de l’accès au serveur d’API Kubernetes est l’une des choses les plus importantes à faire pour protéger votre cluster. Intégrez le contrôle d’accès en fonction du rôle Kubernetes à Azure Active Directory pour contrôler l’accès au serveur d’API. Ces contrôles vous permettent de sécuriser AKS de la même façon que vous sécurisez l’accès à vos abonnements Azure.

Le serveur d’API Kubernetes propose un point de connexion unique pour que les requêtes exécutent des actions dans un cluster. Pour sécuriser et auditer l’accès au serveur d’API, limitez l’accès et proposez les autorisations d’accès avec le moins de privilèges requises. Cette approche n’est pas unique à Kubernetes, mais est particulièrement importante lorsque le cluster AKS est isolé de façon logique pour une utilisation avec plusieurs locataires.

Azure Active Directory (AD) fournit une solution de gestion des identités d’entreprise qui s’intègre aux clusters AKS. Comme Kubernetes ne fournit pas de solution de gestion des identités, il peut être difficile de proposer une méthode granulaire pour restreindre l’accès au serveur d’API. Avec les clusters intégrés Azure AD dans AKS, vous utilisez vos comptes groupe et utilisateur existants pour authentifier les utilisateurs sur le serveur d’API.

![Intégration Azure Active Directory aux clusters AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Utilisez l’intégration Azure AD Kubernetes et le contrôle d’accès en fonction du rôle pour sécuriser le serveur d’API et fournir le nombre minimal d’autorisations requises pour un ensemble donné de ressources, comme un espace de noms unique. Différents utilisateurs ou groupes dans Azure AD peuvent se voir accorder différents rôles de contrôle d’accès en fonction du rôle. Ces autorisations granulaires vous permettent de restreindre l’accès au serveur d’API et fournissent une piste d’audit claire des actions effectuées.

La meilleure pratique recommandée consiste à utiliser des groupes pour fournir un accès aux fichiers et dossiers plutôt que des identités individuelles ; utilisez l’appartenance au *groupe* Azure AD pour lier les utilisateurs aux rôles de contrôle d’accès en fonction du rôle plutôt que des *utilisateurs* individuels. Étant donné que l’appartenance au groupe d’un utilisateur change, ses autorisations d’accès sur le cluster AKS changent en conséquence. Si vous liez l’utilisateur directement à un rôle, sa fonction peut changer. Les appartenances au groupe Azure AD peuvent être mises à jour, mais les autorisations sur le cluster AKS ne le refléteraient pas. Dans ce scénario, l’utilisateur finit par se voir accorder davantage d’autorisations que nécessaire.

Pour plus d’informations sur l’intégration Azure AD et le contrôle d’accès en fonction du rôle, consultez [Options d’accès et d’identité pour Azure Kubernetes Service (AKS)][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Sécuriser l’accès du conteneur aux ressources

**Meilleures pratiques** - Limitez l’accès aux actions que les conteneurs peuvent effectuer. Fournissez le plus petit nombre d’autorisations et évitez d’utiliser l’escalade de privilèges/racine.

De la même façon que vous devez accorder aux utilisateurs ou groupes le nombre minimal de privilèges requis, les conteneurs doivent également être limités aux actions et processus nécessaires uniquement. Pour réduire le risque d’attaque, ne configurez pas les applications et les conteneurs qui nécessitent des privilèges escaladés ou un accès racine. Par exemple, définissez `allowPrivilegeEscalation: false` dans le manifeste de pod. Ces *contextes de sécurité de pod* sont intégrés à Kubernetes et vous permettent de définir des autorisations supplémentaires telles que l’identité du groupe ou de l’utilisateur à exécuter, ou les fonctionnalités Linux à exposer. Pour plus de meilleures pratiques, consultez [Secure pod access to resources][pod-security-contexts] (Sécuriser l’accès du pod aux ressources).

Pour un contrôle plus précis des actions de conteneur, vous pouvez également utiliser les fonctionnalités de sécurité Linux intégrées telles que *AppArmor* et *seccomp*. Ces fonctionnalités sont définies au niveau du nœud, puis implémentées via un manifeste de pod.

> [!NOTE]
> Les environnements Kubernetes, dans AKS ou ailleurs, ne sont pas totalement sûrs pour une utilisation multi-locataire hostile. Des fonctionnalités de sécurité supplémentaires, comme *AppArmor*, *seccomp* ou des *stratégies de sécurité Pod*, ainsi que des contrôles d'accès en fonction du rôle (RBAC) plus détaillés pour les nœuds rendent les attaques plus difficiles. Mais lors de l'exécution de charges de travail multi-locataires hostiles, seul un hyperviseur garantira véritablement la sécurité. Le domaine de sécurité de Kubernetes devient le cluster, et non un nœud individuel. Pour ces types de charges de travail multi-locataires hostiles, vous devez utiliser des clusters physiquement isolés.

### <a name="app-armor"></a>AppArmor

Pour limiter les actions réalisables par les conteneurs, vous pouvez utiliser le module de sécurité du noyau Linux [AppArmor][k8s-apparmor]. AppArmor est disponible dans le cadre du système d’exploitation de nœud AKS sous-jacent et est activé par défaut. Vous créez des profils AppArmor qui limitent les actions telles que la lecture, l’écriture ou l’exécution ou des fonctions système telles que le montage de systèmes de fichiers. Les profils AppArmor par défaut limitent l’accès à divers emplacements `/proc` et `/sys`, et fournissent un moyen pour isoler de façon logique les conteneurs à partir du nœud sous-jacent. AppArmor fonctionne pour n’importe quelle application qui s’exécute sur Linux, pas seulement les pods Kubernetes.

![Profils AppArmor en cours d’utilisation dans un cluster AKS pour limiter les actions de conteneur](media/operator-best-practices-container-security/apparmor.png)

Pour voir AppArmor en action, l’exemple suivant crée un profil qui empêche l’écriture de fichiers. Établissez une connexion [SSH][aks-ssh] vers un nœud AKS, puis créez un fichier nommé *deny-write.profile*, et collez le contenu suivant :

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Les profils AppArmor sont ajoutés à l’aide de la commande `apparmor_parser`. Ajoutez le profil à AppArmor et spécifiez le nom du profil créé dans l’étape précédente :

```console
sudo apparmor_parser deny-write.profile
```

Aucune sortie n’est renvoyée si le profil est correctement analysé et appliqué à AppArmor. Vous êtes redirigé vers l’invite de commandes.

Depuis votre ordinateur local, créez maintenant un manifeste de pod nommé *aks-apparmor.yaml* et collez le contenu suivant. Ce manifeste définit une annotation pour `container.apparmor.security.beta.kubernetes` et fait référence au profil *deny-write* créé dans les étapes précédentes :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

Déployez l’exemple de pod à l’aide de la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f aks-apparmor.yaml
```

Avec le pod déployé, utilisez la commande [kubectl exec][kubectl-exec] pour écrire dans un fichier. La commande ne peut pas être exécutée, comme indiqué dans l’exemple de sortie suivant :

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Pour plus d’informations sur AppArmor, consultez [AppArmor][k8s-apparmor] (AppArmor).

### <a name="secure-computing"></a>Sécuriser le calcul

Tandis qu’AppArmor fonctionne pour toutes les applications Linux, [seccomp (*sec*ure *comp*uting)][seccomp] agit au niveau du processus. Seccomp est également un module de sécurité du noyau Linux, pris en charge de façon native par le runtime Docker utilisé par les nœuds AKS. Avec seccomp, les appels de processus que les conteneurs peuvent effectuer sont limités. Vous créez des filtres qui définissent les actions à autoriser ou refuser, puis utilisez des annotations au sein d’un manifeste YAML de pod à associer au filtre seccomp. Cela coïncide avec la meilleure pratique consistant à accorder au conteneur uniquement les autorisations minimales devant être exécutées.

Pour voir seccomp en action, créez un filtre qui empêche la modification des autorisations sur un fichier. Établissez une connexion [SSH][aks-ssh] vers un nœud AKS, puis créez un filtre seccomp nommé */var/lib/kubelet/seccomp/prevent-chmod* et collez le contenu suivant :

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Depuis votre ordinateur local, créez maintenant un manifeste de pod nommé *aks-seccomp.yaml* et collez le contenu suivant. Ce manifeste définit une annotation pour `seccomp.security.alpha.kubernetes.io` et fait référence au filtre *prevent-chmod* créé dans les étapes précédentes :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

Déployez l’exemple de pod à l’aide de la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f ./aks-seccomp.yaml
```

Affichez l’état des pods à l’aide de la commande [kubectl get pods][kubectl-get]. Le pod signale une erreur. La commande `chmod` ne peut pas être exécutée en raison du filtre seccomp, comme affiché dans l’exemple de sortie suivant :

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Pour plus d’informations sur les filtres disponibles, consultez [Seccomp security profiles for Docker][seccomp] (Profils de sécurité seccomp pour Docker).

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Effectuer des mises à jour régulières vers la dernière version de Kubernetes

**Meilleures pratiques** - Pour rester informé des nouvelles fonctionnalités et correctifs de bogues, effectuez des mises à jour régulières vers la version Kubernetes dans votre cluster AKS.

Kubernetes sort de nouvelles fonctionnalités à un rythme plus effréné que les plateformes d’infrastructure plus traditionnelles. Les mises à jour Kubernetes incluent de nouvelles fonctionnalités et des correctifs de sécurité ou de bogues. En général, les nouvelles fonctionnalités passent d’un état *alpha*, puis *bêta* avant d’atteindre un état *stable* et d’être disponibles et recommandées pour une utilisation en production. Ce cycle devrait vous permettre de mettre à jour Kubernetes sans rencontrer de changements cassants ou ajuster vos déploiements et modèles.

AKS prend en charge quatre versions mineures de Kubernetes. Cela signifie que, quand une nouvelle version de correctif mineure est introduite, la version mineure et les publications des correctifs les plus anciennes prises en charge sont mises hors service. Les mises à jour mineures de Kubernetes se produisent sur une base périodique. Vérifiez que vous disposez d’un processus de gouvernance pour consulter et effectuer les mises à niveau nécessaires, afin de ne pas vous retrouver sans support. Pour plus d’informations, consultez [Versions de Kubernetes prises en charge dans Azure Kubernetes Service (AKS)][aks-supported-versions]

Pour consulter les versions disponibles pour votre cluster, utilisez la commande [az aks get-upgrades][az-aks-get-upgrades] comme indiqué dans l’exemple suivant :

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Vous pouvez ensuite mettre à niveau votre cluster AKS à l’aide de la commande [az aks upgrade][az-aks-upgrade]. Le processus de mise à niveau ferme et vide en toute sécurité un nœud à la fois, planifie les pods sur les nœuds restants, puis déploie un nouveau nœud exécutant les dernières versions de Kubernetes et du système d’exploitation.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.3
```

Pour plus d’informations sur les mises à niveau dans AKS, consultez [Versions de Kubernetes prises en charge dans Azure Kubernetes Service (AKS)][aks-supported-versions] et [Mise à jour d’un cluster Azure Kubernetes Service (AKS)][aks-upgrade].

## <a name="process-node-updates-and-reboots-using-kured"></a>Traiter les mises à jour et redémarrages de nœud avec kured

**Meilleures pratiques** - AKS télécharge et installe automatiquement les correctifs de sécurité sur chacun des nœuds de travail, mais il ne redémarre pas automatiquement si nécessaire. Utilisez `kured` pour surveiller les redémarrages en attente, puis fermez et videz le nœud en toute sécurité pour qu’il puisse redémarrer, appliquer les mises à jour et être aussi sûr que possible à l’égard du système d’exploitation.

Chaque soir, les nœuds AKS reçoivent les correctifs de sécurité disponibles via leur canal de mise à jour de distribution. Ce comportement est automatiquement configuré à mesure que les nœuds sont déployés dans un cluster AKS. Pour minimiser les perturbations et l’impact potentiel sur les charges de travail en cours d’exécution, les nœuds ne sont pas automatiquement redémarrés si un correctif de sécurité ou mise à jour du noyau l’exige.

Le projet [kured (KUbernetes REboot Daemon)][kured] open source de Weaveworks surveille les redémarrages de nœud en attente. Lorsqu’un nœud applique des mises à jour nécessitant un redémarrage, le nœud est en toute sécurité fermé et vidé pour déplacer et planifier les pods sur d’autres nœuds du cluster. Après le redémarrage du nœud, ce dernier est de nouveau ajouté au cluster, et Kubernetes reprend la planification des pods sur celui-ci. Pour limiter les perturbations, un seul nœud à la fois est autorisé à être redémarré par `kured`.

![Le processus de redémarrage du nœud AKS à l’aide de kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Si vous souhaitez contrôler plus précisément le moment où les redémarrages se produisent, `kured` peut s’intégrer à Prometheus afin d’éviter les redémarrages si d’autres événements de maintenance ou problèmes de cluster sont en cours. Cette intégration réduit les complications supplémentaires en redémarrant les nœuds pendant que vous résolvez de façon active les autres problèmes.

Pour plus d’informations sur le traitement des redémarrages de nœud, consultez [Appliquer des mises à jour de sécurité et du noyau à des nœuds dans Azure Kubernetes Service (AKS)][aks-kured].

## <a name="next-steps"></a>Étapes suivantes

Cet article était dédié à la sécurisation de votre cluster AKS. Pour implémenter quelques-unes de ces zones, consultez les articles suivants :

* [Intégrer Azure Active Directory à AKS][aks-aad]
* [Mise à jour d’un cluster Azure Kubernetes Service (AKS)][aks-upgrade]
* [Appliquer des mises à jour de sécurité et du noyau à des nœuds dans Azure Kubernetes Service (AKS)][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://docs.docker.com/engine/security/seccomp/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: aad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
