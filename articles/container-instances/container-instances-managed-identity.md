---
title: Utiliser une identité managée avec Azure Container Instances
description: Découvrez comment utiliser une identité managée pour s’authentifier auprès d’autres services Azure à partir d’Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 6d31e6a4d424b8e3c634c9d5075b1f02c512187b
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651131"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Comment utiliser une identité managée avec Azure Container Instances

Utilisez les [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) pour exécuter du code dans Azure Container Instances interagissant avec d’autres services Azure, et ce sans avoir à gérer les codes secrets ou les informations d’identification dans le code. Cette fonctionnalité fournit un déploiement Azure Container Instances avec une identité managée automatiquement dans Azure Active Directory.

Dans cet article, vous en apprendrez davantage sur les identités managées dans Azure Container Instances, et vous découvrirez comment :

> [!div class="checklist"]
> * Activer une identité attribuée par l’utilisateur ou par le système dans un groupe de conteneurs
> * Autoriser l’identité à accéder à un coffre de clés Azure Key Vault
> * Utiliser l’identité managée pour accéder à un coffre de clés à partir d’un conteneur en cours d’exécution

Adaptez les exemples pour activer et utiliser des identités dans Azure Container Instances pour accéder aux autres services Azure. Ces exemples sont interactifs. Toutefois, en pratique, vos images de conteneur exécuteraient du code pour accéder aux services Azure.

## <a name="why-use-a-managed-identity"></a>Pourquoi utiliser une identité managée ?

