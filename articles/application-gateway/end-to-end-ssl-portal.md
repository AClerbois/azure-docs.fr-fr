---
title: Configurer le chiffrement SSL de bout en bout à l’aide du portail
titleSuffix: Azure Application Gateway
description: Découvrez comment utiliser le portail Azure pour créer une passerelle d’application avec chiffrement SSL de bout en bout.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a878b966266bdd326db35d266bc14b2f81161e92
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075139"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Configurer le chiffrement SSL de bout en bout avec Application Gateway et le portail

Cet article explique comment utiliser le portail Azure pour configurer un chiffrement SSL (Secure Sockets Layer) de bout en bout par le biais du SKU Azure Application Gateway v1.

> [!NOTE]
> Application Gateway v2 requiert des certificats racine approuvés pour permettre la configuration de bout en bout.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer le protocole SSL de bout en bout avec une passerelle d’application, vous avez besoin d’un certificat pour la passerelle. Les certificats sont également exigés pour les serveurs back-end. Le certificat de passerelle est utilisé pour dériver une clé symétrique conformément aux spécifications du protocole SSL. La clé symétrique est ensuite utilisée pour chiffrer et déchiffrer le trafic envoyé à la passerelle. 

Pour le chiffrement SSL de bout en bout, les serveurs back-end appropriés doivent être autorisés dans la passerelle d’application. Pour autoriser cet accès, chargez le certificat public des serveurs back-end, aussi appelés certificats d’authentification (v1) ou certificats racines approuvés (v2), sur la passerelle d’application. L’ajout du certificat permet à la passerelle d’application de communiquer uniquement avec des instances de serveur back-end connues. Cette configuration renforce la sécurité de la communication de bout en bout.

