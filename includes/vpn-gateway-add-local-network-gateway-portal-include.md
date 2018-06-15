---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ab0dfac298ab7246935649010100c14dbe9c76a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
ms.locfileid: "30921430"
---
1. Dans le portail, cliquez sur **+Créer une ressource**.
2. Dans la zone de recherche, tapez **passerelle de réseau local**, puis appuyez sur **Entrée** pour lancer la recherche. Une liste de résultats apparaîtra. Cliquez sur **Passerelle de réseau local**, puis cliquez sur le bouton **Créer** pour ouvrir la page **Créer une passerelle de réseau local**.

  ![Créer la passerelle de réseau local](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Créer la passerelle de réseau local")

3. Dans la page **Créer une passerelle de réseau local**, spécifiez les valeurs de votre passerelle de réseau local.

  - **Nom :** spécifiez un nom pour votre objet de passerelle de réseau local.
  - **Adresse IP :** il s’agit de l’adresse IP publique du périphérique VPN auquel vous souhaitez qu’Azure se connecte. Spécifiez une adresse IP publique valide. L’adresse IP ne peut pas se trouver derrière NAT et doit être accessible par Azure. Si vous ne disposez pas de l’adresse IP actuellement, vous pouvez utiliser les valeurs indiquées dans l’exemple. Toutefois, vous devrez revenir en arrière et remplacer votre adresse IP d’espace réservé par l’adresse IP publique de votre périphérique VPN. Dans le cas contraire, Azure ne sera pas en mesure de se connecter.
  - **Espace d’adressage** fait référence aux plages d’adresses du réseau qui représente ce réseau local. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Assurez-vous que les plages que vous spécifiez ici ne se chevauchent pas avec les plages d’autres réseaux auxquels vous souhaitez vous connecter. Azure achemine la plage d’adresses que vous spécifiez vers l’adresse IP du périphérique VPN local. *Utilisez ici vos propres valeurs (et non les valeurs indiquées dans l’exemple) si vous voulez vous connecter à votre site local*.
  - **Configurer les paramètres de BGP** : sélectionnez cette option uniquement pour la configuration du protocole BGP.
  - **Abonnement** : vérifiez que l’abonnement approprié s’affiche.
  - **Groupe de ressources :** sélectionnez le groupe de ressources que vous souhaitez utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un déjà créé.
  - **Emplacement** : sélectionnez l’emplacement dans lequel cet objet sera créé. Vous pouvez sélectionner l’emplacement dans lequel se trouve votre réseau virtuel, mais vous n’êtes pas obligé de le faire.

4. Quand vous avez terminé de spécifier les valeurs, cliquez sur le bouton **Créer** en bas de la page pour créer la passerelle de réseau local.