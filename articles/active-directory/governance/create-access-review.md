---
title: Créer une révision d’accès de groupes ou d’applications - Azure Active Directory | Microsoft Docs
description: Découvrez comment créer une révision d’accès des membres du groupe ou l’accès aux applications dans les révisions d’accès Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 804efa6e0a39e009e18bbb9dec5ad1638a163597
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495052"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Créer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD

L’accès aux groupes et aux applications pour les employés et les invités change au fil du temps. Afin de réduire les risques associés aux affectations d’accès obsolètes, les administrateurs peuvent utiliser Azure Active Directory (Azure AD) pour créer des révision des accès pour les membres de groupes ou pour l’accès aux applications. Si vous devez régulièrement passer en revue les accès, vous pouvez aussi créer des révisions d’accès périodiques. Pour plus d’informations sur ces scénarios, consultez [Gérer l’accès des utilisateurs](manage-user-access-with-access-reviews.md) et [Gérer l’accès des invités](manage-guest-access-with-access-reviews.md).

Cet article décrit comment créer un ou plusieurs révisions d’accès pour les membres du groupe ou l’accès aux applications.

## <a name="prerequisites"></a>Conditions préalables

- [Révisions d’accès](access-reviews-overview.md)
- Administrateur général ou administrateur d’utilisateurs

## <a name="create-one-or-more-access-reviews"></a>Créer un ou plusieurs révisions d’accès

