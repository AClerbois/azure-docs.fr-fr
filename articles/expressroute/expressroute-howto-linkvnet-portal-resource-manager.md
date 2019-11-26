---
title: 'ExpressRoute : Connecter un réseau virtuel à un circuit : Portail Azure'
description: Connecter un réseau virtuel à un circuit Azure ExpressRoute. Étapes de la procédure.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022188"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Connecter un réseau virtuel à un circuit ExpressRoute à l’aide du portail
> [!div class="op_single_selector"]
> * [Portail Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interface de ligne de commande Azure](howto-linkvnet-cli.md)
> * [Vidéo - portail Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-linkvnet-classic.md)
> 

Cet article vous aide à créer une connexion pour lier un réseau virtuel à un circuit Azure ExpressRoute à l’aide du portail Azure. Les réseaux virtuels que vous connectez à votre circuit Azure ExpressRoute peuvent appartenir au même abonnement ou faire partie d’un autre abonnement.

## <a name="before-you-begin"></a>Avant de commencer

* Avant de commencer la configuration, examinez les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md).

* Vous devez disposer d’un circuit ExpressRoute actif.
  * Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) et faites-le activer par votre fournisseur de service de connectivité.
  * Vérifiez que le peering privé Azure est configuré pour votre circuit. Consultez l’article [Créer et modifier le Peering pour un circuit ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) pour obtenir des instructions sur le Peering et le routage.
  * Vérifiez que le peering privé Azure est configuré, et que le peering BGP entre votre réseau et Microsoft est opérationnel pour pouvoir activer la connectivité de bout en bout.
  * Vérifiez qu’un réseau virtuel et une passerelle de réseau virtuel ont été créés et entièrement approvisionnés. Suivez les instructions pour [créer une passerelle de réseau virtuel pour ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Une passerelle de réseau virtuel pour ExpressRoute utilise le type de passerelle « ExpressRoute », pas de VPN.

* Vous pouvez lier jusqu’à 10 réseaux virtuels à un circuit ExpressRoute standard. Tous les réseaux virtuels doivent figurer dans la même région géopolitique lors de l’utilisation d’un circuit ExpressRoute standard.

* Un réseau virtuel unique peut être lié à quatre circuits ExpressRoute maximum. Utilisez la procédure ci-dessous afin de créer un objet de connexion pour chaque circuit ExpressRoute auquel vous vous connectez. Les circuits ExpressRoute peuvent être dans le même abonnement, dans des abonnements différents ou dans une combinaison des deux.

* Vous pouvez lier un réseau virtuel à l’extérieur de la zone géopolitique du circuit ExpressRoute ou lier un plus grand nombre de réseaux virtuels à votre circuit ExpressRoute si vous avez activé le module complémentaire Premium d’ExpressRoute. Pour plus d’informations sur le module complémentaire Premium, consultez le [FAQ](expressroute-faqs.md) .

* Vous pouvez [visualiser une vidéo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) avant de commencer afin de mieux comprendre les étapes.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Connecter un réseau virtuel à un circuit - même abonnement

> [!NOTE]
> Les informations de configuration BGP ne s’affichent pas si vos peerings ont été configurés par le fournisseur de la couche 3. Si votre circuit est dans l’état Approvisionné, vous pouvez créer des connexions.
>

### <a name="to-create-a-connection"></a>Pour créer une connexion

1. Assurez-vous que votre circuit ExpressRoute et le peering privé Azure ont été correctement configurés. Suivez les instructions fournies dans [Création d’un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et [Créer et modifier le peering pour un circuit ExpressRoute](expressroute-howto-routing-arm.md). Votre circuit ExpressRoute doit être similaire à l’image suivante :

   [![Capture d’écran Circuit ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Afficher le circuit")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Vous pouvez maintenant commencer à approvisionner une connexion pour lier votre passerelle de réseau virtuel à votre circuit ExpressRoute. Cliquez sur **Connexion** > **Ajouter** pour ouvrir la page **Ajouter une connexion**, puis configurez les valeurs.

   [![Capture d’écran Ajouter une connexion](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Capture d’écran Ajouter une connexion")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. Une fois votre connexion correctement configurée, votre objet de connexion affiche les informations de la connexion.

   ![Capture d’écran Objet de connexion](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Connecter un réseau virtuel à un circuit - autre abonnement

Vous pouvez partager un circuit ExpressRoute entre plusieurs abonnements. La figure suivante montre un schéma simple sur le fonctionnement du partage de circuits ExpressRoute entre plusieurs abonnements.

![Connectivité entre abonnements](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Chacun des petits clouds dans le cloud principal est utilisé pour représenter les abonnements appartenant à différents services au sein d’une organisation.
- Chacun des services au sein de l’organisation peut utiliser son propre abonnement pour déployer ses services, mais ils peuvent partager un même circuit ExpressRoute pour se connecter à votre réseau local.
- Un seul service (dans cet exemple : le service informatique) peut détenir le circuit ExpressRoute. D’autres abonnements au sein de l’organisation peuvent utiliser le circuit ExpressRoute et des autorisations associées au circuit, notamment des abonnements à d’autres clients Azure Active Directory et des inscriptions à un Contrat Entreprise.

  > [!NOTE]
  > Les frais de connectivité et de bande passante pour le circuit dédié s’appliquent au propriétaire du circuit ExpressRoute. Tous les réseaux virtuels partagent la même bande passante.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administration : à propos des propriétaires du circuit et des utilisateurs du circuit

Le « propriétaire du circuit » est l’utilisateur avec pouvoir autorisé de la ressource de circuit ExpressRoute. Le propriétaire du circuit peut créer des autorisations utilisables par les « utilisateurs du circuit ». Les utilisateurs du circuit sont les propriétaires des passerelles de réseau virtuel qui ne figurent pas dans le même abonnement que le circuit ExpressRoute. Les utilisateurs du circuit peuvent échanger des autorisations (une seule autorisation par réseau virtuel).

Le propriétaire du circuit a le pouvoir de modifier et de révoquer les autorisations à tout moment. La révocation d’une autorisation entraîne la suppression de toutes les connexions de l’abonnement dont l’accès a été révoqué.

### <a name="circuit-owner-operations"></a>Opérations du propriétaire du circuit

**Création d’une autorisation de connexion**

Le propriétaire du circuit crée une autorisation. Cela entraîne la création d'une clé d'autorisation qui peut être utilisée par un utilisateur du circuit pour se connecter à ses passerelles de réseau virtuel vers un circuit ExpressRoute. Une autorisation n’est valide que pour une seule connexion.

> [!NOTE]
> Chaque connexion nécessite une autorisation distincte.
>

1. Dans la page ExpressRoute, cliquez sur **Autorisations**, tapez un **nom** pour l’autorisation, puis cliquez sur **Enregistrer**.

   ![Autorisations](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Une fois la configuration enregistrée, copiez **l’ID de ressource** et la **clé d’autorisation**.

   ![Clé d’autorisation](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Suppression d’une autorisation de connexion**

Vous pouvez supprimer une connexion en sélectionnant l’icône **Supprimer** dans la page de votre connexion.

### <a name="circuit-user-operations"></a>Opérations de l’utilisateur du circuit

L’utilisateur du circuit a besoin de l’ID de ressource et d’une clé d’autorisation du propriétaire du circuit.

**Réclamation d’une autorisation de connexion**

1. Cliquez sur le bouton **+Nouveau**.

   ![Click New](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Recherchez l’option **Connexion** dans la Place de marché. Sélectionnez-la, puis cliquez sur **Créer**.

   ![Rechercher une connexion](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Vérifiez que l’option **Type de connexion** est définie sur « ExpressRoute ».
4. Renseignez les détails, puis cliquez sur **OK** dans la page De base.

   ![Page De base](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Dans la page **Paramètres**, sélectionnez **Passerelle de réseau virtuel**, puis cochez la case **Utiliser l’autorisation**.
6. Entrez la **clé d’autorisation** et **l’URI du circuit appairé**, puis donnez un nom à la connexion. Cliquez sur **OK**. L'**URI du circuit homologue** est l’ID de ressource du circuit ExpressRoute (que vous pouvez trouver dans le volet des paramètres des propriétés du circuit ExpressRoute).

   ![Page Paramètres](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Passez en revue les informations contenues dans la page **Résumé**, puis cliquez sur **OK**.

**Libération d’une autorisation de connexion**

Vous pouvez libérer une autorisation en supprimant la connexion qui lie le circuit ExpressRoute et le réseau virtuel.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Supprimer une connexion pour annuler la liaison d’un réseau virtuel

Vous pouvez supprimer une connexion et annuler la liaison de votre réseau virtuel à un circuit ExpressRoute en sélectionnant l’icône **Supprimer** dans la page de votre connexion.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
