---
title: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
description: Apprenez à gérer les ressources Azure Cosmos DB à l’aide du portail Azure, de PowerShell, de l’interface CLI et des modèles Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873673"
---
# <a name="manage-an-azure-cosmos-account"></a>Gérer un compte Azure Cosmos

Cet article décrit comment gérer différentes tâches sur un compte Azure Cosmos à l’aide du portail Azure, d’Azure PowerShell, d’Azure CLI et de modèles Azure Resource Manager.

## <a name="create-an-account"></a>Créer un compte

### <a id="create-database-account-via-portal"></a>Portail Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interface CLI Azure

Voir [Créer un compte Azure Cosmos DB avec Azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>Azure PowerShell

Voir [Créer un compte Azure Cosmos DB avec PowerShell](manage-with-powershell.md#create-account)

### <a id="create-database-account-via-arm-template"></a>Modèle Azure Resource Manager

Ce modèle Azure Resource Manager va créer un compte Azure Cosmos pour n’importe quelle API SQL configurée avec deux régions et des options pour sélectionner le niveau de cohérence, le basculement automatique et la fonction multimaître. Pour déployer ce modèle, cliquez sur Déployer sur Azure dans la page lisezmoi (readme) [Créer un compte Azure Cosmos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql).

## <a name="addremove-regions-from-your-database-account"></a>Ajouter/supprimer des régions à partir de votre compte de base de données

### <a id="add-remove-regions-via-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Azure Cosmos, puis ouvrez le menu **Répliquer les données globalement**.

1. Pour ajouter des régions, sélectionnez les hexagones sur la carte avec l’étiquette **+** correspondante. Sinon, pour ajouter une région, sélectionnez l’option **+ Ajouter une région**, puis choisissez une région dans le menu déroulant.

1. Pour supprimer des régions, désactivez une ou plusieurs régions à partir de la carte en sélectionnant les hexagones bleus avec des coches. Vous pouvez aussi sélectionner l’icône de la « Corbeille » (🗑) en regard de la région, sur le côté droit.

1. Pour enregistrer vos modifications, sélectionnez **OK**.

   ![Ajouter ou supprimer le menu des régions](./media/how-to-manage-database-account/add-region.png)

Dans un mode écriture dans une seule région, vous ne pouvez pas supprimer la région d’écriture. Vous devez basculer vers une autre région avant de pouvoir supprimer la région d’écriture actuelle.

Dans un mode d’écriture dans plusieurs régions, vous pouvez ajouter ou supprimer n’importe quelle région si vous en avez au moins une.

### <a id="add-remove-regions-via-cli"></a>Interface CLI Azure

Voir [Ajouter ou supprimer des régions avec Azure CLI](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

Voir [Ajouter ou supprimer des régions avec PowerShell](manage-with-powershell.md#update-account)

## <a id="configure-multiple-write-regions"></a>Configurer plusieurs régions d’écriture

### <a id="configure-multiple-write-regions-portal"></a>Portail Azure

Ouvrez l’onglet **Répliquer les données globalement** et sélectionnez **Activer** pour activer l’écriture dans plusieurs régions. Une fois que vous avez activé l’écriture dans plusieurs régions, toutes les régions de lecture indiquées sur votre compte deviennent des régions de lecture et d’écriture.

![Capture d’écran de la configuration de la fonction multimaître dans un compte Azure Cosmos](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>Interface CLI Azure

Voir [Activer plusieurs régions d’écriture avec Azure CLI](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Voir [Activer plusieurs régions d’écriture avec PowerShell](manage-with-powershell.md#multi-master)

### <a id="configure-multiple-write-regions-arm"></a>modèle Azure Resource Manager

Un compte peut passer de maître unique à multimaître en déployant le modèle Resource Manager utilisé pour créer le compte et définir `enableMultipleWriteLocations: true`. Le modèle Azure Resource Manager suivant est un modèle minimal qui va déployer un compte Azure Cosmos pour l’API SQL avec deux régions et plusieurs emplacements d’écriture activés.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Activer le basculement automatique pour votre compte Azure Cosmos

L’option de basculement automatique permet à Azure Cosmos DB de basculer vers la région ayant la priorité de basculement la plus élevée sans action de l’utilisateur au cas où une région devienne indisponible. Lorsque le basculement automatique est activé, la priorité des régions peut être modifiée. Le compte doit avoir au moins deux régions pour activer le basculement automatique.

### <a id="enable-automatic-failover-via-portal"></a>Portail Azure

1. Dans votre compte Azure Cosmos, ouvrez le volet **Répliquer les données globalement**.

2. En haut du volet, sélectionnez **Basculement automatique**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le volet **Basculement automatique**, vérifiez que l’option **Activer le basculement automatique** est définie sur **ACTIVÉ**. 

4. Sélectionnez **Enregistrer**.

   ![Menu du portail pour le basculement automatique](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Interface CLI Azure

Voir [Activer le basculement automatique avec Azure CLI](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Voir [Activer le basculement automatique avec PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Définir les priorités de basculement pour votre compte Azure Cosmos

Une fois qu’un compte Cosmos est configuré pour le basculement automatique, la priorité de basculement des régions peut être modifiée.

> [!IMPORTANT]
> Vous ne pouvez pas modifier la région d’écriture (priorité de basculement de zéro) lorsque le compte est configuré pour le basculement automatique. Pour changer la région d’écriture, vous devez désactiver le basculement automatique et effectuer un basculement manuel.

### <a id="set-failover-priorities-via-portal"></a>Portail Azure

1. Dans votre compte Azure Cosmos, ouvrez le volet **Répliquer les données globalement**.

2. En haut du volet, sélectionnez **Basculement automatique**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le volet **Basculement automatique**, vérifiez que l’option **Activer le basculement automatique** est définie sur **ACTIVÉ**.

4. Pour modifier la priorité de basculement, faites glisser les régions de lecture via les trois points à gauche de la ligne qui s’affichent quand vous placez le curseur dessus.

5. Sélectionnez **Enregistrer**.

   ![Menu du portail pour le basculement automatique](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Interface CLI Azure

Voir [Définir la priorité de basculement avec Azure CLI](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Voir [Définir la priorité de basculement avec PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a id="manual-failover"></a>Effectuer un basculement manuel sur un compte Azure Cosmos

> [!IMPORTANT]
> Le compte Azure Cosmos doit être configuré pour le basculement manuel pour que l’opération fonctionne.

Le processus de basculement manuel implique de changer la région d’écriture du compte (priorité de basculement = 0) pour une autre région configurée pour le compte.

> [!NOTE]
> Les comptes multimaître ne peuvent pas être basculés manuellement. Pour les applications qui utilisent le SDK Azure Cosmos, celui-ci détecte le moment où une région devient indisponible, puis redirige automatiquement vers la région la plus proche si vous utilisez une API d’hébergement multiple dans le SDK.

### <a id="enable-manual-failover-via-portal"></a>Portail Azure

1. Accédez à votre compte Azure Cosmos, puis ouvrez le menu **Répliquer les données globalement**.

2. En haut du menu, sélectionnez **Basculement manuel**.

   ![Menu pour répliquer les données globalement](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Dans le menu **Basculement manuel**, sélectionnez votre nouvelle région d’écriture. Sélectionnez la case à cocher pour indiquer que vous comprenez que cette option modifie votre région d’écriture.

4. Pour déclencher le basculement, sélectionnez **OK**.

   ![Menu du portail pour le basculement manuel](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interface CLI Azure

Voir [Déclencher un basculement manuel avec Azure CLI](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Voir [Déclencher un basculement manuel avec PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations et d’exemples de gestion du compte Azure Cosmos ainsi que des bases de données et conteneurs, consultez les articles suivants :

* [Gérer Azure Cosmos DB à l’aide d’Azure PowerShell](manage-with-powershell.md)
* [Gérer Azure Cosmos DB à l’aide d’Azure CLI](manage-with-cli.md)
