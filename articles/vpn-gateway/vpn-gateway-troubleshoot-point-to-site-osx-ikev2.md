---
title: Résoudre les problèmes de connexion VPN point à site Azure à partir de clients Mac OS X | Microsoft Docs
description: Étapes de résolution des problèmes de connexion point à site des clients Mac OS X
services: vpn-gateway
documentationcenter: na
author: anzaman
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 0c058cb6547d67469d3138dc331b6181c07e6e65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60457032"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Résoudre les problèmes de connexion VPN point à site à partir de clients Mac OS X

Cet article vous aidera à résoudre les problèmes de connectivité point à site à partir de Mac OS X à l’aide du client VPN natif et d’IKEv2. Très basique, le client VPN disponible dans Mac pour IKEv2 n’offre pas beaucoup de possibilités de personnalisation. Il y a seulement quatre paramètres à vérifier :

* Adresse du serveur
* Identifiant distant
* Identifiant local
* Paramètres d’authentification
* Version du système d’exploitation (10.11 ou ultérieure)


## <a name="VPNClient"></a> Résoudre les problèmes d’authentification par certificat
1. Vérifiez les paramètres du client VPN. Accédez à la fenêtre **Réseau** en appuyant sur Commande + Maj, puis tapez « VPN » pour vérifier les paramètres du client VPN. Dans la liste, cliquez sur l’entrée VPN à examiner.

   ![Authentification par certificat IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Vérifiez que l’**adresse du serveur** correspond au FQDN complet et inclut cloudapp.net.
3. L’**identifiant distant** doit être identique à l’adresse du serveur (FQDN de la passerelle).
4. L’**identifiant local** doit être identique à l’**objet** du certificat client.
5. Cliquez sur **Réglages d’authentification** pour ouvrir la page Réglages d’authentification.

   ![Authentication settings](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Vérifiez que **Certificat** est sélectionné dans la liste déroulante.
7. Cliquez sur le bouton **Sélectionner** et vérifiez que le certificat approprié est sélectionné. Cliquez sur **OK** pour enregistrer les éventuelles modifications apportées.

## <a name="ikev2"></a>Résoudre les problèmes d’authentification par nom d’utilisateur et mot de passe

1. Vérifiez les paramètres du client VPN. Accédez à la fenêtre **Réseau** en appuyant sur Commande + Maj, puis tapez « VPN » pour vérifier les paramètres du client VPN. Dans la liste, cliquez sur l’entrée VPN à examiner.

   ![Nom d’utilisateur et mot de passe IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Vérifiez que l’**adresse du serveur** correspond au FQDN complet et inclut cloudapp.net.
3. L’**identifiant distant** doit être identique à l’adresse du serveur (FQDN de la passerelle).
4. L’**identifiant local** peut être vide.
5. Cliquez sur le bouton **Réglages d’authentification** et vérifiez que « Nom d’utilisateur » est sélectionné dans la liste déroulante.

   ![Authentication settings](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Vérifiez que les informations d’identification saisies sont correctes.

## <a name="additional"></a>Étapes supplémentaires

Si vous avez suivi les étapes précédentes et que tout est correctement configuré, téléchargez [Wireshark](https://www.wireshark.org/#download) et effectuez une capture de paquets.

1. Filtrez sur *isakmp* et examinez les paquets **IKE_SA**. Les détails de la proposition de l’association de sécurité doivent figurer sous **Payload: Security Association** (Charge utile : association de sécurité). 
2. Vérifiez que le client et le serveur présentent un jeu commun.

   ![Paquet](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. S’il n’existe aucune réponse du serveur sur les traces réseau, vérifiez que vous avez activé le protocole IKEv2 dans la page Configuration de la passerelle Azure sur le site web du portail Azure.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir de l’aide supplémentaire, consultez [Support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
