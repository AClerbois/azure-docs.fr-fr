---
title: "Créer un équilibrage de charge interne dans le portail Azure | Microsoft Docs"
description: "Découvrez comment créer un équilibreur de charge interne dans Resource Manager à l’aide du portail Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ac14fb9-8d14-4892-bfe6-8bc74c48ae2c
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 3be595b03f667cf9700d2f17eb2080aa74f41dd9
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Créer un équilibreur de charge interne dans le portail Azure

> [!div class="op_single_selector"]
> * [Portail Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Interface de ligne de commande Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modèle](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du [modèle de déploiement classique](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Prise en main de la création d’un équilibreur de charge interne avec le portail Azure

Pour créer un équilibreur de charge interne à partir du portail Azure, suivez les étapes ci-dessous.

1. Ouvrez un navigateur, accédez au [portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Dans l’angle supérieur gauche de l’écran, cliquez sur **Nouveau** > **Réseau** > **Équilibreur de charge**.
3. Dans le panneau **Créer un équilibreur de charge**, tapez le **nom** de votre équilibreur de charge.
4. Sous **Schéma**, cliquez sur **Interne**.
5. Cliquez sur **Réseau virtuel**, puis sélectionnez le réseau virtuel dans lequel vous souhaitez créer l’équilibreur de charge.

   > [!NOTE]
   > Si vous ne voyez pas le réseau virtuel que vous souhaitez utiliser, vérifiez l’ **Emplacement** que vous utilisez pour l’équilibreur de charge et modifiez-le en conséquence.

6. Cliquez sur **Sous-réseau**, puis sélectionnez le sous-réseau sur lequel vous souhaitez créer l’équilibreur de charge.
7. Sous **Attribution d’adresses IP**, cliquez sur **Dynamique** ou **Statique**, selon que vous souhaitez une adresse IP statique ou non pour l’équilibreur de charge.

   > [!NOTE]
   > Si vous choisissez d’utiliser une adresse IP statique, vous devez fournir une adresse pour l’équilibreur de charge.

8. Sous **Groupe de ressources**, spécifiez le nom d’un nouveau groupe de ressources pour l’équilibreur de charge ou cliquez sur **Sélectionner** et choisissez un groupe de ressources existant.
9. Cliquez sur **Create**.

## <a name="configure-load-balancing-rules"></a>Configuration des règles d’équilibrage de la charge

Après la création de l’équilibreur de charge, accédez à la ressource d’équilibreur de charge pour la configurer.
Configurez un pool d’adresses principal, ainsi qu’une sonde avant de configurer une règle d’équilibrage de charge.

### <a name="step-1-configure-a-backend-pool"></a>Étape 1 : Configurer un pool principal

1. Dans le portail Azure, cliquez sur **Parcourir** > **Équilibreurs de charge**, puis cliquez sur l’équilibreur de charge créé précédemment.
2. Dans le panneau **Paramètres**, cliquez sur **Pools principaux**.
3. Dans le panneau **Pools d’adresses principales**, cliquez sur **Ajouter**.
4. Dans le panneau **Ajouter un pool principal**, tapez le **nom** du pool principal, puis cliquez sur **OK**.

### <a name="step-2-configure-a-probe"></a>Étape 2 : Configurer une sonde

1. Dans le portail Azure, cliquez sur **Parcourir** > **Équilibreurs de charge**, puis cliquez sur l’équilibreur de charge créé précédemment.
2. Dans le panneau **Paramètres**, cliquez sur **Sondes**.
3. Dans le panneau **Sondes**, cliquez sur **Ajouter**.
4. Dans le panneau **Ajouter une sonde**, tapez le **nom** de la sonde.
5. Sous **Protocole**, sélectionnez **HTTP** (pour les sites web) ou **TCP** (pour les autres applications basées sur TCP).
6. Sous **Port**, spécifiez le port à utiliser pour accéder à la sonde.
7. Sous **Chemin d’accès** (pour les sondes HTTP uniquement), spécifiez le chemin d’accès à utiliser comme sonde.
8. Sous **Intervalle** , spécifiez la fréquence de sondage de l’application.
9. Sous **Seuil défectueux**, spécifiez le nombre de tentatives devant échouer avant que la machine virtuelle principale ne soit marquée comme défectueuse.
10. Cliquez sur **OK** pour créer la sonde.

### <a name="step-3-configure-load-balancing-rules"></a>Étape 3 : Configurer les règles d’équilibrage de charge

1. Dans le portail Azure, cliquez sur **Parcourir** > **Équilibreurs de charge**, puis cliquez sur l’équilibreur de charge créé précédemment.
2. Dans le panneau **Paramètres**, cliquez sur **Règles d’équilibrage de la charge**.
3. Dans le panneau **Règles d’équilibrage de la charge**, cliquez sur **Ajouter**.
4. Dans le panneau **Ajouter une règle d’équilibrage de charge**, tapez le **nom** de la règle.
5. Sous **Protocole**, sélectionnez **HTTP** (pour les sites web) ou **TCP** (pour les autres applications basées sur TCP).
6. Sous **Port**, spécifiez le port auquel les clients se connectent dans l’équilibreur de charge.
7. Sous **Port principal**, spécifiez le port à utiliser dans le pool principal (en règle générale, le port de l’équilibreur de charge et le port principal sont identiques).
8. Sous **Pool principal**, sélectionnez le pool principal créé précédemment.
9. Sous **Persistance de session**, sélectionnez la façon dont vous souhaitez que les sessions soient conservées.
10. Sous **Délai d’inactivité (minutes)**, spécifiez le délai d’inactivité.
11. Sous **Adresse IP flottante (retour serveur direct)**, cliquez sur **Désactivé** ou **Activé**.
12. Cliquez sur **OK**.

## <a name="next-steps"></a>Étapes suivantes

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)