Pour plus d’information, consultez [Arrêt SSL et SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Créer une passerelle d’application avec SSL de bout en bout

Pour créer une passerelle d’application avec chiffrement SSL de bout en bout, vous devez d’abord activer un arrêt SSL lors de la création d’une passerelle d’application. Cette action active le chiffrement SSL pour la communication entre le client et la passerelle d’application. Ensuite, vous devez placer dans la liste des destinataires approuvés les certificats des serveurs back-end indiqués dans les paramètres HTTP. Cette configuration active le chiffrement SSL pour la communication entre la passerelle d’application et les serveurs back-end. Ainsi est effectué le chiffrement SSL de bout en bout.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Activer l’arrêt SSL lors de la création d’une passerelle d’application

Pour en savoir plus, consultez [Activer l’arrêt SSL lors de la création d’une passerelle d’application](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Ajouter des certificats d’authentification/racines des serveurs back-end

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Sélectionnez **Paramètres HTTP** dans le menu de gauche. Azure a automatiquement créé un paramètre HTTP par défaut, appelé **appGatewayBackendHttpSettings**, quand vous avez créé la passerelle d’application. 

3. Sélectionnez **appGatewayBackendHttpSettings**.

4. Sous **Protocole**, sélectionnez **HTTPS**. Un volet relatif aux **certificats d’authentification back-end ou certificats racines approuvés** s’affiche.

5. Sélectionnez **Créer nouveau**.

6. Dans le champ **Nom**, entrez un nom approprié.

7. Sélectionnez le fichier de certificat dans la zone **Charger le certificat CER**.

   Pour les passerelles d’application standard et WAF (v1), vous devez charger la clé publique de votre certificat de serveur back-end au format .cer.

   ![Ajouter un certificat](./media/end-to-end-ssl-portal/addcert.png)

   Pour les passerelles d’application Standard_v2 et WAF_v2, vous devez charger le certificat racine du certificat de serveur back-end au format .cer. Si le certificat back-end est émis par une autorité de certification connue, vous pouvez cocher la case **Utiliser le certificat de l’autorité de certification connue** et vous n’avez pas besoin de charger un certificat.

   ![Ajouter un certificat racine approuvé](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certificat racine](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Sélectionnez **Enregistrer**.

## <a name="enable-end-to-end-ssl-for-an-existing-application-gateway"></a>Activer le protocole SSL de bout en bout pour une passerelle d’application existante

Pour configurer une passerelle d’application existante avec le chiffrement SSL de bout en bout, vous devez d’abord activer un arrêt SSL sur l’écouteur. Cette action active le chiffrement SSL pour la communication entre le client et la passerelle d’application. Ensuite, placez ces certificats pour les serveurs back-end dans les paramètres HTTP, dans la liste des destinataires approuvés. Cette configuration active le chiffrement SSL pour la communication entre la passerelle d’application et les serveurs back-end. Ainsi est effectué le chiffrement SSL de bout en bout.

Vous devez utiliser un écouteur avec le protocole HTTPS et un certificat pour activer l’arrêt SSL. Vous pouvez soit utiliser un écouteur existant qui remplit ces conditions, soit créer un écouteur. Si vous choisissez la première option, vous pouvez ignorer la section « Activer l’arrêt SSL dans une passerelle d’application existante » pour passer directement à la section « Ajouter des certificats d’authentification/racines approuvés pour des serveurs back-end ».

Si vous choisissez la seconde option, appliquez les étapes de la procédure suivante.
### <a name="enable-ssl-termination-in-an-existing-application-gateway"></a>Activer l’arrêt SSL dans une passerelle d’application existante

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Dans le menu de gauche, sélectionnez **Écouteurs**.

3. Sélectionnez un écouteur **de base** ou **multisite** en fonction de vos besoins.

4. Sous **Protocole**, sélectionnez **HTTPS**. Un volet de **certificat** s’affiche.

5. Chargez le certificat PFX que vous souhaitez utiliser pour l’arrêt SSL entre le client et la passerelle d’application.

   > [!NOTE]
   > Dans le cadre d’un test, vous pouvez utiliser un certificat auto-signé. Toutefois, ces derniers ne sont pas recommandés pour les charges de travail de production, car elles sont plus difficiles à gérer et ne sont pas entièrement sécurisées. Pour plus d’informations, consultez [Créer un certificat auto-signé](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Ajoutez les autres paramètres exigés pour l’**écouteur** en fonction de vos besoins.

7. Sélectionnez **OK** pour enregistrer.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Ajouter des certificats racines d’authentification/approuvés pour les serveurs back-end

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Sélectionnez **Paramètres HTTP** dans le menu de gauche. Vous pouvez soit placer des certificats dans un paramètre HTTP de back-end existant dans la liste des destinataires approuvés, soit créer un paramètre HTTP. (À l’étape suivante, le certificat du paramètre HTTP par défaut, **appGatewayBackendHttpSettings**, est ajouté à la liste des destinataires approuvés.)

3. Sélectionnez **appGatewayBackendHttpSettings**.

4. Sous **Protocole**, sélectionnez **HTTPS**. Un volet relatif aux **certificats d’authentification back-end ou certificats racines approuvés** s’affiche. 

5. Sélectionnez **Créer nouveau**.

6. Dans le champ **Nom**, entrez un nom approprié.

7. Sélectionnez le fichier de certificat dans la zone **Charger le certificat CER**.

   Pour les passerelles d’application standard et WAF (v1), vous devez charger la clé publique de votre certificat de serveur back-end au format .cer.

   ![Ajouter un certificat](./media/end-to-end-ssl-portal/addcert.png)

   Pour les passerelles d’application Standard_v2 et WAF_v2, vous devez charger le certificat racine du certificat de serveur back-end au format .cer. Si le certificat back-end est émis par une autorité de certification connue, vous pouvez cocher la case **Utiliser le certificat de l’autorité de certification connue** et vous n’avez pas besoin de charger un certificat.

   ![Ajouter un certificat racine approuvé](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer le trafic web avec une passerelle d’application à l’aide d’Azure CLI](./tutorial-manage-web-traffic-cli.md)
