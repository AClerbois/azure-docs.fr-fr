---
title: Vue d’ensemble de Cloudyn dans Azure
description: Cloudyn est une solution de gestion des coûts multicloud qui vous aide à utiliser Azure et autres ressources cloud.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 3acc13ca535808f14cb01d50e38f6bd4d12902fc
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684437"
---
# <a name="what-is-the-cloudyn-service"></a>Qu’est-ce que le service de Cloudyn ?

Cloudyn, affilié à Microsoft, vous permet de suivre l’utilisation du cloud et les dépenses liées à vos ressources Azure et celles d’autres fournisseurs de services cloud, notamment AWS et Google. Les rapports du tableau de bord vous aident à comprendre la répartition des coûts, de même que la rétrofacturation et la facturation interne. Cloudyn vous permet d’optimiser vos dépenses cloud en identifiant les ressources sous-utilisées que vous pouvez ainsi gérer et ajuster.

Pour regarder une vidéo d’introduction, consultez [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo/).
 
Azure Cost Management offre des fonctionnalités similaires à Cloudyn. Azure Cost Management est une solution native de gestion des coûts Azure. Il vous permet d’analyser les coûts, de créer et de gérer des budgets, d’exporter des données, et de consulter des recommandations d’optimisation et d’agir en conséquence pour dépenser moins. Pour plus d’informations, consultez [Azure Cost Management](../cost-management-billing-overview.md).
 
[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Regardez la [vidéo Azure Cost Management et Cloudyn](https://www.youtube.com/watch?v=15DzKPMBRxM) pour savoir quand utiliser Azure Cost Management ou Cloudyn, en fonction des besoins de votre entreprise.
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

## <a name="monitor-usage-and-spending"></a>Surveiller l’utilisation et les dépenses

Il est extrêmement important de surveiller l’utilisation et les dépenses en infrastructures cloud, car les organisations sont facturées sur les ressources qu’elles consomment dans le temps. Quand l’utilisation dépasse les seuils contractuels, les coûts peuvent augmenter de façon rapide et inattendue. Une supervision ad-hoc peut s’avérer difficile pour plusieurs raisons. Tout d’abord, la projection des coûts en fonction de l’utilisation moyenne suppose que votre consommation reste constante sur une période de facturation donnée. En second lieu, quand les coûts sont proches de votre budget ou le dépassent, il est important que vous en soyez averti à un stade précoce pour ajuster vos dépenses. Enfin, les fournisseurs de services cloud ne proposent pas nécessairement de rapports de projection des coûts par rapport aux seuils ou des rapports de comparaison de périodes.

Les rapports vous aident à surveiller les dépenses et ainsi d’analyser et suivre l’utilisation du cloud, les coûts et les tendances. Grâce aux rapports d’utilisation dans le temps, vous pouvez détecter des anomalies par rapport aux tendances normales. La mauvaise utilisation des ressources dans votre déploiement cloud est visible dans les rapports d’optimisation. Vous pouvez aussi identifier une mauvaise utilisation des ressources dans les rapports d’analyse des coûts.

## <a name="manage-costs"></a>Gérer les coûts

Les données historiques peuvent vous aider à gérer les coûts quand vous analysez l’utilisation et les coûts dans le temps pour identifier les tendances. Celles-ci permettent ensuite de prévoir les dépenses futures. Cloudyn propose aussi des rapports d’estimation des coûts très utiles.

La répartition des coûts permet de gérer les coûts en analysant vos coûts en fonction de votre stratégie de balisage. Vous pouvez utiliser des balises sur vos comptes, ressources et entités personnalisés pour affiner la répartition des coûts. Le Gestionnaire de catégories organise vos balises pour optimiser la gouvernance. Enfin, la répartition des coûts dans la rétrofacturation et la facturation interne met en évidence l’utilisation des ressources et les coûts associés dans le but d’influencer les comportements de consommation ou de facturer les clients du locataire.

Le contrôle d’accès facilite la gestion des coûts en autorisant les utilisateurs et les équipes à accéder uniquement aux données de gestion des coûts dont ils ont besoin. Vous vous servez de la structure des entités, de la gestion des utilisateurs et des rapports planifiés avec les listes de destinataires pour attribuer l’accès.

La gestion des coûts est aussi facilitée par les alertes, qui vous avertissent automatiquement en cas de dépenses inhabituelles ou excessives. Les alertes peuvent aussi être envoyées automatiquement à d’autres parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Plusieurs rapports prennent en charge les alertes basées sur les seuils budgétaires et de coûts. Toutefois, les alertes ne sont actuellement pas prises en charge pour les comptes ou abonnements de partenaires CSP.

## <a name="improve-efficiency"></a>Améliorer l’efficacité

Avec Cloudyn, vous pouvez déterminer quelle est l’utilisation optimale des machines virtuelles, identifier ou supprimer les machines virtuelles inactives, ainsi que les disques non attachées. Les informations fournies par les rapports d’optimisation du dimensionnement et les rapports de mauvaise utilisation des ressources permettent d’élaborer un plan visant à réduire la taille ou supprimer les machines virtuelles inactives. Toutefois, les rapports d’optimisation ne sont actuellement pas pris en charge pour les comptes ou abonnements de partenaires CSP.

Si vous avez approvisionné des instances réservées AWS, vous pouvez optimiser leur utilisation grâce aux rapports d’optimisation. Vous y trouverez des recommandations d’achat et ils vous permettront de modifier les réservations inutilisées et de planifier l’approvisionnement.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez un peu plus sur Cloudyn, passez à l’étape suivante, qui consiste à inscrire votre environnement cloud et à commencer à explorer vos données.

- [S’inscrire au programme pour les partenaires CSP et afficher les données de coût](quick-register-csp.md)