1. Connectez-vous pour le portail Azure et ouvrez le [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Dans le menu de gauche, cliquez sur **révisions d’accès**.

1. Cliquez sur **Nouvelle révision d’accès** pour créer une révision d’accès.

    ![Révision d’accès - Contrôles](./media/create-access-review/access-reviews.png)

1. Nommez la révision d’accès. Si vous le souhaitez, vous pouvez fournir une description de cette révision. Les réviseurs ont accès au nom et à la description de la révision.

    ![Créer une révision d’accès - Nom et description de la révision](./media/create-access-review/name-description.png)

1. Définissez la **Date de début**. Par défaut, une révision d’accès se produit une seule fois, démarre le jour même de sa création et s’arrête un mois plus tard. Vous pouvez modifier les dates de début et de fin pour que la révision d’accès démarre ultérieurement et dure le nombre de jours souhaité.

    ![Créer une révision d’accès - Dates de début et de fin](./media/create-access-review/start-end-dates.png)

1. Pour que la révision d’accès périodique, modifiez le **fréquence** définir à partir de **une fois** à **hebdomadaire**, **mensuel**,  **Tous les trimestres** ou **une fois par an**. Utilisez le **durée** curseur ou zone de texte pour définir le nombre de jours chaque révision de la série périodique sera ouvre pour l’entrée des réviseurs. Par exemple, la durée maximale d’une révision mensuelle est de 27 jours, ce qui permet d’éviter le chevauchement des révisions.

1. Utilisez le paramètre **Fin** pour spécifier comment mettre fin à la série de révisions d’accès récurrentes. Les séries accès récurrentes peuvent se terminer de trois façons : elles peuvent s’exécuter de façon continue pour démarrer des révisions indéfiniment, s’exécuter jusqu’à une date spécifique ou s’exécuter jusqu’à ce qu’un nombre défini d’occurrences se soient produites. Vous, un autre administrateur de l’utilisateur ou un autre administrateur général arrêter la série après la création en modifiant la date dans **paramètres**, afin qu’elle se termine à cette date.

1. Dans le **utilisateurs** section, spécifiez les utilisateurs qui la révision d’accès s’applique à. Les révisions d’accès peuvent porter sur les membres d’un groupe ou sur les utilisateurs qui ont été assignés à une application. Vous pouvez affiner davantage la révision d’accès pour passer en revue seulement les utilisateurs invités qui sont des membres (ou affectés à l’application) au lieu d’examiner tous les utilisateurs qui sont des membres ou qui ont accès à l’application.

    ![Créer une révision d’accès - Utilisateurs](./media/create-access-review/users.png)

1. Dans le **groupe** , sélectionnez un ou plusieurs groupes que vous souhaitez revoir l’appartenance de.

    > [!NOTE]
    > Sélection de plusieurs groupes créera plusieurs révisions d’accès. Par exemple, en sélectionnant les cinq groupes créera cinq révisions d’accès distincts.
    
    ![Créer une révision d’accès - sélectionner un groupe](./media/create-access-review/select-group.png)

1. Dans le **Applications** section (si vous avez sélectionné **affectés à une application** à l’étape 8), sélectionnez les applications que vous souhaitez revoir l’accès à.

    > [!NOTE]
    > Sélection de plusieurs applications créera plusieurs révisions d’accès. Par exemple, en sélectionnant cinq applications créera cinq révisions d’accès distincts.
    
    ![Créer une révision d’accès - sélectionner l’application](./media/create-access-review/select-application.png)

1. Dans la section **Réviseurs**, sélectionnez une ou plusieurs personnes pour passer en revue tous les utilisateurs concernés. Sinon, vous pouvez choisir de faire en sorte que les membres vérifient leur propre accès. Si la ressource est un groupe, vous pouvez demander aux propriétaires de groupe d’effectuer la révision. Vous pouvez également demander à ce que les réviseurs fournissent un motif lorsqu’ils approuvent l’accès.

    ![Créer une révision d’accès - Réviseurs](./media/create-access-review/reviewers.png)

1. Dans la section **Programmes**, sélectionnez le programme que vous voulez utiliser. Vous pouvez simplifier le suivi et la collecte des révisions d’accès à des fins différentes en les organisant dans des programmes. Le **programme par défaut »** est toujours présent, mais vous pouvez aussi créer un autre programme. Par exemple, vous pouvez choisir de disposer d’un programme pour chaque initiative de conformité ou objectif de l’entreprise.

    ![Créer une révision d’accès - Programmes](./media/create-access-review/programs.png)

### <a name="upon-completion-settings"></a>Paramètres de saisie semi-automatique

1. Pour spécifier ce qui se passe après la fin d’une révision, développez la section **Paramètres de saisie semi-automatique**.

    ![Paramètres de saisie semi-automatique](./media/create-access-review/upon-completion-settings.png)

1. Si vous voulez supprimer automatiquement l’accès pour les utilisateurs qui ont été refusés, définissez **Appliquer automatiquement les résultats à ressource** sur **Activer**. Si vous voulez appliquer manuellement les résultats quand la révision est terminée, cliquez sur **Désactiver**.

1. Utilisez la liste **Le réviseur ne doit pas répondre** pour spécifier ce qui se passe pour les utilisateurs qui ne sont pas vérifiés par le réviseur au cours de la révision. Ce paramètre n’affecte pas les utilisateurs qui ont été vérifiés manuellement par les réviseurs. Si la décision finale du réviseur est Refuser, l’accès de l’utilisateur est supprimé.

    - **Aucune modification** : laisser l’accès de l’utilisateur inchangé
    - **Supprimer l’accès** : supprimer l’accès de l’utilisateur
    - **Approuver l’accès** : approuver l’accès de l’utilisateur
    - **Accepter les recommandations** : accepter la recommandation du système sur le refus ou l’approbation de la prolongation de l’accès de l’utilisateur

### <a name="advanced-settings"></a>Paramètres avancés

1. Pour spécifier d’autres paramètres, développez la section **Paramètres avancés**.

    ![Paramètres avancés](./media/create-access-review/advanced-settings.png)

1. Définissez **Afficher les recommandations** sur **Activer** pour montrer aux réviseurs les recommandations du système basées sur les informations des accès de l’utilisateur.

1. Définissez **Exiger la raison lors de l’approbation** sur **Activer** afin d’exiger que le réviseur indique un motif pour l’approbation.

1. Définissez **Notifications par e-mail** sur **Activer** pour qu’Azure AD envoie des notifications par e-mail aux réviseurs quand une révision d’accès commence et aux administrateurs quand une révision se termine.

1. Définissez **Rappels** sur **Activer** pour qu’Azure AD envoie des rappels concernant les révisions d’accès en cours aux réviseurs qui n’ont pas terminé leur révision.

## <a name="start-the-access-review"></a>Démarrer la révision d’accès

Une fois que vous avez spécifié les paramètres pour une révision d’accès, cliquez sur **Démarrer**. La révision d’accès s’affiche dans votre liste avec un indicateur de son état.

![Liste des révisions d’accès](./media/create-access-review/access-reviews-list.png)

Par défaut, Azure AD envoie un e-mail aux réviseurs peu de temps après le démarrage de la révision. Si vous ne souhaitez pas qu’Azure AD envoie cet e-mail, veillez à informer les réviseurs qu’une révision d’accès leur a été assignée. Vous pouvez leur montrer les instructions pour savoir comment [réviser l’accès à des groupes ou des applications](perform-access-review.md). Si la révision s’adresse aux invités de revoir leur propre accès, donnez-leur des instructions pour savoir comment [vous-même réviser l’accès à des groupes ou des applications](review-your-access.md).

Si certains réviseurs sont invités, ces derniers sont uniquement notifiés par e-mail s’ils ont déjà accepté leur invitation.

## <a name="manage-the-access-review"></a>Gestion de la révision d’accès

Vous pouvez suivre la progression à mesure que les réviseurs effectuent des révisions sur le **vue d’ensemble** page de la révision d’accès. Aucun droit d’accès n’est modifié dans le répertoire avant que [la révision ne soit terminée](complete-access-review.md).

![Progression des révisions d’accès](./media/create-access-review/overview-progress.png)

S’il s’agit d’une révision à usage unique, puis une fois la période de révision d’accès ou l’administrateur interrompt la révision d’accès, suivez les étapes de [effectuer une révision d’accès des groupes ou des applications](complete-access-review.md) pour voir et appliquer les résultats.  

Pour gérer une série d’accès révisions, accédez à la révision d’accès, et vous trouver des occurrences à venir dans les révisions planifiée et modifier la date de fin ou ajouter/supprimer des réviseurs en conséquence.

Selon vos sélections dans **paramètres de saisie semi-automatique**, appliquer automatiquement les va être exécutée après la date de fin de la révision ou lorsque vous arrêtez manuellement la révision. L’état de la révision ne pourra **terminé** par le biais des états intermédiaires comme **application** et enfin à l’état **appliqué**. Les utilisateurs dont l’accès est refusé doivent normalement perdre leur appartenance au groupe ou leur affectation d’applications au bout de quelques minutes.

## <a name="create-reviews-via-apis"></a>Créer des révisions via des API

Vous pouvez également créer des révisions d’accès avec des API. Ce que vous faites pour gérer les révisions d’accès des groupes et des utilisateurs d’applications dans le portail Azure peut également être effectué avec les API Microsoft Graph. Pour plus d’informations, consultez le [référence de l’API des révisions d’accès d’Azure AD](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Pour obtenir un exemple de code, consultez [passe en revue de l’exemple de récupération de l’accès Azure AD via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Étapes suivantes

- [Réviser l’accès à des groupes ou des applications](perform-access-review.md)
- [Vous-même réviser l’accès à des groupes ou des applications](review-your-access.md)
- [Effectuer une révision d’accès des groupes ou des applications](complete-access-review.md)
