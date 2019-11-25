---
title: Créer des espaces de travail pour les portails Mes applications dans Azure Active Directory | Microsoft Docs
description: Utilisez les espaces de travail Mes applications pour personnaliser les pages Mes applications afin de simplifier l’expérience de Mes applications pour vos utilisateurs finaux. Organisez les applications en groupes avec des onglets distincts.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a986c2032fc41bbd34230c5b0ead745f88b000f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199829"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>Créer des espaces de travail sur le portail Mes applications (préversion)

Vos utilisateurs peuvent utiliser le portail Mes applications (préversion) pour afficher et démarrer les applications cloud auxquelles ils ont accès. Par défaut, toutes les applications auxquelles un utilisateur peut accéder sont répertoriées sur une seule page. Pour mieux organiser cette page pour vos utilisateurs, si vous disposez d’une licence Azure AD Premium P1 ou P2, vous pouvez configurer des espaces de travail. Avec un espace de travail, vous pouvez regrouper les applications associées (par exemple, par rôle de travail, tâche ou projet) et les afficher dans un onglet distinct. Un espace de travail applique essentiellement un filtre aux applications auxquelles un utilisateur peut déjà accéder, de sorte que l’utilisateur ne voit que les applications qui lui ont été affectées dans l’espace de travail.

> [!NOTE]
> Cet article explique comment un administrateur peut activer et créer des espaces de travail. Pour plus d’informations sur l’utilisation du portail et des espaces de travail Mes applications, consultez [Accéder et utiliser les espaces de travail](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-my-apps-preview-features"></a>Activer les fonctionnalités en préversion de Mes applications

1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’administrateur général ou administrateur utilisateur.

2. Accédez à **Azure Active Directory** > **Paramètres utilisateur**.

3. Sous **Préversions des fonctionnalités utilisateur**, sélectionnez **Gérer les paramètres de préversion de la fonctionnalité utilisateur**.

4. Sous **Les utilisateurs peuvent utiliser les fonctionnalités en préversion pour Mes applications**, choisissez une des options suivantes :
   * **Sélectionné** : active les fonctionnalités en préversion pour un groupe spécifique. Utilisez l’option **Sélectionner un groupe** pour sélectionner le groupe pour lequel vous souhaitez activer les fonctionnalités en préversion.  
   * **Tous** : active les fonctionnalités en préversion pour tous les utilisateurs.

   ![Fonctionnalités utilisateur en préversion](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> Pour ouvrir le portail Mes applications, les utilisateurs peuvent utiliser le lien `https://myapps.microsoft.com` ou le lien personnalisé pour votre organisation, par exemple `https://myapps.microsoft.com/contoso.com`. Si les utilisateurs ne sont pas redirigés vers la préversion de Mes applications, ils peuvent essayer `https://myapplications.microsoft.com` ou `https://myapplications.microsoft.com/contoso.com`.

## <a name="create-a-workspace"></a>Créer un espace de travail

Pour créer un espace de travail, vous devez disposer d’une licence Azure AD Premium P1 ou P2.

1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’administrateur avec une licence Azure AD Premium P1 ou P2.

2. Accédez à **Azure Active Directory** > **Enterprise applications (Applications d’entreprise)** .

3. Sous **Gérer**, sélectionnez **Espaces de travail (préversion)** .

4. Sélectionnez **Nouvel espace de travail**. Dans la page **Nouvel espace de travail**, entrez un **Nom** pour l’espace de travail (nous vous recommandons de ne pas utiliser « workspace » dans le nom. Saisissez ensuite une **Description**.

   ![Créer un espace de travail](media/access-panel-workspaces/new-workspace.png)

5. Sélectionnez **Vérifier + créer**. Les propriétés du nouvel espace de travail s’affichent.

6. Sélectionnez l’onglet **Applications** . Sous **Ajouter des applications**, sélectionnez toutes les applications que vous souhaitez ajouter à l’espace de travail, ou utilisez la zone **Rechercher** pour rechercher des applications. 

   ![Ajouter des applications à l’espace de travail](media/access-panel-workspaces/add-applications.png)

7. Sélectionnez **Ajouter**. La liste des applications sélectionnées s’affiche. Vous pouvez utiliser les flèches haut et bas pour modifier l’ordre des applications dans la liste.

   ![Liste des applications dans l’espace de travail](media/access-panel-workspaces/add-applications-list.png)

8. Sélectionnez l’onglet **Utilisateurs et groupes**. Pour ajouter un utilisateur ou un groupe, sélectionnez **Ajouter utilisateur**. 

9. Dans la page **Sélectionner les membres**, sélectionnez les utilisateurs ou groupes auxquels vous souhaitez affecter l’espace de travail. Ou utilisez la zone **Rechercher** pour rechercher des utilisateurs ou des groupes.

   ![Affecter des utilisateurs et des groupes à l’espace de travail](media/access-panel-workspaces/add-users-and-groups.png)

10. Une fois que vous avez fini de sélectionner les utilisateurs et groupes, choisissez **Sélectionner**.

11. Pour modifier le rôle d’un utilisateur de **Accès en lecture** en **Propriétaire** ou vice versa, cliquez sur le rôle actuel et sélectionnez un nouveau rôle.

    ![Attribuer des rôles aux utilisateurs et groupes](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>Afficher les journaux d’audit

Les journaux d’audit enregistrent les opérations des espaces de travail Mes applications, y compris la création d’espaces de travail pour les utilisateurs finaux. Les événements suivants sont générés pour ces actions à partir de Mes applications :

* Créer un espace de travail
* Modifier l’espace de travail
* Supprimer un espace de travail
* Lancer une application (utilisateur final)
* Ajouter des applications en libre-service (utilisateur final)
* Supprimer une application libre-service (utilisateur final)

Vous pouvez accéder aux journaux d’audit dans le [portail Azure](https://portal.azure.com) en sélectionnant **Azure Active Directory** > **Applications d’entreprise**  > **Journaux d’audit** dans la section Activité. Pour **Service**, sélectionnez **Mes applications**.

   ![Attribuer des rôles aux utilisateurs et groupes](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>Étapes suivantes
[Expériences d’utilisateurs finaux auprès des applications dans Azure Active Directory](end-user-experiences.md)