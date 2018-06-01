---
title: Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel | Microsoft Docs
description: Découvrez comment joindre un runtime d’intégration Azure-SSIS à un réseau virtuel Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: douglasl
ms.openlocfilehash: 2bb6491a470e7041568bb6b9183e996d2a9119d9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939906"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel
Joignez le runtime d’intégration (IR) Azure-SSIS à un réseau virtuel Azure dans les scénarios suivants : 

- Vous hébergez la base de données du catalogue SQL Server Integration Services (SSIS) sur Azure SQL Database Managed Instance (préversion) sur un réseau virtuel.
- Vous souhaitez vous connecter à des magasins de données sur site à partir de packages SSIS en cours d’exécution sur un runtime d’intégration Azure-SSIS.

 Azure Data Factory version 2 (préversion) vous permet de joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel créé par le biais du modèle de déploiement classique ou du modèle de déploiement Azure Resource Manager. 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale (GA), consultez la [documentation relative à Data Factory version 1](v1/data-factory-introduction.md).

## <a name="access-to-on-premises-data-stores"></a>Accéder aux magasins de données locaux
Si les packages SSIS accèdent uniquement aux magasins de données cloud publics, vous n’avez pas besoin de joindre le runtime d’intégration Azure-SSIS à un réseau virtuel. Si les packages SSIS accèdent à des magasins de données locaux, vous devez joindre le runtime d’intégration Azure-SSIS à un réseau virtuel qui est connecté au réseau local. 

Voici quelques points importants à prendre en compte : 

