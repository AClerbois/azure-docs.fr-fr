---
title: Qu’est-ce qu’Azure Application Gateway
description: Découvrez comment vous pouvez utiliser une passerelle d’application Azure pour gérer le trafic web vers votre application.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 5/31/2019
ms.author: victorh
ms.openlocfilehash: e2e29bf0068fae5d6f4987ec5c3f2a52b883e4bd
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418110"
---
# <a name="what-is-azure-application-gateway"></a>Qu’est-ce qu’Azure Application Gateway ?

Azure Application Gateway est un équilibreur de charge du trafic web qui vous permet de gérer le trafic vers vos applications web. Les équilibreurs de charge traditionnels fonctionnent au niveau de la couche de transport (couche OSI 4 - TCP et UDP) et acheminent le trafic en fonction de l’adresse IP et du port sources, vers une adresse IP et un port de destination.

![Concepts Application Gateway](media/overview/figure1-720.png)

Avec Application Gateway, vous pouvez prendre des décisions de routage basées sur des attributs supplémentaires d’une requête HTTP, tels que des en-têtes d’hôte ou le chemin d’un URI. Par exemple, vous pouvez acheminer le trafic en fonction de l’URL entrante. Par conséquent, si `/images` est dans l’URL entrante, vous pouvez acheminer le trafic vers un ensemble spécifique de serveurs (considérés comme un pool) configurés pour les images. Si `/video` est dans l’URL, ce trafic est acheminé vers un autre pool qui est optimisé pour les vidéos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ce type de routage est connu comme l’équilibrage de charge de la couche d’application (couche OSI 7). Azure Application Gateway permet d’effectuer un routage basé sur une URL et bien plus encore.

Les fonctionnalités suivantes sont incluses dans Azure Application Gateway :

## <a name="secure-sockets-layer-ssltls-termination"></a>Terminaison Secure Sockets Layer (SSL/TLS)

Application Gateway prend en charge l’arrêt SSL/TLS au niveau de la passerelle, après lequel le trafic transite généralement de façon non chiffrée vers les serveurs backend. Grâce à cette fonctionnalité, les serveurs web ne sont plus chargés des opérations coûteuses de chiffrement et de déchiffrement. Cependant, une communication non chiffrée vers les serveurs n’est parfois pas une option acceptable. Cela peut être dû à des d’exigences de sécurité et de conformité, ou au fait que l’application accepte uniquement une connexion sécurisée. Pour ces applications, la passerelle Application Gateway prend désormais en charge le chiffrement SSL/TLS de bout en bout.

## <a name="autoscaling"></a>Mise à l’échelle automatique

