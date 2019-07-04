---
title: Guide de démarrage rapide - Créer un profil Traffic Manager pour assurer une haute disponibilité à vos applications à l’aide du portail Azure
description: Cet article de démarrage rapide décrit comment créer un profil Traffic Manager pour créer des applications web hautement disponibles.
services: traffic-manager
author: asudbring
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: allensu
ms.openlocfilehash: d9b1d0624aa94884c269eb33131f8b61671e99ee
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050999"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Démarrage rapide : Créer un profil Traffic Manager à l’aide du portail Azure

Ce démarrage rapide explique comment créer un profil Traffic Manager qui assure une haute disponibilité pour votre application web.

Dans ce démarrage rapide, il est question de deux instances d’une application web. Chacune d’elles s’exécute dans une région Azure distincte. Vous allez créer un profil Traffic Manager en fonction de la [priorité du point de terminaison](traffic-manager-routing-methods.md#priority). Le profil dirige le trafic utilisateur vers le site principal exécutant l’application web. Traffic Manager supervise en permanence l’application web. Si le site principal est indisponible, il assure un basculement automatique vers le site de secours.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prérequis

Pour ce guide de démarrage rapide, vous aurez besoin de deux instances d’une application web déployée dans deux régions Azure différentes (*USA Est* et *Europe Ouest*). Chacune servira de point de terminaison principal et de point de terminaison de basculement à Traffic Manager.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource** > **Web** > **Application web**.
2. Dans **Application web**, entrez ou sélectionnez ces paramètres :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de l’application | Attribuez un nom unique à votre application web.  |
    | Abonnement | Sélectionnez l’abonnement auquel vous souhaitez appliquer l’application web. |
    | Groupe de ressources | Sélectionnez **Créer** et entrez *myResourceGroupTM1*. |
    | OS | Sélectionnez **Windows** comme système d’exploitation. |
    | Publish | Sélectionnez **Code** comme format de publication. |

3. Sélectionnez **Plan App Service/Emplacement**.
4. Dans **Plan App Service**, sélectionnez **Créer**.
5. Dans **Nouveau plan App Service**, entrez ou sélectionnez ces paramètres :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Plan App Service | Entrez *myAppServicePlanEastUS*. |
    | Location | USA Est |
    | Niveau tarifaire | S1 Standard |

6. Sélectionnez **OK**.

7. Dans **Application web**, sélectionnez **Créer**. Quand l’application web est déployé correctement, elle crée un site web par défaut.

8. Pour créer un deuxième site web dans une autre région Azure, répétez les étapes 1 à 7 avec ces paramètres :

    | Paramètre | Valeur |
    | --------| ----- |
    | Nom | Attribuez un nom unique à votre application web. |
    | Abonnement | Sélectionnez l’abonnement auquel vous souhaitez appliquer l’application web. |
    | Groupe de ressources | Sélectionnez **Créer**, puis entrez *myResourceGroupTM2*. |
    | OS | Sélectionnez **Windows** comme système d’exploitation. |
    | Publish | Sélectionnez **Code** comme format de publication. |
    | Plan/lieu App Service | Entrez *myAppServicePlanWestEurope*. |
    | Location | Europe Ouest |
    | Niveau tarifaire | S1 Standard |

## <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager

Créez un profil Traffic Manager qui dirige le trafic utilisateur en fonction de la priorité du point de terminaison.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Profil Traffic Manager**.
2. Dans **Créer un profil Traffic Manager**, entrez ou sélectionnez ces paramètres :

    | Paramètre | Valeur |
    | --------| ----- |
    | Nom | Attribuez un nom unique à votre profil Traffic Manager.|
    | Méthode de routage | Sélectionnez **Priorité**.|
    | Abonnement | Sélectionnez l’abonnement auquel vous souhaitez appliquer le profil Traffic Manager. |
    | Groupe de ressources | Sélectionnez *myResourceGroupTM1*.|
    | Location |Ce paramètre fait référence à l’emplacement du groupe de ressources. Il n’a aucun effet sur le profil Traffic Manager qui sera déployé globalement.|

3. Sélectionnez **Créer**.

## <a name="add-traffic-manager-endpoints"></a>Ajouter des points de terminaison Traffic Manager

Ajoutez le site web dans la région *USA Est* en tant que point de terminaison principal pour acheminer tout le trafic utilisateur. Ajoutez le site web dans la région *Europe Ouest* comme point de terminaison de basculement. Quand le point de terminaison principal n’est pas disponible, le trafic est automatiquement routé vers le point de terminaison de basculement.

1. Dans la barre de recherche du portail, entrez le nom du profil Traffic Manager que vous avez créé dans la section précédente.
2. Sélectionnez le profil dans les résultats de recherche.
3. Dans **Profil Traffic Manager**, dans la section **Paramètres**, sélectionnez **Points de terminaison**, puis sélectionnez **Ajouter**.
4. Entrez ou sélectionnez ces paramètres :

    | Paramètre | Valeur |
    | ------- | ------|
    | Type | Sélectionnez **Point de terminaison Azure**. |
    | Nom | Entrez *myPrimaryEndpoint*. |
    | Type de ressource cible | Sélectionner **App Service**. |
    | Ressource cible | Sélectionnez **Choisir un service d’application** > **USA Est**. |
    | Priorité | Sélectionnez **1**. Tout le trafic se dirige vers ce point de terminaison quand il est sain. |

    ![Capture d’écran montrant à quel endroit vous ajoutez un point de terminaison à votre profil Traffic Manager.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Sélectionnez **OK**.
6. Pour créer un point de terminaison de basculement pour votre deuxième région Azure, répétez les étapes 3 et 4 avec ces paramètres :

    | Paramètre | Valeur |
    | ------- | ------|
    | Type | Sélectionnez **Point de terminaison Azure**. |
    | Nom | Entrez *myFailoverEndpoint*. |
    | Type de ressource cible | Sélectionner **App Service**. |
    | Ressource cible | Sélectionnez **Choisir un service d’application** > **Europe Ouest**. |
    | Priorité | Sélectionnez **2**. Tout le trafic se dirige vers ce point de terminaison de basculement si le point de terminaison principal n’est pas sain. |

7. Sélectionnez **OK**.

Une fois que vous avez ajouté les deux points de terminaison, il figurent dans **Profil Traffic Manager**. Notez que leur état de supervision est maintenant **En ligne**.

## <a name="test-traffic-manager-profile"></a>Tester le profil Traffic Manager

Dans cette section, vous allez vérifier le nom de domaine de votre profil Traffic Manager. Vous allez aussi configurer le point de terminaison principal pour le rendre indisponible. Enfin, vous allez pouvoir constater que l’application est toujours disponible. Cela est dû au fait que Traffic Manager envoie le trafic au point de terminaison de basculement.

### <a name="check-the-dns-name"></a>Vérifier le nom DNS

1. Dans la barre de recherche du portail, recherchez le nom du **profil Traffic Manager** que vous avez créé dans la section précédente.
2. Sélectionnez le profil Traffic Manager. La **Vue d’ensemble** s’affiche.
3. Le **profil Traffic Manager** affiche le nom DNS de votre profil Traffic Manager nouvellement créé.
  
   ![Capture d’écran de l’emplacement du nom DNS de votre profil Traffic Manager](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Afficher Traffic Manager en action

1. Dans un navigateur web, entrez le nom DNS de votre profil Traffic Manager pour afficher le site web par défaut de votre application web.

    > [!NOTE]
    > Dans ce scénario de démarrage rapide, toutes les demandes sont routées vers le point de terminaison principal. Il est défini sur **Priorité 1**.

    ![Capture d’écran de la page web permettant de vérifier la disponibilité du profil Traffic Manager](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. Pour voir le basculement de Traffic Manager en action, désactivez votre site principal :
    1. Dans la page Profil Traffic Manager, à partir de la **Vue d’ensemble**, sélectionnez **myPrimaryEndpoint**.
    2. Dans *MyPrimaryEndpoint*, sélectionnez **Désactivé** > **Enregistrer**.
    3. Fermez **myPrimaryEndpoint**. Notez que l’état est maintenant **Désactivé**.
3. Copiez le nom DNS de votre profil Traffic Manager de l’étape précédente pour afficher le site web dans une nouvelle session de navigateur web.
4. Vérifiez que l’application web est toujours disponible.

Le point de terminaison principal n’étant pas disponible, vous avez été routé vers le point de terminaison de basculement.

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous avez terminé, supprimez les groupes de ressources, les applications web et toutes les ressources associées. Pour ce faire, sélectionnez individuellement chaque élément dans le tableau de bord, puis sélectionnez **Supprimer** en haut de chaque page.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un profil Traffic Manager. Celui-ci vous permet de diriger le trafic utilisateur pour les applications web à haute disponibilité. Pour plus d’informations sur le routage du trafic, passez aux tutoriels Traffic Manager.

> [!div class="nextstepaction"]
> [Didacticiels Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
