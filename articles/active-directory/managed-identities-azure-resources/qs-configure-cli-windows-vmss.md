---
title: Comment configurer le système et affectée à l’utilisateur des identités gérées à l’échelle de machine virtuelle Azure définie à l’aide d’Azure CLI
description: Instructions pour la configuration système et affectée à l’utilisateur des identités gérées à l’échelle de machine virtuelle Azure définie étape par étape, à l’aide d’Azure CLI.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88bcd38890baea2d6bc0460937fe4b7882f7fd23
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226043"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Configurer des identités managées pour ressources Azure sur un groupe de machines virtuelles identiques en utilisant Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à effectuer les identités gérées suivantes pour les opérations de ressources Azure sur un jeu de mise à l’échelle de machine virtuelle Azure, à l’aide de l’interface CLI :
- Activer et désactiver l’identité managée affectée par le système sur un groupe de machines virtuelles identiques Azure
- Ajouter et supprimer une identité managée affectée par l’utilisateur sur un groupe de machines virtuelles identiques Azure


## <a name="prerequisites"></a>Conditions préalables

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer de ces affectations de contrôle d'accès basé sur les rôles Azure :

    > [!NOTE]
    > Aucune attribution de rôle d'annuaire Azure AD supplémentaire n’est requise.

    - [Contributeur de machine virtuelle](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pour créer un groupe de machines virtuelles identiques, puis activer ou supprimer l’identité managée affectée par le système et/ou par l’utilisateur à partir d’un groupe de machines virtuelles identiques.
    - [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) pour créer une identité managée affectée par l’utilisateur.
    - [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pour attribuer et supprimer une identité managée affectée par l’utilisateur dans un groupe de machines virtuelles identiques.
- Pour exécuter les exemples de script d’Azure CLI, vous disposez de trois options :
    - Utilisez [Azure Cloud Shell](../../cloud-shell/overview.md) à partir du portail Azure (voir section suivante).
    - Utilisez l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit de chaque bloc de code.
    - [Installez la dernière version d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou ultérieure) si vous préférez utiliser une console CLI locale. 
      
      > [!NOTE]
      > Les commandes ont été mises à jour pour refléter la dernière version [d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

Dans cette section, vous allez apprendre à activer et désactiver l’identité gérée attribué par le système pour une échelle de machine virtuelle Azure définie à l’aide d’Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Activer une identité managée affectée par le système lors de la création d’un groupe de machines virtuelles identiques Azure

Pour créer un groupe de machines virtuelles identiques avec l’identité managée affectée par le système activée :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az-login). Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez déployer le groupe de machines virtuelles identiques :

   ```azurecli-interactive
   az login
   ```

2. Créez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#terminology) pour l’imbrication et le déploiement de votre groupe de machines virtuelles identiques et de ses ressources connexes, à l’aide de la commande [az group create](/cli/azure/group/#az-group-create). Vous pouvez ignorer cette étape si vous disposez déjà d’un groupe de ressources que vous souhaitez utiliser à la place :

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Créez un groupe de machines virtuelles identiques avec la commande [az vmss create](/cli/azure/vmss/#az-vmss-create). L’exemple suivant crée un groupe de machines virtuelles identiques nommé *myVMSS* avec une identité managée affectée par le système, comme le demande le paramètre `--assign-identity`. Les paramètres `--admin-username` et `--admin-password` spécifient le nom d’utilisateur et le mot de passe d’administration du compte pour la connexion à la machine virtuelle. Mettez à jour ces valeurs en fonction de votre environnement : 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Activer une identité managée affectée par le système sur un groupe de machines virtuelles identiques Azure existant

Si vous devez activer l’identité managée affectée par le système sur un groupe de machines virtuelles identiques Azure existant :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az-login). Utilisez un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.

   ```azurecli-interactive
   az login
   ```

2. Utilisez la commande [az vm identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) pour activer l’identité managée affectée par le système sur une machine virtuelle existante :

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Désactiver une identité managée affectée par le système d’un groupe de machines virtuelles identiques Azure

Si vous disposez d’un groupe de machines virtuelles identiques qui n’a plus besoin de l’identité managée affectée par le système, mais qui a toujours besoin d’identités managées affectées par l’utilisateur, utilisez la commande suivante :

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Si vous disposez d’une machine virtuelle qui n’a plus besoin de l’identité managée affectée par le système et qui n’a pas d’identité managée affectée par l’utilisateur, utilisez la commande suivante :

> [!NOTE]
> La valeur `none` est sensible à la casse. Elle doit être en minuscules. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

> [!NOTE]
> Si vous avez configuré l’identité gérée pour les ressources Azure VM extension (déconseillé), vous devez supprimer à l’aide de [delete de az vmss extension](https://docs.microsoft.com/cli/azure/vm/). Pour plus d’informations, consultez [migrer vers IMDS Azure pour l’authentification à partir de l’extension de machine virtuelle](howto-migrate-vm-extension.md).

## <a name="user-assigned-managed-identity"></a>Identité managée affectée par l’utilisateur

Dans cette section, vous allez découvrir comment activer et supprimer une identité managée affectée par l’utilisateur en utilisant Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Attribuer une identité managée affectée par l’utilisateur lors de la création d’un groupe de machines virtuelles identiques

Cette section vous guide la création d’un jeu de mise à l’échelle de machine virtuelle et l’affectation d’une identité gérée affectée à l’utilisateur pour les machines virtuelles identiques. Si vous avez déjà un jeu de mise à l’échelle de machine virtuelle à utiliser, ignorez cette section et passez à la suivante.

1. Vous pouvez ignorer cette étape si vous disposez déjà d’un groupe de ressources que vous souhaitez utiliser. Créez un [groupe de ressources](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pour contenir et déployer votre identité managée affectée par l’utilisateur en utilisant la commande [az group create](/cli/azure/group/#az-group-create). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<LOCATION>` par vos propres valeurs. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Créez une identité managée affectée par l’utilisateur en utilisant la commande [az identity create](/cli/azure/identity#az-identity-create).  Le paramètre `-g` spécifie le groupe de ressources où l’identité managée affectée par l’utilisateur est créée, et le paramètre `-n` spécifie son nom. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   La réponse contient les détails de l’identité managée affectée par l’utilisateur qui a été créée, comme dans l’exemple suivant. La valeur d’`id` de ressource attribuée à l’identité managée affectée par l’utilisateur est utilisée à l’étape suivante.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Créer une échelle de machine virtuelle à l’aide de [créer az vmss](/cli/azure/vmss/#az-vmss-create). L’exemple suivant crée un jeu de mise à l’échelle de machine virtuelle associé à la nouvelle affectée à l’utilisateur gérée identité, tel que spécifié par le `--assign-identity` paramètre. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` et `<USER ASSIGNED IDENTITY>` par vos propres valeurs. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Attribuer une identité managée affectée par l’utilisateur à un groupe de machines virtuelles identiques existant

1. Créez une identité managée affectée par l’utilisateur en utilisant la commande [az identity create](/cli/azure/identity#az-identity-create).  Le paramètre `-g` spécifie le groupe de ressources où l’identité managée affectée par l’utilisateur est créée, et le paramètre `-n` spécifie son nom. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   La réponse contient les détails de l’identité managée affectée par l’utilisateur qui a été créée, comme dans l’exemple suivant.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Attribuer l’identité gérée affectée à l’utilisateur à l’échelle de vos machines virtuelles définies à l’aide [affecter d’identité de vmss az](/cli/azure/vmss/identity). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VIRTUAL MACHINE SCALE SET NAME>` par vos propres valeurs. `<USER ASSIGNED IDENTITY>` est la propriété `name` de ressource de l’identité managée affectée par l’utilisateur, telle que créée à l’étape précédente :

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Supprimer d’un groupe de machines virtuelles identiques Azure une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur d’un groupe de machines virtuelles identiques, utilisez la commande [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove). S’il s’agit de la seule identité managée affectée par l’utilisateur attribuée au groupe de machines virtuelles identiques, `UserAssigned` est supprimé de la valeur du type d’identité.  N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VIRTUAL MACHINE SCALE SET NAME>` par vos propres valeurs. `<USER ASSIGNED IDENTITY>` sera la propriété `name` de l’identité managée affectée par l’utilisateur, qui est accessible dans la section identity du groupe de machines virtuelles identiques en utilisant la commande `az vmss identity show` :

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Si votre groupe de machines virtuelles identiques ne dispose pas d’une identité managée affectée par le système, et souhaitez supprimer toutes les identités managées affectées par l’utilisateur qu’elle contient, utilisez la commande suivante :

> [!NOTE]
> La valeur `none` est sensible à la casse. Elle doit être en minuscules.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Si votre groupe de machines virtuelles identiques dispose à la fois d’identités managées affectées par l’utilisateur et d’identités affectées par le système, vous pouvez supprimer toutes les identités affectées par l’utilisateur en choisissant de n’utiliser que l’identité managée affectée par le système. Utilisez la commande suivante :

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des identités managées pour les ressources Azure](overview.md)
- Pour le démarrage rapide de la création d’un groupe de machines virtuelles identiques Azure, consultez : 

  - [Créer un groupe de machines virtuelles identiques avec CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















