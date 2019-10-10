---
title: Plans de déploiement - Azure Active Directory | Microsoft Docs
description: Conseils de bout en bout sur le déploiement d’un grand nombre de fonctionnalités Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7c851c2d865a7b553be2cd0f619ad09985115a3
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336875"
---
# <a name="azure-active-directory-deployment-plans"></a>Plans de déploiement Azure Active Directory
Vous souhaitez des conseils de bout en bout sur le déploiement des fonctionnalités Azure Active Directory (Azure AD) ? Les plans de déploiement Azure AD abordent les notions de base (valeur commerciale, planification et procédures opérationnelles) nécessaires pour réussir à déployer les fonctionnalités Azure AD les plus courantes.

À partir de l’une des pages de plan, utilisez la fonctionnalité Imprimer dans un fichier PDF de votre navigateur pour créer une version hors connexion de la documentation.
## <a name="include-the-right-stakeholders"></a>Inclure les parties prenantes appropriées

Lorsque vous commencez la planification du déploiement d’une nouvelle fonctionnalité, il est important d’inclure les parties prenantes clés au sein de votre organisation. Nous vous recommandons d’identifier et de documenter la ou les personnes qui occupent les rôles suivants et de travailler avec elles pour déterminer leur implication dans le projet.  

Les rôles peuvent inclure : 

|Role |Description |
|-|-|
|Utilisateur final|Groupe représentatif des utilisateurs pour lesquels la fonctionnalité sera implémentée. Visualise souvent la préversion des changements dans un programme pilote.
|Responsable du support informatique|Représentant de l’organisation de support informatique qui peut fournir une entrée sur la capacité de prise en charge de ce changement du point de vue du support technique.  
|Architecte de l’identité ou administrateur général Azure|Représentant de l’équipe de gestion des identités, responsable de la définition de l’alignement de ce changement sur l’infrastructure principale de gestion des identités dans votre organisation.|
|Responsable d’entreprise de l’application |Dans l’entreprise, responsable général de la ou des applications affectées, qui peuvent inclure la gestion de l’accès.  Il peut également fournir une entrée sur l’expérience utilisateur et l’utilité de ce changement du point de vue d’un utilisateur final.
|Responsable de la sécurité|Représentant de l’équipe de sécurité qui peut valider que le plan répondra aux exigences de sécurité de votre organisation.|
|Compliance Manager|Personne responsable au sein de votre organisation de la conformité aux exigences de l’entreprise, du secteur et du gouvernement.|

**Les niveaux d’implication peuvent inclure :**

- **R**esponsable de la mise en œuvre du plan de projet et des résultats 

- **A**pprobation du plan de projet et des résultats 

- **C**ontributeur au plan de projet et aux résultats 

- **I**nformé du plan de projet et des résultats


## <a name="best-practices-for-a-pilot"></a>Bonnes pratiques pour un pilote
Un pilote vous permet de tester sur un petit groupe avant d’activer une fonctionnalité pour tout le monde. Vérifiez que, dans le cadre de vos tests, chaque cas d’usage au sein de votre organisation est soigneusement testé. Il est préférable de cibler un groupe spécifique d’utilisateurs pilotes avant le déploiement sur l’ensemble de votre organisation.

Dans votre première vague, ciblez le service informatique, la convivialité et d’autres utilisateurs appropriés qui peuvent tester et fournir des commentaires. Ces commentaires doivent être utilisés pour développer davantage les communications et les instructions que vous envoyez à vos utilisateurs, et pour donner des insights sur les types de problèmes que votre personnel de support technique peut voir. 

L’extension du déploiement à des groupes d’utilisateurs plus importants doit être effectuée en augmentant l’étendue du ou des groupes ciblés. Pour ce faire, vous pouvez utiliser l’[appartenance de groupe dynamique](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) ou ajouter manuellement des utilisateurs aux groupes ciblés.


## <a name="deploy-authentication"></a>Déployer l’authentification

