---
title: Vue d’ensemble d’Enterprise State Roaming | Microsoft Docs
description: Fournit des informations sur les paramètres Enterprise State Roaming dans les périphériques Windows. Enterprise State Roaming fournit aux utilisateurs une expérience unifiée sur leurs appareils Windows et réduit le temps nécessaire à la configuration d’un nouveau périphérique.
services: active-directory
keywords: Qu’est-ce que Enterprise State Roaming, la synchronisation d’entreprise et Windows cloud
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: markvi
ms.openlocfilehash: c2c94f488da6399965c59f761ff9f0539cf4de2f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227776"
---
# <a name="enterprise-state-roaming-overview"></a>Présentation d’Enterprise State Roaming
Avec Windows 10, les utilisateurs [Azure Active Directory (Azure AD)](fundamentals/active-directory-whatis.md) ont la possibilité de synchroniser en toute sécurité leurs paramètres utilisateur et les données de paramètres d’application dans le cloud. Enterprise State Roaming fournit aux utilisateurs une expérience unifiée sur leurs appareils Windows et réduit le temps nécessaire à la configuration d’un nouveau périphérique. Enterprise State Roaming fonctionne comme la [synchronisation des paramètres de consommateur](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs) présentée dans Windows 8. En outre, Enterprise State Roaming offre :

* **Séparation des données d’entreprise des données client** : les organisations maîtrisent leurs données, et les données d’entreprise ne sont pas mélangées dans le compte cloud client ou aucune donnée client ne figure dans le compte cloud d’entreprise.
* **Sécurité renforcée** : les données sont automatiquement chiffrées avant de quitter l’appareil Windows 10 de l’utilisateur à l’aide d’Azure Rights Management (Azure RMS) et les données restent chiffrées au repos dans le cloud. Tout le contenu reste chiffré au repos dans le cloud, à l’exception des espaces de noms tels que les noms de paramètres et les noms d’application Windows.  
* **Meilleure gestion et meilleure surveillance** : vous offre plus de contrôle et de visibilité sur qui synchronise les paramètres dans votre organisation et sur quels appareils à travers l’intégration du portail Azure AD. 

Enterprise State Roaming est disponible dans plusieurs régions Azure. Pour consulter la liste à jour des régions disponibles, voir la page [Régions Azure - Services par région](https://azure.microsoft.com/regions/#services) sous Azure Active Directory.

| Article | Description |
| --- | --- |
| [Activer Enterprise State Roaming dans Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) |Enterprise State Roaming est disponible pour toute organisation ayant souscrit un abonnement premium à Azure Active Directory (Azure AD). Pour plus de détails sur l’obtention d’un abonnement Azure AD, consultez la page [du produit Azure AD](https://azure.microsoft.com/services/active-directory) . |
| [FAQ sur l’itinérance des paramètres et des données](active-directory-windows-enterprise-state-roaming-faqs.md) |Cette rubrique répond à certaines questions que les administrateurs informatiques peuvent se poser sur les paramètres et la synchronisation des données d’application. |
| [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |Windows 10 fournit les paramètres de stratégie de groupe et de gestion d’appareil mobile (MDM) pour limiter la synchronisation des paramètres. |
| [Référence des paramètres d’itinérance Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |Voici une liste complète de tous les paramètres destinés à l’itinérance et/ou à la sauvegarde dans Windows 10. |
| [Dépannage](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |Cette rubrique présente certaines étapes de base pour la résolution des problèmes et contient une liste de problèmes connus. |

