---
title: Déployer un partenaire de sécurité de confiance Azure Firewall Manager
description: Découvrez comment déployer un partenaire de sécurité de confiance Azure Firewall Manager à l’aide du portail Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: fe733b686f2b56beee26a6c33c4d6264d621e627
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511362"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Déployer un partenaire de sécurité de confiance (préversion)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Les *partenaires de sécurité de confiance* dans Azure Firewall Manager vous permettent d’utiliser les meilleures offres SECaaS (sécurité en tant que service) tierces et familières afin de protéger l’accès à Internet pour vos utilisateurs.

Pour en savoir plus sur les scénarios pris en charge et les bonnes pratiques, consultez [Présentation des partenaires de sécurité de confiance (préversion)](trusted-security-partners.md).

Les partenaires de sécurité pris en charge sont **ZScaler** et **iboss** pour cette préversion. Les régions prises en charge sont USA Centre-Ouest, USA Centre Nord, USA Ouest, USA Ouest 2 et USA Est.

## <a name="prerequisites"></a>Prérequis

> [!IMPORTANT]
> Azure Firewall Manager Preview doit être explicitement activée à l’aide de la commande PowerShell `Register-AzProviderFeature`.

À partir d’une invite de commande PowerShell, exécutez les commandes suivantes :

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Il faut compter 30 minutes pour l’inscription de la fonctionnalité. Exécutez la commande suivante pour vérifier l’état de votre inscription :

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Déployer un fournisseur de sécurité tiers dans un nouveau hub

1. Connectez-vous au portail Azure sur https://portal.azure.com.
2. Dans **Recherche**, tapez **Firewall Manager** et sélectionnez-le sous **Services**.
3. Accédez à **Prise en main**. Sélectionnez **Créer un hub virtuel sécurisé**. 
4. Entrez votre abonnement et votre groupe de ressources, sélectionnez une région prise en charge et ajoutez vos informations de réseau étendu virtuel et de hub. 
5. L’option **Déployer la passerelle VPN** est activée par défaut. Une passerelle VPN est nécessaire pour déployer un partenaire de sécurité de confiance dans le hub. 
6. Sélectionnez **Suivant : Pare-feu Azure**
   > [!NOTE]
   > Les partenaires de sécurité de confiance se connectent à votre hub à l’aide de tunnels de passerelle VPN. Si vous supprimez la passerelle VPN, les connexions à vos partenaires de sécurité de confiance sont perdues.