| Fonctionnalité | Description|
| -| -|
| [Azure Multi-Factor Authentication](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-getstarted)| Azure Multi-Factor Authentication (MFA) est la solution de vérification en deux étapes de Microsoft. À l’aide des méthodes d’authentification approuvées par l’administrateur, Azure MFA contribue à sécuriser l’accès à vos données et à vos applications, tout en répondant à la nécessité de mettre en place un processus d’authentification simple. |
| [Accès conditionnel](https://aka.ms/deploymentplans/ca)| Avec l’accès conditionnel, vous pouvez mettre en œuvre des décisions de contrôle d’accès automatisées pour déterminer qui peut accéder à vos applications cloud, sur la base de conditions. |
| [Réinitialisation de mot de passe en libre service](https://aka.ms/deploymentplans/sspr)| La réinitialisation de mot de passe en libre-service offre à vos utilisateurs la possibilité de réinitialiser leur mot de passe, sans intervention de l’administrateur, quand et où ils en ont besoin. |

## <a name="deploy-application-management"></a>Gestion des applications de déploiement

| Fonctionnalité | Description|
| -| - |
| [Authentification unique](https://aka.ms/deploymentplans/sso)| Avec l’authentification unique, vos utilisateurs peuvent accéder à toutes les applications et ressources dont ils ont besoin pour travailler en se connectant une seule fois. Une fois qu’ils se sont connectés, ils peuvent passer de Microsoft Office à SalesForce, à Box ou à des applications internes sans devoir entrer encore une fois les informations d’identification. |
| [Volet d’accès](https://aka.ms/deploymentplans/accesspanel)| Offrez aux utilisateurs un hub simple leur permettant de découvrir toutes leurs applications et d’y accéder. Donnez-leur la possibilité d’accroître leur productivité grâce à des fonctionnalités en libre-service, pour demander l’accès à de nouveaux groupes et applications ou gérer l’accès à ces ressources pour le compte d’autres utilisateurs. |


## <a name="deploy-hybrid-scenarios"></a>Déployer des scénarios hybrides

| Fonctionnalité | Description|
| -| -|
| [ADFS et synchronisation de hachage de mot de passe](https://aka.ms/deploymentplans/adfs2phs)| Avec la synchronisation de hachage de mot de passe, les hachages des mots de passe utilisateur sont synchronisés sur Azure AD à partir de l’installation Active Directory locale. Ainsi, Azure AD permet d’authentifier les utilisateurs sans aucune interaction avec l’installation Active Directory locale. |
| [ADFS et authentification directe](https://aka.ms/deploymentplans/adfs2pta)| L’authentification directe Azure AD permet à vos utilisateurs de se connecter aux applications locales et aux applications cloud à l’aide des mêmes mots de passe. Cette fonctionnalité offre aux utilisateurs une meilleure expérience : moins de mots de passe à mémoriser et réduction des coûts de support technique informatique, car les utilisateurs sont moins susceptibles d’oublier comment se connecter. Lorsque les utilisateurs se connectent à l’aide d’Azure AD, cette fonctionnalité valide leurs mots de passe directement à partir de l’installation Active Directory locale. |
| [Proxy d’application Azure AD](https://aka.ms/deploymentplans/appproxy)| Aujourd’hui, les employés veulent être productifs partout, tout le temps, depuis n’importe quel appareil. Ils doivent accéder aux applications SaaS dans le cloud et aux applications d’entreprise locales. Le proxy Azure AD Application offre cet accès sans réseaux privés virtuels (VPN) ni zones démilitarisées (DMZ), qui sont des systèmes coûteux et complexes. |
| [Authentification unique fluide](https://aka.ms/SeamlessSSODPDownload)| L’authentification unique transparente Azure Active Directory connecte automatiquement les utilisateurs lorsque leurs appareils d’entreprise sont connectés au réseau de l’entreprise. Avec cette fonctionnalité, les utilisateurs n’ont plus besoin de taper leur mot de passe pour se connecter à Azure AD ni même, dans la plupart des cas, d’entrer leur nom d’utilisateur. Cette fonctionnalité offre à vos utilisateurs autorisés un accès facilité à vos applications cloud sans nécessiter de composants locaux supplémentaires. |

## <a name="deploy-user-provisioning"></a>Déployer l’approvisionnement d’utilisateurs

| Fonctionnalité | Description|
| -| -|
| [Approvisionnement d’utilisateurs](https://aka.ms/UserProvisioningDPDownload)| Azure AD vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans les applications cloud (SaaS) comme Dropbox, Salesforce, ServiceNow, etc. |
| [Provisionnement entrant des utilisateurs piloté par Workday](https://aka.ms/WorkdayDeploymentPlan)| Le provisionnement entrant des utilisateurs vers Active Directory piloté par Workday crée une base pour la gouvernance continue des identités et améliore la qualité des processus métier qui s’appuient sur les données d’identité faisant autorité. À l’aide de cette fonctionnalité, vous pouvez gérer en toute transparence le cycle de vie des identités des employés et des intérimaires en configurant des règles qui mappent les processus JLM (Joiner-Mover-Leaver) (nouveau recrutement, résiliation ou transfert, par exemple) aux actions d’approvisionnement du service informatique (telles que la création, l’activation, la désactivation). |

## <a name="deploy-governance-and-reporting"></a>Déployer la gouvernance et la création de rapports

| Fonctionnalité | Description|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) vous aide à gérer les rôles d’administrateur privilégiés dans Azure AD, les ressources Azure et d’autres services Microsoft Online Services. PIM fournit des solutions telles que l’accès juste-à-temps, des workflows d’approbation de demande et des révisions d’accès entièrement intégrées pour que vous puissiez identifier, découvrir et empêcher les activités malveillantes de rôles privilégiés en temps réel. |
| [Création de rapports et surveillance](https://aka.ms/deploymentplans/reporting)| La conception de votre solution de rapports et de surveillance Azure AD dépend de vos exigences légales, de sécurité et opérationnelles, ainsi que de votre environnement et de vos processus existants. Cet article présente les différentes options de conception et vous aide à choisir la stratégie de déploiement qui convient. |