Les déploiements d’Application Gateway ou du WAF sous la référence SKU Standard_v2 ou WAF_v2 prennent en charge la mise à l’échelle automatique et peuvent subir un scale-up ou un scale-down en fonction de l’évolution des modèles de charge du trafic. La mise à l’échelle automatique vous évite aussi d’avoir à choisir une taille de déploiement ou un nombre d’instances au moment du provisionnement. Pour plus d’informations sur les fonctionnalités standard_v2 et WAF_v2 d’Application Gateway, consultez [Mise à l’échelle automatique de la référence SKU v2](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Redondance de zone

Les déploiements d’Application Gateway ou du WAF sous la référence SKU Standard_v2 ou WAF_v2 peuvent englober plusieurs zones de disponibilité, offrant une meilleure résilience d’erreurs et supprimant la nécessité de provisionner des passerelles d’application distinctes dans chaque zone.

## <a name="static-vip"></a>Adresse IP virtuelle statique

L’adresse IP virtuelle de la passerelle d’application sur la référence SKU Standard_v2 ou WAF_v2 prend exclusivement en charge le type d’adresse IP virtuelle statique. Cela garantit que l’adresse IP virtuelle associée à la passerelle d’application ne change pas même au cours de la durée de vie de la passerelle d’application.

## <a name="web-application-firewall"></a>Pare-feu d’application web

Le pare-feu d’applications Web (WAF) est une fonctionnalité de passerelle d’application qui protège vos applications web de manière centralisée contre les vulnérabilités et exploits courants. WAF suit les règles des [Ensembles de règles de base OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. 

Les applications Web sont de plus en plus la cible d’attaques malveillantes qui exploitent des vulnérabilités connues. Les types d’attaques les plus courantes sont l’injection de code SQL, les attaques de script site à site, entre autres. Empêcher ces attaques dans le code d’application peut se révéler difficile et nécessiter une maintenance rigoureuse, des mises à jour correctives ainsi que la surveillance au niveau d’un grand nombre de couches de la topologie de l’application. Un pare-feu d’applications web centralisé facilite grandement la gestion de la sécurité et offre une meilleure garantie de protection aux administrateurs de l’application contre les menaces ou les intrusions. Une solution WAF peut également réagir plus rapidement à une menace de sécurité en exécutant la mise à jour corrective d’une vulnérabilité connue dans un emplacement central plutôt que de sécuriser individuellement chacune des applications web. Les passerelles d’application existantes peuvent être facilement converties en une passerelle d’application avec un pare-feu d’applications web.

Pour plus d’informations, consultez [Pare-feu d’applications web (WAF) dans Application Gateway](https://docs.microsoft.com/azure/application-gateway/waf-overview).

## <a name="url-based-routing"></a>Routage basé sur des URL

Le routage basé sur le chemin d’accès de l’URL vous permet d’acheminer le trafic vers des pools de serveurs principaux en fonction des chemins d’accès de l’URL de la demande. L’un des scénarios consiste à acheminer les requêtes pour différents types de contenu vers un pool différent.

Par exemple, les requêtes adressées à `http://contoso.com/video/*` sont acheminées vers VideoServerPool et les requêtes adressées à `http://contoso.com/images/*` sont acheminées vers ImageServerPool. DefaultServerPool est sélectionné si aucun des modèles de chemin d’accès ne correspond.

Pour plus d’informations, consultez [Routage basé sur l’URL avec Application Gateway](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Hébergement de plusieurs sites

L’hébergement de plusieurs sites vous permet de configurer plusieurs sites web sur la même instance de passerelle d’application. Cette fonctionnalité vous permet de configurer une topologie plus efficace pour vos déploiements en ajoutant jusqu’à 100 sites web à une passerelle d’application. Chaque site web peut être dirigé vers son propre pool. Par exemple, la passerelle d’application peut traiter le trafic pour `contoso.com` et `fabrikam.com` à partir de deux pools de serveurs appelés ContosoServerPool et FabrikamServerPool.

Les requêtes adressées à `http://contoso.com` sont acheminées vers ContosoServerPool et les requêtes adressées à `http://fabrikam.com` sont acheminées vers FabrikamServerPool.

De même, deux sous-domaines du même domaine parent peuvent également être hébergés sur le même déploiement de passerelle d’application. Par exemple, les sous-domaines `http://blog.contoso.com` et `http://app.contoso.com` peuvent être hébergés sur un déploiement de passerelle d’application unique.

Pour plus d’informations, consultez [Hébergement de plusieurs sites avec Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Redirection

Un scénario courant pour de nombreuses applications web consiste à prendre en charge la redirection automatique de HTTP vers HTTPS pour vous assurer que toutes les communications entre l’application et ses utilisateurs auront lieu via un chemin d’accès chiffré.

Dans le passé, vous avez peut-être utilisé des techniques telles que la création d’un pool dédié dont le seul objectif consistait à rediriger les requêtes qu’il recevait sur HTTP vers HTTPS. La passerelle d’application permet désormais de rediriger le trafic sur Application Gateway. Cela simplifie la configuration de l’application, optimise l’utilisation des ressources et prend en charge de nouveaux scénarios de redirection, notamment la redirection globale et basée sur le chemin d’accès. La prise en charge de la redirection Application Gateway n’est pas limitée à la seule redirection HTTP vers HTTPS. Il s’agit d’un mécanisme de redirection générique, vous pouvez ainsi rediriger depuis et vers n’importe quel port défini à l’aide de règles. Il prend également en charge la redirection vers un site externe.

La prise en charge de la redirection Application Gateway offre les fonctionnalités suivantes :

- Redirection globale à partir d’un port vers un autre port sur la passerelle. Cela permet la redirection HTTP vers HTTPS sur un site.
- Redirection basée sur un chemin d’accès. Ce type de redirection permet la redirection HTTP vers HTTPS uniquement sur une zone de site spécifique, par exemple une zone de panier d’achat indiquée par `/cart/*`.
- Procédez à la redirection vers un site externe.

Pour plus d’informations, consultez [Redirection du trafic](https://docs.microsoft.com/azure/application-gateway/redirect-overview) avec Application Gateway.

## <a name="session-affinity"></a>Affinité de session

La fonctionnalité d’affinité de session basée sur les cookies est utile lorsque vous souhaitez conserver une session utilisateur sur le même serveur. En utilisant des cookies gérés de passerelle, la passerelle Application Gateway peut diriger le trafic pour traitement à partir d’une session utilisateur vers le même serveur. Ceci est important lorsque l’état de la session est enregistré localement sur le serveur pour une session utilisateur.

## <a name="websocket-and-http2-traffic"></a>Trafic WebSocket et HTTP/2

Application Gateway prend en charge les protocoles WebSocket et HTTP/2 de manière native. Il n’existe aucun paramètre configurable par l’utilisateur permettant d’activer ou de désactiver de manière sélective la prise en charge de WebSocket.

Les protocoles WebSocket et HTTP/2 permettent une communication en duplex intégral entre le serveur et le client via une connexion TCP de longue durée. Cela assure une communication plus interactive entre le serveur web et le client, qui peut être bidirectionnelle sans nécessiter d’interrogations, comme c’est le cas pour les implémentations basées sur le protocole HTTP. Ces protocoles engendrent une faible surcharge, contrairement à HTTP, et peuvent réutiliser la même connexion TCP pour plusieurs demandes/réponses, ce qui entraîne une utilisation plus efficace des ressources. Ces protocoles sont conçus pour fonctionner sur les ports HTTP traditionnels (80 et 443).

Pour plus d’informations, consultez [Prise en charge de WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) et [Prise en charge de HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="azure-kubernetes-service-aks-ingress-controller-preview"></a>Préversion du contrôleur d’entrée d’Azure Kubernetes Service (AKS) 

Le contrôleur Application Gateway Ingress s’exécute en tant que pod au sein du cluster AKS et permet à Application Gateway de servir d’entrée à un cluster AKS. Cela est pris en charge avec Application Gateway v2 uniquement.

Pour plus d’informations, consultez [Contrôleur d’entrée d’Azure Application Gateway](https://azure.github.io/application-gateway-kubernetes-ingress/).

## <a name="connection-draining"></a>Vidage des connexions

Le vidage des connexions permet d’éliminer délicatement les membres du pool principal lors des mises à jour planifiées de maintenance. Ce paramètre est activé via le paramètre du http principal et peut s’appliquer à tous les membres d’un pool principal pendant la création de règles. Une fois activée, Application Gateway s’assure que toutes les instances de désinscription d’un pool principal ne reçoivent aucune nouvelle requête tout en permettant aux requêtes existantes de se terminer dans un délai défini. Cela s’applique à la fois aux instances principales explicitement supprimées du pool principal par un appel d’API et aux instances secondaires signalées comme étant non intègres d’après les résultats des sondes d’intégrité.

## <a name="custom-error-pages"></a>Pages d’erreur personnalisées

Application Gateway vous permet de créer des pages d’erreur personnalisées au lieu d’afficher les pages d’erreur par défaut. Vous pouvez utiliser votre marque et votre mise en page personnelle à l’aide d’une page d’erreur personnalisée.

Pour plus d’informations, consultez [Réécrire les en-têtes HTTP](rewrite-http-headers.md).

## <a name="rewrite-http-headers"></a>Réécrire les en-têtes HTTP

Les en-têtes HTTP permettent au client et au serveur de passer des informations supplémentaires dans la requête ou la réponse. La réécriture de ces en-têtes HTTP vous permet d’accomplir plusieurs tâches importantes, comme :

- L’ajout de champs d’en-tête liés à la sécurité comme HSTS/ X-XSS-Protection
- La suppression de champs d’en-tête de réponse qui peuvent comprendre des informations sensibles
- La suppression des informations de port dans les en-têtes X-Forwarded-For

Application Gateway permet d’ajouter, de supprimer et de mettre à jour les en-têtes de requête et de réponse HTTP pendant le déplacement des paquets de requête et de réponse entre le pool client et le pool back-end. Il permet également d’ajouter des conditions de sorte que les en-têtes spécifiés soient réécrits uniquement lorsque certaines conditions sont remplies.

Pour plus d’informations, consultez [Réécrire les en-têtes HTTP](rewrite-http-headers.md).

## <a name="sizing"></a>Dimensionnement

Les références SKU Standard_v2 et WAF_v2 d’Application Gateway peuvent être configurées dans le cadre de déploiements avec mise à l’échelle automatique ou de taille fixe. Ces références SKU n’offrent pas de tailles d’instance différentes.

La référence SKU WAF et Standard d’Application Gateway est proposée en trois tailles : **Petit**, **Moyen** et **Grand**. Les instances de petite taille sont conçues pour les scénarios de développement et de test.

Pour obtenir la liste complète des limites de la passerelle Application Gateway, consultez la page [Application Gateway limits](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits) (Limites de la passerelle Application Gateway).

Le tableau suivant présente un débit moyen de performances pour chaque instance d’application Gateway avec le déchargement SSL activé :

| Taille moyenne de la réponse de la page principale | Petite | Moyenne | grand |
| --- | --- | --- | --- |
| 6 Ko |7,5 Mbits/s |13 Mbits/s |50 Mbits/s |
| 100 Ko |35 Mbits/s |100 Mbits/s |200 Mbits/s |

> [!NOTE]
> Ces valeurs sont des valeurs approximatives pour un débit de passerelle d’application. Le débit réel dépend de divers détails d’environnement, tels que la taille de page moyenne, l’emplacement des instances de serveur principal et le temps de traitement d’une page par le serveur. Pour des calculs de performance exacts, vous devez exécuter vos propres tests. Ces valeurs sont fournies uniquement pour vous donner des conseils de planification de la capacité.

## <a name="next-steps"></a>Étapes suivantes

Selon vos besoins et l’environnement, vous pouvez créer une passerelle Application Gateway test à l’aide du portail Azure, de Azure PowerShell ou de Azure CLI :

- [Démarrage rapide : diriger le trafic web avec Azure Application Gateway - Portail Azure](quick-create-portal.md)
- [Démarrage rapide : diriger le trafic web avec Azure Application Gateway - Azure PowerShell](quick-create-powershell.md)
- [Démarrage rapide : diriger le trafic web avec Azure Application Gateway - Azure CLI](quick-create-cli.md)