---
title: Rediriger un domaine Internet d’entreprise sur un nom de domaine Traffic Manager | Microsoft Docs
description: Cet article vous aide à rediriger votre nom de domaine d’entreprise vers un nom de domaine Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 29822946-2d45-4434-ba47-fc180a445cc3
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 63a78332742dcefab20401575b1df0e23c06aa78
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035144"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Redirection d’un domaine Internet d’entreprise vers un domaine Azure Traffic Manager

Lorsque vous créez un profil Traffic Manager, Azure attribue automatiquement un nom DNS pour ce profil. Pour utiliser un nom de votre zone DNS, créez un enregistrement DNS CNAME qui mappe sur le nom de domaine de votre profil Traffic Manager. Vous pouvez trouver le nom de domaine Traffic Manager dans la section **Général** de la page Configuration du profil Traffic Manager.

Par exemple, pour pointer le nom `www.contoso.com` vers le nom DNS de Traffic Manager `contoso.trafficmanager.net`, vous créez l’enregistrement de ressource DNS suivant :

    www.contoso.com IN CNAME contoso.trafficmanager.net

L’intégralité des demandes de trafic pour *www.contoso.com* est redirigée vers *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Vous ne pouvez pas rediriger un domaine de second niveau tel que *contoso.com*vers le domaine Traffic Manager. Les normes de protocole DNS n’autorisent pas les enregistrements CNAME pour les noms de domaine de second niveau.

## <a name="next-steps"></a>Étapes suivantes

* [Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md)
* [Traffic Manager - Désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)
* [Traffic Manager - Désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)
