---
title: Configurer la mise à l’échelle automatique d’une instance du service Gestion des API Azure | Microsoft Docs
description: Cette rubrique explique comment configurer le comportement de mise à l’échelle automatique pour une instance du service Gestion des API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
origin.date: 06/20/2018
ms.date: 12/31/2018
ms.author: v-yiso
ms.openlocfilehash: a01e50debf11daf2f1163a56726f5574f7e3e379
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123465"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Mettre à l’échelle automatiquement une instance du service Gestion des API Azure  

Une instance du service Gestion des API Azure peut se mettre à l’échelle automatiquement en fonction d’un ensemble de règles. Ce comportement peut être activé et configuré via Azure Monitor, et il est pris en charge seulement dans les niveaux **Standard** et **Premium** du service Gestion des API Azure.

Cet article décrit le processus de configuration de la mise à l’échelle automatique et suggère une configuration optimale des règles de cette mise à l’échelle.

## <a name="prerequisites"></a>Conditions préalables

Pour suivre les étapes décrites dans cet article, vous devez :

+ Avoir un abonnement Azure actif.
+ Disposer d’une instance du service Gestion des API Azure. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).
+ Comprendre le concept de [Capacité d’une instance du service Gestion des API Azure](api-management-capacity.md).
+ Comprendre le [processus de mise à l’échelle manuelle d’une instance du service Gestion des API Azure](upgrade-and-scale.md), notamment les conséquences en termes de coûts.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Limitations de la mise à l’échelle automatique du service Gestion des API Azure

Certaines limitations et conséquences des décisions de mise à l’échelle doivent être prises en compte avant de configurer le comportement de mise à l’échelle automatique.

+ La mise à l’échelle automatique peut être activée seulement pour les niveaux **Standard** et **Premium** du service Gestion des API Azure.
+ Les niveaux tarifaires spécifient également le nombre maximal d’unités pour une instance du service.
+ Le processus de mise à l’échelle prend au moins 20 minutes.
+ Si le service est verrouillé par une autre opération, la demande de mise à l’échelle échoue et une nouvelle tentative est effectuée automatiquement.
+ Dans le cas d’un service avec des déploiements sur plusieurs régions, seules les unités de **l’emplacement principal** peuvent être mises à l’échelle. Les unités dans les autres emplacements ne peuvent pas être mises à l’échelle.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Activer et configurer la mise à l’échelle automatique pour le service Gestion des API Azure

Suivez les étapes ci-dessous pour configurer la mise à l’échelle automatique pour un service Gestion des API Azure :

1. Dans le portail Azure, accédez à l’instance de **Monitor**.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Sélectionnez **Mise à l’échelle automatique** dans le menu de gauche.

    ![Mettre à l’échelle automatiquement une ressource dans Azure Monitor](media/api-management-howto-autoscale/02.png)

3. Recherchez votre service Gestion des API Azure en utilisant les filtres dans les menus déroulants.
4. Sélectionnez l’instance souhaitée du service Gestion des API Azure.
5. Dans la section qui vient de s’ouvrir, cliquez sur le bouton **Activer la mise à l’échelle automatique** .

    ![Activer la mise à l’échelle automatique dans Azure Monitor](media/api-management-howto-autoscale/03.png)

6. Dans la section **Règles**, cliquez sur **+ Ajouter une règle**.

    ![Ajouter une règle de mise à l’échelle automatique dans Azure Monitor](media/api-management-howto-autoscale/04.png)

