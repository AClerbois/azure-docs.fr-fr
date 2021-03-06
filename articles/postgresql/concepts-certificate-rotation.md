---
title: Rotation des certificats pour le serveur unique Azure Database pour PostgreSQL
description: En savoir plus sur les modifications à venir dues aux changements de certificat racine qui affectent le serveur unique Azure Database pour PostgreSQL
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 5b46dc0197022aa72b6ca4c206d1da4369234167
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707677"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>Comprendre les modifications liées au changement d’autorité de certification racine pour le serveur unique Azure Database pour PostgreSQL

Azure Database pour PostgreSQL va modifier le certificat racine d’application cliente/de pilote activé avec SSL et que vous utilisez pour [vous connecter au serveur de base de données](concepts-connectivity-architecture.md). Le certificat racine actuellement disponible est configuré pour expirer le 26 octobre 2020 (26/10/2020) dans le cadre de la maintenance standard et conformément aux meilleures pratiques de sécurité. Cet article vous donne plus de détails sur les modifications à venir, les ressources qui seront affectées et les étapes nécessaires pour garantir que votre application maintient sa connectivité à votre serveur de base de données.

## <a name="what-update-is-going-to-happen"></a>Quelle mise à jour va se produire ?

Dans certains cas, les applications utilisent un fichier de certificat local généré à partir du fichier de certificat d’une autorité de certification approuvée pour se connecter de façon sécurisée. Actuellement, les clients peuvent utiliser uniquement le certificat prédéfini pour se connecter à un serveur Azure Database pour PostgreSQL, qui est situé [ici](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Toutefois, le [forum Certificate Authority (CA) Browser](https://cabforum.org/) a récemment publié des rapports indiquant que plusieurs certificats émis par les fournisseurs d’autorité de certification n’étaient pas conformes.

Conformément aux exigences de conformité du secteur, les fournisseurs d’autorité de certification ont commencé à révoquer les certificats d’autorité de certification non conformes, en demandant aux serveurs d’utiliser des certificats émis par des autorités de certification conformes et signés par des certificats d’autorité de certification provenant de ces dernières. Étant donné qu’Azure Database pour PostgreSQL utilise actuellement l’un de ces certificats non conformes utilisés par les applications clientes pour valider leurs connexions SSL, nous devons veiller à ce que les actions appropriées soient effectuées (décrites ci-dessous) afin de réduire au minimum l’impact potentiel sur vos serveurs PostgreSQL.

Le nouveau certificat sera utilisé à partir du 26 octobre 2020 (26/10/2020). Si vous utilisez la validation de l’autorité de certification ou la validation complète du certificat de serveur lors de la connexion à partir d’un client PostgreSQL (sslmode=verify-ca ou sslmode=verify-full), vous devez mettre à jour la configuration de votre application avant le 26 octobre 2020 (26/10/2020).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Comment savoir si ma base de données va être affectée ?

Toutes les applications qui utilisent SSL/TLS et vérifient le certificat racine doivent mettre à jour le certificat racine. Vous pouvez déterminer si vos connexions vérifient le certificat racine en examinant votre chaîne de connexion.
-   Si votre chaîne de connexion inclut `sslmode=verify-ca` ou `sslmode=verify-full`, vous devez mettre à jour le certificat.
-   Si votre chaîne de connexion inclut `sslmode=disable`, `sslmode=allow`, `sslmode=prefer` ou `sslmode=require`, vous n’avez pas besoin de mettre à jour les certificats. 
-   Si votre chaîne de connexion ne spécifie pas sslmode, vous n’avez pas besoin de mettre à jour les certificats.

Si vous utilisez un client qui abstrait la chaîne de connexion, consultez la documentation du client pour savoir s’il vérifie les certificats.

Pour comprendre PostgreSQL sslmode, consultez les [descriptions du mode SSL](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) dans la documentation PostgreSQL.

Pour éviter toute interruption de la disponibilité de votre application en raison de la révocation inattendue de certificats ou pour mettre à jour un certificat qui a été révoqué, reportez-vous à la section [**Que dois-je faire pour maintenir la connectivité**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity).

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Procédure à suivre pour maintenir la connectivité

Pour éviter toute interruption de la disponibilité de votre application en raison de la révocation inattendue de certificats ou pour mettre à jour un certificat qui a été révoqué, suivez les étapes ci-dessous. L’idée est de créer un fichier *.pem*, qui combine le certificat actuel et le nouveau, et pendant la validation du certificat SSL, une des valeurs autorisées sera utilisée. Reportez-vous aux étapes ci-dessous :

*   Téléchargez l’autorité de certification racine BaltimoreCyberTrustRootDigiCertGlobalRootG2 à partir des liens ci-dessous :
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Générez un magasin de certificats d’autorité de certification combiné où les certificats **BaltimoreCyberTrustRoot** et **DigiCertGlobalRootG2** sont inclus.
    *   Si vous utilisez Java (PostgreSQL JDBC) avec DefaultJavaSSLFactory, exécutez :

          ```azurecli-interactive
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Remplacez ensuite le fichier de magasin de clés d’origine par le nouveau fichier généré :
        *   System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file"); 
        *   System.setProperty("javax.net.ssl.trustStorePassword","password");
        
    *   Si vous utilisez .NET (Npgsql) sous Windows, assurez-vous que **Baltimore CyberTrust Root** et **DigiCert Global Root G2** existent dans le magasin de certificats Windows et dans les autorités de certification racines de confiance. S’il n’existe aucun certificat, importez le certificat manquant.

        ![Certificat .net Azure Database pour PostgreSQL](media/overview/netconnecter-cert.png)

    *   Si vous utilisez .NET (Npgsql) sur Linux avec SSL_CERT_DIR, assurez-vous que **BaltimoreCyberTrustRoot** et **DigiCertGlobalRootG2** existent tous deux dans le répertoire indiqué par SSL_CERT_DIR. Si des certificats n’existent pas, créez le fichier de certificat manquant.

    *   Les autres utilisateurs du client PostgreSQL peuvent fusionner deux fichiers de certificat d’autorité de certification comme ci-dessous

        </br>-----BEGIN CERTIFICATE-----
 </br>(Root CA1: BaltimoreCyberTrustRoot.crt.pem)
 </br>-----END CERTIFICATE-----
 </br>-----BEGIN CERTIFICATE-----
 </br>(Root CA2: DigiCertGlobalRootG2.crt.pem)
 </br>-----END CERTIFICATE-----

*   Remplacez le fichier .pem d’origine de l’autorité de certification racine par le fichier d’autorité de certification racine combiné et redémarrez votre application/client.
*   Ensuite, après le déploiement du nouveau certificat côté serveur, vous pouvez remplacer votre fichier .pem d’autorité de certification par DigiCertGlobalRootG2.crt.pem.

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>Quel peut être l’impact de l’absence de mise à jour du certificat ?
Si vous utilisez le certificat racine CyberTrust pour vérifier la connexion SSL pour Azure Database pour PostgreSQL comme indiqué ici, la disponibilité de votre application peut être interrompue, car la base de données ne sera pas accessible. Selon votre application, vous pouvez recevoir différents messages d’erreur, notamment :
*   Certificat non valide/certificat révoqué
*   Délai de connexion dépassé

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Si je n’utilise pas SSL/TLS, dois-je quand même mettre à jour l’autorité de certification racine ?
Aucune action n’est requise si vous n’utilisez pas SSL/TLS. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Si j’utilise SSL/TLS, dois-je redémarrer mon serveur de base de données pour mettre à jour l’autorité de certification racine ?
Non, vous n’avez pas besoin de redémarrer le serveur de base de données pour commencer à utiliser le nouveau certificat. Il s’agit d’une modification côté client et les connexions clientes entrantes doivent utiliser le nouveau certificat pour s’assurer qu’elles peuvent se connecter au serveur de base de données.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. Que se passe-t-il si je ne mets pas à jour le certificat racine avant le 26 octobre 2020 (26/10/2020) ?
Si vous ne mettez pas à jour le certificat racine avant le 26 octobre 2020, vos applications qui se connectent via SSL/TLS et effectuent une vérification pour le certificat racine ne pourront pas communiquer avec le serveur de base de données PostgreSQL et l’application rencontrera des problèmes de connectivité avec ce serveur.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. Quel est l’impact de l’utilisation d’App Service avec Azure Database pour PostgreSQL ?
Pour les services Azure App Service qui se connectent à Azure Database pour PostgreSQL, deux scénarios sont possibles et dépendent de la façon dont vous utilisez SSL avec votre application.
*   Ce nouveau certificat a été ajouté à App Service au niveau de la plateforme. Si vous utilisez les certificats SSL inclus sur la plateforme App Service dans votre application, aucune action n’est nécessaire.
*   Si vous incluez explicitement le chemin d’accès au fichier de certificat SSL dans votre code, vous devez télécharger le nouveau certificat et mettre à jour le code de façon à ce qu’il utilise le nouveau certificat.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. Quel est l’impact de l’utilisation d’Azure Kubernetes Services (AKS) avec Azure Database pour PostgreSQL ?
Si vous essayez de vous connecter au serveur Azure Database pour PostgreSQL à l’aide d’Azure Kubernetes Services (AKS), cela est similaire à un accès à partir d’un environnement d’hôte de clients dédié. Reportez-vous aux étapes indiquées [ici](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. Quel est l’impact de l’utilisation d’Azure Data Factory pour se connecter à Azure Database pour PostgreSQL ?
Le connecteur qui utilise Azure Integration Runtime exploite les certificats du magasin de certificats Windows dans l’environnement hébergé par Azure. Comme ces certificats sont déjà compatibles avec les certificats récemment appliqués, aucune action n’est nécessaire.

Pour le connecteur utilisant le runtime d’intégration auto-hébergé où vous incluez explicitement le chemin d’accès au fichier de certificat SSL dans votre chaîne de connexion, vous devez télécharger le [nouveau certificat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) et mettre à jour la chaîne de connexion de façon à ce qu’elle l’utilise.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. Dois-je prévoir un temps d’arrêt lié à la maintenance du serveur de base de données pour effectuer cette modification ?
Non. Étant donné que la modification est uniquement côté client pour la connexion au serveur de base de données, aucun temps d’arrêt lié à la maintenance n’est nécessaire pour le serveur de base de données lors de cette modification.

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>8.  Que se passe-t-il si je ne peux pas prévoir un temps d’arrêt planifié pour cette modification avant le 26 octobre 2020 (26/10/2020) ?
Étant donné que les clients utilisés pour la connexion au serveur doivent mettre à jour les informations de certificat comme décrit dans la section de correction disponible [ici](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity), aucun temps d’arrêt n’est requis pour le serveur dans ce cas.

### <a name="9-if-i-create-a-new-server-after-october-26-2020-will-i-be-impacted"></a>9. Si je crée un serveur après le 26 octobre 2020, celui-ci sera-t-il affecté ?
Pour les serveurs créés après le 26 octobre 2020 (26/10/2020), vous pouvez utiliser le certificat récemment émis pour que vos applications se connectent à l’aide de SSL.

### <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10. Quelle est la fréquence à laquelle Microsoft met à jour ses certificats ou quelle est la stratégie d’expiration ?
Les certificats utilisés par Azure Database pour PostgreSQL sont fournis par des autorités de certification approuvées. Par conséquent, la prise en charge de ces certificats sur Azure Database pour PostgreSQL est liée à la prise en charge de ces certificats par l’autorité de certification. Toutefois, comme dans le cas présent, il peut y avoir des bogues imprévus dans ces certificats prédéfinis et ceux-ci doivent être corrigés au plus tôt.

### <a name="11-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-the-read-replicas"></a>11. Si j’utilise des réplicas en lecture, dois-je effectuer cette mise à jour uniquement sur le serveur maître ou sur les réplicas de lecture ?
Étant donné que cette mise à jour est une modification côté client, si le client avait l’habitude de lire les données du serveur de réplica, vous devez également appliquer les modifications pour ces clients. 

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. Existe-t-il une requête côté serveur pour vérifier si SSL est utilisé ?
Pour vérifier si vous utilisez une connexion SSL pour vous connecter au serveur, consultez [Vérification SSL](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity).

### <a name="13-what-if-i-have-further-questions"></a>13. Que se passe-t-il si j’ai d’autres questions ?
Si vous avez des questions, posez-les aux experts de la communauté dans [Microsoft Q&A](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com). Si vous avez un plan de support et que vous avez besoin d’une aide technique, [contactez-nous](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)