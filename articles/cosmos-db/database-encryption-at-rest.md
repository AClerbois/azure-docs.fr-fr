---
title: Chiffrement au repos dans Azure Cosmos DB
description: Découvrez comment Azure Cosmos DB assure le chiffrement des données au repos, et comment s’effectue son implémentation.
author: markjbrown
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: a9e89336973b0b13544c5bc0bccec41652c6952e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755100"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Chiffrement des données dans Azure Cosmos DB 

Le chiffrement au repos est une expression qui se réfère en général au chiffrement des données situées sur des appareils de stockage non volatile, comme des disques SSD et des lecteurs de disques durs. Cosmos DB stocke ses bases de données primaires sur des SSD. Ses pièces jointes multimédias et ses sauvegardes sont stockées dans le Stockage Blob Azure, qui est généralement sauvegardé sur des disques durs. Avec l’apparition du chiffrement au repos pour Cosmos DB, toutes vos bases de données, pièces jointes multimédias et sauvegardes sont maintenant chiffrées. Vos données sont désormais chiffrées pendant le transit (sur le réseau) et au repos (stockage non volatile), ce qui vous donne un chiffrement de bout en bout.

Cosmos DB, en tant que service PaaS, est très facile à utiliser. Comme toutes les données utilisateur stockées dans Cosmos DB sont chiffrées au repos et lors du transport, vous n’avez aucune action à effectuer. En d’autres termes, le chiffrement au repos est « activé » par défaut. Il n’existe aucun contrôle à activer ou à désactiver. Azure Cosmos DB utilise le chiffrement AES-256 dans toutes les régions où le compte est exécuté. Nous fournissons cette fonctionnalité dans le cadre de nos efforts pour respecter notre [contrat SLA de disponibilité et de performances](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implémentation du chiffrement au repos pour Azure Cosmos DB

Le chiffrement au repos est implémenté à l’aide d’un certain nombre de technologies de sécurité, notamment des systèmes de stockage de clés sécurisés, des réseaux chiffrés et des API de chiffrement. Les systèmes qui déchiffrent et traitent les données doivent communiquer avec les systèmes de gestion de clés. Le schéma montre la façon dont le stockage des données chiffrées et la gestion des clés sont séparés. 

![Schéma montrant la conception](./media/database-encryption-at-rest/design-diagram.png)

Le flux de base d’une demande d’un utilisateur est le suivant :
- Le compte de base de données de l’utilisateur est préparé et les clés de stockage sont récupérées via une demande au fournisseur de ressources du service de gestion.
- Un utilisateur crée une connexion à Cosmos DB via le transport HTTPS/sécurisé. (Les SDK permettent de s’abstraire des détails.)
- L’utilisateur envoie un document JSON à stocker sur la connexion sécurisée créée précédemment.
- Le document JSON est indexé, à moins que l’utilisateur ait désactivé l’indexation.
- Le document JSON et les données d’index sont écrits dans le stockage sécurisé.
- Périodiquement, les données sont lues à partir du stockage sécurisé et sauvegardées dans le magasin d’objets blob chiffré d’Azure.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>Q : Quel est le coût supplémentaire du stockage Azure si la fonctionnalité Storage Service Encryption est activée ?
R : Aucun coût supplémentaire n’est facturé.

### <a name="q-who-manages-the-encryption-keys"></a>Q : Qui gère les clés de chiffrement ?
R : Les clés sont gérées par Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>Q : À quelle fréquence les clés de chiffrement tournent-elles ?
R : Microsoft a constitué un ensemble de règles internes pour la rotation des clés de chiffrement, celles-ci sont suivies par Cosmos DB. Les règles spécifiques ne sont pas publiées. Microsoft publie le [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), qui est vu comme un sous-ensemble de règles internes et qui contient des bonnes pratiques utiles pour les développeurs.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Q : Puis-je utiliser mes propres clés de chiffrement ?
R : Cosmos DB est un service PaaS, et nous nous sommes efforcés de le rendre facile à utiliser. Nous avons constaté que cette question est souvent posée en rapport avec la conformité à des normes comme PCI-DSS. Dans le cadre du développement de cette fonctionnalité, nous avons collaboré avec des auditeurs de conformité pour garantir que les clients utilisant Cosmos DB répondent à leurs exigences sans devoir gérer les clés eux-mêmes.

### <a name="q-what-regions-have-encryption-turned-on"></a>Q : Dans quelles régions le chiffrement est-il activé ?
R : Le chiffrement est activé dans toutes les régions Azure Cosmos DB pour l’ensemble des données utilisateur.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Q : Le chiffrement modifie-t-il les SLA en termes de débit et de latence des performances ?
R : L’activation du chiffrement au repos sur tous les comptes (existants et nouveaux) ne modifie en rien les SLA en termes de performance. Pour en savoir plus sur les dernières garanties, consultez la page [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db).

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Q : L’émulateur local prend-il en charge le chiffrement au repos ?
R : L’émulateur est un outil de développement/de test autonome ; il n’exploite pas les services de gestion de clés que le service Cosmos DB managé utilise. Nous vous conseillons d’activer BitLocker sur les lecteurs où vous stockez des données de test d’émulateur sensibles. [L’émulateur prend en charge la modification du répertoire de données par défaut](local-emulator.md) ainsi que l’utilisation d’un emplacement connu.

## <a name="next-steps"></a>Étapes suivantes

Pour une vue d’ensemble de la sécurité de Cosmos DB et des dernières améliorations, consultez [Sécurité de la base de données Azure Cosmos](database-security.md).
Pour plus d’informations sur les certifications Microsoft, consultez le [Centre de confidentialité Azure](https://azure.microsoft.com/support/trust-center/).
