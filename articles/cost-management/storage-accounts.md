---
title: Configurer les comptes de stockage pour Azure Cost Management| Microsoft Docs
description: Cet article décrit la configuration des comptes de stockage Azure et des compartiments de stockage AWS pour Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: dab7100c97fab7e086352916ec222ec70a0d0400
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143636"
---
# <a name="configure-storage-accounts-for-cost-management"></a>Configurer des comptes de stockage pour Cost Management

<!--- intent: As a Cost Management user, I want to configure Cost Management to use my cloud service provider storage account to store my reports. -->

Vous pouvez enregistrer des rapports Cost Management dans le portail Cloudyn, dans le Stockage Azure ou dans les compartiments de stockage AWS. L’enregistrement de vos rapports sur le portail Cloudyn est gratuit. Toutefois, l’enregistrement de vos rapports sur le stockage de votre fournisseur de services cloud est facultatif et entraîne des coûts supplémentaires. Cet article vous aide à configurer des comptes de stockage Azure et des compartiments de stockage Amazon Web Services (AWS) pour stocker vos rapports.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer d’un compte de stockage Azure ou d’un compartiment de stockage Amazon.

Si vous n’avez pas de compte de stockage Azure, vous devez en créer un. Pour plus d’informations sur la création d’un compte de stockage Azure, consultez la section [Créer un compte de stockage](../storage/common/storage-quickstart-create-account.md).

Si vous ne possédez pas de compartiment de service de stockage simple AWS (S3), vous devez en créer un. Pour en savoir plus sur la création d’un compartiment S3, consultez la section [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) (Créer un compartiment).

## <a name="configure-your-azure-storage-account"></a>Configurer votre compte de stockage Azure

La configuration de votre compte de stockage Azure pour une utilisation par Cost Management est simple. Collectez des détails sur le compte de stockage, puis copiez-les dans le portail Cloudyn.

1. Connectez-vous au portail Azure sur http://portal.azure.com.
2. Cliquez sur **Tous les services**, sélectionnez **Comptes de stockage**, faites défiler jusqu’au compte de stockage que vous souhaitez utiliser, puis sélectionnez le compte.
3. Sur la page de votre compte de stockage, sous **Paramètres**, cliquez sur **Clés d’accès**.
4. Copiez vos **Nom du compte de stockage** et **Chaîne de connexion** sous key1.  
![Clés d’accès de stockage Azure](./media/storage-accounts/azure-storage-access-keys.png)  
5. Ouvrez le portail Cloudyn à partir du portail Azure ou accédez à https://azure.cloudyn.com et connectez-vous.
6. Cliquez sur le symbole de roue dentée, puis sélectionnez **Gestion des rapports de stockage**.
7. Cliquez sur **Ajouter nouveau** et assurez-vous que Microsoft Azure est sélectionné. Collez le nom de votre compte de stockage Azure dans la zone **Nom**. Collez votre **chaîne de connexion** dans la zone correspondante. Saisissez un nom de conteneur, puis cliquez sur **Enregistrer**.  
![Stockage Cloudyn configuré pour Azure](./media/storage-accounts/azure-cloudyn-storage.png)

  Votre nouvelle entrée de stockage de rapport Azure apparaît dans la liste des comptes de stockage.  
    ![Nouveau stockage de rapport Azure dans la liste](./media/storage-accounts/azure-storage-entry.png)


Vous pouvez désormais enregistrer des rapports sur le stockage Azure. Sur un rapport, cliquez sur **Actions**, puis sélectionnez **Planifier le rapport**. Nommez le rapport, puis ajoutez votre propre URL ou utilisez l’URL créée automatiquement. Sélectionnez **Enregistrer sur un support de stockage**, puis sélectionnez le compte de stockage. Entrez un préfixe qui est ajouté au nom de fichier du rapport. Sélectionnez le format de fichier CSV ou JSON, puis enregistrez le rapport.

## <a name="configure-an-aws-storage-bucket"></a>Configurer un compartiment de stockage AWS

Le portail Cloudyn utilise les informations d’identification AWS existantes : Utilisateur ou Rôle, pour enregistrer les rapports dans votre compartiment. Pour tester l’accès, Cloudyn essaie d’enregistrer un petit fichier texte dans le compartiment, portant le nom _check-bucket-permission.txt_.

Vous fournissez le rôle ou l’utilisateur Cloudyn avec l’autorisation PutObject à votre compartiment. Ensuite, utilisez un compartiment existant ou créez-en un pour enregistrer les rapports. Enfin, déterminez comment gérer la classe de stockage, définir les règles de cycle de vie ou supprimer les fichiers inutiles.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Affecter des autorisations à votre utilisateur ou rôle AWS

Lorsque vous créez une nouvelle stratégie, vous fournissez les autorisations nécessaires pour enregistrer un rapport dans un compartiment S3.

