---
title: Configurer des pare-feux et des réseaux virtuels dans Stockage Azure | Microsoft Docs
description: Configurez une sécurité réseau en couche pour votre compte de stockage.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: cbrooks
ms.subservice: common
ms.openlocfilehash: 6d6ca1fe1256f1571079027ebd299492bfa62f41
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280738"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurer des pare-feux et des réseaux virtuels dans Stockage Azure

Stockage Azure fournit un modèle de sécurité en couche. Ce modèle vous permet de sécuriser vos comptes de stockage pour un ensemble spécifique de réseaux pris en charge. Quand des règles de réseau sont configurées, seules les applications demandant des données sur l’ensemble de réseaux spécifié peuvent accéder à un compte de stockage.

Une application qui accède à un compte de stockage alors que des règles de réseau sont en vigueur requiert une autorisation appropriée sur la demande. L’autorisation est pris en charge avec les informations d’identification Azure Active Directory (Azure AD) pour les objets BLOB et files d’attente, avec une clé d’accès de compte valide ou avec un jeton SAP.

> [!IMPORTANT]
> L’activation des règles de pare-feu pour votre compte de stockage bloque les demandes entrantes pour les données par défaut, sauf si les demandes proviennent d’un service qui fonctionne au sein d’un réseau virtuel (VNet) Azure. Les demandes qui sont bloquées comprennent les demandes émanant d’autres services Azure, du portail Azure, des services de journalisation et de métriques, etc.
>
> Vous pouvez accorder l’accès aux services Azure qui fonctionnent à partir d’un réseau virtuel en autorisant le sous-réseau de l’instance de service. Activez un nombre limité de scénarios via le mécanisme [Exceptions](#exceptions) décrit dans la section suivante. Pour accéder au portail Azure, vous devez utiliser un ordinateur qui se trouve dans la limite de confiance (IP ou réseau virtuel) que vous avez définie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scénarios

Configurez les comptes de stockage pour refuser l’accès au trafic de tous les réseaux (y compris le trafic Internet) par défaut. Accordez ensuite l’accès au trafic de réseaux virtuels spécifiques. Cette configuration vous permet de créer une limite de réseau sécurisée pour vos applications. Vous pouvez aussi accorder l’accès à des plages d’adresses IP Internet publiques, en activant des connexions à partir de clients Internet ou locaux spécifiques.

Les règles de réseau sont appliquées sur tous les protocoles réseau vers le stockage Azure, notamment REST et SMB. Pour accéder aux données avec des outils tels que le portail Azure, l’Explorateur de stockage et AZCopy, des règles de réseau explicites sont requises.

Vous pouvez appliquer des règles de réseau aux comptes de stockage existant ou lorsque vous créez des comptes de stockage.

Une fois appliquées, les règles de réseau concernent toutes les demandes. Les jetons SAS qui accordent l’accès à une adresse IP spécifique servent à limiter l’accès du détenteur du jeton, mais n’accordent pas d’accès au-delà des règles de réseau configurées.

Le trafic des disques de machine virtuelle (notamment les opérations de montage et démontage et les E/S de disque) n’est pas affecté par les règles de réseau. L’accès REST aux objets blob de pages est protégé par les règles de réseau.

Les comptes de stockage Classic ne prennent pas en charge les pare-feux et les réseaux virtuels.

Vous pouvez utiliser des disques non managés dans les comptes de stockage avec des règles de réseau appliquées à la sauvegarde et la restauration de machines virtuelles en créant une exception. Ce processus est décrit dans la section [Exceptions](#exceptions) de cet article. Les exceptions de pare-feu ne sont pas applicables avec disques managés dans la mesure où ils sont déjà gérés par Azure.

## <a name="change-the-default-network-access-rule"></a>Changer la règle d’accès réseau par défaut

Par défaut, les comptes de stockage acceptent les connexions des clients sur n’importe quel réseau. Pour limiter l’accès aux réseaux sélectionnés, vous devez d’abord changer l’action par défaut.

> [!WARNING]
> Le changement des règles de réseau peut impacter la capacité de vos applications à se connecter au stockage Azure. La définition de la règle de réseau par défaut sur **Refuser** bloque tout accès aux données, sauf si des règles de réseau spécifiques **accordant** l’accès sont également appliquées. Accordez l’accès uniquement aux réseaux autorisés à l’aide des règles de réseau avant de changer la règle par défaut pour refuser l’accès.

### <a name="managing-default-network-access-rules"></a>Gestion des règles d’accès réseau par défaut

Vous pouvez gérer les règles d’accès réseau par défaut pour les comptes de stockage via le portail Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portail Azure

1. Accédez au compte de stockage que vous voulez sécuriser.

1. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.

1. Pour refuser l’accès par défaut, choisissez d’autoriser l’accès à partir de **Réseaux sélectionnés**. Pour autoriser le trafic de tous les réseaux, choisissez d’autoriser l’accès à partir de **Tous les réseaux**.

1. Cliquez sur **Enregistrer** pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell

1. Installez [Azure PowerShell](/powershell/azure/install-Az-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).

1. Affichez l’état de la règle par défaut pour le compte de stockage.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Définissez la règle par défaut pour refuser l’accès réseau par défaut.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Définissez la règle par défaut pour autoriser l’accès réseau par défaut.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Installez [Azure CLI](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).

1. Affichez l’état de la règle par défaut pour le compte de stockage.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Définissez la règle par défaut pour refuser l’accès réseau par défaut.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Définissez la règle par défaut pour autoriser l’accès réseau par défaut.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Accorder l’accès à partir d’un réseau virtuel

Vous pouvez configurer des comptes de stockage pour autoriser l’accès uniquement à partir de réseaux virtuels spécifiques.

Activez un [point de terminaison de service](/azure/virtual-network/virtual-network-service-endpoints-overview) pour le stockage Azure dans le réseau virtuel. Ce point de terminaison donne au trafic une route optimale vers le service Stockage Azure. Les identités du réseau virtuel et du sous-réseau sont également transmises avec chaque demande. Les administrateurs peuvent ensuite configurer des règles de réseau pour le compte de stockage qui autorisent la réception des demandes à partir de sous-réseaux spécifiques dans le réseau virtuel. Les clients qui obtiennent un accès par le biais de ces règles de réseau doivent continuer à respecter les exigences d’autorisation du compte de stockage pour accéder aux données.

Chaque compte de stockage prend en charge jusqu'à 100 règles de réseau virtuel qui peuvent être combinées avec des [règles de réseau IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Régions de réseau virtuel disponibles

En général, les points de terminaison de service fonctionnent entre les réseaux virtuels et les instances de service d’une même région Azure. Quand les points de terminaison de service sont utilisés avec Stockage Azure, cette étendue inclut aussi la [région jumelée](/azure/best-practices-availability-paired-regions). Les points de terminaison de service permettent une continuité des activités pendant un basculement régional ainsi qu’un accès sans interruption aux instances de stockage géoredondantes en lecture seule (RA-GRS). Les règles de réseau qui autorisent l’accès à un compte de stockage à partir d’un réseau virtuel accordent également l’accès à toutes les instances RA-GRS.

Quand vous planifiez une récupération d’urgence en cas de panne régionale, vous devez créer les réseaux virtuels à l’avance dans la région jumelée. Activez les points de terminaison de service pour le Stockage Azure, avec des règles de réseau accordant l’accès à partir de ces réseaux virtuels alternatifs. Appliquez ensuite ces règles à vos comptes de stockage géoredondants.

> [!NOTE]
> Les points de terminaison de service ne s’appliquent pas au trafic extérieur à la région du réseau virtuel et à la région jumelée désignée. Vous pouvez appliquer les règles de réseau accordant l’accès à partir de réseaux virtuels aux comptes de stockage dans la région principale d’un compte de stockage ou dans la région jumelée désignée.

### <a name="required-permissions"></a>Autorisations requises

Pour appliquer une règle de réseau virtuel à un compte de stockage, l’utilisateur doit disposer des autorisations appropriées pour les sous-réseaux à ajouter. L’autorisation nécessaire est *joindre le service à un sous-réseau* et est incluse dans le rôle intégré *Contributeur de compte de stockage*. Elle peut également être ajoutée aux définitions de rôles personnalisés.

Le compte de stockage et les réseaux virtuels qui ont un accès peuvent se trouver dans des abonnements différents, mais ces abonnements doivent faire partie du même locataire Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>Gestion des règles de réseau virtuel

Vous pouvez gérer les règles de réseau virtuel pour les comptes de stockage via le portail Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portail Azure

1. Accédez au compte de stockage que vous voulez sécuriser.

1. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.

1. Vérifiez que vous avez choisi d’autoriser l’accès à partir des **Réseaux sélectionnés**.

1. Pour accorder l’accès à un réseau virtuel avec une nouvelle règle de réseau, sous **Réseaux virtuels**, cliquez sur **Ajouter un réseau virtuel existant**, sélectionnez les options **Réseaux virtuels** et **Sous-réseaux**, puis cliquez sur **Ajouter**. Pour créer un réseau virtuel et lui accorder l’accès, cliquez sur **Ajouter un nouveau réseau virtuel**. Fournissez les informations nécessaires pour créer le nouveau réseau virtuel, puis cliquez sur **Créer**.

    > [!NOTE]
    > Si un point de terminaison de service pour le stockage Azure n’a pas déjà été configuré pour le réseau virtuel et les sous-réseaux sélectionnés, vous pouvez le configurer dans le cadre de cette opération.

1. Pour supprimer une règle de réseau ou sous-réseau virtuel, cliquez sur **...** pour ouvrir le menu contextuel du réseau ou sous-réseau virtuel, puis cliquez sur **Supprimer**.

1. Cliquez sur **Enregistrer** pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell

1. Installez [Azure PowerShell](/powershell/azure/install-Az-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).

1. Listez les règles de réseau virtuel.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Activez le point de terminaison de service pour Stockage Azure sur un réseau virtuel et un sous-réseau existants.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Ajoutez une règle de réseau pour un réseau virtuel et un sous-réseau.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

1. Supprimez une règle de réseau pour un réseau virtuel et un sous-réseau.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon les règles de réseau n’ont aucun effet.

#### <a name="cliv2"></a>CLIv2

1. Installez [Azure CLI](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).

1. Listez les règles de réseau virtuel.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Activez le point de terminaison de service pour Stockage Azure sur un réseau virtuel et un sous-réseau existants.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Ajoutez une règle de réseau pour un réseau virtuel et un sous-réseau.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

1. Supprimez une règle de réseau pour un réseau virtuel et un sous-réseau.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon les règles de réseau n’ont aucun effet.

## <a name="grant-access-from-an-internet-ip-range"></a>Accorder l’accès à partir d’une plage d’adresses IP Internet

Vous pouvez configurer les comptes de stockage pour autoriser l’accès à partir de plages d’adresses IP Internet publiques spécifiques. Cette configuration donne l’accès à des services Internet et des réseaux locaux spécifiques et bloque le trafic Internet général.

Fournissez les plages d’adresses Internet autorisées à l’aide de la [notation CIDR](https://tools.ietf.org/html/rfc4632) sous la forme *16.17.18.0/24* ou sous la forme d’adresses IP individuelles de type *16.17.18.19*.

   > [!NOTE]
   > Les petites plages d’adresses qui utilisent les tailles de préfixe « /31 » ou « /32 » ne sont pas prises en charge. Ces plages doivent être configurées à l’aide des règles d’adresses IP individuelles.

Les règles de réseau IP sont autorisées uniquement pour les adresses IP **Internet publiques**. Les plages d’adresses IP réservées aux réseaux privés (comme défini dans [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) ne sont pas autorisées dans les règles IP. Les réseaux privés incluent des adresses qui commencent par _10.*_, _172.16.*_ - _172.31.*_ et _192.168.*_.

   > [!NOTE]
   > Les règles de réseau IP n’ont aucun effet sur les requêtes provenant de la même région Azure que le compte de stockage. Utilisez des [règles de réseau virtuel](#grant-access-from-a-virtual-network) pour autoriser les requêtes de même région.

Seules les adresses IPv4 sont prises en charge pour le moment.

Chaque compte de stockage prend en charge jusqu’à 100 règles de réseau IP qui peuvent être combinées avec des [règles de réseau virtuel](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configuration de l’accès à partir de réseaux locaux

Pour accorder l’accès à votre compte de stockage à partir de réseaux locaux avec une règle de réseau IP, vous devez identifier les adresses IP Internet utilisées par votre réseau. Contactez votre administrateur réseau pour obtenir de l’aide.

Si vous utilisez [ExpressRoute](/azure/expressroute/expressroute-introduction) localement, pour le Peering public ou Microsoft, vous devez identifier les adresses IP NAT (traduction d’adresses réseau) utilisées. Pour l’homologation publique, chaque circuit ExpressRoute utilise par défaut deux adresses IP NAT qui sont appliquées au trafic de service Azure lorsque le trafic entre dans le réseau principal de Microsoft Azure. Pour l’homologation Microsoft, les adresses IP NAT qui sont utilisées sont fournies par le client ou par le fournisseur de services. Pour autoriser l’accès à vos ressources de votre service, vous devez autoriser ces adresses IP publiques dans le paramètre de pare-feu IP de ressource. Pour trouver les adresses IP de votre circuit ExpressRoute d’homologation publique, [ouvrez un ticket de support avec ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via le portail Azure. Découvrez d’autres informations sur [l’homologation publique et Microsoft NAT pour ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gestion des règles de réseau IP

Vous pouvez gérer les règles de réseau IP pour les comptes de stockage via le portail Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portail Azure

1. Accédez au compte de stockage que vous voulez sécuriser.

1. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.

1. Vérifiez que vous avez choisi d’autoriser l’accès à partir des **Réseaux sélectionnés**.

1. Pour accorder l’accès à une plage d’adresses IP Internet, entrez l’adresse IP ou la plage d’adresses IP (au format CIDR) sous **Pare-feu** > **Plages d’adresses**.

1. Pour supprimer une règle de réseau IP, cliquez sur l’icône Corbeille à côté de la plage d’adresses.

1. Cliquez sur **Enregistrer** pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell

1. Installez [Azure PowerShell](/powershell/azure/install-Az-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).

1. Listez les règles de réseau IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Ajoutez une règle de réseau pour une adresse IP individuelle.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Ajoutez une règle de réseau pour une plage d’adresses IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Supprimez une règle de réseau pour une adresse IP individuelle.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Supprimez une règle de réseau pour une plage d’adresses IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon les règles de réseau n’ont aucun effet.

#### <a name="cliv2"></a>CLIv2

1. Installez [Azure CLI](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).

1. Listez les règles de réseau IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Ajoutez une règle de réseau pour une adresse IP individuelle.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Ajoutez une règle de réseau pour une plage d’adresses IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Supprimez une règle de réseau pour une adresse IP individuelle.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Supprimez une règle de réseau pour une plage d’adresses IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon les règles de réseau n’ont aucun effet.

## <a name="exceptions"></a>Exceptions

Les règles de réseau peuvent activer une configuration de réseau sécurisée dans la plupart des scénarios. Toutefois, il existe certains cas où des exceptions doivent être accordées pour activer toutes les fonctionnalités. Vous pouvez configurer les comptes de stockage avec des exceptions pour les services Microsoft approuvés et pour accéder aux données Storage Analytics.

### <a name="trusted-microsoft-services"></a>Services Microsoft approuvés

Certains services Microsoft qui interagissent avec les comptes de stockage fonctionnent à partir de réseaux qui ne peuvent pas obtenir l’accès par le biais des règles de réseau.

Pour que ce type de service fonctionne comme prévu, autorisez l’ensemble des services Microsoft approuvés à contourner les règles de réseau. Ces services utilisent alors une authentification forte pour accéder au compte de stockage.

Si vous activez l’exception **Services Microsoft approuvés**, les services suivants (s’ils sont inscrits dans votre abonnement) ont accès au compte de stockage :

|de diffusion en continu|Nom du fournisseur de ressources|Objectif|
|:------|:---------------------|:------|
|Sauvegarde Azure|Microsoft.Backup|Effectuez des sauvegardes et des restaurations de disques non managés dans des machines virtuelles IAAS. (non requis pour les disques managés). [Plus d’informations](/azure/backup/backup-introduction-to-azure-backup)|
|Azure Data Box|Microsoft.DataBox|Permet l’importation des données vers Azure à l’aide de la zone de données. [Plus d’informations](/azure/databox/data-box-overview)|
|Azure DevTest Labs|Microsoft.DevTestLab|Création d’une image personnalisée et installation de l’artefact. [Plus d’informations](/azure/devtest-lab/devtest-lab-overview)|
|Azure Event Grid|Microsoft.EventGrid|Permettez la publication d’événements Stockage Blob et autorisez Event Grid à effectuer des publications dans les files d’attente de stockage. En savoir plus sur les [événements Stockage Blob](/azure/event-grid/event-sources) et la [publication dans les files d’attente](/azure/event-grid/event-handlers).|
|Hubs d'événements Azure|Microsoft.EventHub|Archivage des données avec Event Hubs Capture. [En savoir plus](/azure/event-hubs/event-hubs-capture-overview)|
|Azure HDInsight|Microsoft.HDInsight|Configurer le contenu initial du système de fichiers par défaut pour un nouveau cluster HDInsight. [Plus d’informations](https://azure.microsoft.com/en-us/blog/enhance-hdinsight-security-with-service-endpoints/)|
|Azure Monitor|Microsoft.Insights|Autorisation de l’écriture de données de supervision dans un compte de stockage sécurisé. [En savoir plus](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security).|
|Mise en réseau Azure|Microsoft.Networking|Stockage et analyse des journaux du trafic réseau. [Plus d’informations](/azure/network-watcher/network-watcher-packet-capture-overview)|
|Azure Site Recovery|Microsoft.SiteRecovery |Configurez la récupération d’urgence en activant la réplication pour les machines virtuelles Azure IaaS. Cela est nécessaire si vous utilisez un compte de stockage de cache avec pare-feu activé, un compte de stockage source ou un compte de stockage cible.  [Plus d’informations](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)|
|Azure SQL Data Warehouse|Microsoft.Sql|Permet d’importer et d’exporter des scénarios à l’aide de PolyBase. [Plus d’informations](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)|

### <a name="storage-analytics-data-access"></a>Accès aux données Storage Analytics

Dans certains cas, un accès en lecture aux journaux et aux métriques de diagnostic est nécessaire en dehors de la limite du réseau. Vous pouvez accorder des exceptions aux règles de réseau afin d’autoriser l’accès en lecture aux fichiers journaux ou aux tables de métriques du compte de stockage, ou aux deux. [En savoir plus sur l’utilisation d’analytique de stockage.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gestion des exceptions

Vous pouvez gérer les exceptions de règle de réseau dans le portail Azure, PowerShell ou Azure CLI v2.

#### <a name="azure-portal"></a>Portail Azure

1. Accédez au compte de stockage que vous voulez sécuriser.

1. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.

1. Vérifiez que vous avez choisi d’autoriser l’accès à partir des **Réseaux sélectionnés**.

1. Sous **Exceptions**, sélectionnez les exceptions que vous voulez accorder.

1. Cliquez sur **Enregistrer** pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell

1. Installez [Azure PowerShell](/powershell/azure/install-Az-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).

1. Affichez les exceptions pour les règles de réseau du compte de stockage.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Configurez les exceptions aux règles de réseau du compte de stockage.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Supprimez les exceptions aux règles de réseau du compte de stockage.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon la suppression des exceptions n’a aucun effet.

#### <a name="cliv2"></a>CLIv2

1. Installez [Azure CLI](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).

1. Affichez les exceptions pour les règles de réseau du compte de stockage.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Configurez les exceptions aux règles de réseau du compte de stockage.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Supprimez les exceptions aux règles de réseau du compte de stockage.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon la suppression des exceptions n’a aucun effet.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur les points de terminaison de service du réseau Azure dans [Points de terminaison de service](/azure/virtual-network/virtual-network-service-endpoints-overview).

Explorez en détail la sécurité de Stockage Azure dans [Guide de sécurité de Stockage Azure](storage-security-guide.md).
