---
title: Questions fréquentes concernant les réseaux dans Azure Functions
description: Répond à certaines questions courantes concernant les réseaux dans Azure Functions.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 4534e20a1156f1d3550d247dfd15d9dc538e9d80
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226829"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Questions fréquentes concernant les réseaux dans Azure Functions

Cet article liste les questions fréquentes concernant les réseaux dans Azure Functions. Pour obtenir une présentation plus complète, consultez [Options de réseau dans Azure Functions](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Comment définir une adresse IP statique dans Azure Functions ?

Le seul moyen d’obtenir une adresse IP statique entrante et sortante pour votre fonction est de déployer celle-ci dans App Service Environment. Pour plus d’informations sur l’utilisation d’App Service Environment, commencez par lire l’article [Créer et utiliser un équilibreur de charge interne avec App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Comment limiter l’accès Internet à ma fonction ?

Vous pouvez limiter l’accès Internet de deux façons :

* [Restrictions d’adresse IP](../app-service/app-service-ip-restrictions.md) : limite le trafic entrant vers votre application de fonction en se basant sur la plage d’adresses IP.
    * Lorsque des restrictions d’adresse IP sont appliquées, vous pouvez également configurer des [points de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md),afin de contraindre votre fonction à n’accepter que le trafic entrant provenant d’un réseau virtuel particulier.
* Suppression de tous les déclencheurs HTTP. Pour certaines applications, il suffit simplement d’éviter les déclencheurs HTTP et d’utiliser toute autre source d’événement pour déclencher votre fonction.

N’oubliez pas que l’éditeur du portail Azure nécessite un accès direct à la fonction actuellement exécutée. Les modifications apportées au code via le portail Azure nécessitent que l’appareil que vous utilisez accède au portail afin d’obtenir que son adresse IP soit ajoutée à la liste verte. Toutefois, vous pouvez aussi utiliser l’une des options qui se trouvent sous l’onglet Fonctionnalités de la plateforme lorsque des restrictions de réseau sont appliquées.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Comment limiter mon application de fonction à un réseau virtuel ?

Vous pouvez limiter le trafic **entrant** d’une application de fonction à un seul réseau virtuel à l’aide de [points de terminaison de service](./functions-networking-options.md#private-site-access). Cette configuration permet toujours à l’application de fonction d’effectuer des appels sortants vers Internet.

La seule façon de restreindre totalement une fonction afin que tout le trafic transite via un réseau virtuel consiste à utiliser un environnement App Service Environment dont la charge est équilibrée en interne. Cette option déploie votre site sur une infrastructure dédiée à l’intérieur d’un réseau virtuel, et envoie le trafic ainsi que l’ensemble des déclencheurs via le réseau virtuel. 

Pour plus d’informations sur l’utilisation d’App Service Environment, commencez par lire l’article [Créer et utiliser un équilibreur de charge interne avec App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Comment accéder aux ressources d’un réseau virtuel à partir d’une application de fonction ?

Vous pouvez accéder aux ressources d’un réseau virtuel à partir d’une fonction en cours d’exécution, à l’aide de l’intégration de réseau virtuel. Pour plus d’informations, consultez [Intégration de réseau virtuel](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Comment accéder aux ressources protégées par des points de terminaison de service ?

Avec l’intégration de réseau virtuel, vous pouvez accéder aux ressources sécurisées par des points de terminaison de service à partir d’une fonction en cours d’exécution. Pour plus d’informations, consultez [Intégration de réseau virtuel](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Comment déclencher une fonction à partir d’une ressource d’un réseau virtuel ?

Vous pouvez permettre aux déclencheurs HTTP d’être appelés à partir d’un réseau virtuel en utilisant des [points de terminaison de service](./functions-networking-options.md#private-site-access). 

Vous pouvez également déclencher une fonction à partir d’une ressource d’un réseau virtuel en déployant votre application de fonction dans App Service Environment. Pour plus d’informations sur l’utilisation d’App Service Environment, consultez [Créer et utiliser un équilibreur de charge interne avec App Service Environment](../app-service/environment/create-ilb-ase.md).

Les plans Premium et App Service prennent en charge les déclencheurs HTTP d’un réseau virtuel. Toutefois, seul App Service Environment est capable de prendre en charge tous les autres types de déclencheurs de fonction via un réseau virtuel.

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Comment déployer mon application de fonction dans un réseau virtuel ?

Un déploiement dans App Service Environment est le seul moyen de créer une application de fonction qui se trouve entièrement dans un réseau virtuel. Pour plus d’informations sur l’utilisation d’un équilibreur de charge interne avec App Service Environment, commencez par lire l’article [Créer et utiliser un équilibreur de charge interne avec App Service Environment](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Si vous avez seulement besoin d’un accès unidirectionnel aux ressources d’un réseau virtuel ou d’un isolement réseau moins complet, consultez [Vue d’ensemble des réseaux Azure Functions](functions-networking-options.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les réseaux et les fonctions : 

* [Tutoriel pour bien démarrer avec l’intégration de réseau virtuel](./functions-create-vnet.md)
* [En savoir plus sur les options de réseau dans Azure Functions](./functions-networking-options.md)
* [En savoir plus sur l’intégration de réseau virtuel avec App Service et Functions](../app-service/web-sites-integrate-with-vnet.md)
* [En savoir plus sur les réseaux virtuels dans Azure](../virtual-network/virtual-networks-overview.md)
* [Activer davantage de fonctionnalités de réseau et plus de contrôle avec Azure App Service Environment](../app-service/environment/intro.md)
