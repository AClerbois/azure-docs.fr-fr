---
title: Prise en charge du protocole d’en-têtes HTTP dans Azure Front Door Service | Microsoft Docs
description: Cet article décrit le protocole d’en-têtes HTTP pris en charge par Front Door Service.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736636"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Prise en charge du protocole d’en-têtes HTTP dans Azure Front Door Service
Cet article décrit le protocole pris en charge par Front Door Service avec certaines parties du chemin d’appel (voir l’image). Les sections suivantes fournissent plus d’informations sur les en-têtes HTTP pris en charge par Front Door Service.

![Protocole d’en-têtes HTTP Azure Front Door Service][1]

>[!IMPORTANT]
>Front Door Service ne certifie pas les en-têtes HTTP qui ne sont pas documentés ici.

## <a name="client-to-front-door-service"></a>Du client à Front Door Service
Front Door Service accepte la plupart des en-têtes de la requête entrante sans les modifier. Certains en-têtes réservés sont supprimés de la requête entrante en cas d’envoi, y compris les en-têtes avec un préfixe X-FD-*.

## <a name="front-door-service-to-backend"></a>Front Door Service vers le back-end

Front Door Service inclut les en-têtes d’une requête entrante, sauf s’ils sont supprimés en raison de restrictions. Front Door ajoute également les en-têtes suivants :

| En-tête  | Exemple et description |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> Front Door ajoute la version HTTP du client suivie d’*Azure* comme valeur pour l’en-tête Via. Cela indique la version HTTP du client et signale que Front Door était un destinataire intermédiaire pour la requête entre le client et le back-end.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Représente l’adresse IP du client associée à la requête traitée. Par exemple, une requête en provenance d’un proxy peut ajouter l’en-tête X-Forwarded-For pour indiquer l’adresse IP de l’appelant d’origine. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Représente l’adresse IP de socket associée à la connexion TCP d’où provient la requête actuelle. L’adresse IP de client d’une requête peut ne pas être identique à son adresse IP de socket, car elle peut être arbitrairement remplacée par un utilisateur.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Chaîne de référence unique qui identifie une requête prise en charge par Front Door. Elle sert à effectuer des recherches dans les journaux d’accès et est essentielle pour la résolution des problèmes.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops=1 </br> En-tête utilisé par Front Door pour détecter les boucles de requête, et pour lequel les utilisateurs ne doivent pas établir de dépendance. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Le champ d’en-tête HTTP X-Forwarded-For (XFF) identifie souvent l’adresse IP d’origine d’un client qui se connecte à un serveur web par le biais d’un équilibreur de charge ou d’un proxy HTTP. S’il existe un en-tête XFF, Front Door y ajoute l’adresse IP de socket du client ou ajoute l’en-tête XFF avec l’adresse IP de socket du client. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Le champ d’en-tête HTTP X-Forwarded-Host est une méthode couramment utilisée pour identifier l’hôte d’origine demandé par le client dans l’en-tête de requête HTTP Host. En effet, le nom d’hôte de Front Door peut être différent pour le serveur back-end gérant la requête. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Le champ d’en-tête HTTP X-Forwarded-Proto est souvent utilisé pour identifier le protocole d’origine d’une requête HTTP, car Front Door, conformément à la configuration, est susceptible de communiquer avec le back-end à l’aide de HTTPS. Cela est vrai même si la requête envoyé au proxy inverse est HTTP. |

## <a name="front-door-service-to-client"></a>De Front Door Service au client

Les en-têtes envoyés à Front Door à partir du back-end sont également transmis au client. Les en-têtes ci-dessous sont ceux envoyés de Front Door aux clients.

| En-tête  | Exemples |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Il s’agit d’une chaîne de référence unique qui identifie une requête prise en charge par Front Door. Elle est essentielle pour la résolution des problèmes, car elle est utilisée pour effectuer des recherches dans les journaux d’accès.|

## <a name="next-steps"></a>Étapes suivantes

- [Créer une porte d’entrée](quickstart-create-front-door.md)
- [Fonctionnement de Front Door](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png