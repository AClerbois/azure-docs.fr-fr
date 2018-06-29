---
title: Chiffrer des disques sur une machine virtuelle Linux dans Azure | Microsoft Docs
description: Guide de chiffrage de disques virtuels sur une machine virtuelle Linux pour renforcer la sécurité à l’aide d’Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/31/2018
ms.author: iainfou
ms.openlocfilehash: 343408366c2970d10a952634ac671721caed74d4
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936868"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Chiffrement d’une machine virtuelle Linux dans Azure
Pour renforcer la sécurité et la conformité de la machine virtuelle, les disques virtuels et la machine virtuelle elle-même peuvent être chiffrés. Les machines virtuelles sont chiffrées à l’aide de clés de chiffrement sécurisées dans un coffre de clés Azure. Vous contrôlez ces clés de chiffrement et pouvez effectuer un audit de leur utilisation. Cet article explique comment chiffrer des disques virtuels sur une machine virtuelle Linux à l’aide de l’interface Azure CLI 2.0. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.30 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Présentation du chiffrement de disque
Les disques virtuels sur des machines virtuelles Linux sont chiffrés au repos à l’aide de la commande [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Le chiffrement de disques virtuels dans Azure n’entraîne aucun frais. Les clés de chiffrement sont stockées dans le coffre de clés Azure à l’aide d’une protection logicielle, mais vous pouvez importer ou générer vos clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Vous gardez le contrôle de ces clés de chiffrement et pouvez effectuer un audit de leur utilisation. Ces clés de chiffrement servent à chiffrer et à déchiffrer les disques virtuels connectés à votre machine virtuelle. Un principal de service Azure Active Directory fournit un mécanisme sécurisé pour l’émission de ces clés de chiffrement lors de la mise sous tension et hors tension des machines virtuelles.

Le processus de chiffrement d’une machine virtuelle est le suivant :

1. Créez une clé de chiffrement dans un coffre de clés Azure.
2. Configurez la clé de chiffrement afin de la rendre utilisable pour le chiffrement des disques.
3. Pour lire la clé de chiffrement à partir du coffre de clés Azure, créez un principal de service Azure Active Directory avec les autorisations appropriées.
4. Exécutez la commande pour chiffrer vos disques virtuels, en spécifiant le principal de service Azure Active Directory et la clé de chiffrement appropriée à utiliser.
5. Le principal de service Azure Active Directory demande la clé de chiffrement requise au coffre de clés Azure.
6. Les disques virtuels sont chiffrés à l’aide de la clé de chiffrement fournie.

## <a name="encryption-process"></a>Processus de chiffrement
Le chiffrement de disque s’appuie sur les composants supplémentaires suivants :

* **Coffre de clés Azure** - permet de sauvegarder les clés de chiffrement et les clés secrètes utilisées pour le processus de chiffrement/déchiffrement de disque.
  * Le cas échéant, vous pouvez utiliser un coffre de clés Azure existant. Vous n’êtes pas obligé de dédier un coffre de clés au chiffrement des disques.
  * Pour séparer les limites administratives et la visibilité de la clé, vous pouvez créer un coffre de clés dédié.
* **Azure Active Directory** - gère l’échange sécurisé des clés de chiffrement requises et l’authentification des actions demandées.
  * Vous pouvez généralement utiliser une instance Azure Active Directory existante pour héberger votre application.
  * Le principal du service fournit un mécanisme sécurisé pour demander et émettre les bonnes clés de chiffrement. Vous ne développez pas de réelle application qui s’intègre à Azure Active Directory.

## <a name="requirements-and-limitations"></a>Spécifications et limitations
Configuration requise et scénarios pris en charge pour le chiffrement de disque :

* Les versions serveur Linux suivantes : Ubuntu, CentOS, SUSE, SUSE Linux Enterprise Server (SLES) et Red Hat Enterprise Linux.
* Toutes les ressources (par exemple, le coffre de clés, le compte de stockage, la machine virtuelle, etc.) doivent appartenir à la même région et au même abonnement Azure.
* Machines virtuelles standard des séries A, D, DS, G, GS, etc.
* Mise à jour des clés de chiffrement sur une machine virtuelle Linux déjà chiffrée.

Le chiffrement de disque n’est actuellement pas pris en charge dans les scénarios suivants :

* Machines virtuelles de niveau de base.
* Machines virtuelles créées à l’aide du modèle de déploiement Classic.
* Désactivation du chiffrement de disque du système d’exploitation sur les machines virtuelles Linux.
* Utilisation d’images personnalisées Linux.

Pour plus d’informations sur les scénarios pris en charge et les limitations, consultez [Chiffrement de disque Azure pour des machines virtuelles IaaS](../../security/azure-security-disk-encryption.md).


## <a name="create-an-azure-key-vault-and-keys"></a>Créer un coffre Azure Key Vault et des clés
Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *myKey* et *myVM*.

La première étape consiste à créer un coffre de clés Azure pour y stocker les clés de chiffrement. Un coffre de clés Azure peut stocker des clés, des clés secrètes ou des mots de passe vous permettant de les implémenter en toute sécurité dans vos applications et services. Pour le chiffrement de disque virtuel, vous utilisez le coffre de clés afin de stocker une clé de chiffrement pour chiffrer ou déchiffrer vos disques virtuels.

Activez le fournisseur Azure Key Vault au sein de votre abonnement Azure avec [az provider register](/cli/azure/provider#az-provider-register) puis créez un groupe de ressources avec [az group create](/cli/azure/group#az-group-create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement `eastus` :

```azurecli-interactive
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Le coffre de clés Azure contenant les clés de chiffrement et les ressources de calcul associées tels que le stockage et la machine virtuelle elle-même doit résider dans la même région. Créez un coffre Azure Key Vault avec [az keyvault create](/cli/azure/keyvault#az-keyvault-create) et activez Key Vault pour une utilisation avec le chiffrement de disque. Spécifiez un nom de Key Vault unique pour *keyvault_name* comme suit :

```azurecli-interactive
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Vous pouvez stocker des clés de chiffrement à l’aide d’une protection logicielle ou HSM (modèle de sécurité matériel). L’utilisation d’une protection HSM nécessite un coffre de clés Premium. Contrairement à l’utilisation d’un coffre de clés standard qui stocke les clés protégées par logiciel, la création d’un coffre de clés Premium entraîne des frais. Pour créer un coffre de clés Premium, ajoutez `--sku Premium` à la commande de l’étape précédente. L’exemple suivant utilise des clés protégées par logiciel, car vous avez créé un coffre de clés standard.

Pour les deux modèles de protection, la plateforme Azure doit être autorisée à demander les clés de chiffrement lorsque la machine virtuelle démarre pour déchiffrer les disques virtuels. Créez une clé de chiffrement dans le coffre Key Vault avec [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create). L’exemple qui suit permet de créer une clé nommée *myKey* :

```azurecli-interactive
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-an-azure-active-directory-service-principal"></a>Créer un principal du service Azure Active Directory
Lorsque des disques virtuels sont chiffrés ou déchiffrés, vous spécifiez un compte pour gérer l’authentification et l’échange de clés de chiffrement à partir du coffre de clés. Ce compte, un principal de service Azure Active Directory, permet à la plateforme Azure de demander les clés de chiffrement appropriées pour le compte de la machine virtuelle. Une instance Azure Active Directory par défaut est disponible dans votre abonnement, bien que de nombreuses organisations utilisent des répertoires Azure Active Directory dédiés.

Créez un principal de service à l’aide d’Azure Active Directory avec [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). L’exemple suivant lit les valeurs du principal de service et du mot de passe à utiliser dans les commandes ultérieures :

```azurecli-interactive
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Le mot de passe s’affiche uniquement lorsque vous créez le principal de service. Si vous le souhaitez, affichez et enregistrez le mot de passe (`echo $sp_password`). Vous pouvez répertorier vos principaux de service avec [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) et afficher des informations supplémentaires sur un principal de service spécifique avec [az ad sp show](/cli/azure/ad/sp#az-ad-sp-show).

Pour chiffrer ou déchiffrer correctement des disques virtuels, des autorisations d’accès à la clé de chiffrement stockée dans le coffre de clés doivent être définies afin que le principal de service Azure Active Directory puisse lire les clés. Définissez des autorisations sur votre Key Vault avec [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Dans l’exemple suivant, l’ID du principal de service est fourni à partir de la commande précédente :

```azurecli-interactive
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Créez une machine virtuelle avec [az vm create](/cli/azure/vm#az-vm-create) et attachez un disque de données de 5 Go. Seules certaines images Marketplace prennent en charge le chiffrement de disque. L’exemple qui suit permet de créer une machine virtuelle nommée *myVM* qui utilise une image *Ubuntu 16.04 LTS* :

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Connectez-vous avec SSH à votre machine virtuelle à l’aide de la valeur *publicIpAddress* indiquée dans la sortie de la commande précédente. Créez une partition et un système de fichiers, puis montez le disque de données. Pour plus d’informations, consultez [Connexion à la machine virtuelle Linux pour monter le nouveau disque](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Fermez votre session SSH.


## <a name="encrypt-the-virtual-machine"></a>Chiffrer la machine virtuelle
Pour chiffrer les disques virtuels, vous allez réunir tous les composants précédents :

1. Spécifiez le principal de service Azure Active Directory et le mot de passe.
2. Spécifiez le coffre de clés qui stockera les métadonnées de vos disques chiffrés.
3. Spécifiez les clés de chiffrement à utiliser pour le chiffrement et le déchiffrement réels.
4. Indiquez si vous souhaitez chiffrer le disque du système d’exploitation, les disques de données ou l’ensemble.

Chiffrez votre machine virtuelle avec [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable). L’exemple suivant utilise les variables *$sp_id* et *$sp_password* de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) :

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Il faut un certain temps pour que le processus de chiffrement de disque se termine. Surveillez l’état du processus avec [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) :

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Le résultat ressemble à l’exemple tronqué suivant :

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress"
]
```

Attendez que l’état du disque du système d’exploitation signale **VMRestartPending**, puis redémarrez votre machine virtuelle avec [az vm restart](/cli/azure/vm#az-vm-restart) :

```azurecli-interactive
az vm restart --resource-group myResourceGroup --name myVM
```

Le processus de chiffrement de disque est finalisé pendant le processus de démarrage, aussi attendez quelques minutes avant de vérifier à nouveau l’état de chiffrement à l’aide de [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) :

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

L’état doit maintenant signaler que le disque du système d’exploitation et le disque de données ont l’état **Chiffré**.


## <a name="add-additional-data-disks"></a>Ajouter des disques de données supplémentaires
Une fois que vous avez chiffré vos disques de données, vous pouvez ajouter ultérieurement d’autres disques virtuels à votre machine virtuelle et également les chiffrer. Par exemple, ajoutons un second disque virtuel à votre machine virtuelle comme suit :

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --disk myDataDisk \
    --new \
    --size-gb 5
```

Réexécutez la commande pour chiffrer les disques virtuels comme suit :

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```


## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la gestion du coffre de clés Azure, y compris la suppression des clés de chiffrement et des coffres, consultez [Gérer le coffre de clés à l’aide de l’interface de ligne de commande](../../key-vault/key-vault-manage-with-cli2.md).
* Pour plus d’informations sur le chiffrement de disque, notamment la préparation d’une machine virtuelle personnalisée chiffrée à télécharger vers Azure, consultez [Chiffrement de disque Azure](../../security/azure-security-disk-encryption.md).