- S’il n’existe pas encore de réseau virtuel connecté à votre réseau local, vous devez commencer par créer un [réseau virtuel Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) ou un [réseau virtuel classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pour le runtime d’intégration Azure-SSIS à joindre. Ensuite, configurez une [connexion de passerelle VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) de site à site ou une connexion [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) de ce réseau virtuel vers votre réseau local.
- S’il existe déjà un réseau virtuel classique ou Azure Resource Manager connecté à votre réseau local, au même emplacement que votre runtime d’intégration Azure-SSIS, vous pouvez joindre ce dernier à ce réseau virtuel.
- S’il existe déjà un réseau virtuel classique connecté à votre réseau local, à un autre emplacement que celui de votre runtime d’intégration Azure-SSIS, vous devez commencer par créer un [réseau virtuel classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pour le runtime d’intégration Azure-SSIS à joindre. Ensuite, configurez une connexion de [réseau virtuel classique vers classique](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md). Vous pouvez également créer un [réseau virtuel Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) pour le runtime d’intégration Azure-SSIS à joindre. Ensuite, configurez une connexion du type [réseau virtuel classique vers Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).
- S’il existe déjà un réseau virtuel Azure Resource Manager connecté à votre réseau local, à un autre emplacement que celui de votre runtime d’intégration Azure-SSIS, vous devez commencer par créer un [réseau virtuel Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) pour le runtime d’intégration Azure-SSIS à joindre. Ensuite, configurez une connexion du type réseau virtuel Azure Resource Manager vers Azure Resource Manager. Vous pouvez également créer un [réseau virtuel classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pour le runtime d’intégration Azure-SSIS à joindre. Ensuite, configurez une connexion du type [réseau virtuel classique vers Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="host-the-ssis-catalog-database-on-azure-sql-database-managed-instance"></a>Héberger la base de données du catalogue SSIS sur Azure SQL Database Managed Instance 
Si le catalogue SSIS est hébergé dans SQL Database Managed Instance (préversion) sur un réseau virtuel, vous pouvez joindre un runtime d’intégration Azure SSIS :

- Au même réseau virtuel.
- À un réseau virtuel différent qui dispose d’une connexion réseau-à-réseau avec celui où se trouve SQL Database Managed Instance (préversion). 

Le réseau virtuel peut être déployé par le biais du modèle de déploiement classique ou du modèle de déploiement Azure Resource Manager. Si vous envisagez de joindre le runtime d’intégration Azure SSIS au *même réseau virtuel* que celui où se trouve SQL Database Managed Instance (préversion), vérifiez que le runtime d’intégration Azure SSIS est sur un *autre sous-réseau* que celui où se trouve SQL Database Managed Instance (préversion).   

Pour plus d’informations, lisez les sections suivantes.

## <a name="requirements-for-virtual-network-configuration"></a>Conditions requises pour la configuration du réseau virtuel

-   Vérifiez que `Microsoft.Batch` est un fournisseur inscrit sous l’abonnement de votre sous-réseau de machine virtuelle qui héberge le runtime d’intégration Azure SSIS. Si vous utilisez un réseau virtuel classique, vous devez également joindre `MicrosoftAzureBatch` au rôle Collaborateur de machine virtuelle classique pour ce réseau virtuel.

-   Sélectionnez le sous-réseau approprié pour héberger le runtime d’intégration Azure SSIS. Consultez [Sélectionner le sous-réseau](#subnet).

-   Si vous utilisez votre propre serveur DNS (Domain Name Services) sur le réseau virtuel, consultez [Serveur Domain Name Services (DNS)](#dns_server).

-   Si vous utilisez un groupe de sécurité réseau sur le sous-réseau, consultez [Groupe de sécurité réseau](#nsg)

-   Si vous utilisez Azure ExpressRoute ou configurez un itinéraire défini par l’utilisateur, consultez [Utiliser Azure ExpressRoute ou un itinéraire défini par l’utilisateur](#route).

-   Vérifiez que le groupe de ressources du réseau virtuel peut créer et supprimer certaines ressources réseau Azure. Consultez [Configuration requise pour le groupe de ressources](#resource-group).

### <a name="subnet"></a> Sélectionner le sous-réseau
-   Ne sélectionnez pas le sous-réseau GatewaySubnet pour déployer un runtime d’intégration Azure-SSIS, car il est dédié aux passerelles de réseau virtuel.

-   Vérifiez que le sous-réseau sélectionné dispose de suffisamment d’espace d’adressage pour le runtime d’intégration Azure-SSIS. Laissez au moins deux fois le nombre de nœuds de runtime d’intégration dans les adresses IP disponibles. Azure réserve dans chaque sous-réseau des adresses IP qui ne peuvent pas être utilisées. Les première et dernière adresse IP des sous-réseaux sont réservées à la conformité du protocole, et 3 adresses supplémentaires sont utilisées pour les services Azure. Pour plus d’informations, consultez la section [L’utilisation des adresses IP au sein de ces sous-réseaux est-elle soumise à des restrictions ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

-   N’utilisez pas un sous-réseau qui est exclusivement occupé par d’autres services Azure (par exemple, SQL Database Managed Instance, App Service).

### <a name="dns_server"></a> Serveur Domain Name Services (DNS) 
Si vous devez utiliser votre propre serveur DNS (Domain Name Services) dans un réseau virtuel joint par le runtime d’intégration Azure SSIS, vérifiez qu’il peut résoudre les noms d’hôte Azure publics (par exemple, un nom Azure Storage Blob, `<your storage account>.blob.core.windows.net`).

Les étapes suivantes sont recommandées :

-   Configurez le serveur DNS personnalisé pour transférer des requêtes à Azure DNS. Vous pouvez transférer des enregistrements DNS non résolus à l’adresse IP des programmes de résolution récursifs d’Azure (168.63.129.16) sur votre propre serveur DNS.

-   Configurez le serveur DNS personnalisé comme serveur principal et Azure DNS comme serveur secondaire pour le réseau virtuel. Inscrivez l’adresse IP des programmes de résolution récursifs d’Azure (168.63.129.16) comme serveur DNS secondaire au cas où votre propre serveur DNS ne serait pas disponible.

Pour plus d’informations, consultez [Résolution de noms utilisant votre propre serveur DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

### <a name="nsg"></a> Groupe de sécurité réseau
Si vous avez besoin d’implémenter un groupe de sécurité réseau (NSG) sur un réseau virtuel joint par votre runtime d’intégration Azure-SSIS, autorisez le trafic entrant et sortant par le biais des ports suivants :

| Direction | Protocole de transfert | Source | Plage de ports sources | Destination | Plage de ports de destination | Commentaires |
|---|---|---|---|---|---|---|
| Trafic entrant | TCP | Internet | * | VirtualNetwork | 29876, 29877 (si vous joignez le runtime d’intégration à un réseau virtuel Azure Resource Manager) <br/><br/>10100, 20100, 30100 (si vous joignez le runtime d’intégration à un réseau virtuel classique)| Le service Data Factory utilise ces ports pour communiquer avec les nœuds de votre runtime d’intégration Azure SSIS sur le réseau virtuel. |
| Règle de trafic sortant | TCP | VirtualNetwork | * | Internet | 443 | Les nœuds de votre runtime d’intégration Azure SSIS sur le réseau virtuel utilisent ce port pour accéder aux services Azure comme Stockage Azure et Azure Event Hubs. |
| Règle de trafic sortant | TCP | VirtualNetwork | * | Internet ou SQL | 1433, 11000-11999, 14000-14999 | Les nœuds de votre runtime d’intégration Azure SSIS sur le réseau virtuel utilisent ces ports pour accéder à la base de données SSISDB hébergée par le serveur Azure SQL Database. Ne s’applique pas à la base de données SSISDB hébergée par SQL Database Managed Instance (préversion). |
||||||||

### <a name="route"></a> Utiliser Azure ExpressRoute ou un itinéraire défini par l’utilisateur

Vous pouvez connecter un circuit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à votre infrastructure de réseau virtuel pour étendre votre réseau local à Azure. 

L’une des configurations courantes consiste à utiliser le tunneling forcé (annoncer un itinéraire BGP, 0.0.0.0/0 vers le réseau virtuel) qui force le trafic Internet sortant du flux de réseau virtuel vers l’appliance réseau locale à des fins d’inspection et de journalisation. Ce flux de trafic rompt la connectivité entre le runtime d’intégration Azure-SSIS du réseau virtuel et les services Azure Data Factory dépendants. La solution consiste à définir un (ou plusieurs) [itinéraires définis par l’utilisateur (UDR)](../virtual-network/virtual-networks-udr-overview.md) sur le sous-réseau qui contient le runtime d’intégration Azure-SSIS. Un itinéraire défini par l’utilisateur définit les itinéraires propres au sous-réseau qui seront respectés au lieu de l’itinéraire BGP.

Vous pouvez aussi définir des itinéraires définis par l’utilisateur pour forcer le trafic Internet sortant à partir du sous-réseau qui héberge le runtime d’intégration Azure SSIS vers un autre sous-réseau, qui héberge une appliance virtuelle réseau comme un pare-feu ou un hôte du réseau DMZ pour l’inspection et la journalisation.

Dans les deux cas, appliquez un itinéraire 0.0.0.0/0 avec le type de tronçon suivant **Internet** sur le sous-réseau qui héberge le runtime d’intégration Azure SSIS pour que la communication entre le service Data Factory et le runtime d’intégration Azure SSIS aboutisse. 

![Ajouter un itinéraire](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Si vous ne souhaitez pas risquer de perdre la possibilité d’inspecter le trafic Internet sortant de ce sous-réseau, vous pouvez également ajouter une règle de groupe de sécurité réseau au sous-réseau pour restreindre les destinations sortantes aux [adresses IP de centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Vous en trouverez un exemple dans [ce script PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c). Vous devez exécuter le script une fois par semaine pour maintenir la liste d’adresses IP de centres de données à jour.

### <a name="resource-group"></a> Configuration requise pour le groupe de ressources
Le runtime d’intégration Azure SSIS doit créer certaines ressources réseau sous le même groupe de ressources que le réseau virtuel, dont un équilibreur de charge Azure, une adresse IP publique Azure et un groupe de sécurité réseau.

-   Vérifiez qu’il n’existe aucun verrou de ressource sur le groupe de ressources ou l’abonnement auquel appartient le réseau virtuel. Si vous configurez un verrou en lecture seule ou un verrou de suppression, le démarrage et l’arrêt du runtime d’intégration peuvent échouer ou se bloquer.

-   Vérifiez qu’aucune stratégie Azure n’empêche la création des ressources suivantes sous le groupe de ressources ou l’abonnement auquel appartient le réseau virtuel :
    -   Microsoft.Network/LoadBalancers
    -   Microsoft.Network/NetworkSecurityGroups
    -   Microsoft.Network/PublicIPAddresses

## <a name="azure-portal-data-factory-ui"></a>Portail Azure (interface utilisateur de Data Factory)
Cette section vous montre comment joindre un runtime Azure SSIS existant à un réseau virtuel (classique ou Azure Resource Manager) à l’aide du portail Azure et de l’interface utilisateur de Data Factory. Tout d’abord, vous devez configurer le réseau virtuel de façon appropriée avant d’y joindre votre runtime d’intégration Azure SSIS. Effectuez l’une des deux sections suivantes, en fonction du type de votre réseau virtuel (classique ou Azure Resource Manager). Ensuite, passez à la troisième section pour joindre votre runtime d’intégration Azure SSIS au réseau virtuel. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Utiliser le portail pour configurer un réseau virtuel Azure Resource Manager
Vous devez configurer un réseau virtuel avant d’y joindre un runtime d’intégration Azure-SSIS.

1. Démarrez Microsoft Edge ou Google Chrome. Actuellement, l’interface utilisateur de Data Factory est prise en charge uniquement dans ces navigateurs web.
2. Connectez-vous au [Portail Azure](https://portal.azure.com).
3. Sélectionnez **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels**.
4. Filtrez et sélectionnez votre réseau virtuel dans la liste. 
5. Dans la page **Réseau virtuel**, sélectionnez **Propriétés**. 
6. Sélectionnez le bouton Copier au niveau de **ID DE RESSOURCE** pour copier l’ID de ressource du réseau virtuel dans le Presse-papiers. Enregistrez dans OneNote ou un fichier l’ID se trouvant dans le Presse-papiers.
7. Sélectionnez **Sous-réseaux** dans le menu de gauche. Vérifiez que le nombre d’**adresses disponibles** est supérieur à celui des nœuds dans votre runtime d’intégration Azure-SSIS.
8. Vérifiez que le fournisseur Azure Batch est bien inscrit dans l’abonnement Azure qui contient le réseau virtuel. Si ce n’est pas le cas, inscrivez le fournisseur Azure Batch. Si vous possédez déjà un compte Azure Batch dans votre abonnement, ce dernier est inscrit pour Azure Batch. (Si vous créez le runtime d’intégration Azure SSIS dans le portail Data Factory, le fournisseur Azure Batch est inscrit automatiquement pour vous.)

   a. Dans le portail Azure, sélectionnez **Abonnements** dans le menu de gauche.

   b. Sélectionnez votre abonnement. 
   
   c. Sélectionnez **Fournisseurs de ressources** sur la gauche, puis confirmez que **Microsoft.Batch** est un fournisseur inscrit.     
      ![Confirmation de l’état « Inscrit »](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Si **Microsoft.Batch** ne figure pas dans la liste, il vous faut l’inscrire. Pour cela, [créez un compte Azure Batch vide](../batch/batch-account-create-portal.md) dans votre abonnement. Vous pourrez le supprimer par la suite.

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Utiliser le portail pour configurer un réseau virtuel classique
Vous devez configurer un réseau virtuel avant d’y joindre un runtime d’intégration Azure-SSIS.

1. Démarrez Microsoft Edge ou Google Chrome. Actuellement, l’interface utilisateur de Data Factory est prise en charge uniquement dans ces navigateurs web.
2. Connectez-vous au [Portail Azure](https://portal.azure.com).
3. Sélectionnez **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels (classiques)**.
4. Filtrez et sélectionnez votre réseau virtuel dans la liste. 
5. Dans la page **Réseau virtuel (classique)**, sélectionnez **Propriétés**. 

    ![ID de ressource de réseau virtuel classique](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Sélectionnez le bouton Copier au niveau de **ID DE RESSOURCE** pour copier l’ID de ressource du réseau classique dans le Presse-papiers. Enregistrez dans OneNote ou un fichier l’ID se trouvant dans le Presse-papiers.
6. Sélectionnez **Sous-réseaux** dans le menu de gauche. Vérifiez que le nombre d’**adresses disponibles** est supérieur à celui des nœuds dans votre runtime d’intégration Azure-SSIS.

    ![Nombre d’adresses disponibles sur le réseau virtuel](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Joignez **MicrosoftAzureBatch** au rôle **Contributeur de machines virtuelles classiques** pour le réseau virtuel.

    a. Sélectionnez **Contrôle d’accès (IAM)** dans le menu de gauche, sur **Ajouter** dans la barre d’outils. 
       ![Boutons « Contrôle d’accès » et « Ajouter »](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Dans la page **Ajouter des autorisations**, sélectionnez **Contributeur de machines virtuelles classiques** pour **Rôle**. Collez **ddbf3205-c6bd-46ae-8127-60eb93363864** dans la zone **Sélectionner**, puis sélectionnez **Microsoft Azure Batch** dans la liste des résultats de recherche.   
       ![Résultats de recherche dans la page « Ajouter des autorisations »](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Sélectionnez **Enregistrer** pour enregistrer les paramètres et fermer la page.  
       ![Enregistrer les paramètres d’accès](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Confirmez que **Microsoft Azure Batch** apparaît bien dans la liste des contributeurs.  
       ![Confirmer l’accès à Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. Vérifiez que le fournisseur Azure Batch est bien inscrit dans l’abonnement Azure qui contient le réseau virtuel. Si ce n’est pas le cas, inscrivez le fournisseur Azure Batch. Si vous possédez déjà un compte Azure Batch dans votre abonnement, ce dernier est inscrit pour Azure Batch. (Si vous créez le runtime d’intégration Azure SSIS dans le portail Data Factory, le fournisseur Azure Batch est inscrit automatiquement pour vous.)

   a. Dans le portail Azure, sélectionnez **Abonnements** dans le menu de gauche.

   b. Sélectionnez votre abonnement.

   c. Sélectionnez **Fournisseurs de ressources** sur la gauche, puis confirmez que **Microsoft.Batch** est un fournisseur inscrit.     
      ![Confirmation de l’état « Inscrit »](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Si **Microsoft.Batch** ne figure pas dans la liste, il vous faut l’inscrire. Pour cela, [créez un compte Azure Batch vide](../batch/batch-account-create-portal.md) dans votre abonnement. Vous pourrez le supprimer par la suite. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel

1. Démarrez Microsoft Edge ou Google Chrome. Actuellement, l’interface utilisateur de Data Factory est prise en charge uniquement dans ces navigateurs web.
2. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Fabriques de données** dans le menu de gauche. Si vous ne voyez pas **Fabriques de données** dans le menu, sélectionnez **Autres services**, puis sélectionnez **Fabriques de données** dans la section **INTELLIGENCE + ANALYSE**. 
    
   ![Liste de fabriques de données](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. Dans la liste, sélectionnez votre fabrique de données avec le runtime d’intégration Azure SSIS. La page d’accueil de votre fabrique de données apparaît. Sélectionnez la vignette **Créer et déployer**. L’interface utilisateur de Data Factory apparaît sous un onglet séparé. 

   ![Page d'accueil Data Factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. Dans l’interface utilisateur de Data Factory, basculez vers l’onglet **Modifier**, sélectionnez **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration**. 

   ![Onglet « Runtimes d’intégration »](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Si votre runtime d’intégration Azure SSIS est en cours d’exécution, sélectionnez le bouton **Arrêter** dans la colonne **Actions** en regard de votre runtime d’intégration Azure SSIS. Vous ne pouvez pas modifier un runtime d’intégration tant que vous ne l’arrêtez pas. 

   ![Arrêter le runtime d’intégration](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. Dans la liste des runtimes d’intégration, sélectionnez le bouton **Modifier** dans la colonne **Actions** en regard de votre runtime d’intégration Azure SSIS.

   ![Modifier le runtime d’intégration](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. Dans la page **Paramètres généraux** de la fenêtre **Configuration du runtime d’intégration**, sélectionnez **Suivant**. 

   ![Paramètres généraux pour la configuration du runtime d’intégration](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. Dans la page **Paramètres SQL**, entrez le mot de passe d’administrateur, puis sélectionnez **Suivant**.

   ![Paramètres SQL Server pour la configuration du runtime d’intégration](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. Dans la page **Paramètres avancés**, effectuez les actions suivantes : 

   a. Cochez la case pour **sélectionner un réseau virtuel auquel joindre votre runtime d’intégration Azure-SSIS et autoriser les services Azure à configurer les paramètres/autorisations du réseau virtuel**.

   b. Pour **Type**, indiquez si le réseau virtuel est un réseau virtuel classique ou Azure Resource Manager. 

   c. Pour **Nom du réseau virtuel**, sélectionnez votre réseau virtuel.

   d. Pour **Nom du sous-réseau**, sélectionnez votre sous-réseau sur le réseau virtuel.

   e. Sélectionnez **Update**. 

   ![Paramètres avancés pour la configuration du runtime d’intégration](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Maintenant, vous pouvez démarrer le runtime d’intégration à l’aide du bouton **Démarrer** situé dans la colonne **Actions** en regard de votre runtime d’intégration Azure SSIS. Le démarrage d’un runtime d’intégration Azure SSIS prend 20 minutes environ. 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Configurer un réseau virtuel
Vous devez configurer un réseau virtuel avant d’y joindre un runtime d’intégration Azure-SSIS. Pour configurer automatiquement les paramètres/autorisations du réseau virtuel pour le runtime d’intégration Azure-SSIS à joindre au réseau virtuel, ajoutez le script suivant :

```powershell
# Register to the Azure Batch resource provider
# Make sure to run this script against the subscription to which the VNet belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Créer un runtime d’intégration Azure-SSIS et le joindre à un réseau virtuel
Vous pouvez créer un runtime d’intégration Azure-SSIS et le joindre à un réseau virtuel en même temps. Pour obtenir le script complet et toutes les instructions, consultez [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel
Le script fourni dans l’article [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md) explique comment créer un runtime d’intégration Azure-SSIS et le joindre à un réseau virtuel dans le même script. Si vous avez un runtime d’intégration Azure-SSIS, effectuez les opérations suivantes pour le joindre au réseau virtuel : 

1. Arrêtez le runtime d’intégration Azure-SSIS.
2. Configurez le runtime d’intégration Azure-SSIS pour le joindre au réseau virtuel. 
3. Démarrez le runtime d’intégration Azure-SSIS. 

### <a name="define-the-variables"></a>Définir les variables

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (Preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Arrêter le runtime d’intégration Azure-SSIS
Arrêtez le runtime d’intégration Azure-SSIS avant de le joindre à un réseau virtuel. Cette commande libère tous ses nœuds et arrête la facturation :

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurer les paramètres du réseau virtuel pour le runtime d’intégration Azure-SSIS à joindre

```powershell
# Register to the Azure Batch resource provider
# Make sure to run this script against the subscription to which the VNet belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configurer le runtime d’intégration Azure-SSIS
Pour configurer le runtime d’intégration Azure-SSIS afin qu’il joigne le réseau virtuel, exécutez la commande `Set-AzureRmDataFactoryV2IntegrationRuntime` : 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Démarrer le runtime d’intégration Azure-SSIS
Pour démarrer le runtime d’intégration Azure-SSIS, exécutez la commande suivante : 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Cette commande prend de 20 à 30 minutes.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le runtime Azure-SSIS, consultez les rubriques suivantes : 

- [Runtime d’intégration Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles sur les runtimes d’intégration en général, notamment sur le runtime d’intégration Azure-SSIS. 
- [Didacticiel : deploy SSIS packages to Azure](tutorial-create-azure-ssis-runtime-portal.md) (Déployer des packages SSIS vers Azure). Cet article fournit des instructions pas à pas pour créer un runtime d’intégration Azure-SSIS. Il utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel et fournit des instructions sur la façon d’utiliser Azure SQL Database Managed Instance (préversion) et de joindre le runtime d’intégration à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article explique comment récupérer des informations sur un runtime d’intégration Azure-SSIS ainsi que des descriptions d’état dans les informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer un runtime d’intégration Azure-SSIS. Il vous montre également comment faire monter le runtime d’intégration Azure-SSIS en puissance en lui ajoutant des nœuds. 
