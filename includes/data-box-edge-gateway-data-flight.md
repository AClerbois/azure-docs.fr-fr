---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "67120557"
---
Pour les données à la volée :

- Standard TLS 1.2 est utilisé pour les données qui transitent entre l’appareil et Azure. Il n’existe aucun protocole de secours pour TLS 1.1 et versions antérieures. Les communications de l’agent seront bloquées si TLS 1.2 n’est pas pris en charge. TLS 1.2 est également requis pour la gestion du portail et du Kit de développement logiciel (SDK).
- Lorsque les clients accèdent à votre appareil via l’interface utilisateur web locale d’un navigateur, Standard TLS 1.2 est utilisé comme protocole sécurisé par défaut.

    - La meilleure pratique consiste à configurer votre navigateur pour utiliser TLS 1.2.
    - Si le navigateur ne prend pas en charge TLS 1.2, vous pouvez utiliser TLS 1.1 ou TLS 1.0.
- Nous vous recommandons d’utiliser SMB 3.0 avec chiffrement pour protéger les données lorsque vous les copiez depuis vos serveurs de données.
