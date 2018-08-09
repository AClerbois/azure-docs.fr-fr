---
title: Démarrage rapide – Bloquer l’accès lorsqu’un risque de session est détecté avec l’accès conditionnel Azure Active Directory | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous apprendrez à configurer une stratégie d’accès conditionnel Azure Active Directory (Azure AD) afin de bloquer les connexions en fonction des risques de session.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2bd52486a78ca103e0070d94ea423c069f845587
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627506"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Démarrage rapide : Bloquer l’accès lorsqu’un risque de session est détecté avec l’accès conditionnel Azure Active Directory  

Pour protéger votre environnement, il se peut que vous souhaitiez empêcher les utilisateurs suspects de se connecter à l’activité. [Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analyse chaque connexion et calcule la probabilité qu’une tentative de connexion n’émane pas du propriétaire légitime d’un compte d’utilisateur. La probabilité (faible, moyenne ou élevée) est indiquée sous forme de valeurs calculées appelées [niveaux de risque de connexion](conditions.md#sign-in-risk). En définissant la condition du risque de connexion, vous pouvez configurer une stratégie d’accès conditionnel afin de répondre aux niveaux de risque de connexion spécifiques. 

Ce démarrage rapide décrit comment configurer une [stratégie d’accès conditionnel](../active-directory-conditional-access-azure-portal.md) qui bloque une connexion lorsqu’un niveau de risque de connexion configuré a été détecté. 

![Créer une stratégie](./media/app-sign-in-risk/1000.png)


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.



## <a name="prerequisites"></a>Prérequis 

Pour suivre le scénario décrit dans ce didacticiel, vous avez besoin de ce qui suit :

- **Accès à une édition Azure AD Premium P2** : bien que l’accès conditionnel soit une fonctionnalité d’Azure AD Premium P1, vous avez besoin d’une édition P2, car le scénario de ce démarrage rapide implique Identity Protection. 

- **Identity Protection** : Identity Protection doit être activé pour suivre le scénario de ce démarrage rapide. Pour en savoir plus sur l’activation d’Identity Protection dans Azure AD, consultez la page [Activer Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Navigateur Tor** : le [navigateur Tor](https://www.torproject.org/projects/torbrowser.html.en) est conçu pour vous aider à préserver votre confidentialité en ligne. Identity Protection détecte les connexions à partir du navigateur Tor en tant que **connexions depuis des adresses IP anonymes**, offrant un niveau de risque moyen. Pour plus d’informations, consultez [Événements à risque dans Azure Active Directory](../reports-monitoring/concept-risk-events.md).  

- **Un compte test nommé Alain Charon** : si vous ignorez comment créer un compte test, consultez la page [Ajouter des utilisateurs basés sur le cloud](../fundamentals/add-users-azure-active-directory.md#add-cloud-based-users).


## <a name="test-your-sign-in"></a>Tester la connexion 

L’objectif de cette étape consiste à vérifier que votre compte test peut accéder à votre locataire à l’aide du navigateur Tor.

**Pour tester la connexion :**

1. Connectez-vous à votre [portail Azure](https://portal.azure.com) en tant que **Alain Charon**.

2. Déconnectez-vous. 


## <a name="create-your-conditional-access-policy"></a>Créer votre stratégie d’accès conditionnel 

Le scénario de ce démarrage rapide utilise une connexion à partir du navigateur Tor pour générer la détection d’un événement à risque (**connexion depuis une adresse IP anonyme**). Le niveau de risque de cet événement est moyen. Pour répondre à cet événement à risque, définissez le niveau de risque de connexion sur Moyen. Dans un environnement de production, vous devez définir la condition de risque de connexion sur Élevé ou sur Moyen à élevé.     

Cette section montre comment créer la stratégie d’accès conditionnel requise. Dans votre stratégie, définissez :

|Paramètre |Valeur|
|---     | --- |
| Utilisateurs et groupes | Alain Charon  |
| Applications cloud | Toutes les applications cloud |
| Risque à la connexion | Moyenne |
| Grant (Autoriser) | Bloquer l’accès |
 

![Créer une stratégie](./media/app-sign-in-risk/130.png)

 


**Pour configurer votre stratégie d’accès conditionnel, procédez comme suit :**

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.

2. Dans la barre de navigation gauche du portail Azure, cliquez sur **Azure Active Directory**. 

    ![Azure Active Directory](./media/app-sign-in-risk/02.png)

3. Dans la page **Azure Active Directory**, dans la section **Gérer**, cliquez sur **Accès conditionnel**.

    ![Accès conditionnel](./media/app-sign-in-risk/03.png)
 
4. Dans la page **Accès conditionnel**, dans la barre d’outils en haut, cliquez sur **Ajouter**.

    ![NOM](./media/app-sign-in-risk/108.png)

5. Sur la page **Nouveau**, dans la zone de texte **Nom**, saisissez **Block access for medium risk level**.

    ![NOM](./media/app-sign-in-risk/104.png)

6. Dans la section **Affectation**, cliquez sur **Utilisateurs et groupes**.

    ![Utilisateurs et groupes](./media/app-sign-in-risk/06.png)

7. Sur la page **Utilisateurs et groupes** :

    ![Accès conditionnel](./media/app-sign-in-risk/107.png)

    a. Cliquez sur **Sélectionner des utilisateurs et des groupes**, puis choisissez **des utilisateurs et des groupes**.

    b. Cliquez sur **Sélectionner**.

    c. Dans la page **Sélectionner**, sélectionnez **Alain Charon**, puis cliquez sur **Sélectionner**.

    d. Dans la page **Utilisateurs et groupes**, cliquez sur **Terminé**.

8. Cliquez sur **Applications cloud**.

    ![Applications cloud](./media/app-sign-in-risk/08.png)

9. Sur la page **Applications cloud** :

    ![Accès conditionnel](./media/app-sign-in-risk/109.png)

    a. Cliquez sur **Toutes les applications cloud**.

    b. Cliquez sur **Done**.

10. Cliquez sur **Conditions**. 

    ![Contrôles d’accès](./media/app-sign-in-risk/19.png)

11. Dans la page **Conditions** :

    ![Niveau de risque d’une connexion](./media/app-sign-in-risk/21.png)

    a. Cliquez sur **Risque de connexion**.
 
    b. Sous **Configurer**, cliquez sur **Oui**.

    c. Pour le niveau de risque de connexion, sélectionnez **Moyen**.

    d. Cliquez sur **Sélectionner**.

    e. Dans la page **Conditions**, cliquez sur **Terminé**.



10. Dans la section **Contrôles d’accès**, cliquez sur **Accorder**.

    ![Contrôles d’accès](./media/app-sign-in-risk/10.png)

11. Sur la page des **octrois** :

    ![Accès conditionnel](./media/app-sign-in-risk/105.png)

    a. Sélectionnez **Bloquer l’accès**.

    b. Cliquez sur **Sélectionner**.

12. Dans la section **Activer la stratégie**, cliquez sur **Activée**.

    ![Activer la stratégie](./media/app-sign-in-risk/18.png)

13. Cliquez sur **Créer**.


## <a name="evaluate-a-simulated-sign-in"></a>Évaluer une connexion simulée

À présent que vous avez configuré votre stratégie d’accès conditionnel, vous souhaitez probablement savoir s’il fonctionne comme prévu. Dans un premier temps, utilisez **l’outil de stratégie d’accès conditionnel What If** pour simuler une connexion de votre utilisateur de test. La simulation évalue l’impact cette connexion sur vos stratégies et génère un rapport de simulation.  

Lorsque vous exécutez **l’outil de stratégie d’accès conditionnel What If** pour ce scénario, le paramètre **Block access for medium risk level** doit apparaître sous **Stratégies qui vont s’appliquer**. 

![Utilisateur](./media/app-sign-in-risk/117.png)


**Pour évaluer votre stratégie d’accès conditionnel :**

1. Dans la page [Accès conditionnel - Stratégies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies), dans le menu en haut, cliquez sur **What If**.  
 
    ![What If](./media/app-sign-in-risk/14.png)

2. Cliquez sur **Utilisateur**, sélectionnez **Alain Charon** sur la page **Utilisateurs**, puis cliquez sur **Sélectionner**.

    ![Utilisateur](./media/app-sign-in-risk/116.png)

3. Dans **Risque de connexion**, choisissez **Moyen**.

    ![Utilisateur](./media/app-sign-in-risk/119.png)


3. Cliquez sur **What If**.


## <a name="test-your-conditional-access-policy"></a>Tester votre stratégie d’accès conditionnel

Dans la section précédente, vous avez appris à évaluer une connexion simulée. En plus d’une simulation, vous devez tester votre stratégie d’accès conditionnel pour vous assurer qu’elle fonctionne comme prévu. 

Pour tester votre stratégie, essayez de vous connecter à votre [portail Azure](https://portal.azure.com) en tant que **Alain Charon** à l’aide du navigateur Tor. Votre tentative de connexion doit être bloquée par votre stratégie d’accès conditionnel.

![Authentification multifacteur](./media/app-sign-in-risk/118.png)


## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, supprimez l’utilisateur test, le navigateur Tor et la stratégie d’accès conditionnel :

- Si vous ignorez comment supprimer un utilisateur Azure AD, voir [Supprimer des utilisateurs d’Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Pour supprimer votre stratégie, sélectionnez-la, puis cliquez sur **Supprimer** dans la barre d’outils Accès rapide.

    ![Authentification multifacteur](./media/app-sign-in-risk/33.png)

- Pour savoir comment supprimer le navigateur Tor, consultez la page de [désinstallation](https://tb-manual.torproject.org/en-US/uninstalling.html).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exiger l’acceptation des conditions d’utilisation](require-tou.md)
> [Exiger une authentification multifacteur pour des applications spécifiques](app-based-mfa.md)

