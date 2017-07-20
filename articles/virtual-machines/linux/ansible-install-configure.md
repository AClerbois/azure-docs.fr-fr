---
title: Installer et configurer Ansible pour une utilisation avec des machines virtuelles Azure | Microsoft Docs
description: "Découvrez comment installer et configurer Ansible pour la gestion des ressources Azure sur Ubuntu, CentOS et SLES"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 52b763274437961dccfc862c8a45fbd57ea9fc4e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017

---

# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Installer et configurer Ansible pour gérer des machines virtuelles dans Azure
Cet article explique comment installer Ansible et les modules du SDK Azure Python nécessaires pour quelques-unes des distributions Linux les plus courantes. Vous pouvez installer Ansible sur d’autres distributions en adaptant les packages installés à votre plateforme spécifique. Pour créer des ressources Azure de manière sécurisée, vous découvrez également comment créer et définir des informations d’identification à utiliser par Ansible. 

Pour plus d’options d’installation et des étapes pour d’autres plateformes, consultez le [guide d’installation d’Ansible](https://docs.ansible.com/ansible/intro_installation.html).


## <a name="install-ansible"></a>Installer Ansible
Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupAnsible* dans l’emplacement *eastus* :

```azurecli
az group create --name myResourceGroupAnsible --location eastus
```

Créez maintenant une machine virtuelle et installez Ansible pour une des distributions suivantes :

- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [CentOS 7.3](#centos-73)
- [SLES 12.2 SP2](#sles-122-sp2)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS
Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). L’exemple suivant crée une machine virtuelle nommée *myVMAnsible* :

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Connectez-vous via SSH à votre machine virtuelle en utilisant la `publicIpAddress` indiquée dans la sortie de l’opération de création de la machine virtuelle :

```bash
ssh azureuser@<publicIpAddress>
```

Sur votre machine virtuelle, installez les packages nécessaires pour les modules du SDK Azure Python et Ansible comme suit :

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Azure SDKs via pip
pip install "azure==2.0.0rc5" msrestazure

## Install Ansible via apt
sudo apt-get install -y software-properties-common
sudo apt-add-repository -y ppa:ansible/ansible
sudo apt-get update && sudo apt-get install -y ansible
```

Passez maintenant à [Créer des informations d’identification Azure](#create-azure-credentials).


### <a name="centos-73"></a>CentOS 7.3
Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). L’exemple suivant crée une machine virtuelle nommée *myVMAnsible* :

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Connectez-vous via SSH à votre machine virtuelle en utilisant la `publicIpAddress` indiquée dans la sortie de l’opération de création de la machine virtuelle :

```bash
ssh azureuser@<publicIpAddress>
```

Sur votre machine virtuelle, installez les packages nécessaires pour les modules du SDK Azure Python et Ansible comme suit :

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Azure SDKs via pip
sudo pip install "azure==2.0.0rc5" msrestazure

## Install Ansible via yum
sudo yum install -y ansible
```

Passez maintenant à [Créer des informations d’identification Azure](#create-azure-credentials).


### <a name="sles-122-sp2"></a>SLES 12.2 SP2
Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). L’exemple suivant crée une machine virtuelle nommée *myVMAnsible* :

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image SLES \
    --admin-username azureuser \
    --generate-ssh-keys
```

Connectez-vous via SSH à votre machine virtuelle en utilisant la `publicIpAddress` indiquée dans la sortie de l’opération de création de la machine virtuelle :

```bash
ssh azureuser@<publicIpAddress>
```

Sur votre machine virtuelle, installez les packages nécessaires pour les modules du SDK Azure Python et Ansible comme suit :

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 python-devel \
    libopenssl-devel python-pip python-setuptools python-azure-sdk

## Install Ansible via zypper
sudo zypper addrepo http://download.opensuse.org/repositories/systemsmanagement/SLE_12_SP2/systemsmanagement.repo
sudo zypper refresh && sudo zypper install ansible
```

Passez maintenant à [Créer des informations d’identification Azure](#create-azure-credentials).


## <a name="create-azure-credentials"></a>Créer des informations d’identification Azure
Ansible communique avec Azure en utilisant un nom d’utilisateur et un mot de passe, ou un principal de service. Un principal de service Azure est une identité de sécurité que vous pouvez utiliser avec des applications, des services et des outils d’automatisation comme Ansible. Vous contrôlez et vous définissez les opérations que le principal du service est autorisé à effectuer dans Azure. Pour renforcer la sécurité par rapport à la simple saisie d’un nom d’utilisateur et d’un mot de passe, cet exemple crée un principal de service de base.

Créez un principal de service avec la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) et affichez les informations d’identification nécessaires à Ansible :

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

Voici un exemple de la sortie des commandes précédentes :

```json
[
  "eec5624a-90f8-4386-8a87-02730b5410d5",
  "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "72f988bf-86f1-41af-91ab-2d7cd011db47"
]
```

Pour s’authentifier sur Azure, vous devez également obtenir votre ID d’abonnement Azure avec [az account show](/cli/azure/account#show) :

```azurecli
az account show --query [id] --output tsv
```

Vous utilisez la sortie de ces deux commandes à l’étape suivante.


## <a name="create-ansible-credentials-file"></a>Créer un fichier d’informations d’identification Ansible
Pour fournir des informations d’identification à Ansible, vous définissez des variables d’environnement ou vous créez un fichier d’informations d’identification local. Pour plus d’informations sur la définition des informations d’identification Ansible, consultez [Fourniture d’informations d’identification aux modules Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules). 

Pour un environnement de développement, créez un fichier *d’informations d’identification* pour Ansible sur votre machine virtuelle hôte comme suit :

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Le fichier *d’informations d’identification* combine l’ID d’abonnement avec la sortie de la création d’un principal de service. La sortie de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) précédente est dans l’ordre nécessaire pour *client_id*, *secret* et *locataire*. L’exemple suivant de fichier *d’informations d’identification* montre ces valeurs correspondant à la sortie précédente. Saisissez vos propres valeurs, comme suit :

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=66cf7166-dd13-40f9-bca2-3e9a43f2b3a4
secret=b8326643-f7e9-48fb-b0d5-952b68ab3def
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```


## <a name="use-ansible-environment-variables"></a>Utiliser des variables d’environnement Ansible
Si vous devez utiliser des outils comme Ansible Tower ou Jenkins, vous pouvez définir des variables d’environnement comme suit. Ces variables combinent l’ID d’abonnement à la sortie de la création d’un principal de service. La sortie de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) précédente est dans l’ordre nécessaire pour *AZURE_CLIENT_ID*, *AZURE_SECRET* et *AZURE_TENANT*. 

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=66cf7166-dd13-40f9-bca2-3e9a43f2b3a4
export AZURE_SECRET=8326643-f7e9-48fb-b0d5-952b68ab3def
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Étapes suivantes
Ansible et les modules du SDK Azure Python SDK nécessaires sont maintenant installés, et des informations d’identification sont définies pour être utilisées par Ansible. Découvrez comment [créer une machine virtuelle avec Ansible](ansible-create-vm.md). Vous pouvez également découvrir comment [créer une machine virtuelle Azure complète et des ressources de support avec Ansible](ansible-create-complete-vm.md).
