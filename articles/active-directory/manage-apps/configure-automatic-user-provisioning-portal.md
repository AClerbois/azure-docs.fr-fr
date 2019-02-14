---
title: Gestion de l’approvisionnement d’utilisateurs pour les applications d’entreprise dans Azure Active Directory | Microsoft Docs
description: Découvrez comment gérer l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise à l’aide d’Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2018
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4239c07c73825f75dd39053e312ae731f6f0d7d1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162711"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise dans le portail Azure
Cet article explique comment utiliser le [portail Azure](https://portal.azure.com) pour gérer l’approvisionnement et l’annulation de l’approvisionnement automatiques de comptes d’utilisateur pour les applications qui les prennent en charge. Pour en savoir plus sur l’approvisionnement automatique de comptes d’utilisateur, consultez [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Recherche de vos applications dans le portail
Toutes les applications d’entreprise qui sont configurées pour l’authentification unique peuvent être affichées et gérées dans le [portail Azure](https://portal.azure.com). Les applications figurent dans la section **Tous les services** &gt; **Applications d’entreprise** du portail. Les applications d’entreprise sont des applications qui sont déployées et utilisées au sein de votre organisation.

![Volet Applications d’entreprise](./media/configure-automatic-user-provisioning-portal/enterprise-apps-pane.png)

La sélection du lien **Toutes les applications** à gauche permet d’afficher une liste de toutes les applications qui ont été configurées, y compris les applications ajoutées depuis la galerie. La sélection d’une application charge le volet de ressources de cette application, qui vous permet de consulter des rapports concernant l’application et de gérer un large éventail de paramètres.

Les paramètres d’approvisionnement de comptes d’utilisateur peuvent être gérés en sélectionnant **Approvisionnement** sur la gauche.

![Volet de ressources d’application](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modes d’approvisionnement
Le volet **Approvisionnement** commence par un menu **Mode**, qui indique les modes d’approvisionnement pris en charge pour une application d’entreprise et permet de les configurer. Les options disponibles incluent :

* **Automatique** : cette option est affichée si Azure AD prend en charge l’approvisionnement ou l’annulation de l’approvisionnement automatiques basés sur une API de comptes d’utilisateur pour cette application. La sélection de ce mode affiche une interface qui guide les administrateurs à travers la configuration d’Azure AD pour se connecter à l’API de gestion des utilisateurs de l’application, la création de mappages et de flux de travail de comptes définissant le mode de circulation des données de comptes d’utilisateur entre Azure AD et l’application, et la gestion du service d’approvisionnement d’Azure AD.
* **Manuel** : cette option est affichée si Azure AD ne prend pas en charge l’approvisionnement automatique de comptes d’utilisateur pour cette application. Avec cette option, les enregistrements de comptes d’utilisateur stockés dans l’application doivent être gérés à l’aide d’un processus externe qui dépend des fonctionnalités de gestion et d’approvisionnement des utilisateurs fournies par cette application (par exemple l’approvisionnement SAML juste-à-temps).

## <a name="configuring-automatic-user-account-provisioning"></a>Configuration de l’approvisionnement automatique de comptes d’utilisateur
La sélection de l’option **Automatique** affiche un écran divisé en quatre sections :

### <a name="admin-credentials"></a>Admin Credentials (Informations d’identification de l’administrateur)
C’est dans cette section que les informations d’identification requises par Azure AD pour se connecter à l’API de gestion des utilisateurs de l’application doivent être saisies. Les entrées requises dépendent de l’application. Pour en savoir plus sur les types d’informations d’identification requis pour une application spécifique, consultez le [didacticiel de configuration de cette application](user-provisioning.md).

La sélection du bouton **Tester la connexion** vous permet de tester les informations d’identification. Azure AD essaie alors de se connecter à l’API de configuration de l’application à l’aide des informations d’identification fournies.

### <a name="mappings"></a>Mappages
C’est dans cette section que les administrateurs peuvent afficher et modifier les attributs utilisateur qui circulent entre Azure AD et l’application cible lorsque des comptes d’utilisateur sont configurés ou mis à jour.

Il existe un ensemble préconfiguré de mappages entre les objets utilisateur Azure AD et les objets utilisateur de chaque application SaaS. Certaines applications gèrent d’autres types d’objets, tels que des groupes ou des contacts. La sélection d’un de ces mappages dans le tableau affiche l’éditeur de mappage, qui permet de le visualiser et de le personnaliser.

![Volet de ressources d’application](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Les personnalisations prises en charge sont notamment les suivantes :

* L’activation et la désactivation des mappages d’objets spécifiques, par exemple entre l’objet utilisateur Azure AD et l’objet utilisateur de l’application SaaS.
* Modification des attributs qui circulent entre l’objet utilisateur Azure AD et l’objet utilisateur de l’application. Pour plus d’informations sur le mappage d’attributs, voir [Présentation des types de mappages d’attributs](customize-application-attributes.md#understanding-attribute-mapping-types).
* Le filtrage des actions d’approvisionnement qu’effectue Azure AD sur l’application ciblée. Plutôt qu’Azure AD synchronise entièrement les objets, vous pouvez limiter les actions exécutées. Par exemple, si vous sélectionnez uniquement **Mettre à jour**, Azure AD met à jour les comptes d’utilisateur d’une application et n’en crée pas de nouveaux. Si vous sélectionnez uniquement **Créer**, Azure crée des comptes d’utilisateur, mais ne met pas à jour les comptes existants. Cette fonctionnalité permet aux administrateurs de créer des mappages différents pour les flux de travail de création et de mise à jour de comptes.

### <a name="settings"></a>Paramètres
Cette section permet aux administrateurs de démarrer et d’arrêter le service d’approvisionnement d’Azure AD pour l’application sélectionnée, et au besoin d’effacer le cache d’approvisionnement et de redémarrer le service.

Si c’est la première fois que l’approvisionnement est activé pour une application, activez le service en définissant le paramètre **État de la configuration** sur **Activé**. Suite au changement, le service d’approvisionnement d’Azure AD exécute une synchronisation initiale, durant laquelle il lit les utilisateurs affectés dans la section **Utilisateurs et groupes**, interroge l’application cible à leur sujet, puis effectue les actions d’approvisionnement définies dans la section **Mappages** d’Azure AD. Au cours de ce processus, le service d’approvisionnement stocke des données en cache sur les comptes d’utilisateur qu’il gère, de sorte que les opérations d’annulation de l’approvisionnement n’ont aucun effet sur les comptes non gérés des applications cibles qui n’ont jamais été concernés par l’affectation. Après la synchronisation initiale, le service d’approvisionnement synchronise automatiquement les objets utilisateur et groupe toutes les dix minutes.

La définition du paramètre **État de la configuration** sur **Désactivé** suspend simplement le service d’approvisionnement. Dans cet état, Azure ne crée, met à jour ou supprime aucun objet utilisateur ou groupe dans l’application. Si l’état est de nouveau défini sur Activé, le service reprend là où il s’était arrêté.

Si la case à cocher **Clear current state and restart synchronization** (Effacer l’état en cours et redémarrer la synchronisation) est activée et la configuration enregistrée, le service d’approvisionnement s’arrête, vide les données en cache sur les comptes d’utilisateur gérés par Azure AD, redémarre les services et exécute à nouveau la synchronisation initiale. Cette option permet aux administrateurs de recommencer le processus de déploiement d’approvisionnement.

### <a name="synchronization-details"></a>Détails de la synchronisation
Cette section fournit des informations supplémentaires sur le fonctionnement du service d’approvisionnement, y compris la première et la dernière exécution du service d’approvisionnement pour l’application et le nombre d’objets utilisateur et groupe gérés.

Elle contient également des liens vers le **rapport d’activité d’approvisionnement**, qui fournit un journal de tous les utilisateurs et les groupes créés, mis à jour et supprimés entre Azure AD et l’application cible, et vers le **rapport d’erreurs d’approvisionnement**, qui fournit des messages d’erreur plus détaillés pour les objets utilisateur et groupe dont la lecture, la création, la mise à jour ou la suppression a échoué. 



