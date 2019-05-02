---
title: Connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels - tutoriel - Portail Azure | Microsoft Docs
description: Dans ce tutoriel, vous apprendrez à connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels en utilisant le portail Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 943cad871330e2f3b6e13b33dca582ab545fe4be
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726567"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Didacticiel : Connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels en utilisant le portail Azure

Vous pouvez connecter des réseaux virtuels entre eux à l’aide de l’appairage de réseaux virtuels. Ces réseaux virtuels peuvent appartenir à la même région ou à des régions différentes (connexion également appelée Global VNet Peering). Une fois que les deux réseaux virtuels sont appairés, leurs ressources peuvent communiquer entre elles avec les mêmes bande passante et latence, comme si elles se trouvaient sur le même réseau virtuel. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer deux réseaux virtuels
> * Connecter deux réseaux virtuels à l’aide de l’homologation de réseaux virtuels
> * Déployer une machine virtuelle sur chaque réseau virtuel
> * Établir une communication entre les machines virtuelles

Si vous préférez, vous pouvez suivre ce tutoriel en utilisant [Azure CLI](tutorial-connect-virtual-networks-cli.md) ou [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-virtual-networks"></a>Créer des réseaux virtuels

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
3. Entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis choisissez **Créer** :

    |Paramètre|Valeur|
    |---|---|
    |Nom|myVirtualNetwork1|
    |Espace d’adressage|10.0.0.0/16|
    |Abonnement| Sélectionnez votre abonnement.|
    |Groupe de ressources| Sélectionnez **Créer** et entrez *myResourceGroup*.|
    |Lieu| Sélectionnez **USA Est**.|
    |Nom du sous-réseau|Sous-réseau1|
    |Plage d’adresses de sous-réseau|10.0.0.0/24|

      ![Créez un réseau virtuel](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Effectuez à nouveau les étapes 1 à 3, avec les modifications suivantes :

    |Paramètre|Valeur|
    |---|---|
    |Nom|myVirtualNetwork2|
    |Espace d’adressage|10.1.0.0/16|
    |Groupe de ressources| Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**.|
    |Plage d’adresses de sous-réseau|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Appairer des réseaux virtuels

1. Dans la zone de recherche en haut du portail Azure, commencez à taper *MyVirtualNetwork1*. Quand la mention **myVirtualNetwork1** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez **Peerings** (Appairages) sous **PARAMÈTRES**, puis **+ Ajouter**, comme indiqué dans l’image suivante :

    ![Créer un appairage](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.

    |Paramètre|Valeur|
    |---|---|
    |Nom|myVirtualNetwork1-myVirtualNetwork2|
    |Abonnement| Sélectionnez votre abonnement.|
    |Réseau virtuel|myVirtualNetwork2 : Pour sélectionner le réseau virtuel *myVirtualNetwork2*, sélectionnez **Réseau virtuel**, puis **myVirtualNetwork2**. Vous pouvez sélectionner un réseau virtuel figurant dans la même région ou dans une région différente.|

    ![Paramètres d’appairage](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    **L’ÉTAT D’APPAIRAGE** est *Initié*, comme indiqué dans l’image suivante :

    ![État d’appairage](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Si vous ne voyez pas l’état, actualisez votre navigateur.

4. Dans la zone **Recherche** en haut du portail Azure, commencez à taper *MyVirtualNetwork2*. Quand **myVirtualNetwork2** apparaît dans les résultats de recherche, sélectionnez-le.
5. Effectuez à nouveau les étapes 2 et 3, avec les modifications suivantes, puis sélectionnez **OK** :

    |Paramètre|Valeur|
    |---|---|
    |Nom|myVirtualNetwork2-myVirtualNetwork1|
    |Réseau virtuel|myVirtualNetwork1|

    **L’ÉTAT D’APPAIRAGE** est *Connecté*. Azure a également changé l’état *Initié* de l’appairage *myVirtualNetwork2-myVirtualNetwork1* en *Connecté*. L’appairage de réseaux virtuels n’est pas entièrement établi tant que l’état d’appairage pour les deux réseaux virtuels n’est pas *Connecté*. 

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez une machine virtuelle sur chaque réseau virtuel afin de pouvoir établir une communication entre elles dans une étape ultérieure.

### <a name="create-the-first-vm"></a>Créer la première machine virtuelle

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. Vous pouvez sélectionner différents systèmes d’exploitation, mais les étapes restantes partent du principe que vous avez sélectionné **Windows Server 2016 Datacenter**. 
3. Entrez ou sélectionnez les informations suivantes pour **De base**, acceptez les valeurs par défaut pour les autres paramètres, puis choisissez **Créer** :

    |Paramètre|Valeur|
    |---|---|
    |Nom|myVm1|
    |Nom d'utilisateur| Entrez un nom d’utilisateur de votre choix.|
    |Mot de passe| Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Groupe de ressources| Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**.|
    |Emplacement| Sélectionnez **USA Est**.|
4. Sélectionnez une taille de machine virtuelle sous **Choisir une taille**.
5. Dans **Paramètres**, sélectionnez les valeurs suivantes, puis sélectionnez **OK** :

    |Paramètre|Valeur|
    |---|---|
    |Réseau virtuel| myVirtualNetwork1 : S’il n’est pas déjà sélectionné, sélectionnez **Réseau virtuel**, puis **myVirtualNetwork1** sous **Choisir un réseau virtuel**.|
    |Sous-réseau| Subnet1 : S’il n’est pas déjà sélectionné, sélectionnez **Sous-réseau**, puis sélectionnez **Subnet1** sous **Choisir un sous-réseau**.|
    
    ![Paramètres de la machine virtuelle](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. Sous **Créer** dans **Résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle.

### <a name="create-the-second-vm"></a>Créer la seconde machine virtuelle

Effectuez à nouveau les étapes 1 à 6, avec les modifications suivantes :

|Paramètre|Valeur|
|---|---|
|Nom | myVm2|
|Réseau virtuel | myVirtualNetwork2|

La création des machines virtuelles peut prendre plusieurs minutes. Attendez que les deux machines virtuelles aient été créées avant de passer aux étapes restantes.

## <a name="communicate-between-vms"></a>Établir une communication entre les machines virtuelles

1. Dans la zone de *recherche* en haut du portail, commencez à taper *myVm1*. Quand **myVm1** apparaît dans les résultats de la recherche, sélectionnez-la.
2. Créez une connexion Bureau à distance avec la machine virtuelle *myVm1* en sélectionnant **Connecter**, comme indiqué dans l’image suivante :

    ![Connexion à la machine virtuelle](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Pour vous connecter à la machine virtuelle, ouvrez le fichier RDP téléchargé. Si vous y êtes invité, sélectionnez **Connexion**.
4. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle (il se peut que vous deviez choisir **Plus de choix**, puis **Utiliser un compte différent** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis sélectionnez **OK**.
5. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Sélectionnez**Oui** pour poursuivre le processus de connexion.
6. Dans une étape ultérieure, un test ping sera utilisé pour communiquer avec la machine virtuelle *myVm2* depuis la machine virtuelle *myVm1*. Le test ping utilise le protocole ICMP, qui est interdit par le Pare-feu Windows par défaut. Sur la machine virtuelle *myVm1*, autorisez ICMP à travers le pare-feu Windows afin de pouvoir effectuer un test ping pour cette machine virtuelle à partir de *myVm2* lors d’une étape ultérieure, en utilisant PowerShell :

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Bien que ce test ping soit utilisé pour établir une communication entre les machines virtuelles, il n’est pas recommandé d’autoriser le protocole IMCP via le pare-feu Windows lors de déploiements de production.

7. Pour établir une connexion avec la machine virtuelle *myVm2*, entrez la commande suivante à partir d’une invite de commandes sur la machine virtuelle *myVm1* :

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Étant donné que vous avez activé la commande ping sur *myVm1*, vous pouvez désormais y effectuer un test ping par adresse IP :

    ```
    ping 10.0.0.4
    ```
    
9. Déconnectez vos sessions RDP sur *myVm1* et *myVm2*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le ainsi que toutes les ressources qu’il contient : 

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à connecter deux réseaux situés dans la même région Azure à l’aide de l’appairage de réseaux virtuels. Vous pouvez également appairer des réseaux virtuels situés dans des [régions différentes](virtual-network-manage-peering.md#cross-region) et dans des [abonnements Azure différents](create-peering-different-subscriptions.md#portal). Vous pouvez aussi créer des [conceptions réseau hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) avec l’appairage. Pour en savoir plus sur l’appairage de réseaux virtuels, consultez [Aperçu de présentation de l’appairage de réseaux virtuels](virtual-network-peering-overview.md) et [Gérer les appairages de réseau virtuels](virtual-network-manage-peering.md).

Pour connecter votre propre ordinateur à un réseau virtuel via un VPN et interagir avec des ressources d’un réseau virtuel ou de réseaux virtuels appairés, consultez [Connecter votre ordinateur à un réseau virtuel](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