1. Connectez-vous à la console AWS, puis sélectionnez **Services** (Services).
2. Dans la liste des services, sélectionnez **IAM**.
3. Sélectionnez **Policies** (Stratégies) sur le côté gauche de la console, puis cliquez sur **Create Policy** (Créer une stratégie).
4. Cliquez sur l’onglet **JSON**.
5. La stratégie suivante vous permet d’enregistrer un rapport dans un compartiment S3. Copiez et collez l’exemple de stratégie suivant sur l’onglet **JSON**. Remplacez &lt;bucketname&gt; par votre nom de compartiment.

  ```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
}
```

6. Click **Review policy** (Vérifier la stratégie).  
    ![Review policy](./media/storage-accounts/aws-policy.png)(Vérifier la stratégie)  
7. Sur la page Review policy, saisissez un nom pour votre stratégie. Par exemple, _CloudynSaveReport2S3_.
8. Cliquez sur **Create policy** (Créer une stratégie).

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Joindre la stratégie à un rôle ou un utilisateur Cloudyn dans votre compte

Pour joindre la nouvelle stratégie, vous ouvrez la console AWS et modifiez le rôle ou l’utilisateur Cloudyn.

1. Connectez-vous à la console AWS, puis sélectionnez **Services**, puis **IAM** dans la liste des services.
2. Sélectionnez **Roles** (Rôles) ou **Users** (Utilisateurs) sur le côté gauche de la console.

**Pour les rôles :**

  1. Cliquez sur le nom de votre rôle Cloudyn.
  2. Sur l’onglet **Permissions** (Autorisations), cliquez sur **Attach Policy** (Joindre une stratégie).
  3. Recherchez la stratégie que vous avez créée, sélectionnez-la, puis cliquez sur **Attach Policy** (Joindre une stratégie).
    ![AWS - Joindre une stratégie à un rôle](./media/storage-accounts/aws-attach-policy-role.png)

**Pour les utilisateurs :**

1. Sélectionnez l’utilisateur Cloudyn.
2. Sous l’onglet **Permissions** (Autorisations), cliquez sur **Add permissions** (Ajouter des autorisations).
3. Dans la section **Grant Permission** (Accorder une autorisation), sélectionnez **Attach existing policies directly** (Joindre les stratégies existantes directement).
4. Recherchez la stratégie que vous avez créée, sélectionnez-la, puis cliquez sur **Next: Review** (Suivant : Vérification).
5. Sur la page d’ajout des autorisations à un nom de rôle, cliquez sur **Add permissions** (Ajouter des autorisations).  
    ![AWS - Joindre une stratégie à un utilisateur](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Facultatif : Définir une autorisation avec une stratégie de compartiment

Vous pouvez également définir une autorisation pour créer des rapports sur votre compartiment S3 à l’aide d’une stratégie de compartiment. Dans la vue S3 classique :

1. Créez un compartiment ou sélectionnez-en un existant.
2. Sélectionnez l’onglet **Permissions** (Autorisations), puis cliquez sur **Bucket policy** (Stratégie de compartiment).
3. Copiez et collez l’exemple de stratégie suivant. Remplacez &lt;bucket\_name&gt; et &lt;Cloudyn\_principle&gt; par l’ARN de votre compartiment. Remplacez l’ARN du rôle ou de l’utilisateur utilisés par Cloudyn.

  ```
{
  "Id": "Policy1485775646248",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
  ]
}
```

4. Dans l’éditeur de stratégie de compartiment, cliquez sur **Enregistrer**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Ajouter le stockage de rapports AWS à Cloudnyn

1. Ouvrez le portail Cloudyn à partir du portail Azure ou accédez à https://azure.cloudyn.com et connectez-vous.
2. Cliquez sur le symbole de roue dentée, puis sélectionnez **Gestion des rapports de stockage**.
3. Cliquez sur **Ajouter nouveau +** et assurez-vous qu’AWS est sélectionné.
4. Sélectionnez un compte et un compartiment de stockage. Le nom du compartiment de stockage AWS est automatiquement rempli.  
    ![Ajouter un stockage de rapport pour le compartiment AWS](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Cliquez sur **Enregistrer**, puis sur **OK**.

    Votre nouvelle entrée de stockage de rapport AWS apparaît dans la liste des comptes de stockage.  
    ![Nouveau stockage de rapport AWS dans la liste](./media/storage-accounts/aws-storage-entry.png)


Vous pouvez désormais enregistrer des rapports sur le stockage Azure. Sur un rapport, cliquez sur **Actions**, puis sélectionnez **Planifier le rapport**. Nommez le rapport, puis ajoutez votre propre URL ou utilisez l’URL créée automatiquement. Sélectionnez **Enregistrer sur un support de stockage**, puis sélectionnez le compte de stockage. Entrez un préfixe qui est ajouté au nom de fichier du rapport. Sélectionnez le format de fichier CSV ou JSON, puis enregistrez le rapport.

## <a name="next-steps"></a>Étapes suivantes

- Consultez l’article [Compréhension des rapports de gestion des coûts](understanding-cost-reports.md) pour en savoir plus sur la structure de base et les fonctions des rapports de gestion des coûts.
