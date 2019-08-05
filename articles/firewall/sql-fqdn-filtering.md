---
title: Configurer des règles d’application de pare-feu Azure avec des noms de domaine complets SQL
description: Dans cet article, vous allez apprendre à configurer des noms de domaine complets SQL dans les règles d’application de pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 239998f29ac9a578174c5dba547bb24ba0755505
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318185"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configurer des règles d’application de pare-feu Azure avec des noms de domaine complets SQL

> [!IMPORTANT]
> Les règles d’application de pare-feu Azure avec des noms de domaine complets SQL sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez maintenant configurer des règles d’application de pare-feu Azure avec des noms de domaine complets SQL. Cela vous permet de limiter l’accès à partir de vos réseaux virtuels aux seules instances de serveur SQL spécifiées.

Grâce aux noms de domaine complets SQL, vous pouvez filtrer le trafic :

- À partir de vos réseaux virtuels pour une Azure SQL Database ou un Azure SQL Data Warehouse. Par exemple :  Autoriser uniquement l’accès à *sql-server1.database.windows.net*.
- Envoi local vers Azure SQL Managed Instances ou IaaS SQL en cours d’exécution dans vos réseaux virtuels.
- Envoi rayon à rayon vers Azure SQL Managed Instances ou IaaS SQL en cours d’exécution dans vos réseaux virtuels.

Dans la préversion publique, le filtrage de nom de domaine complet SQL est pris en charge uniquement en [mode proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (port 1433). Si vous utilisez SQL dans le mode de redirection par défaut, vous pouvez filtrer les accès à l’aide de la balise de service SQL dans le cadre des [règles de réseau](overview.md#network-traffic-filtering-rules).
Si vous utilisez des ports qui ne sont pas par défaut pour le trafic IaaS SQL, vous pouvez configurer ces ports dans les règles d’application de pare-feu.

Les règles d’applications avec des noms de domaine complets SQL sont actuellement disponibles dans toutes les régions via le portail Azure, Azure CLI, REST et les modèles.

## <a name="configure-using-azure-cli"></a>Configurer à l’aide d’Azure CLI

1. Déployer un [pare-feu Azure à l’aide de Azure CLI](deploy-cli.md).
2. Si vous filtrez le trafic vers Azure SQL Database, SQL Data Warehouse ou SQL Managed Instance, assurez-vous que le mode de connectivité SQL est défini sur **Proxy**. Pour savoir comment changer de mode de connectivité SQL, consultez [Architecture de connectivité Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > Le mode *proxy* de SQL peut entraîner une plus grande latence par rapport au mode de *redirection*. Si vous souhaitez continuer à utiliser le mode de redirection, qui est le mode par défaut pour les clients se connectant à Azure, vous pouvez filtrer l’accès en utilisant la [balise de service](service-tags.md) SQL dans les [règles de réseau](tutorial-firewall-deploy-portal.md#configure-a-network-rule) du pare-feu.

3. Configurer une règle d’application avec des noms de domaine complet SQL pour autoriser l’accès à un serveur SQL :

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Configurer à l’aide du portail Azure
1. Déployer un [pare-feu Azure à l’aide de Azure CLI](deploy-cli.md).
2. Si vous filtrez le trafic vers Azure SQL Database, SQL Data Warehouse ou SQL Managed Instance, assurez-vous que le mode de connectivité SQL est défini sur **Proxy**. Pour savoir comment changer de mode de connectivité SQL, consultez [Architecture de connectivité Azure SQL](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > Le mode *proxy* de SQL peut entraîner une plus grande latence par rapport au mode de *redirection*. Si vous souhaitez continuer à utiliser le mode de redirection, qui est le mode par défaut pour les clients se connectant à Azure, vous pouvez filtrer l’accès en utilisant la [balise de service](service-tags.md) SQL dans les [règles de réseau](tutorial-firewall-deploy-portal.md#configure-a-network-rule) du pare-feu.
3. Ajouter la règle d’application avec le protocole approprié, le port et le nom de domaine complet SQL, puis sélectionnez **Enregistrer**.
   ![règle d’application avec des noms de domaine complet SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Accédez à SQL à partir d’une machine virtuelle dans un réseau virtuel qui filtre le trafic à l’aide du pare-feu. 
5. Vérifiez que les [journaux du pare-feu d’Azure](log-analytics-samples.md) sont autorisés à afficher le trafic.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le proxy de SQL et sur les modes de redirection, consultez [Architecture de connectivité de base de données SQL Azure](../sql-database/sql-database-connectivity-architecture.md).