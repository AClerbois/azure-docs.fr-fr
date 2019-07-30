---
title: Restrictions d’accès - Azure App Service | Microsoft Docs
description: Comment utiliser des restrictions d’accès avec Azure App Service
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d3c547fbc09aeb034df5b7ed579639e1ff4bc0b4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705800"
---
# <a name="azure-app-service-access-restrictions"></a>Restrictions d’accès dans Azure App Service #

Les restrictions d’accès permettent de définir une liste verte/d’exclusion classée par ordre de priorité qui contrôle l’accès réseau à votre application. La liste peut inclure des adresses IP ou des sous-réseaux de Réseau virtuel Microsoft Azure. Lorsqu’il y a une ou plusieurs entrées, il existe une règle implicite « Tout refuser » qui se trouve à la fin de la liste.

La fonctionnalité Restrictions d’accès peut être utilisée avec toutes les charges de travail hébergées par App Service, notamment les applications web, les applications API, les applications Linux, les applications de conteneur Linux et Functions.

Lorsqu’une demande est envoyée à votre application, l’adresse dont provient la demande est vérifiée par rapport aux règles d’adresses IP de votre liste de restriction des accès. Si l’adresse dont provient la demande se trouve dans un sous-réseau configuré avec des points de terminaison de service sur Microsoft.Web, le sous-réseau source est comparé aux règles de réseau virtuel de votre liste des restrictions d’accès. Si l’adresse n’est pas autorisée à y accéder selon les règles définies dans la liste, le service répond avec le code d’état [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

La fonctionnalité de restriction des accès est implémentée dans les rôles frontend App Service, qui sont en amont des hôtes de travail où votre code s’exécute. C’est pourquoi les restrictions d’accès sont comparables à des listes de contrôle d’accès (ACL) réseau.

La possibilité de restreindre l’accès à votre application web à partir d’un réseau virtuel Azure (VNet) est appelée [points de terminaison de service][serviceendpoints]. Les points de terminaison de service vous permettent de restreindre l’accès à un service multilocataire à partir de sous-réseaux sélectionnés. Le point de terminaison de service doit être activé côté réseau et côté service avec lequel il est activé. Il n’est pas destiné à limiter le trafic vers les applications hébergées dans un environnement App Service Environment.  Si vous êtes dans un environnement App Service Environment, vous pouvez contrôler l’accès à votre application avec des règles d’adresses IP.

![flux de restrictions d’accès](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Ajout et modification des règles de restriction d’accès dans le portail ##

Pour ajouter une règle de restriction d’accès à votre application, dans le menu, ouvrez **Réseau**>**Restrictions d’accès**, puis cliquez sur **Configurer des restrictions d’accès**.

![Options réseau d’App Service](media/app-service-ip-restrictions/access-restrictions.png)  

Dans l’interface utilisateur Restrictions d’accès, vous pouvez consulter la liste des règles de restriction d’accès définies pour votre application.

![répertorier les restrictions d’accès](media/app-service-ip-restrictions/access-restrictions-browse.png)

La liste affiche l’ensemble des restrictions actuelles de votre application. Si une restriction de réseau virtuel s’applique à votre application, le tableau indique si des points de terminaison de service sont activés pour Microsoft.Web. En l’absence de restriction appliquée à votre application, votre application est accessible depuis n’importe où.  

## <a name="adding-ip-address-rules"></a>Ajout de règles d’adresses IP

Vous pouvez cliquer sur **[+] Ajouter** pour ajouter une nouvelle règle de restriction d’accès. Quand vous ajoutez une règle, celle-ci est appliquée immédiatement. Les règles sont appliquées par ordre de priorité, du chiffre le moins élevé au chiffre le plus élevé. Il existe une règle implicite « Tout refuser » qui s’applique dès que vous ajoutez une règle.

Lorsque vous créez une règle, vous devez sélectionner autoriser/refuser, ainsi que le type de règle. Vous devez également indiquer la valeur de priorité et l’élément sur lequel porte la restriction d’accès.  Vous pouvez éventuellement ajouter un nom et une description à la règle.  

![ajouter une règle de restriction d’accès IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Pour définir une règle reposant sur une adresse IP, sélectionnez le type IPv4 ou IPv6. Pour les adresses IPv4 et IPv6, la notation CIDR doit être utilisée. Pour spécifier une adresse exacte, vous pouvez utiliser le format 1.2.3.4/32, où les quatre premiers octets représentent votre adresse IP, et /32 correspond au masque. La notation CIDR IPv4 est 0.0.0.0/0 pour toutes les adresses. Pour plus d’informations sur la notation CIDR, consultez [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Points de terminaison de service

Les points de terminaison de service vous permettent de restreindre l’accès à une sélection de sous-réseaux de réseau virtuel Azure. Pour restreindre l’accès à un sous-réseau spécifique, créez une règle de restriction dont le type est Réseau virtuel. Vous pouvez choisir l’abonnement, le réseau virtuel et le sous-réseau avec lequel vous souhaitez autoriser ou refuser l’accès. Si les points de terminaison de service ne sont pas encore activés avec Microsoft.Web pour le sous-réseau sélectionné, ils le seront automatiquement pour vous, sauf si vous cochez la case demandant de ne pas le faire. Le fait de vouloir les activer sur l’application, mais pas sur le sous-réseau est lié en grande partie au fait de détenir ou non les autorisations d’activer les points de terminaison de service sur le sous-réseau. Si vous avez besoin que quelqu’un d’autre active des points de terminaison de service sur le sous-réseau, vous pouvez cocher la case et faire en sorte que votre application soit configurée pour les points de terminaison de service en prévision que celle-ci soit activée ultérieurement sur le sous-réseau. 

![ajouter une règle de restriction d’accès de réseau virtuel](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Les points de terminaison de service ne peuvent pas être utilisés pour restreindre l’accès aux applications qui s’exécutent dans un environnement App Service Environment. Si votre application se trouve dans un environnement App Service Environment, vous pouvez contrôler l’accès à votre application avec des règles d’accès IP. 

Avec les points de terminaison de service, vous pouvez configurer votre application avec les passerelles d’application ou d’autres appareils WAF. Vous pouvez également configurer des applications à plusieurs niveaux avec des serveurs principaux sécurisés. Pour plus d’informations sur certaines possibilités, consultez [Fonctionnalités de mise en réseau et App Service](networking-features.md).

## <a name="managing-access-restriction-rules"></a>Gestion des règles de restriction d’accès

Vous pouvez cliquer sur n’importe quelle ligne pour modifier une règle existante de restriction d’accès. Les modifications sont appliquées immédiatement, y compris les changements de priorité.

![modifier une règle de restriction d’accès](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Lorsque vous modifiez une règle, vous ne pouvez pas modifier le type entre une règle d’adresse IP et une règle de réseau virtuel. 

![modifier une règle de restriction d’accès](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Pour supprimer une règle, cliquez sur les trois points ( **...** ) dans votre règle, puis cliquez sur **Supprimer**.

![supprimer une règle de restriction d’accès](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Bloquer une adresse IP unique ##

Lorsque vous ajoutez votre première règle de restriction IP, le service ajoute une règle **tout refuser** ayant la priorité 2147483647. Dans la pratique, la règle explicite **Tout refuser** est la dernière règle exécutée et bloque l’accès à toute adresse IP n’étant pas autorisée de manière explicite par une règle **Autoriser**.

Lorsque les utilisateurs souhaitent bloquer explicitement une seule adresse IP ou le bloc d’adresses IP, mais autoriser tout autre accès, il est nécessaire d’ajouter une règle explicite **Tout autoriser**.

![bloquer une adresse IP unique](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Site GCL 

Outre la possibilité de contrôler l’accès à votre application, vous pouvez également restreindre l’accès au site gcl utilisé par votre application. Le site gcl est le point de terminaison de déploiement web, ainsi que la console Kudu. Vous pouvez affecter séparément des restrictions d’accès au site gcl à partir de l’application ou utiliser le même ensemble pour l’application et le site gcl. Lorsque vous cochez la case pour avoir les mêmes restrictions que votre application, tout s’affiche en grisé. Si vous décochez la case, les paramètres dont vous disposiez précédemment sur le site gcl sont appliqués. 

![répertorier les restrictions d’accès](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipulation par programmation des règles de restriction d’accès ##

Il n’existe aucune interface CLI ou PowerShell pour la nouvelle fonctionnalité Restrictions d’accès. Vous pouvez cependant définir les valeurs manuellement à l’aide d’une opération PUT d’[API REST Azure](https://docs.microsoft.com/rest/api/azure/) dans la configuration d’application dans le Gestionnaire des ressources. Par exemple, vous pouvez utiliser resources.azure.com et modifier le bloc ipSecurityRestrictions pour ajouter le code JSON nécessaire.

Dans Resource Manager, ces informations se trouvent à l’emplacement suivant :

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

La syntaxe JSON de l’exemple précédent est la suivante :

    {
      "properties": {
        "ipSecurityRestrictions": [
          {
            "ipAddress": "122.133.144.0/24",
            "action": "Allow",
            "tag": "Default",
            "priority": 100,
            "name": "IP example rule"
          }
        ]
      }
    }

## <a name="function-app-ip-restrictions"></a>Restrictions d’adresse IP Function App

Les restrictions d’adresse IP sont disponibles pour les deux applications de fonctions avec la même fonctionnalité que les plans App Service. L’activation des restrictions d’adresse IP désactive l’éditeur de code du portail pour les adresses IP non autorisées.

[En savoir plus ici](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
