---
title: Créer une machine virtuelle Linux SQL Server 2017 dans Azure | Microsoft Docs
description: Ce didacticiel montre comment créer une machine virtuelle Linux SQL Server 2017 dans le portail Azure.
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 12/5/2018
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 4087cd93b7342958717a20a08832daba1123417f
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330736"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Approvisionnement d’une machine virtuelle Linux SQL Server dans le portail Azure

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

Dans ce guide de démarrage rapide, vous utilisez le portail Azure pour créer une machine virtuelle Linux dans laquelle est installé SQL Server 2017.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

* [Créer une machine virtuelle Linux SQL à partir de la galerie](#create)
* [Se connecter à la nouvelle machine virtuelle avec SSH](#connect)
* [Modifier le mot de passe d’administrateur](#password)
* [Configurer des connexions à distance](#remote)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a id="create"></a> Créer une machine virtuelle Linux dans laquelle est installée SQL Server

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Dans le volet de gauche, sélectionnez **Créer une ressource**.

1. Dans le volet **Créer une ressource**, sélectionnez **Calcul**.

1. Sélectionnez **Tout afficher** à côté du titre **Sélection**.

   ![Afficher toutes les images de machines virtuelles](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Dans la zone de recherche, tapez **SQL Server 2017**, puis sélectionnez **Entrée** pour démarrer la recherche.

1. Limitez les résultats de recherche en sélectionnant **Système d’exploitation** > **Redhat**. Ensuite, sous **Éditeur**, choisissez **Microsoft**.

    ![En filtrant, chercher les images de machines virtuelles SQL Server 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Sélectionnez une image Linux SQL Server 2017 dans les résultats de la recherche. Ce tutoriel utilise **Licence gratuite SQL Server : SQL Server 2017 Developer sur Red Hat Enterprise Linux 7.4**.

   > [!TIP]
   > L’édition Developer vous permet de tester ou de développer en utilisant les fonctionnalités de l’édition Enterprise, sans les coûts liés à une licence SQL Server. Vous payez uniquement pour le coût d’exécution de la machine virtuelle Linux.

1. Sous **Sélectionner un modèle de déploiement**, choisissez un modèle qui correspond à vos besoins en charge de travail.

    > [!Note]
    > Pour les nouvelles charges de travail, utilisez **Resource Manager**. Pour vous connecter à un réseau virtuel existant, sélectionnez la méthode de déploiement du réseau virtuel pour votre charge de travail. Pour plus d’informations sur les modèles de déploiement, consultez [Modèles de déploiement Azure Resource Manager et modèles de déploiement classique](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

1. Sélectionnez **Créer**.

### <a name="set-up-your-linux-vm"></a>Configurer votre machine virtuelle Linux

1. Sous l’onglet **De base**, sélectionnez votre **Abonnement** et votre **Groupe de ressources**. 

    ![Fenêtre Bases](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. Dans **Nom de la machine virtuelle**, indiquez le nom de votre nouvelle machine virtuelle Linux.
1. Ensuite, tapez ou sélectionnez les valeurs suivantes :
    * **Région** : sélectionnez la région Azure qui vous convient.
    * **Options de disponibilité** : choisissez l’option de redondance et de disponibilité qui convient le mieux à vos applications et données.
    * **Modifier la taille** : sélectionnez cette option pour choisir une taille de machine ; lorsque cela est fait, choisissez **Sélectionner**. Pour en savoir plus sur les tailles de machines virtuelles, consultez [Tailles de machine virtuelle Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

    ![Choisir une taille de machine virtuelle](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Pour des tests fonctionnels et de développement, utilisez une taille de machine virtuelle **DS2** ou supérieure. Pour des tests de performances, utilisez **DS13** ou supérieure.

    * **Type d'authentification** : sélectionnez **Clé publique SSH**.

    > [!Note]
    > Vous pouvez choisir d’utiliser une clé publique SSH ou un mot de passe pour l’authentification. L’utilisation d’une clé SSH est plus sécurisée. Pour savoir comment générer une clé SSH, consultez [Créer des clés SSH sur Linux et Mac pour les machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

    * **Nom d’utilisateur** : indiquez le nom d’administrateur pour la machine virtuelle.
    * **Clé publique SSH** : entrez votre clé publique RSA.
    * **Ports d’entrée publics** : sélectionnez **Autoriser les ports sélectionnés** et choisissez le port **SSH (22)** dans la liste **Sélectionner les ports d’entrée publics**. Dans ce guide de démarrage rapide, cette étape est nécessaire pour établir une connexion à SQL Server et terminer la configuration. Si vous souhaitez vous connecter à distance à SQL Server, sélectionnez également **MS SQL (1433)** afin d’ouvrir le port 1433 pour les connexions sur Internet.

   ![Ports entrants](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Apportez les modifications que vous souhaitez aux paramètres des onglets supplémentaires suivants ou conservez les paramètres par défaut.
    * **Disques**
    * **Mise en réseau**
    * **Gestion**
    * **Configuration de l’invité**
    * **Balises**

1. Sélectionnez **Revoir + créer**.
1. Dans le volet **Vérifier + créer**, sélectionnez **Créer**.

## <a id="connect"></a> Se connecter à la machine virtuelle Linux

Si vous utilisez déjà un interpréteur de commandes BASH, connectez-vous à la machine virtuelle Azure à l’aide de la commande **SSH**. Dans la commande suivante, remplacez le nom d’utilisateur et l’adresse IP de la machine virtuelle pour vous connecter à votre machine virtuelle Linux.

```bash
ssh azureadmin@40.55.55.555
```

Vous pouvez trouver l’adresse IP de votre machine virtuelle dans le portail Azure.

![Adresse IP dans le portail Azure](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Si vous êtes sous Windows et que vous n’avez pas d’interpréteur de commandes BASH, installez un client SSH, tel que PuTTY.

1. [Téléchargez et installez PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Exécutez PuTTY.

1. À l’écran de configuration de PuTTY, entrez l’adresse IP publique de votre machine virtuelle.

1. Sélectionnez **Open** (Ouvrir) et entrez votre nom d’utilisateur et votre mot de passe lorsque vous y êtes invité.

Pour en savoir plus la connexion aux machines virtuelles Linux, consultez [Création d’une machine virtuelle Linux sur Azure à l’aide du portail](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm).

> [!Note]
> Si vous voyez une alerte de sécurité PuTTY relative à la clé d’hôte du serveur non mise en cache dans le Registre, choisissez parmi les options suivantes. Si vous faites confiance à cet ordinateur hôte, sélectionnez **Yes** (Oui) pour ajouter la clé à la mémoire cache de PuTTy et poursuivre la connexion. Si vous souhaitez vous connecter une seule fois, sans ajouter la clé dans le cache, sélectionnez **No** (Non). Si vous ne faites pas confiance à cet ordinateur hôte, sélectionnez **Cancel** (Annuler) pour abandonner la connexion.

## <a id="password"></a> Modifier le mot de passe d’administrateur

La nouvelle machine virtuelle installe SQL Server avec un mot de passe d’administrateur aléatoire. Réinitialisez ce mot de passe pour vous connecter à SQL Server avec les informations d’administrateur.

1. Une fois connecté à votre machine virtuelle Linux, ouvrez un nouveau terminal de commande.

1. Modifiez le mot de passe d’administrateur avec les commandes suivantes :

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Entrez un nouveau mot de passe d’administrateur et confirmez-le lorsque vous y êtes invité.

1. Redémarrez le service SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Ajoutez les outils à votre chemin (facultatif)

Plusieurs [packages](sql-server-linux-virtual-machines-overview.md#packages) SQL Server sont installés par défaut, y compris le package d’outils de lignes de commandes SQL Server. Le package contient les outils **sqlcmd** et **bcp**. Pour des questions pratiques, vous pouvez ajouter le chemin des outils, `/opt/mssql-tools/bin/`, à la variable d’environnement **CHEMIN** (facultatif).

1. Exécutez les commandes suivantes pour modifier le **CHEMIN** des sessions de connexion et des sessions interactives :

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a> Configurer des connexions à distance

Si vous avez besoin de vous connecter à distance à SQL Server sur la machine virtuelle Azure, vous devez configurer une règle entrante sur le groupe de sécurité réseau. La règle autorise le trafic sur le port qu’écoute SQL Server (port 1433 par défaut). Les étapes suivantes montrent comment utiliser le portail Azure.

> [!TIP]
> Si vous avez sélectionné le port entrant **MS SQL (1433)** dans les paramètres durant le provisionnement, ces changements ont été apportés pour vous. Vous pouvez passer à la section suivante pour configurer le pare-feu.

1. Dans le portail, sélectionnez **Machines virtuelles**, puis sélectionnez votre machine virtuelle SQL Server.
1. Dans le volet de navigation gauche, sous **Paramètres**, sélectionnez **Mise en réseau**.
1. Dans la fenêtre Mise en réseau, sous **Règles des ports d’entrée**, sélectionnez **Ajouter une règle de port d’entrée**.

   ![Règles des ports d’entrée](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. Dans la liste **Service**, sélectionnez **MS SQL**.

    ![Règle de groupe de sécurité réseau MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Cliquez sur **OK** pour enregistrer la règle pour votre machine virtuelle.

### <a name="open-the-firewall-on-rhel"></a>Ouvrez les ports du pare-feu sur RHEL

Ce didacticiel vous a montré comment créer une machine virtuelle Linux Red Hat Enterprise (RHEL). Si vous voulez vous connecter à distance aux machines virtuelles RHEL, vous devez aussi ouvrir le port 1433 du pare-feu Linux.

1. [Connectez-vous](#connect) à votre machine virtuelle RHEL.

1. Dans l’interpréteur de commandes BASH, exécutez les commandes suivantes :

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une machine virtuelle SQL Server 2017  dans Azure, vous pouvez vous connecter en local avec **sqlcmd** pour exécuter des requêtes Transact-SQL.

Si vous avez configuré la machine virtuelle Azure pour les connexions SQL Server à distance, vous devez pouvoir vous connecter à distance. Si vous voulez un exemple de connexion à distance à SQL Server sur Linux ou Windows, consultez [Utiliser SSMS sur Windows pour se connecter à SQL Server sur Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Pour vous connecter avec Visual Studio Code, consultez [Utilisez Visual Studio Code pour créer et exécuter des scripts Transact-SQL pour SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)

Pour en savoir plus sur SQL Server sur Linux, consultez [Vue d’ensemble de SQL Server 2017 sur Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Pour en savoir plus sur l’utilisation de machines virtuelles Azure Linux SQL Server 2017 , consultez [Vue d’ensemble des machines virtuelles SQL Server 2017 sur Azure](sql-server-linux-virtual-machines-overview.md).
