---
title: Appliquer les mises à jour système dans Azure Security Center | Microsoft Docs
description: Ce document vous explique comment implémenter les recommandations de l’Azure Security Center **Appliquer les mises à jour système** et **Redémarrer après l’application des mises à jour système**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: ebd9939128d1f2b870541e82710792d13b69728e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098999"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Appliquer les mises à jour système dans Azure Security Center
Azure Security Center recherche quotidiennement les mises à jour manquantes du système d’exploitation sur les ordinateurs et machines virtuelles Windows et Linux. Security Center récupère une liste des mises à jour de sécurité et critiques disponibles dans Windows Update ou Windows Server Update Services (WSUS), selon le service configuré sur un ordinateur Windows. Security Center recherche également les dernières mises à jour dans les systèmes Linux. S’il manque une mise à jour système sur votre machine virtuelle ou ordinateur, Security Center vous recommande de l’appliquer.

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
L’application des mises à jour système est présentée sous forme de recommandation dans Security Center. S’il manque une mise à jour système sur votre machine virtuelle ou ordinateur, cette recommandation s’affichera dans **Recommandations** et dans **Calcul**.  Lorsque vous sélectionnez la recommandation, le tableau de bord **Appliquer les mises à jour système** s’ouvre.

Dans cet exemple, nous utilisons **Calcul**.

1. Dans le menu principal de Security Center, sélectionnez **Calcul**.

   ![Sélectionner Calcul][1]

2. Dans **Calcul**, sélectionnez **Mises à jour système manquantes**. Le tableau de bord **Appliquer les mises à jour système** s’ouvre.

   ![Tableau de bord Appliquer les mises à jour système][2]

   La partie supérieure du tableau de bord indique :

    - Le nombre total de mises à jour système manquantes pour les ordinateurs et machines virtuelles Windows et Linux.
    - Le nombre total de mises à jour critiques manquantes sur vos ordinateurs et machines virtuelles.
    - Le nombre total de mises à jour de sécurité manquantes sur l’ensemble de vos ordinateurs et machines virtuelles.

   La partie inférieure du tableau de bord répertorie toutes les mises à jour manquantes sur l’ensemble de vos ordinateurs et machines virtuelles, ainsi que le niveau de gravité de la mise à jour manquante.  Cette liste comprend les éléments suivants :

    - NOM : Nom de la mise à jour manquante.
    - NOMBRE DE MACHINES VIRTUELLES ET D’ORDINATEURS : Nombre total de machines virtuelles et d’ordinateurs sur lesquels cette mise à jour n’a pas été installée.
    - ÉTAT : État actuel de la recommandation :

      - Ouverte : La recommandation n’a pas encore été prise en compte.
      - En cours : La recommandation est actuellement appliquée aux ressources ; aucune action de votre part n’est nécessaire.
      - Résolue : La recommandation a déjà été achevée. (Une fois problème résolu, l’entrée a été grisée).

    - GRAVITÉ : Donne le niveau de gravité de chaque recommandation :

      - Élevée : Existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle, groupe de sécurité réseau). Le problème doit être analysé.
      - Moyenne : Certaines étapes supplémentaires sont nécessaires pour terminer un processus ou éliminer une vulnérabilité.
      - Faible : Existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite aucune attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître.

3. Sélectionnez une mise à jour manquante dans la liste pour afficher les détails.

   ![Mise à jour de sécurité manquante][3]

4. Sélectionnez l’icône **Recherche** dans le ruban supérieur.  Une requête de recherche de journaux Azure Monitor filtrée sur les ordinateurs de la mise à jour manquante s’ouvre.

   ![Recherche de journaux de Azure Monitor][4]

5. Sélectionnez un ordinateur dans la liste pour plus d’informations. Un autre résultat de recherche s’ouvre avec les informations filtrées uniquement pour cet ordinateur.

    ![Recherche de journaux de Azure Monitor][5]

## <a name="reboot-after-system-updates"></a>Redémarrer après l’application des mises à jour système
1. Retournons au panneau **Recommandations** . Une fois que vous avez appliqué les mises à jour système, une nouvelle entrée est générée, appelée **Redémarrer après l’application des mises à jour système**. Cette entrée vous permet de savoir que vous devez redémarrer la machine virtuelle pour terminer le processus d’application des mises à jour système.

   ![Redémarrer après l’application des mises à jour système][6]
2. Sélectionnez **Redémarrer après l’application des mises à jour système**. Cette opération ouvre le panneau **Un redémarrage est en attente pour terminer les mises à jour système** qui affiche une liste de machines virtuelles que vous devez redémarrer pour terminer le processus d’application des mises à jour système.

   ![Redémarrage en attente][7]

Redémarrez la machine virtuelle à partir d’Azure pour terminer le processus.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