7. Définissez une nouvelle règle de montée en puissance.

   Par exemple, une règle de montée en puissance peut déclencher l’ajout d’une unité de Gestion des API Azure quand la métrique de capacité moyenne au cours des 30 dernières minutes dépasse 80 %. Le tableau ci-dessous indique la configuration pour une telle règle.

    | Paramètre             | Valeur             | Notes                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Source de la mesure         | Ressource actuelle  | Définit la règle sur la base des métriques de la ressource de Gestion des API Azure actuelle.                                                                                                                                                                                                     |
    | *Critères*            |                   |                                                                                                                                                                                                                                                                                 |
    | Agrégation du temps      | Moyenne           |                                                                                                                                                                                                                                                                                 |
    | Nom de métrique           | Capacité          | Une métrique de capacité est une métrique de Gestion des API Azure reflétant l’utilisation des ressources d’une instance du service Gestion des API Azure.                                                                                                                                                            |
    | Statistique de fragment de temps  | Moyenne           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Supérieur à      |                                                                                                                                                                                                                                                                                 |
    | Seuil             | 80 %               | Seuil pour la métrique de capacité moyenne.                                                                                                                                                                                                                                 |
    | Durée (en minutes) | 30                | L’intervalle de temps pour calculer la moyenne de la métrique de capacité est spécifique aux modèles d’utilisation. Plus la période de temps est longue, plus la réaction sera fluide : les pics intermittents auront moins d’impact sur la décision de montée en puissance. Ceci va cependant retarder le déclenchement de la montée en puissance. |
    | *Action*              |                   |                                                                                                                                                                                                                                                                                 |
    | Opération             | Augmenter le nombre de |                                                                                                                                                                                                                                                                                 |
    | Nombre d’instances        | 1                 | Monte en charge l’instance de Gestion des API Azure de 1 unité.                                                                                                                                                                                                                          |
    | Refroidissement (minutes)   | 60                | Au moins 20 minutes sont nécessaires pour la montée en puissance du service Gestion des API Azure. Dans la plupart des cas, la période de retour au calme de 60 minutes empêche le déclenchement de nombreuses montées en puissance.                                                                                                  |

8. Cliquez sur **Ajouter** pour enregistrer la règle.

    ![Règle de montée en puissance dans Azure Monitor](media/api-management-howto-autoscale/05.png)

9. Cliquez à nouveau sur **+ Ajouter une règle**.

    Cette fois, vous devez définir une échelle pour la règle. Elle garantit que des ressources ne sont pas gaspillées quand l’utilisation des API diminue.

10. Définissez une nouvelle règle de diminution de la puissance.

    Par exemple, une règle de diminution de la puissance peut déclencher la suppression d’une unité de Gestion des API Azure quand la métrique de capacité moyenne au cours des 30 dernières minutes a été inférieure à 35 %. Le tableau ci-dessous indique la configuration pour une telle règle.

    | Paramètre             | Valeur             | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Source de la mesure         | Ressource actuelle  | Définit la règle sur la base des métriques de la ressource de Gestion des API Azure actuelle.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Critères*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Agrégation du temps      | Moyenne           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nom de métrique           | Capacité          | Métrique identique à celle utilisée pour la règle de montée en puissance.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Statistique de fragment de temps  | Moyenne           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Inférieur à         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Seuil             | 35 %               | De façon similaire à la règle de montée en puissance, cette valeur dépend fortement des modèles d’utilisation du service Gestion des API Azure. |
    | Durée (en minutes) | 30                | Valeur identique à celle utilisée pour la règle de montée en puissance.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Action*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Opération             | Diminuer le nombre de | Le contraire de ce qui a été utilisé pour la règle de montée en puissance.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Nombre d’instances        | 1                 | Valeur identique à celle utilisée pour la règle de montée en puissance.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Refroidissement (minutes)   | 90                | La diminution de puissance doit se faire de façon plus prudente qu’une augmentation de puissance : la période de retour au calme doit donc être plus longue.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Cliquez sur **Ajouter** pour enregistrer la règle.

    ![Règle de diminution de puissance dans Azure Monitor](media/api-management-howto-autoscale/06.png)

12. Définissez le nombre **maximal** d’unités du service Gestion des API Azure.

    > [!NOTE]
    > La montée en puissance d’une instance du service Gestion des API Azure est limitée quant au nombre d’unités. La limite dépend d’un niveau de service.

    ![Règle de diminution de puissance dans Azure Monitor](media/api-management-howto-autoscale/07.png)

13. Cliquez sur **Enregistrer**. Votre mise à l’échelle automatique a été configurée.

## <a name="next-steps"></a>Étapes suivantes

+ [Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure](api-management-howto-deploy-multi-region.md)
