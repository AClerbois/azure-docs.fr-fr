---
title: 'Résoudre les problèmes d’un client VPN avec l’authentification Azure AD : passerelle VPN Azure | Microsoft Docs'
description: Résoudre les problèmes rencontrés par les clients avec les connexions de passerelle VPN point à site (P2S) à l’aide de l’authentification Azure AD
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: e239f283d5065e032e98e87a72a4bfe9fbafbdb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511650"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Résoudre les problèmes d’un client VPN avec l’authentification Azure AD

Cet article vous aide à résoudre les problèmes rencontrés par un client VPN pour se connecter à un réseau virtuel à l’aide d’un VPN point à site et de l’authentification Azure Active Directory.

## <a name="status"></a>Afficher le journal d’état

Affichez le journal d’état pour examiner les messages d’erreur.

![logs](./media/troubleshoot-ad-vpn-client/1.png)

1. Cliquez sur l’icône des flèches en bas à droite dans la fenêtre du client pour afficher les **journaux d’état**.
2. Recherchez dans les journaux des erreurs susceptibles d’indiquer le problème.
3. Les messages d’erreur sont affichés en rouge.

## <a name="clear"></a>Effacer les informations de connexion

Effacez les informations de connexion.

![se connecter](./media/troubleshoot-ad-vpn-client/2.png)

1. Sélectionnez « … » à côté du profil qui pose problème. Sélectionnez **Configure -> Clear Saved Account** (Configurer -> Effacer le compte enregistré).
2. Sélectionnez **Enregistrer**.
3. Essayez de vous connecter.
4. Si la connexion échoue encore, passez à la section suivante.

## <a name="diagnostics"></a>Exécuter les diagnostics

Exécutez les diagnostics sur le client VPN.

![diagnostics](./media/troubleshoot-ad-vpn-client/3.png)

1. Cliquez sur **…** à côté du profil sur lequel vous voulez exécuter les diagnostics. Sélectionnez **Diagnose -> Run Diagnosis** (Diagnostiquer -> Exécuter les diagnostics).
2. Le client exécute alors une série de tests et affiche les résultats.

   * Accès à Internet : vérifie si le client dispose d’une connectivité Internet
   * Informations d’identification du client : vérifie si le point de terminaison de l’authentification Azure Active Directory est accessible
   * Server Resolvable (Résolution par le serveur) : contacte le serveur DNS pour résoudre l’adresse IP du serveur VPN configuré
   * Serveur accessible : vérifie si le serveur VPN répond
3. Si l’un de ces tests échoue, contactez votre administrateur réseau pour résoudre le problème.
4. La section suivante vous montre comment collecter les journaux, si vous en avez besoin.

## <a name="logfiles"></a>Collecter les fichiers journaux du client

Collectez les fichiers journaux du client. Vous pouvez ensuite envoyer les fichiers journaux au support technique ou à votre administrateur par la méthode de votre choix (par exemple, par e-mail).

1. Cliquez sur « ... » à côté du profil sur lequel vous voulez exécuter les diagnostics. Sélectionnez **Diagnose -> Show Logs Directory** (Diagnostiquer -> Afficher le répertoire des journaux).

   ![afficher les journaux](./media/troubleshoot-ad-vpn-client/4.png)
2. L’Explorateur Windows ouvre le dossier qui contient les fichiers journaux.

   ![afficher le fichier](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Créer un locataire Azure Active Directory qui utilise l'authentification Azure AD pour les connexions Open VPN P2S](openvpn-azure-ad-tenant.md).