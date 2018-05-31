---
title: Utiliser une MSI de machine virtuelle Linux pour accéder à Azure Cosmos DB
description: Un didacticiel qui vous guide tout au long du processus d’utilisation d’une MSI (Managed Service Identity) assignée par le système sur une machine virtuelle Linux en vue d’accéder à Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: skwan
ms.openlocfilehash: b84426ad28dbc2264bd9b28fe0697a88390bb58d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304128"
---
# <a name="tutorial-use-a-linux-vm-msi-to-access-azure-cosmos-db"></a>Didacticiel : Utiliser un MSI de machine virtuelle Linux pour accéder à Azure Cosmos DB 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


Ce didacticiel vous montre comment créer et utiliser une MSI de machine virtuelle Linux. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Linux avec une MSI activée
> * Création d’un compte Cosmos DB
> * Créer une collection dans le compte Cosmos DB
> * Permettre à l’identité MSI d’accéder à une instance Azure Cosmos DB
> * Récupérer le `principalID` de la MSI de la machine virtuelle Linux
> * Obtenir un jeton d’accès et utiliser celui-ci pour appeler Azure Resource Manager
> * Obtenir les clés d’accès à partir d’Azure Resource Manager pour effectuer des appels Cosmos DB

## <a name="prerequisites"></a>Prérequis


Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com) avant de continuer.

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Pour exécuter les exemples de script CLI dans ce didacticiel, vous avez deux possibilités :

- Utiliser [Azure Cloud Shell](~/articles/cloud-shell/overview.md) dans le portail Azure ou via le bouton **Essayer** situé en haut à droite de chaque bloc de code.
- [Installer la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou ultérieure) si vous préférez utiliser une console CLI locale.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Créer une machine virtuelle Linux dans un nouveau groupe de ressources

Pour ce didacticiel, créez une nouvelle machine virtuelle Linux.

Pour créer une machine virtuelle compatible avec l’identité du service administré :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az_login). Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez déployer la machine virtuelle :

   ```azurecli-interactive
   az login
   ```

2. Créez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#terminology) pour l’imbrication et le déploiement de votre machine virtuelle et de ses ressources connexes, à l’aide de la commande [az group create](/cli/azure/group/#az_group_create). Vous pouvez ignorer cette étape si vous possédez déjà le groupe de ressources que vous souhaitez utiliser à la place :

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm/#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVM* avec une identité du service administré, comme le demande le paramètre `--assign-identity`. Les paramètres `--admin-username` et `--admin-password` spécifient le nom d’utilisateur et le mot de passe d’administration du compte pour la connexion à la machine virtuelle. Mettez à jour ces valeurs en fonction de votre environnement : 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="create-a-cosmos-db-account"></a>Création d’un compte Cosmos DB 

Si vous n’en avez pas déjà un, créez un compte Cosmos DB. Vous pouvez ignorer cette étape et utiliser un compte Cosmos DB existant. 

1. Cliquez sur le bouton **+/Créer un service** dans l’angle supérieur gauche du portail Azure.
2. Cliquez sur **Bases de données**, puis sur **Azure Cosmos DB** pour afficher le panneau Nouveau compte.
3. Entrez un **ID** pour le compte Cosmos DB, que vous pourrez utiliser plus tard.  
4. **API** doit être défini sur « SQL ». L’approche décrite dans ce didacticiel peut être utilisée avec les autres types d’API disponibles. Toutefois, les étapes de ce didacticiel correspondent à celles de l’API SQL.
5. Assurez-vous que les champs **Abonnement** et **Groupe de ressources** correspondent à ceux que vous avez spécifiés lorsque vous avez créé votre machine virtuelle à l’étape précédente.  Sélectionnez un **emplacement** où Cosmos DB est disponible.
6. Cliquez sur **Créer**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Créer une collection dans le compte Cosmos DB

Ensuite, ajoutez une collection de données dans le compte Cosmos DB que vous pourrez interroger dans les prochaines étapes.

1. Accédez au compte Cosmos DB que vous venez de créer.
2. Sous l’onglet **Vue d’ensemble**, cliquez sur le bouton **+ Ajouter une collection** pour afficher le panneau Ajouter une collection.
3. Attribuez à la collection un ID de base de données et un ID de collection. Sélectionnez une capacité de stockage, entrez une clé de partition et une valeur de débit, puis cliquez sur **OK**.  Pour ce didacticiel, il suffit d’utiliser « Test » comme ID de base de données et ID de collection, et de sélectionner une capacité de stockage fixe et le débit le plus bas (400 RU/s).  

## <a name="retrieve-the-principalid-of-the-linux-vms-msi"></a>Récupérer le `principalID` de la MSI de la machine virtuelle Linux

