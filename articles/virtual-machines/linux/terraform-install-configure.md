---
title: Installer et configurer Terraform pour une utilisation avec Azure | Microsoft Docs
description: Découvrez comment installer et configurer Terraform pour créer des ressources Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/19/2018
ms.author: echuvyrov
ms.openlocfilehash: 71cf07b227a75e53119f2f35e79ccd7926b551e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60418861"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Installer et configurer Terraform pour approvisionner les machines virtuelles et d’autres infrastructures dans Azure
 
Terraform fournit un moyen simple de définir, de prévisualiser et de déployer l’infrastructure cloud à l’aide d’un [langage simple basé sur des templates](https://www.terraform.io/docs/configuration/syntax.html). Cet article décrit les étapes nécessaires pour utiliser Terraform pour provisionner des ressources dans Azure.

Pour en savoir plus sur l’utilisation de Terraform avec Azure, visitez [Terraform Hub](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Terraform est installé par défaut dans [Cloud Shell](/azure/terraform/terraform-cloud-shell). Si vous choisissez d’installer Terraform localement, procédez comme suit, sinon passez à [Configurer l’accès de Terraform à Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Installer Terraform

Pour installer Terraform, [téléchargez](https://www.terraform.io/downloads.html) le package correspondant à votre système d’exploitation dans un répertoire d’installation distinct. Le téléchargement contient un seul fichier exécutable, pour lequel vous devez également définir un chemin d’accès global. Pour obtenir des instructions sur la définition du chemin d’accès sous Linux et Mac, consultez [cette page web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Pour obtenir des instructions sur la définition du chemin d’accès sous Windows, consultez [cette page web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Vérifiez la configuration de votre chemin d’accès à l’aide de la commande `terraform`. Une liste des options Terraform disponibles apparaît, comme dans l’exemple de sortie suivant :

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Configurer l’accès Terraform à Azure

Créez un [principal de service Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli) pour permettre à Terraform d’approvisionner des ressources dans Azure. Le principal de service autorise vos scripts Terraform à approvisionner des ressources dans votre abonnement Azure.

Si vous avez plusieurs abonnements Azure, commencez par interroger votre compte avec [az account show](/cli/azure/account#az-account-show) pour obtenir une liste des valeurs d’ID d’abonnement et d’ID abonné :

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Pour utiliser un abonnement sélectionné, paramétrez l’abonnement de cette session avec [az account set](/cli/azure/account#az-account-set). Définissez la variable d’environnement `SUBSCRIPTION_ID` pour qu’elle contienne la valeur du champ `id` retourné à partir de l’abonnement que vous souhaitez utiliser :

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Vous pouvez maintenant créer un principal de service pour une utilisation avec Terraform. Utilisez [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) et paramétrez l’*étendue* à votre abonnement comme suit :

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Vos valeurs `appId`, `password`, `sp_name` et `tenant` sont retournées. Notez les valeurs `appId` et `password`.

## <a name="configure-terraform-environment-variables"></a>Configurer des variables d’environnement Terraform

Pour configurer Terraform afin qu’il utilise votre principal de service Azure AD, définissez les variables d’environnement suivantes, qui sont ensuite utilisées par les [modules Azure Terraform](https://registry.terraform.io/modules/Azure). Vous pouvez également définir l’environnement si vous travaillez sur un cloud Azure autre qu’Azure public.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Vous pouvez utiliser l’exemple de script shell suivant pour définir ces variables :

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Exécuter un exemple de script

Créez un fichier `test.tf` dans un répertoire vide et collez-y le script suivant.

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Enregistrez le fichier puis initialisez le déploiement Terraform. Cette étape télécharge les modules Azure nécessaires pour créer un groupe de ressources Azure.

```bash
terraform init
```

Le résultat ressemble à l’exemple suivant :

```bash
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Vous pouvez prévisualiser les actions à effectuer par le script Terraform avec `terraform plan`. Lorsque vous êtes prêt à créer le groupe de ressources, appliquez votre plan Terraform comme suit :

```bash
terraform apply
```

Le résultat ressemble à l’exemple suivant :

```bash
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez installé Terraform ou utilisé Cloud Shell pour configurer les informations d’identification Azure et commencer à créer des ressources dans votre abonnement Azure. Pour créer un déploiement Terraform plus complet dans Azure, consultez l’article suivant :

> [!div class="nextstepaction"]
> [Créer une machine virtuelle Azure avec Terraform](terraform-create-complete-vm.md)
