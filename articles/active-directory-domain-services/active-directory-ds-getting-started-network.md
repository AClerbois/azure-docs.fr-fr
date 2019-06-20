---
title: 'Azure Active Directory Domain Services : Prise en main | Microsoft Docs'
description: Activer Azure Active Directory Domain Services à l’aide du portail Azure
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 65cc63b32afcc565f1901c4df2893ad103ec0da3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234908"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Activer Azure Active Directory Domain Services à l’aide du portail Azure


## <a name="before-you-begin"></a>Avant de commencer
Reportez-vous à [Considérations relatives à la mise en réseau pour Azure Active Directory Domain Services](network-considerations.md).


## <a name="task-2-configure-network-settings"></a>Tâche 2 : Configurer les paramètres réseau
La tâche de configuration suivante consiste à créer un réseau virtuel Azure et un sous-réseau dédié à l’intérieur de celui-ci. Vous activez Azure Active Directory Domain Services dans ce sous-réseau de votre réseau virtuel. Vous pouvez également sélectionner un réseau virtuel actuel et créez le sous-réseau dédié à l’intérieur de celui-ci.

1. Cliquez sur **Réseau virtuel** pour choisir un réseau virtuel.
    > [!NOTE]
    > **Les réseaux virtuels classiques ne sont pas pris en charge pour les nouveaux déploiements.** Les réseaux virtuels classiques ne sont pas pris en charge pour les nouveaux déploiements. Les domaines managés existants déployés dans des réseaux virtuels classiques continuent à être pris en charge. Microsoft va vous permettre de migrer un domaine managé existant depuis un réseau virtuel Classic vers un réseau virtuel Resource Manager.
    >

2. Dans la page **Choisir un réseau virtuel**, vous voyez tous les réseaux virtuels actuels. Vous voyez uniquement les réseaux virtuels qui appartiennent au groupe de ressources et à l’emplacement Azure que vous avez sélectionnés dans la page **Fonctions de base** de l’Assistant.
3. Choisissez le réseau virtuel dans lequel Azure AD Domain Services doit être activé. Vous pouvez choisir un réseau virtuel existant ou en créer un.

   > [!TIP]
   > **Vous ne pouvez pas déplacer le domaine géré vers un autre réseau virtuel une fois Azure AD Domain Services activé.** Sélectionnez le réseau virtuel adapté pour activer votre domaine géré. Après avoir créé un domaine géré, vous ne pouvez pas le déplacer vers un autre réseau virtuel sans supprimer le domaine géré. Nous vous recommandons de consulter les [Considérations relatives à la mise en réseau pour Azure Active Directory Domain Services](network-considerations.md) avant de continuer.  
   >

4. **Créer un réseau virtuel :** Cliquez sur **Créer** pour créer un réseau virtuel. Utilisez un sous-réseau dédié pour Azure AD Domain Services. Par exemple, créez un sous-réseau nommé « DomainServices », de façon à aider les autres administrateurs à comprendre ce qui est déployé au sein du sous-réseau. Cliquez sur **OK** quand vous avez terminé.

    ![Sélectionner un réseau virtuel](./media/getting-started/domain-services-blade-network-pick-vnet.png)

   > [!WARNING]
   > Veillez à choisir un espace d’adressage qui se trouve dans l’espace d’adressage IP privé. Les adresses IP qui ne vous appartiennent pas et qui se trouvent dans l’espace d’adressage public provoquent des erreurs dans Azure AD Domain Services.

5. **Réseau virtuel existant :** Si vous envisagez de sélectionner un réseau virtuel existant, [créez un sous-réseau dédié à l’aide de l’extension de réseaux virtuels](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet), puis choisissez ce sous-réseau. Cliquez sur **Réseau virtuel** pour choisir le réseau virtuel existant. Cliquez sur **Sous-réseau** pour sélectionner le sous-réseau dédié dans votre réseau virtuel existant, afin d’y activer votre nouveau domaine managé. Cliquez sur **OK** quand vous avez terminé.

    ![Sélectionner un sous-réseau au sein du réseau virtuel](./media/getting-started/domain-services-blade-network-pick-subnet.png)

   > [!NOTE]
   > **Indications pour la sélection d’un sous-réseau**
   > 1. Utilisez un sous-réseau dédié pour Azure AD Domain Services. Ne déployez pas d’autres machines virtuelles sur ce sous-réseau. Cette configuration vous permet de configurer des groupes de sécurité réseau (NSG) pour vos charges de travail ou machines virtuelles sans perturber votre domaine managé. Pour plus de détails, consultez [Considérations relatives à la mise en réseau pour Azure Active Directory Domain Services](network-considerations.md).
   > 2. Ne sélectionnez pas le sous-réseau de passerelle pour déployer Azure AD Domain Services, car cette configuration n’est pas prise en charge.
   > 3. Le sous-réseau que vous avez sélectionné doit avoir au moins 3 à 5 adresses IP disponibles dans son espace d’adressage.

6. Lorsque vous avez terminé, cliquez sur **OK** pour accéder à la page **Groupe des administrateurs** de l’Assistant.


## <a name="next-step"></a>Étape suivante
[Tâche 3 : Configurer le groupe d’administration et activer Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)
