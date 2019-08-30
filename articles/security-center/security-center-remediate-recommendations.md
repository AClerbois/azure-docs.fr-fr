---
title: Appliquer des recommandations dans Azure Security Center | Microsoft Docs
description: Ce document explique comment appliquer les recommandations d’Azure Security Center en vue de protéger vos ressources Azure et de rester en conformité avec les stratégies de sécurité.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2019
ms.author: v-mohabe
ms.openlocfilehash: a32e344ffe33f411bae85763ae3b919040c1109b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575564"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Appliquer les recommandations d’Azure Security Center

Les recommandations fournissent des suggestions visant à mieux sécuriser vos ressources.  Pour appliquer une recommandation, vous devez suivre les étapes de correction qu’elle fournit. 

## Étapes de correction <a name="remediation-steps"></a>

Après avoir lu toutes les recommandations, vous devez décider laquelle appliquer en premier. Pour déterminer quelles recommandations appliquer en premier, il est recommandé de se baser sur l’[impact du degré de sécurisation](security-center-recommendations.md#monitor-recommendations).

1. Dans la liste, cliquez sur la recommandation.
1. Suivez les instructions dans la section **Étapes de correction**. Chaque recommandation comprend un ensemble d’instructions. L’exemple suivant montre les étapes de correction permettant de configurer les applications de manière à autoriser uniquement le trafic via HTTPS.

    ![Détails de la recommandation](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Une fois l’opération terminée, une notification s’affiche pour vous informer si la mise à jour a réussi.

## Correction en un clic (préversion) <a name="one-click"></a>

La correction en un clic vous permet d’appliquer une recommandation sur un lot de ressources par un simple clic. Cette option est disponible uniquement pour des recommandations spécifiques. La correction en un clic simplifie l’implémentation des corrections et vous permet d’améliorer rapidement votre degré de sécurisation ainsi que de renforcer la sécurité dans votre environnement.

Pour implémenter une correction en un clic :

1. Dans la liste des recommandations présentant une étiquette **Correction en 1 clic**, cliquez sur la recommandation souhaitée.  

   ![Sélectionner une correction en un clic](./media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)

2. Sous l’onglet **Ressources défectueuses**, sélectionnez les ressources auxquelles vous souhaitez implémenter la recommandation, puis cliquez sur **Corriger**. 

    > [!NOTE]
    > Certaines des ressources listées peuvent être désactivées, ce qui est le cas si vous n’avez pas les autorisations appropriées pour les modifier.

3. Dans la zone de confirmation, lisez les détails et les implications de la correction. 

   ![Correction en un clic](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Les implications sont listées dans la zone grise de la fenêtre **Corriger les ressources** qui s’ouvre après que vous avez cliqué sur **Corriger**. Elles indiquent les changements apportés si vous implémentez la correction en un clic.

4. Ajoutez les paramètres appropriés, si nécessaire, et approuvez la correction.

    > [!NOTE]
    > Une fois la correction terminée, il peut s’écouler plusieurs minutes avant que les ressources apparaissent sous l’onglet **Ressources saines**. Pour voir les actions de correction, consultez le [journal d’activité](#activity-log).

5. Une fois l’opération terminée, une notification s’affiche pour vous informer si la mise à jour a réussi.

## Journalisation des corrections en un clic dans le journal d’activité <a name="activity-log"></a>

L’opération de correction applique la configuration à la ressource à l’aide d’un déploiement de modèle ou d’un appel à l’API REST PATCH. Ces opérations sont consignées dans le [journal d’activité Azure](../azure-resource-manager/resource-group-audit.md).


## <a name="recommendations-with-one-click-remediation"></a>Recommandations avec correction en un clic

|Recommandation|Implication|
|---|---|
|L’audit sur les serveurs SQL doit être activé|Cette action active l’audit SQL sur ces serveurs et leurs bases de données. <br>**Remarque**: <ul><li>Pour chaque région des serveurs SQL sélectionnés, un compte de stockage utilisé pour l’enregistrement des journaux d’audit est créé et partagé par tous les serveurs situés dans cette région.</li><li>Pour garantir un audit correct, vous ne devez ni supprimer ni renommer le groupe de ressources ou les comptes de stockage.</li></ul>|
|Advanced Data Security doit être activé sur vos instances managées SQL|Cette action active SQL Advanced Data Security (ADS) sur les instances managées SQL sélectionnées. <br>**Remarque**: <ul><li>Pour chaque région et chaque groupe de ressources des instances managées SQL sélectionnées, un compte de stockage utilisé pour l’enregistrement des résultats d’analyse est créé et partagé par toutes les instances exécutées dans cette région.</li><li> ADS est facturé au tarif de 15 $ par instance managée SQL.</li></ul>|
|L’évaluation des vulnérabilités doit être activée sur vos instances managées SQL|Cette action active l’évaluation des vulnérabilités SQL sur les instances managées SQL sélectionnées. <br>**Remarque**:<ul><li>L’évaluation des vulnérabilités SQL fait partie du package SQL Advanced Data Security (ADS). Si ADS n’est pas déjà activé, il est automatiquement activé sur l’instance managée.</li><li>Pour chaque région et chaque groupe de ressources des instances managées SQL sélectionnées, un compte de stockage utilisé pour le stockage des résultats d’analyse est créé et partagé par toutes les instances exécutées dans cette région.</li><li>ADS est facturé au tarif de 15 $ par serveur SQL.</li></ul>||
|Advanced Data Security doit être activé sur vos serveurs SQL|Cette action active Advanced Data Security (ADS) sur ces serveurs sélectionnés et leurs bases de données. <br>**Remarque**:<ul><li>Pour chaque région et chaque groupe de ressources des serveurs SQL sélectionnés, un compte de stockage utilisé pour le stockage des résultats d’analyse est créé et partagé par tous les serveurs situés dans cette région.<</li><li>ADS est facturé au tarif de 15 $ par serveur SQL.</li></ul>||
|L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL|Cette action active l’évaluation des vulnérabilités SQL sur ces serveurs sélectionnés et leurs bases de données. <br>**Remarque**:<ul><li>L’évaluation des vulnérabilités SQL fait partie du package SQL Advanced Data Security (ADS). Si ADS n’est pas déjà activé, il est automatiquement activé sur le serveur SQL.</li><li>Pour chaque région et chaque groupe de ressources des serveurs SQL sélectionnés, un compte de stockage utilisé pour le stockage des résultats d’analyse est créé et partagé par toutes les instances exécutées dans cette région.</li><li>ADS est facturé au tarif de 15 $ par serveur SQL.</li></ul>||
|La technologie Transparent Data Encryption doit être activée sur les bases de données SQL|Cette action active SQL Database Transparent Data Encryption (TDE) sur les bases de données sélectionnées. <br>**Remarque**: Par défaut, les clés TDE managées par le service sont utilisées. 
|La sécurisation du transfert vers des comptes de stockage doit être activée|Cette action met à jour la sécurité de vos comptes de stockage pour autoriser uniquement les requêtes provenant de connexions sécurisées. (HTTPS). <br>**Remarque**:<ul><li>Toutes les requêtes utilisant le protocole HTTP sont rejetées.</li><li>Si vous utilisez le service Azure Files, toute connexion non chiffrée échoue, y compris dans les scénarios utilisant SMB 2.1, SMB 3.0 sans chiffrement et certaines versions du client SMB Linux.  En savoir plus.</li></ul>|
|L'application web ne doit pas être accessible via HTTPS|Cette action redirige tout le trafic HTTP vers HTTPS, sur les ressources sélectionnées. <br>**Remarque**:<ul><li>Un point de terminaison HTTPS sans certificat SSL s’affiche dans le navigateur avec une « Erreur de confidentialité ». Il est donc important que les utilisateurs qui ont un domaine personnalisé s’assurent d’avoir configuré un certificat SSL.</li><li>Assurez-vous que les pare-feux d’applications web et de paquets protégeant l’App Service ont autorisé le transfert de sessions HTTPS.</li></ul>|
|Function App ne doit pas être accessible via HTTPS|Cette action redirige tout le trafic HTTP vers HTTPS, sur les ressources sélectionnées. <br>**Remarque**:<ul><li>Un point de terminaison HTTPS sans certificat SSL s’affiche dans le navigateur avec une « Erreur de confidentialité ». Il est donc important que les utilisateurs qui ont un domaine personnalisé s’assurent d’avoir configuré un certificat SSL.</li><li>Assurez-vous que les pare-feux d’applications web et de paquets protégeant l’App Service ont autorisé le transfert de sessions HTTPS.</li></ul>|
|L'application API doit uniquement être accessible via HTTPS|Cette action redirige tout le trafic HTTP vers HTTPS, sur les ressources sélectionnées. <br>**Remarque**:<ul><li>Un point de terminaison HTTPS sans certificat SSL s’affiche dans le navigateur avec une « Erreur de confidentialité ». Il est donc important que les utilisateurs qui ont un domaine personnalisé s’assurent d’avoir configuré un certificat SSL.</li><li>Assurez-vous que les pare-feux d’applications web et de paquets protégeant l’App Service ont autorisé le transfert de sessions HTTPS.</li></ul>|
|Le débogage à distance doit être désactivé pour l'application web|Cette action désactive le débogage à distance.|
|Le débogage à distance devrait être désactivé pour Function App|Cette action désactive le débogage à distance.|
|Le débogage à distance doit être désactivé pour l’application API|Cette action désactive le débogage à distance.|
|CORS ne doit pas autoriser toutes les ressources à accéder à vos applications web|Cette action empêche d’autres domaines d’accéder à votre application web. Pour autoriser des domaines spécifiques, entrez-les dans le champ Origines autorisées (en les séparant par des virgules). <br>**Remarque**: Si vous laissez le champ vide, tous les appels cross-origin seront bloqués. Titre du champ de paramètres : « Origines autorisées »|
|CORS ne devrait pas autoriser toutes les ressources à accéder à votre Function App|Cette action empêche d’autres domaines d’accéder à votre application de fonction. Pour autoriser des domaines spécifiques, entrez-les dans le champ Origines autorisées (en les séparant par des virgules). <br>**Remarque**: Si vous laissez le champ vide, tous les appels cross-origin seront bloqués. Titre du champ de paramètres : « Origines autorisées »|
|CORS ne doit pas autoriser toutes les ressources à accéder à votre application API|Cette action empêche d’autres domaines d’accéder à votre application API. Pour autoriser des domaines spécifiques, entrez-les dans le champ Origines autorisées (en les séparant par des virgules). <br>**Remarque**: Si vous laissez le champ vide, tous les appels cross-origin seront bloqués. Titre du champ de paramètres : « Origines autorisées »|
|Un agent de supervision doit être activé sur vos machines virtuelles|Cette action installe un agent de supervision sur les machines virtuelles sélectionnées. Sélectionnez un espace de travail vers lequel l’agent doit envoyer ses rapports.<ul><li>Si votre stratégie de mise à jour est définie sur automatique, elle est déployée sur les instances nouvelles et existantes.</li><li>Si votre stratégie de mise à jour est définie sur manuelle et que vous souhaitez installer l’agent sur des instances existantes, cochez la case.  [En savoir plus](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Les journaux de diagnostic doivent être activés dans Key Vault|Cette action active les journaux de diagnostic sur les coffres de clés Key Vault. Les métriques et les journaux de diagnostic sont enregistrés dans l’espace de travail sélectionné.|
|Les journaux de diagnostic doivent être activés dans Service Bus|Cette action active les journaux de diagnostic sur Service Bus. Les métriques et les journaux de diagnostic sont enregistrés dans l’espace de travail sélectionné.|

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment appliquer les recommandations fournies par Security Center. Pour plus d’informations sur Security Center, consultez les rubriques suivantes :

* [Définition de stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