7. Si vous souhaitez déployer Pare-feu Azure pour filtrer le trafic privé et le fournisseur de services tiers pour filtrer le trafic Internet, sélectionnez une stratégie pour Pare-feu Azure. Consultez les scénarios [pris en charge](trusted-security-partners.md#key-scenarios).
8. Si vous souhaitez déployer uniquement un fournisseur de sécurité tiers dans le hub, définissez l’option **Pare-feu Azure : Activé/désactivé** sur **Désactivé**. 
9. Sélectionnez **Suivant : Partenaires de sécurité de confiance**.
10. Définissez l’option **Partenaire de sécurité de confiance** sur **Activé**. Sélectionnez un partenaire. 
11. Sélectionnez **Suivant**. 
12. Passez en revue le contenu, puis sélectionnez **Créer**.

Le déploiement de la passerelle VPN peut prendre plus de 30 minutes.

Pour vérifier que le hub a été créé, accédez à Azure Firewall Manager->Hubs sécurisés. Sélectionnez le hub->page Vue d’ensemble pour afficher le nom du partenaire et l’état (**Connexion sécurisée en attente**).

Une fois le hub créé et le partenaire de sécurité configuré, connectez le fournisseur de sécurité au hub.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Déployer un fournisseur de sécurité tiers dans un hub existant

Vous pouvez également sélectionner un hub existant dans un réseau étendu virtuel et le convertir en *hub virtuel sécurisé*.

1. Dans **Prise en main**, sélectionnez **Convertir les hubs existants**.
2. Sélectionnez un abonnement et un hub existant. Suivez les étapes restantes pour déployer un fournisseur tiers dans un nouveau hub.

N’oubliez pas qu’une passerelle VPN doit être déployée pour convertir un hub existant en hub sécurisé avec des fournisseurs tiers.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configurer des fournisseurs de sécurité tiers pour qu’ils se connectent à un hub sécurisé

Pour que vous puissiez configurer des tunnels vers la passerelle VPN de votre hub virtuel, les fournisseurs tiers doivent disposer de droits d’accès à votre hub. Pour ce faire, associez un principal de service à votre abonnement ou groupe de ressources, puis accordez des droits d’accès. Vous devez ensuite fournir ces informations d’identification au tiers à l’aide de son portail.

1. Créer un principal de service Azure Active Directory (AD) : vous pouvez ignorer l’URL de redirection. 

   [Guide pratique pour Utiliser le portail pour créer une application Azure AD et un principal de service ayant accès aux ressources](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Ajoutez des droits d’accès et une étendue pour le principal de service.
   [Guide pratique pour Utiliser le portail pour créer une application Azure AD et un principal de service ayant accès aux ressources](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Pour un contrôle plus précis, vous pouvez limiter l’accès à votre groupe de ressources.
3. Suivez les instructions du lien suivant.

   - Pour vous connecter au portail partenaire et ajouter vos informations d’identification afin d’accorder au partenaire de confiance l’accès à votre hub sécurisé.
   - Une fois vos informations d’identification d’authentification Azure AD validées, suivez les instructions ci-dessous pour synchroniser les hubs virtuels dans le portail partenaire et configurer le tunnel sur le hub virtuel.

   [ZScaler : configuration d’une intégration Microsoft Azure Virtual WAN](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)
4. Vous pouvez consulter l’état de la création du tunnel sur le portail Azure Virtual WAN dans Azure. Une fois que l’état des tunnels est **connecté** à la fois sur Azure et sur le portail partenaire, passez aux étapes suivantes pour configurer des routes afin de sélectionner les filiales et les réseaux virtuels qui doivent envoyer le trafic Internet au partenaire.

## <a name="configure-route-settings"></a>Configurer les paramètres de routage

1. Accédez à Azure Firewall Manager -> Hubs sécurisés. 
2. Sélectionnez un hub. L’état du hub doit maintenant indiquer **Provisionné** au lieu de **Connexion sécurisée en attente**.

   Vérifiez que le fournisseur tiers peut se connecter au hub. Les tunnels sur la passerelle VPN doivent se trouver dans un état **connecté**. Cet état est plus révélateur de l’intégrité de la connexion entre le hub et le partenaire tiers que l’état précédent.
3. Sélectionnez le hub, puis accédez à **Paramètres de routage**.

   Quand vous déployez un fournisseur tiers sur le hub, il convertit ce dernier en *hub virtuel sécurisé*. Ainsi, le fournisseur tiers publie une route 0.0.0.0/0 (par défaut) vers le hub. Toutefois, les connexions de réseau virtuel et les sites connectés au hub n’obtiennent pas cette route par défaut, sauf si vous choisissez les connexions qui doivent l’obtenir.
4. Sous **Trafic Internet**, sélectionnez **De réseau virtuel vers Internet** ou **De filiale vers Internet** ou les deux pour que les routes soient configurées afin que l’envoi transite par le tiers.

   Cela indique uniquement le type de trafic à router vers le hub, mais n’affecte pas encore les routes sur les réseaux virtuels ou les filiales. Ces routes ne sont pas propagées par défaut à tous les réseaux virtuels/filiales attachés au hub.
5. Vous devez sélectionner **Connexions sécurisées** et sélectionner les connexions sur lesquelles ces routes doivent être définies. Cela indique les réseaux virtuels/filiales qui peuvent commencer à envoyer du trafic Internet vers le fournisseur tiers.
6. Dans **Paramètres de routage**, sélectionnez **Connexions sécurisées** sous Trafic Internet, puis sélectionnez le réseau virtuel ou les filiales (*sites* dans Virtual WAN) à sécuriser. Sélectionnez **Sécuriser le trafic Internet**.
   ![Sécuriser le trafic Internet](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Revenez à la page des hubs. L’état **Partenaire de sécurité de confiance** du hub doit désormais être **Sécurisé**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Trafic Internet de filiale ou de réseau virtuel via un service tiers

Ensuite, vous pouvez vérifier si des machines virtuelles de réseau virtuel ou le site de filiale peuvent accéder à Internet et vérifier que le trafic est acheminé vers le service tiers.

Une fois terminées les étapes du paramétrage des routes, les machines virtuelles de réseau virtuel ainsi que les sites de filiale reçoivent une route 0/0 vers le service tiers. Vous ne pouvez pas utiliser le protocole RDP ou SSH dans ces machines virtuelles. Pour vous connecter, vous pouvez déployer le service [Azure Bastion](../bastion/bastion-overview.md) dans un réseau virtuel appairé.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Sécuriser votre réseau cloud avec Azure Firewall Manager Preview à l’aide du portail Azure](secure-cloud-network.md)




