---
title: Degré de sécurisation dans Azure Security Center | Microsoft Docs
description: " Classez vos recommandations de sécurité par ordre de priorité à l’aide du degré de sécurisation dans Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/5/2018
ms.author: rkarlin
ms.openlocfilehash: 3a377441758fcd7dd91deefb5cae91579e881498
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007048"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Amélioration du degré de sécurisation dans Azure Security Center


Avec autant de services offrant des avantages en matière de sécurité, il est souvent difficile de connaître les étapes à suivre en premier lieu pour sécuriser et renforcer votre charge de travail. Le degré de sécurisation Azure examine vos recommandations de sécurité et les classe par priorité, afin que vous sachiez quelles recommandations appliquer en priorité. Cela vous permet de rechercher les vulnérabilités de sécurité les plus graves afin de définir les priorités d’investigation. Le degré de sécurisation est un outil qui vous aide à évaluer la sécurité de la charge de travail.

![Tableau de bord du degré de sécurisation](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>Calcul du degré de sécurisation

Azure Security Center imite le travail des analystes en sécurité, en examinant vos recommandations en matière de sécurité et en appliquant des algorithmes avancés pour déterminer le degré d’importance de chaque recommandation.
Azure Security Center vérifie constamment vos recommandations actives et calcule votre degré de sécurisation en fonction de celles-ci. Le score d’une recommandation est basé sur sa gravité et sur les meilleures pratiques en matière de sécurité qui affectent le plus la sécurité de votre charge de travail.

Security Center vous fournit également un **degré de sécurisation global**. 

Le **degré de sécurisation global** est l’accumulation de toutes vos recommandations. Vous pouvez afficher votre degré de sécurisation global dans vos abonnements ou groupes d’administration en fonction de votre sélection. Celui-ci varie en fonction de l’abonnement sélectionné et des recommandations actives correspondantes.

 
Pour vérifier les recommandations qui affectent le plus votre degré de sécurisation, vous pouvez afficher les trois principales recommandations ayant l’impact le plus fort dans le tableau de bord de Security Center. Vous pouvez également trier les recommandations contenues dans le panneau de la liste des recommandations à l’aide de la colonne **Impact du degré de sécurisation**.


Pour afficher votre degré de sécurisation global :

1. Dans le tableau de bord Azure, cliquez sur **Security Center**, puis sur **Recommandations**.
2. En haut de la fenêtre, vous pouvez observer le degré de sécurisation qui représente le score par stratégies et par abonnement sélectionné. 
2. Dans le tableau ci-dessous qui répertorie les recommandations, vous pouvez voir que pour chacune d’elles, il existe une colonne **Impact du degré de sécurisation**. Ce nombre indique l’amélioration de votre degré de sécurisation global si vous suivez les recommandations. Par exemple, dans l’écran ci-dessous, si vous **Corrigez les vulnérabilités dans les configurations de sécurité du conteneur**, votre degré de sécurisation sera augmenté de 35 points.

   ![degré de sécurisation](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>Degré de sécurisation individuel

Par ailleurs, les degrés de sécurisation individuels que vous pouvez afficher se trouvent dans le panneau des recommandations individuelles.  

Le **Score de la recommandation** est un calcul basé sur le rapport entre vos ressources saines et vos ressources totales. Si le nombre de ressources saines est égal au nombre total de ressources, vous obtenez le degré de sécurisation maximal de la recommandation, qui est de 50. Pour tenter d’obtenir le degré de sécurisation le plus proche du maximum, corrigez les ressources défectueuses en suivant les recommandations.

**L’Impact de la recommandation** vous permet de connaître l’amélioration de votre degré de sécurisation si vous appliquez les étapes de la recommandation. Par exemple, si votre degré de sécurisation est égal à 42 et si **l’impact de la recommandation** est de +3, votre degré de sécurisation passera à 45 si vous effectuez les étapes présentées dans la recommandation.

La recommandation présente les menaces auxquelles votre charge de travail est exposée si les mesures correctives ne sont pas prises.

![score de la recommandation individuelle](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré comment renforcer votre sécurité à l’aide du **degré de sécurisation** d’Azure Security Center. Pour plus d’informations sur Security Center, consultez :

* [FAQ de Azure Security Center](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.


