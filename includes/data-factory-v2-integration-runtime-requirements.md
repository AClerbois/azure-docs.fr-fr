---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966353"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Si votre magasin de données est configuré de l’une des manières suivantes, vous devez configurer un [runtime d'intégration auto-hébergé](../articles/data-factory/create-self-hosted-integration-runtime.md) pour pouvoir vous connecter à ce magasin de données :

- Le magasin de données se trouve dans un réseau local, au sein d’un réseau virtuel Azure ou au sein d’un cloud privé virtuel Amazon.
- Le magasin de données est un service de données cloud managé dans lequel l’accès est limité à la liste verte d’adresses IP dans les règles de pare-feu.