Utilisez une identité managée dans un conteneur en cours d’exécution pour vous authentifier sur n’importe quel [service prenant en charge l’authentification Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication), sans avoir à gérer les informations d’identification dans le code de votre conteneur. Pour les services qui ne prennent pas en charge l’authentification AD, vous pouvez stocker des codes secrets dans le coffre de clés Azure Key Vault et utiliser l’identité managée pour y accéder à Key Vault et récupérer les informations d’identification. Pour en savoir plus sur l’utilisation des identités managées, consultez la section [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale. Actuellement, les identités managées sont uniquement prises en charge sur les instances de conteneur Linux.
>  

### <a name="enable-a-managed-identity"></a>Activer une identité managée

 Dans Azure Container Instances, les identités managées pour les ressources Azure sont prises en charge à compter de la version 2018-10-01 de l’API REST et dans les outils et kits de développement logiciel correspondants. Lorsque vous créez un groupe de conteneurs, activez une ou plusieurs identités managées en définissant une propriété [ContainerGroupIdentity](/rest/api/container-instances/containergroups/containergroups_createorupdate#containergroupidentity). Vous pouvez également activer ou mettre à jour des identités managées après l’exécution d’un groupe de conteneurs. Ces actions entraînent le redémarrage du groupe de conteneurs. Pour définir les identités sur un nouveau groupe de conteneurs ou sur un groupe existant, utilisez Azure CLI, un modèle Resource Manager ou un fichier YAML. 

Azure Container Instances prend en charge les deux types d’identités Azure managées : attribuées par l’utilisateur et attribuées par le système. Sur un groupe de conteneurs, vous pouvez activer une identité attribuée par le système, une ou plusieurs identités attribuées par l’utilisateur, ou les deux types d’identités. 

* Une identité managée **attribuée par l’utilisateur** est créée en tant que ressource Azure autonome dans le locataire Azure AD approuvé dans l’abonnement en cours d’utilisation. Une fois l’identité créée, elle peut être attribuée à une ou plusieurs ressources Azure (dans Azure Container Instances ou d’autres services Azure). Le cycle de vie d’une identité attribuée par l’utilisateur est géré séparément du cycle de vie des groupes de conteneurs ou des autres ressources de service auxquelles elle est affectée. Ce comportement est particulièrement utile dans Azure Container Instances. Étant donné que l’identité s’étend au-delà de la durée de vie d’un groupe de conteneurs, vous pouvez la réutiliser, ainsi que d’autres paramètres standard, pour faire de vos déploiements de groupe de conteneurs des opérations hautement reproductibles.

* Une identité managée **attribuée par le système** est activée directement sur un groupe de conteneurs dans Azure Container Instances. Lorsqu’elle est activée, Azure crée une identité pour le groupe dans le locataire Azure AD approuvé par l’abonnement de l’instance. Une fois l’identité créée, ses informations d’identification sont provisionnées sur chaque conteneur du groupe. Le cycle de vie d’une identité attribuée par le système est directement lié au groupe de conteneurs sur lequel elle est activée. Si le groupe est supprimé, Azure efface automatiquement les informations d’identification et l’identité dans Azure AD.

### <a name="use-a-managed-identity"></a>Utiliser une identité managée

Pour utiliser une identité managée, l’identité doit d’abord être autorisée à accéder à une ou plusieurs ressources de service Azure (par exemple une application Web, un coffre de clés ou un compte de stockage) dans l’abonnement. Pour accéder aux ressources Azure à partir d’un conteneur en cours d’exécution, votre code doit acquérir un *jeton d’accès* à partir d’un point de terminaison Azure AD. Votre code envoie ensuite le jeton d’accès sur un appel à un service qui prend en charge l’authentification Azure AD. 

L’utilisation d’une identité managée dans un conteneur en cours d’exécution est essentiellement identique à l’utilisation d’une identité dans une machine virtuelle Azure. Consultez l’aide relative aux machines virtuelles pour en savoir plus sur l’utilisation d’un [jeton](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), d’[Azure PowerShell ou Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md), ou des [kits de développement logiciel Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cet article vous demande d’exécuter Azure CLI version 2.0.49 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Créer un Azure Key Vault

Les exemples de cet article utilisent une identité managée dans Azure Container Instances pour accéder à un code secret du coffre de clés Azure Key Vault. 

Commencez par créer un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* à l’aide de la commande [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) suivante :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pour créer un coffre de clés, utilisez la commande [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Spécifiez un nom de coffre de clés Key Vault unique. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Stockez un exemple de code secret dans le coffre de clés Key Vault à l’aide de la commande [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) :

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Utilisez les exemples suivants pour accéder au coffre de clés Key Vault à l’aide d’une identité managée attribuée par l’utilisateur ou attribuée par le système dans Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exemple 1 : utiliser une identité attribuée par l’utilisateur pour accéder à Azure Key Vault

### <a name="create-an-identity"></a>Créer une identité

Créez d’abord une identité dans votre abonnement à l’aide de la commande [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create). Vous pouvez utiliser le même groupe de ressources que celui utilisé pour créer le coffre de clés Key Vault, ou en utiliser un autre.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Pour utiliser l’identité dans les étapes suivantes, utilisez la commande [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) pour stocker l’ID de principal de service et l’ID de ressource de l’identité dans des variables.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Activer une identité attribuée par l’utilisateur dans un groupe de conteneurs

Exécutez la commande [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) suivante pour créer une instance de conteneur basée sur Ubuntu Server. Cet exemple fournit un groupe de conteneurs unique que vous pouvez utiliser de manière interactive pour accéder à d’autres services Azure. Le paramètre `--assign-identity` passe votre identité managée attribuée par l’utilisateur au groupe. Cette commande longue laisse le conteneur s’exécuter. Cet exemple utilise le même groupe de ressources que celui utilisé pour créer le coffre de clés Key Vault, mais vous pouvez en spécifier un autre.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Après quelques secondes, vous devez recevoir une réponse d’Azure CLI indiquant que le déploiement est terminé. Vérifiez son état à l’aide de la commande [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show).

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

La section `identity` de la sortie ressemble à ce qui suit, elle montre la définition de l’identité dans le groupe de conteneurs. La valeur `principalID` sous `userAssignedIdentities` correspond au principal de service de l’identité que vous avez créée dans Azure Active Directory :

```console
...
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
...
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Autoriser l’identité attribuée par l’utilisateur à accéder au coffre de clés Key Vault

Exécutez la commande [az keyvault set-policy](/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) suivante pour définir une stratégie d’accès sur le coffre de clés Key Vault. L’exemple suivant permet à l’identité attribuée par l’utilisateur d’obtenir les codes secrets du coffre de clés Key Vault :

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Utiliser une identité attribuée par l’utilisateur pour obtenir un code secret dans le coffre de clés Key Vault

Maintenant, vous pouvez utiliser l’identité managée pour accéder au coffre de clés Key Vault dans l’instance de conteneur en cours d’exécution. Pour cet exemple, lancez d’abord un shell bash dans le conteneur :

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Exécutez les commandes suivantes dans le shell bash du conteneur. Pour obtenir un jeton d’accès permettant d’utiliser Azure Active Directory pour s’authentifier sur Key Vault, exécutez la commande suivante :

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Sortie :

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Pour stocker le jeton d’accès dans une variable qui pourra être utilisée dans les prochaines commandes pour l’authentification, exécutez la commande suivante :

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Utilisez maintenant le jeton d’accès pour vous authentifier sur Key Vault et lire un code secret. Veillez à remplacer le nom de votre coffre de clés dans l’URL (*https://mykeyvault.vault.azure.net/...*) :

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

La réponse ressemble à ce qui suit, elle affiche le code secret. Dans votre code, il vous faudrait analyser cette sortie pour obtenir le code secret. Utilisez ensuite le code secret dans une opération ultérieure pour accéder à une autre ressource Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exemple 2 : utiliser une identité attribuée par le système pour accéder à Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Activer une identité attribuée par le système dans un groupe de conteneurs

Exécutez la commande [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) suivante pour créer une instance de conteneur basée sur Ubuntu Server. Cet exemple fournit un groupe de conteneurs unique que vous pouvez utiliser de manière interactive pour accéder à d’autres services Azure. Le paramètre `--assign-identity`, sans valeur supplémentaire, active une identité managée attribuée par le système dans le groupe. Cette commande longue laisse le conteneur s’exécuter. Cet exemple utilise le même groupe de ressources que celui utilisé pour créer le coffre de clés Key Vault, mais vous pouvez en spécifier un autre.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Après quelques secondes, vous devez recevoir une réponse d’Azure CLI indiquant que le déploiement est terminé. Vérifiez son état à l’aide de la commande [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show).

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

La section `identity` de la sortie ressemble à ce qui suit. Elle montre qu’une identité attribuée par le système a été créée dans Azure Active Directory :

```console
...
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
...
```

Définissez une variable sur la valeur `principalId` (l’ID du principal de service) de l’identité, pour une utilisation ultérieure.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Autoriser le groupe de conteneurs à accéder au coffre de clés Key Vault

Exécutez la commande [az keyvault set-policy](/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) suivante pour définir une stratégie d’accès sur le coffre de clés Key Vault. L’exemple suivant permet à l’identité managée attribuée par le système d’obtenir les codes secrets du coffre de clés Key Vault :

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Utiliser une identité de groupe de conteneurs pour obtenir un code secret dans le coffre de clés Key Vault

Maintenant, vous pouvez utiliser l’identité managée pour accéder au coffre de clés Key Vault dans l’instance de conteneur en cours d’exécution. Pour cet exemple, lancez d’abord un shell bash dans le conteneur :

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Exécutez les commandes suivantes dans le shell bash du conteneur. Pour obtenir un jeton d’accès permettant d’utiliser Azure Active Directory pour s’authentifier sur Key Vault, exécutez la commande suivante :

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Sortie :

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Pour stocker le jeton d’accès dans une variable qui pourra être utilisée dans les prochaines commandes pour l’authentification, exécutez la commande suivante :

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Utilisez maintenant le jeton d’accès pour vous authentifier sur Key Vault et lire un code secret. Veillez à remplacer le nom de votre coffre de clés dans l’URL (*https://mykeyvault.vault.azure.net/...*) :

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

La réponse ressemble à ce qui suit, elle affiche le code secret. Dans votre code, il vous faudrait analyser cette sortie pour obtenir le code secret. Utilisez ensuite le code secret dans une opération ultérieure pour accéder à une autre ressource Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Activer une identité managée à l’aide d’un modèle Resource Manager

Pour activer une identité managée dans un groupe de conteneurs à l’aide d’un [modèle Resource Manager](container-instances-multi-container-group.md), définissez la propriété `identity` de l’objet `Microsoft.ContainerInstance/containerGroups` avec un objet `ContainerGroupIdentity`. Dans les extraits de code suivants, la propriété `identity` est configurée pour différents scénarios. Consultez les [références sur les modèles Resource Manager](/azure/templates/microsoft.containerinstance/containergroups). Définissez `apiVersion` sur `2018-10-01`.

### <a name="user-assigned-identity"></a>Identité attribuée par l’utilisateur

Une identité attribuée par l’utilisateur est un ID de ressource qui se présente sous cette forme :

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Vous pouvez activer une ou plusieurs identités attribuées par l’utilisateur.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Identité attribuée par le système

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Identités attribuées par l’utilisateur et par le système

Sur un groupe de conteneurs, vous pouvez activer à la fois une identité attribuée par le système, et une ou plusieurs identités attribuées par l’utilisateur.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Activer une identité managée à l’aide du fichier YAML

Pour activer une identité managée dans un groupe de conteneurs déployé à l’aide d’un [fichier YAML](container-instances-multi-container-yaml.md), incluez le code YAML suivant.
Définissez `apiVersion` sur `2018-10-01`.

### <a name="user-assigned-identity"></a>Identité attribuée par l’utilisateur

Une identité attribuée par l’utilisateur est un ID de ressource qui se présente sous la forme 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Vous pouvez activer une ou plusieurs identités attribuées par l’utilisateur.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Identité attribuée par le système

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Identités attribuées par l’utilisateur et par le système

Sur un groupe de conteneurs, vous pouvez activer à la fois une identité attribuée par le système, et une ou plusieurs identités attribuées par l’utilisateur.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous en avez appris davantage sur les identités managées dans Azure Container Instances, et vous avez découvert comment :

> [!div class="checklist"]
> * Activer une identité attribuée par l’utilisateur ou par le système dans un groupe de conteneurs
> * Autoriser l’identité à accéder à un coffre de clés Azure Key Vault
> * Utiliser l’identité managée pour accéder à un coffre de clés à partir d’un conteneur en cours d’exécution

* En savoir plus sur les [identités managées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/).

* Consultez un [exemple SDK Azure Go](https://medium.com/@samkreter/c98911206328) dans lequel une identité managée est utilisée pour accéder à un coffre de clés Key Vault à partir d’Azure Container Instances.