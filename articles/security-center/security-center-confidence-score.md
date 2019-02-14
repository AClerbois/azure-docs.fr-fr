---
title: Score de confiance dans Azure Security Center | Microsoft Docs
description: " Découvrez comment utiliser le score de confiance d’Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: 67442a5861514eadfebfc877d91e735e05f6f63d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117543"
---
# <a name="alert-confidence-score"></a>Score de confiance de l’alerte 

Azure Security Center vous donne une visibilité sur les ressources que vous exécutez dans Azure et vous avertit quand il détecte des problèmes potentiels. Le volume d’alertes peut être difficile à traiter pour l’équipe en charge des opérations de sécurité. Il devient alors nécessaire de hiérarchiser les alertes devant faire l’objet d’une enquête. L’examen des alertes peut être complexe et nécessiter beaucoup de temps. C’est pourquoi certaines alertes sont ignorées.

Le score de confiance dans Security Center peut aider votre équipe à mieux trier et hiérarchiser les alertes. Security Center applique automatiquement les meilleures pratiques du secteur, les algorithmes intelligents et les processus utilisés par les analystes pour déterminer si une menace est légitime. Il fournit des informations pertinentes sous la forme d’un score de confiance.

## <a name="how-the-confidence-score-is-triggered"></a>Déclenchement du score de confiance

Des alertes sont générées lorsque des processus suspects sont détectés sur vos machines virtuelles. Security Center examine et analyse ces alertes sur les machines virtuelles Windows s’exécutant dans Azure. Il effectue des vérifications et corrélations automatiques à l’aide d’algorithmes avancés sur plusieurs entités et sources de données de l’entreprise, et toutes vos ressources Azure. Il renvoie ensuite un score de confiance mesurant son degré de certitude quant à la gravité de l’alerte qui doit, si nécessaire, faire l’objet d’un examen.

## <a name="understanding-the-confidence-score"></a>Comprendre le score de confiance

Le score de confiance est compris entre 1 et 100. Il représente le degré de certitude de Security Center quant au fait qu’une alerte doit faire l’objet d’une enquête. Plus le score est élevé, plus Security Center estime que l’alerte correspond à une véritable activité malveillante. Le score de confiance inclut une liste des principales raisons pour lesquelles l’alerte a reçu ce score de confiance. Le score de confiance aide les analystes à hiérarchiser leur intervention afin de traiter en priorité les attaques les plus urgentes. Au final, cela contribue à réduire le temps nécessaire pour réagir aux attaques et aux failles de sécurité.

Pour afficher le score de confiance :
- Dans le portail Security Center, ouvrez le panneau Alertes de sécurité.
-  Les alertes et les incidents sont organisés du score le plus élevé au plus bas. Cela signifie que plus Security Center estime qu’une alerte représente une menace, plus elle apparaîtra dans le haut de la page. 


 ![Score de confiance][1]

Pour afficher les données sur lesquelles se base Security Center pour lancer une alerte :
- Dans le panneau Alerte de sécurité, sous **Confiance**, consultez les observations qui ont servi à établir le score de confiance et obtenez des informations relatives à l’alerte. Vous en saurez alors davantage sur la nature des activités qui ont provoqué l’alerte.

 ![Score de confiance suspect][2]

Utilisez le score de confiance de Security Center pour hiérarchiser le tri des alertes dans votre environnement. Pour vous faire gagner du temps, le score de confiance lance automatiquement des enquêtes sur les alertes, applique les meilleures pratiques du secteur et des algorithmes intelligents, et fait office d’analyste virtuel pour déterminer les menaces réelles et les points nécessitant une attention particulière.


## <a name="next-steps"></a>Étapes suivantes
Cet article a expliqué comment utiliser le score de confiance pour hiérarchiser les enquêtes sur les alertes. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [FAQ de Azure Security Center](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
