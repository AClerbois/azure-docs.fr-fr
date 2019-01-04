---
title: Connecter un service Azure App Service existant à Azure Database pour MySQL
description: Instructions pour connecter correctement un service Azure App Service existant à une base de données Azure pour MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: eb2fee7c76bcf29aee2dcd70d7975d7631bb23f6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548950"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Connecter un service Azure App Service existant à un serveur de base de données Azure pour MySQL
Cette rubrique explique comment connecter un service Azure App Service existant à votre serveur de base de données Azure pour MySQL.

## <a name="before-you-begin"></a>Avant de commencer
Connectez-vous au [portail Azure](https://portal.azure.com). Créez un serveur de base de données Azure pour MySQL. Pour plus d’informations, reportez-vous à [Guide pratique pour créer un serveur de base de données Azure pour MySQL à partir du portail](quickstart-create-mysql-server-database-using-azure-portal.md) ou à [Guide pratique pour créer un serveur de base de données Azure pour MySQL à l’aide de l’interface CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Il existe actuellement deux solutions pour activer l’accès à partir d’un service Azure App Service vers une base de données Azure pour MySQL. Ces deux solutions impliquent de configurer des règles de pare-feu au niveau du serveur.

## <a name="solution-1---allow-azure-services"></a>Solution 1 - Autoriser les services Azure
La base de données Azure pour MySQL fournit une sécurité d’accès à l’aide d’un pare-feu afin de protéger vos données. Lors de la connexion d’un service Azure App Service à une base de données Azure pour MySQL, gardez à l’esprit que les adresses IP sortantes d’App Service sont dynamiques par nature. Choisir l’option « Autoriser l’accès aux services Azure » permet au service d’application de se connecter au serveur MySQL.

1. Dans le panneau du serveur MySQL, sous le titre Paramètres, cliquez sur **Sécurité de la connexion** pour ouvrir le panneau Sécurité de la connexion pour la base de données Azure pour MySQL.

   ![Portail Azure - cliquez sur Sécurité des connexions](./media/howto-connect-webapp/1-connection-security.png)

2. Sélectionnez **ON** dans **Autoriser l’accès aux services Azure**, puis **Enregistrer**.
   ![Portail Azure - Autoriser l’accès à Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Solution 2 - Créer une règle de pare-feu pour autoriser explicitement des adresses IP sortantes
Vous pouvez ajouter explicitement toutes les adresses IP sortantes de votre service Azure App Service.

1. Dans le panneau Propriétés d’App Service, affichez votre **ADRESSE IP SORTANTE**.

   ![Portail Azure - Afficher des adresses IP sortantes](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. Dans le panneau Sécurité de la connexion de MySQL, ajoutez une par une des adresses IP sortantes.

   ![Portail Azure - Ajouter des adresses IP explicites](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. N’oubliez pas d’**Enregistrer** vos règles de pare-feu.

Bien que le service Azure App Service tente de maintenir les adresses IP constantes au fil du temps, il existe des cas où les adresses IP peuvent changer. Par exemple, cela peut se produire quand l’application est recyclée, qu’une opération de mise à l’échelle se produit ou que de nouveaux ordinateurs sont ajoutés dans des centres de données régionaux Azure pour accroître la capacité. Quand les adresses IP changent, l’application peut subir des temps d’arrêt s’il ne peut plus se connecter au serveur MySQL. Gardez cet élément de considération à l’esprit au moment de choisir l’une des solutions précédentes.

## <a name="ssl-configuration"></a>Configuration SSL
SSL est activé par défaut dans la base de données Azure pour MySQL. Si votre application n’utilise pas le protocole SSL pour se connecter à la base de données, vous devez le désactiver sur le serveur MySQL. Pour plus d’informations sur la configuration de SSL, consultez [Utilisation de SSL avec une base de données Azure pour MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les chaînes de connexion, reportez-vous à [Chaînes de connexions](howto-connection-string.md).
