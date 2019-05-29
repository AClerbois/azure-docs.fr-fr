---
title: Présentation de la sécurité concernant Microsoft Azure Data Box | Microsoft Docs sur les données
description: Décrit les fonctionnalités de sécurité Azure Data Box sur l’appareil, le service et les données qui se trouvent sur Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 3e871b8d4de8ff83e260aded89e09ad5b17b3297
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799237"
---
# <a name="azure-data-box-security-and-data-protection"></a>Protection des données et sécurité Azure Data Box

Data Box fournit une solution sécurisée pour la protection des données en veillant à ce que seules les entités autorisées affichent, modifient ou suppriment vos données. Cet article décrit les fonctionnalités de sécurité du disque Azure Data Box qui permettent de protéger chacun des composants de la solution Data Box et les données stockées sur ces derniers. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Flux de données via des composants

La solution Microsoft Azure Data Box repose sur l’interaction de quatre composants principaux :

- **Service Azure Data Box hébergé dans Azure** : le service de gestion qui vous permet de créer la commande de l’appareil, de configurer l’appareil et de suivre la commande jusqu'à son achèvement.
- **Appareil Data Box** : appareil de transfert qui vous est livré pour importer vos données locales dans Azure. 
- **Les clients/hôtes connectés à l’appareil** : les clients de votre infrastructure se connectant à l’appareil Data Box et contenant des données devant être protégées.
- **Le stockage cloud** : emplacement dans le cloud Azure où les données sont stockées. Il s’agit généralement du compte de stockage lié à la ressource Azure Data Box que vous avez créée.

Le diagramme suivant indique le flux de données à travers la solution Azure Data Box de l’environnement local vers Azure.

![Sécurité Data Box](media/data-box-security/data-box-security-2.png)

Comme les données circulent dans cette solution, les événements sont enregistrés et des journaux sont générés. Pour plus d’informations, accédez à [Suivi et journalisation des événements pour votre Azure Data Box](data-box-logs.md).

## <a name="security-features"></a>Fonctionnalités de sécurité

Data Box fournit une solution sécurisée pour la protection des données en veillant à ce que seules les entités autorisées affichent, modifient ou suppriment vos données. Les fonctionnalités de sécurité de cette solution pour le disque et le service associé, garantissent la sécurité des données stockées sur ces derniers. 

### <a name="data-box-device-protection"></a>Protection de l’appareil Data Box

Le service Data Box est protégé par les fonctionnalités suivantes :

- Un boîtier robuste qui résiste aux chocs, aux aléas du transport et aux conditions environnementales. 
- Des scellés inviolables pour éviter toute altération de l’appareil pendant le transit.
- Un système de détection des altérations matérielles et logicielles empêchant d’autres opérations sur l’appareil.
- Exécute uniquement des logiciels spécifiques à Data Box.
- Démarre dans un état verrouillé.
- Contrôle l’accès à l’appareil via un mot de passe de déverrouillage.
- Identifiants d’accès pour copier des données vers et depuis l’appareil. Tous les accès à la page **Informations d’identification de l’appareil** dans le portail Azure sont consignés dans les [journaux d’activité](data-box-logs.md#query-activity-logs-during-setup).

### <a name="data-box-data-protection"></a>Protection des données Data Box

Les données qui circulent vers et depuis Data Box sont protégées par les fonctionnalités suivantes :

- Chiffrement AES 256 bits pour les données au repos.
- Des protocoles chiffrés peuvent être utilisés pour les données en transit.
- Effacement sécurisé des données de l’appareil une fois le chargement des données vers Azure terminé. L’effacement des données est conforme aux normes NIST 800-88r1. L’événement d’effacement de données est enregistré dans le [l’historique des commandes](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>Protection du service Data Box

Le service Data Box est protégé par les fonctionnalités suivantes.

- L’accès au service Data Box requiert que votre organisation dispose d’un abonnement Azure incluant Data Box. Votre abonnement régit les fonctionnalités auxquelles vous pouvez accéder dans le portail Azure.
- Étant donné que le service Data Box est hébergé dans Azure, il est protégé par les fonctionnalités de sécurité Azure. Pour plus d’informations sur les fonctionnalités de sécurité fournies par Microsoft Azure, accédez au [Centre de confidentialité Microsoft Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx).
- L’accès à la commande Data Box peut être contrôlé par le biais de l’utilisation des rôles de contrôle d’accès en fonction du rôle (RBAC). Pour plus d’informations, consultez [Configurer le contrôle d’accès pour la commande Data Box](data-box-logs.md#set-up-access-control-on-the-order)
- Le service Data Box stocke le mot de passe de déverrouillage utilisé pour déverrouiller l’appareil dans le service.
- Le service Data Box stocke les détails de la commande et l’état dans le service. Ces informations sont supprimées en même temps que la commande.

## <a name="managing-personal-data"></a>Gestion des données personnelles

Azure Data Box collecte et affiche des informations personnelles dans les instances principales suivantes dans le service :

- **Paramètres de notification** : quand vous créez une commande, vous configurez l’adresse e-mail des utilisateurs sous Paramètres de notification. Ces informations peuvent être affichées par l’administrateur. Ces informations sont supprimées par le service lorsque la tâche atteint l’état terminal, ou lorsque vous supprimez la commande.

- **Détails de la commande** : une fois la commande créée, l’adresse de livraison, l’adresse e-mail, et les informations de contact des utilisateurs sont stockées dans le portail Azure. Les informations enregistrées incluent :

  - Nom du contact
  - Numéro de téléphone
  - Email
  - Adresse postale
  - City
  - Code postal
  - État
  - Pays/Province/Région
  - Numéro de compte du transporteur
  - Numéro de suivi d’expédition

    Les détails de la commande sont supprimés par le service Data Box lorsque la tâche est terminée ou quand vous supprimez la commande.

- **Adresse de livraison** : une fois la commande passée, le service Data Box fournit l’adresse de livraison aux transporteurs tiers tels que UPS ou DHL. 

Pour plus d’informations, consultez la [Politique de confidentialité Microsoft sur le Centre de gestion de la confidentialité](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Référence des instructions de sécurité

Les consignes de sécurité suivantes sont implémentées dans Data Box : 

|Instruction   |Description   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Pour la protection contre l’eau et la poussière         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Pour résister à des conditions de transport défavorables          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Pour la mise à jour sécurisée du microprogramme         |
|[FIPS 140-2 niveau 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Pour la protection des données         |
|[NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Pour le nettoyage des données         |

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [Conditions requises pour le système Data Box](data-box-system-requirements.md).
- Comprendre les limites de [Data Box](data-box-limits.md).
- Déployez rapidement [Azure Data Box](data-box-quickstart-portal.md) sur le portail Azure.
