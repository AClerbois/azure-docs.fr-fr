---
title: Se connecter à un compte Azure Cosmos avec Azure Private Link
description: Découvrez comment accéder en toute sécurité au compte Azure Cosmos à partir d’une machine virtuelle en créant un point de terminaison privé.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sngun
ms.openlocfilehash: 80a0a3440a7fb5d056d1d76fb9d82931721b6e16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510706"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Se connecter en privé à un compte Azure Cosmos à l’aide d’Azure Private Link

Azure Private Endpoint est le composant fondamental de Private Link dans Azure. Il permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé avec des ressources Private Link.

Dans cet article, vous allez apprendre à créer une machine virtuelle sur un réseau virtuel Azure et un compte Azure Cosmos avec un point de terminaison privé à l’aide du portail Azure. Ensuite, vous pouvez accéder en toute sécurité au compte Azure Cosmos à partir de la machine virtuelle.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-vm"></a>Créer une machine virtuelle

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource Private Link (un compte Azure Cosmos pour cet exemple).

1. Dans le coin supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.

1. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez *MyVirtualNetwork*. |
    | Espace d’adressage | Entrez *10.1.0.0/16*. |
    | Subscription | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer nouveau**, entrez *myResourceGroup* et sélectionnez **OK**. |
    | Location | Sélectionnez **WestCentralUS**.|
    | Sous-réseau - Nom | Entrez *mySubnet*. |
    | Plage d’adresses du sous-réseau | Entrez *10.1.0.0/24*. |
    |||

1. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.

### <a name="create-the-virtual-machine"></a>Créer la machine virtuelle

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**.

1. Dans **Créer une machine virtuelle - Notions de base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.  |
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom de la machine virtuelle | Entrez *myVm*. |
    | Région | Sélectionnez **WestCentralUS**. |
    | Options de disponibilité | Conservez la valeur par défaut **Aucune redondance d’infrastructure nécessaire**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter**. |
    | Size | Conservez la valeur par défaut **Standard DS1 v2**. |
    | **COMPTE ADMINISTRATEUR** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmer le mot de passe | Entrez de nouveau le mot de passe. |
    | **RÈGLES DES PORTS D’ENTRÉE** |  |
    | Aucun port d’entrée public | Conservez la valeur par défaut **Aucun**. |
    | **ÉCONOMISEZ DE L’ARGENT** |  |
    | Vous disposez déjà d’une licence Windows ? | Conservez la valeur par défaut **Non**. |
    |||

1. Sélectionnez **Suivant : Disques**.

1. Dans **Créer une machine virtuelle - Disks**, conservez les valeurs par défaut et sélectionnez **Suivant : Mise en réseau**.

1. Dans **Créer une machine virtuelle - Mise en réseau**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Réseau virtuel | Conservez la valeur par défaut, **MyVirtualNetwork**.  |
    | Espace d’adressage | Conservez la valeur par défaut, **10.1.0.0/24**.|
    | Subnet | Conservez la valeur par défaut, **mySubnet (10.1.0.0/24)** .|
    | Adresse IP publique | Conservez la valeur par défaut **(new) myVm-ip**. |
    | Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Sélectionnez **HTTP** et **RDP**.|
    ||

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration.

1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

## <a name="create-an-azure-cosmos-account"></a>Créer un compte Azure Cosmos

Créez un [compte d’API SQL Azure Cosmos](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Par souci de simplicité, vous pouvez créer le compte Azure Cosmos dans la même région que les autres ressources (c’est-à-dire « WestCentralUS »).

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Créer un point de terminaison privé pour un compte Azure Cosmos

Créez une liaison privée (Private Link) pour votre compte Azure Cosmos, comme décrit dans la section [Créer une Liaison privée (Private Link) au moyen du portail Azure](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-link-using-the-azure-portal) de l’article lié.

## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Connectez-vous à la machine virtuelle *myVm* à partir d’Internet comme suit :

1. Dans la barre de recherche du portail, entrez *myVm*.

1. Sélectionnez le bouton **Connexion**. Après avoir sélectionné le bouton **Connecter**, **Se connecter à la machine virtuelle** s’ouvre.

1. Sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier *.rdp* (Remote Desktop Protocol) et le télécharge sur votre ordinateur.

1. Ouvrez le fichier *.rdp* téléchargé.

    1. Si vous y êtes invité, sélectionnez **Connexion**.

    1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

        > [!NOTE]
        > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

1. Sélectionnez **OK**.

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez **Oui** ou **Continuer**.

1. Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Accéder au compte Azure Cosmos en privé à partir de la machine virtuelle

Dans cette section, vous allez vous connecter en privé au compte Azure Cosmos à l’aide du point de terminaison privé. 

> [!IMPORTANT]
> La configuration DNS pour le compte Azure Cosmos nécessite une modification manuelle du fichier hosts afin que ce dernier inclue le nom de domaine complet du compte spécifique. Dans des scénarios de production, vous configurez le serveur DNS pour qu’il utilise les adresses IP privées. Toutefois, à des fins de démonstration, vous pouvez utiliser des autorisations d’administrateur sur la machine virtuelle et modifier le fichier `c:\Windows\System32\Drivers\etc\hosts` (sur Windows) ou `/etc/hosts` (sur Linux) pour inclure l’adresse IP et le mappage DNS.

1. Pour inclure l’adresse IP et le mappage DNS, connectez-vous à votre machine virtuelle *myVM*, ouvrez le fichier `c:\Windows\System32\Drivers\etc\hosts` et incluez les informations DNS de l’étape précédente au format suivant :

   [Adresse IP privée] [Point de terminaison de compte].documents.azure.com

   **Exemple :**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. Dans le Bureau à distance de  *myVM*, installez l’[Explorateur Stockage Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Sélectionnez **Comptes Cosmos DB (préversion)** en cliquant avec le bouton droit.

1. Sélectionnez **Se connecter à Cosmos DB**.

1. Sélectionnez **l’API**.

1. Entrez la chaîne de connexion en collant les informations précédemment copiées.

1. Sélectionnez **Suivant**.

1. Sélectionnez **Connecter**.

1. Parcourez les bases de données et les conteneurs Azure Cosmos à partir de *mycosmosaccount*.

1. (Facultatif) Ajoutez de nouveaux éléments à *mycosmosaccount*.

1. Fermez la connexion Bureau à distance avec  *myVM*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous avez fini d’utiliser le point de terminaison privé, le compte Azure Cosmos et la machine virtuelle, supprimez le groupe de ressources et toutes les ressources qu’il contient : 

1. Entrez *myResourceGroup* dans la zone **Rechercher** en haut du portail, puis sélectionnez *myResourceGroup* dans les résultats de la recherche.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez *myResourceGroup* pour **TAPEZ LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé une machine virtuelle sur un réseau virtuel, ainsi qu’un compte Azure Cosmos et un point de terminaison privé. Vous vous êtes connecté à la machine virtuelle à partir d’Internet et avez communiqué de façon sécurisée avec le compte Azure Cosmos via Private Link.

* Pour en savoir plus sur le point de terminaison privé, consultez [Qu’est-ce qu’Azure Private Endpoint ?](private-endpoint-overview.md).

* Pour en savoir plus sur la limitation du point de terminaison privé lors de l’utilisation avec Azure Cosmos DB, consultez l’article [Azure Private Link avec Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md).