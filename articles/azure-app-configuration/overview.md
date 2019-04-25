---
title: Présentation d’Azure App Configuration | Microsoft Docs
description: Vue d’ensemble du service Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: a76eab5d51dd73fb6b38ebebaa8421e789274f84
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994790"
---
# <a name="what-is-azure-app-configuration"></a>Présentation d’Azure App Configuration

Azure App Configuration offre un service de gestion centralisée des paramètres d’application. Les programmes modernes, en particulier les programmes qui s’exécutent dans un cloud, ont généralement de nombreux composants qui sont par nature distribués. La répartition des paramètres de configuration sur tous ces composants peut rendre les erreurs difficiles à corriger pendant le déploiement d’une application. Utilisez App Configuration pour stocker tous les paramètres de votre application et sécuriser leur accès dans un même endroit.

L’utilisation d’App Configuration est gratuite pendant la période de préversion. Si vous souhaitez l’essayer, [inscrivez-vous](https://aka.ms/azconfig/register) pour la préversion.

## <a name="why-use-app-configuration"></a>Pourquoi utiliser App Configuration

Souvent, les applications cloud s’exécutent sur plusieurs machines virtuelles ou conteneurs situés dans plusieurs régions et elles utilisent plusieurs services externes. La création d’une application distribuée de ce type, à la fois robuste et scalable, est un défi. 

Différentes méthodologies de programmation aident les développeurs à gérer la complexité croissante de la génération de ces applications. Par exemple, l’application 12 facteurs décrit de nombreux modèles architecturaux bien testés ainsi que les bonnes pratiques à suivre pour les applications cloud. L’une des principales recommandations de ce guide consiste à séparer la configuration du code. Dans ce cas, les paramètres de configuration d’une application doivent être conservés à l’écart de son exécutable et être lus à partir de son environnement d’exécution ou d’une source externe.

Même si n’importe quelle application peut utiliser App Configuration, les types d’applications suivants sont des exemples qui tirent parti de son utilisation :

* Microservices basés sur Azure Kubernetes Service, Service Fabric ou d’autres applications conteneurisées et déployées dans une ou plusieurs zones géographiques
* Applications serverless, notamment Azure Functions ou d’autres applications de calcul sans état basées sur les événements
* Pipeline de déploiement continu

App Configuration offre les avantages suivants :

* Service complètement managé configurable en quelques minutes
* Représentations et mappages de clés flexibles
* Marquage avec des étiquettes
* Relecture des paramètres à un point dans le temps
* Comparaison de deux jeux de configurations sur les dimensions personnalisées
* Sécurité renforcée par le biais d’identités managées par Azure
* Chiffrements complets des données au repos ou en transit
* Intégration native à des frameworks connus

App Configuration complète [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), qui est utilisé pour stocker des secrets d’application. App Configuration rend les scénarios suivants plus faciles à implémenter :

* Gestion centralisée et distribution des données de configuration hiérarchiques pour différents environnements et zones géographiques
* Modifications de configuration dynamique sans avoir à redéployer ou redémarrer une application
* Gestion des fonctionnalités

## <a name="use-app-configuration"></a>Utilisation d’App Configuration

Le moyen le plus simple d’ajouter un magasin de configuration d’application à votre application fait intervenir une bibliothèque cliente fournie par Microsoft. Voici, en fonction du langage de programmation et du framework, les meilleures méthodes mises à votre disposition.

| Langage de programmation et framework | Comment se connecter |
|---|---|
| .NET Core et ASP.NET Core | Fournisseur d’App Configuration pour .NET Core |
| .NET et ASP.NET | Générateur App Configuration pour .NET |
| Java Spring | Client App Configuration pour Spring cloud |
| Autres | API REST App Configuration |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET Core](./quickstart-aspnet-core-app.md)  