Pour accéder aux clés d’accès au compte Cosmos DB à partir du Gestionnaire de ressources dans la section suivante, vous devez récupérer le `principalID` du MSI de la machine virtuelle Linux.  Veillez à remplacer les valeurs des paramètres `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (groupe de ressources dans lequel vous machine virtuelle réside) et `<VM NAME>` avec vos propres valeurs.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
La réponse inclut les détails de la MSI assignée par le système (notez le principalID tel qu’il est utilisé dans la section suivante) :

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Accordez aux clés d’accès du compte Cosmos DB l’accès à votre MSI de machine virtuelle Linux

Cosmos DB ne prend pas en charge l’authentification Azure AD en mode natif. Toutefois, vous pouvez utiliser une identité MSI pour récupérer une clé d’accès Cosmos DB à partir de Resource Manager, puis utiliser cette clé pour accéder à Cosmos DB. Dans cette étape, vous allez accorder l’accès MSI aux clés pour le compte Cosmos DB.

Pour accorder à votre identité MSI l’accès au compte Cosmos DB dans Azure Resource Manager à l’aide d’Azure CLI, mettez à jour les valeurs pour `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` et `<COSMOS DB ACCOUNT NAME>` pour votre environnement. Remplacez `<MSI PRINCIPALID>` par la propriété `principalId` retournée par la commande `az resource show` dans [Récupérer le principalID de MSI de la machine virtuelle Linux](#retrieve-the-principalID-of-the-linux-VM's-MSI).  Cosmos DB prend en charge deux niveaux de granularité lors de l’utilisation des clés d’accès : accès en lecture/écriture au compte et accès en lecture seule au compte.  Pour obtenir les clés en lecture/écriture du compte, affectez le rôle `DocumentDB Account Contributor`, et pour obtenir les clés en lecture seule du compte, affectez le rôle `Cosmos DB Account Reader Role` :

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

La réponse comprend les détails de l’attribution de rôle créée :

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-linux-vms-msi-and-use-it-to-call-azure-resource-manager"></a>Obtenir un jeton d’accès à l’aide de la MSI de la machine virtuelle Linux et utiliser celui-ci pour appeler Azure Resource Manager

Pour la suite de ce didacticiel, nous allons utiliser la machine virtuelle que nous avons créée précédemment.

Pour effectuer cette procédure, vous avez besoin d’un client SSH. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Dans le portail Azure, accédez à **Machines virtuelles**, accédez à votre machine virtuelle Linux, puis, en haut de la page **Vue d’ensemble**, cliquez sur **Se connecter**. Copiez la chaîne permettant de se connecter à votre machine virtuelle. 
2. Connectez-vous à votre machine virtuelle en utilisant votre client SSH.  
3. Ensuite, vous serez invité à entrer le **Mot de passe** que vous avez ajouté à la création de la **machine virtuelle Linux**. Vous devez être connecté.  
4. Utilisez CURL pour obtenir un jeton d’accès pour Azure Resource Manager : 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > Dans la requête précédente, la valeur du paramètre « resource » doit correspondre exactement à ce qu’Azure AD attend. Lorsque vous utilisez l’ID de ressource Azure Resource Manager, vous devez inclure la barre oblique de fin à l’URI.
    > Dans la réponse suivante, l’élément access_token a été raccourci par souci de concision.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Obtenir les clés d’accès à partir d’Azure Resource Manager pour effectuer des appels Cosmos DB  

Maintenant, utilisez CURL pour appeler le Gestionnaire des ressources à l’aide du jeton d’accès récupéré dans la section précédente, afin de récupérer la clé d’accès au compte Cosmos DB. Une fois la clé d’accès obtenue, nous pouvons interroger Cosmos DB. N’oubliez pas de remplacer les paramètres `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` et `<COSMOS DB ACCOUNT NAME>` par vos propres valeurs. Remplacez la valeur `<ACCESS TOKEN>` par le jeton d’accès que vous avez récupéré précédemment.  Si vous souhaitez récupérer les clés en lecture/écriture, utilisez le type d’opération de clé `listKeys`.  Si vous souhaitez récupérer les clés en lecture seule, utilisez le type d’opération de clé `readonlykeys` :

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Le texte de l’URL précédente respectant la casse, veillez à respecter les majuscules et les minuscules pour vos groupes de ressources. En outre, il est important de savoir qu’il s’agit d’une demande POST et non d’une demande GET. Assurez-vous donc que vous transmettez une valeur pour capturer une limite de longueur avec -d qui peut être zéro.  

La réponse CURL vous donne la liste des clés.  Par exemple, si vous obtenez les clés en lecture seule :  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Maintenant que vous disposez de la clé d’accès pour le compte Cosmos DB, vous pouvez la passer à un SDK Cosmos DB et effectuer des appels pour accéder au compte.  Pour un exemple rapide, vous pouvez passer la clé d’accès à Azure CLI.  Vous pouvez obtenir l’<COSMOS DB CONNECTION URL> sous l’onglet **Vue d’ensemble**, dans le panneau du compte Cosmos DB, dans le portail Azure.  Remplacez <ACCESS KEY> par la valeur que vous avez obtenue ci-dessus :

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Cette commande CLI retourne des informations détaillées sur la collection :

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation de l’identité MSI, consultez [Identité du service administré (MSI) pour les ressources Azure](overview.md).

