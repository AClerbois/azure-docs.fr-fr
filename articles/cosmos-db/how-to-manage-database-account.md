---
title: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
description: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: d3ee6db382f848a029f3e0355abdfe5eecc80656
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035829"
---
# <a name="manage-database-accounts-in-azure-cosmos-db"></a>Gérer les comptes de base de données dans Azure Cosmos DB

Cet article décrit comment gérer votre compte Azure Cosmos DB. Vous apprenez à définir le multihébergement, à ajouter ou supprimer une région, à configurer plusieurs régions d’écriture et à définir les priorités de basculement. 

## <a name="create-a-database-account"></a>Création d’un compte de base de données

### <a id="create-database-account-via-portal"></a>Portail Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interface CLI Azure

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Configurer les clients pour le multihébergement

### <a id="configure-clients-multi-homing-dotnet"></a>Kit SDK .NET

```csharp
// Create a new connection policy.
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults.
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to preferred locations.
// The name of the location will match what you see in the portal, etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Kit SDK Java Async

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Kit SDK Java Sync

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
// Set up the connection policy with your preferred regions.
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client.
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Kit SDK Python

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>Ajouter/supprimer des régions à partir de votre compte de base de données

### <a id="add-remove-regions-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos DB et ouvrez le menu **Répliquer les données globalement**.

2. Pour ajouter des régions, sélectionnez les hexagones sur la carte avec l’étiquette **+** qui correspond à la région souhaitée. Pour ajouter une région, sélectionnez l’option **+ Ajouter une région** et choisissez une région dans le menu déroulant.

3. Pour supprimer des régions, désactivez une ou plusieurs régions à partir de la carte en sélectionnant les hexagones bleus avec des coches. Vous pouvez aussi sélectionner l’icône de la « Corbeille » (🗑) en regard de la région, sur le côté droit.

4. Pour enregistrer vos modifications, sélectionnez **OK**.

   ![Ajouter ou supprimer le menu des régions](./media/how-to-manage-database-account/add-region.png)

Dans un mode d’écriture à une seule région, vous ne pouvez pas supprimer la région d’écriture. Vous devez basculer vers une autre région avant de pouvoir supprimer la région d’écriture actuelle.

Dans un mode d’écriture à plusieurs régions, vous pouvez ajouter ou supprimer n’importe quelle région s’il vous en reste au moins une.

### <a id="add-remove-regions-via-cli"></a>Interface CLI Azure

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>Configurer les régions d’écriture multiples

### <a id="configure-multiple-write-regions-portal"></a>Portail Azure

Lorsque vous créez un compte de base de données, assurez-vous que le paramètre **Multi-region Writes** (Écritures multirégions) est activé.

![Capture d’écran de création de compte Azure Cosmos](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Interface CLI Azure

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>modèle Azure Resource Manager

Le code JSON suivant est un exemple de modèle Resource Manager. Vous pouvez l’utiliser pour déployer un compte Azure Cosmos DB avec une stratégie de cohérence d’obsolescence limitée. L’intervalle d’obsolescence maximale est défini à 5 secondes. Le nombre maximal de requêtes d’obsolescence qui est toléré est défini sur 100. Pour en savoir plus sur le format et la syntaxe du modèle Resource Manager, consultez [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a id="manual-failover"></a>Activer le basculement manuel pour votre compte Azure Cosmos DB

### <a id="enable-manual-failover-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos DB et ouvrez le menu **Répliquer les données globalement**.

2. En haut du menu, sélectionnez **Basculement manuel**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le menu **Basculement manuel**, sélectionnez votre nouvelle région d’écriture. Sélectionnez la case à cocher pour indiquer que vous comprenez que cette option modifie votre région d’écriture.

4. Pour déclencher le basculement, sélectionnez **OK**.

   ![Menu du portail pour le basculement manuel](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interface CLI Azure

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a id="automatic-failover"></a>Activer le basculement automatique pour votre compte Azure Cosmos DB

### <a id="enable-automatic-failover-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos DB et ouvrez le volet **Répliquer les données globalement**. 

2. En haut du volet, sélectionnez **Basculement automatique**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le volet **Basculement automatique**, vérifiez que l’option **Activer le basculement automatique** est définie sur **ACTIVÉ**. 

4. Sélectionnez **Enregistrer**.

   ![Menu du portail pour le basculement automatique](./media/how-to-manage-database-account/automatic-failover.png)

Vous pouvez également définir les priorités de basculement dans ce menu.

### <a id="enable-automatic-failover-via-cli"></a>Interface CLI Azure

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-db-account"></a>Définir les priorités de basculement pour votre compte Azure Cosmos DB

### <a id="set-failover-priorities-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos DB et ouvrez le volet **Répliquer les données globalement**. 

2. En haut du volet, sélectionnez **Basculement automatique**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le volet **Basculement automatique**, vérifiez que l’option **Activer le basculement automatique** est définie sur **ACTIVÉ**. 

4. Pour modifier la priorité de basculement, faites glisser les régions de lecture via les trois points à gauche de la ligne qui s’affichent quand vous placez le curseur dessus. 

5. Sélectionnez **Enregistrer**.

   ![Menu du portail pour le basculement automatique](./media/how-to-manage-database-account/automatic-failover.png)

Vous ne pouvez pas modifier la région d’écriture dans ce menu. Vous devez effectuer un basculement manuel pour modifier la région d’écriture manuellement.

### <a id="set-failover-priorities-via-cli"></a>Interface CLI Azure

```bash
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment gérer les niveaux de cohérence et les conflits de données dans Azure Cosmos DB. Consultez les articles suivants :

* [Gérer la cohérence](how-to-manage-consistency.md)
* [Gérer les conflits entre les régions](how-to-manage-conflicts.md)

