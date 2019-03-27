---
title: Déployer Azure Machine Learning sur un appareil - Azure IoT Edge | Microsoft Docs
description: Dans ce tutoriel, vous allez créer un modèle Azure Machine Learning, puis le déployer en tant que module sur un appareil de périphérie.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 985f1f73fbfc8c75df8393615fca32f5d1c08b9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078310"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Didacticiel : Déployer Azure Machine Learning en tant que module IoT Edge (préversion)

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide tout au long du déploiement d’un module Azure Machine Learning qui prédit l’échec d’un appareil à partir des données de température de machine simulée. Pour plus d’informations sur le service Azure Machine Learning sur IoT Edge, consultez la [documentation Azure Machine Learning](../machine-learning/service/how-to-deploy-to-iot.md).

Le module Azure Machine Learning que vous créez dans ce didacticiel lit les données de l’environnement générées par votre appareil et étiquette les messages comme étant anormaux ou pas.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un module Azure Machine Learning
> * Envoyer (push) un conteneur de module dans un registre de conteneurs Azure
> * Déployer un module Azure Machine Learning sur votre appareil Azure IoT Edge
> * Afficher les données générées

>[!NOTE]
>Les modules Azure Machine Learning sur Azure IoT Edge sont en préversion publique.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre machine virtuelle Azure en tant qu’appareil IoT Edge. Pour cela, suivez les étapes décrites dans le guide de démarrage rapide pour [Linux](quickstart-linux.md).
* Le module Azure Machine Learning ne prend pas en charge les conteneurs Windows.
* Le module Azure Machine Learning ne prend pas en charge les processeurs ARM.

Ressources cloud :

* Un niveau gratuit ou standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure.
* Un espace de travail Azure Machine Learning. Pour savoir comment créer et utiliser un module Azure Machine Learning, suivez les instructions fournies dans [Utiliser le portail Azure pour bien démarrer avec Azure Machine Learning](../machine-learning/service/quickstart-get-started.md).
   * Notez le nom de l’espace de travail, le groupe de ressources et l’ID de l’abonnement. Ces valeurs sont toutes disponibles dans la vue d’ensemble de l’espace de travail, sur le portail Azure. Vous utiliserez ces valeurs plus tard dans ce tutoriel pour connecter un notebook Azure aux ressources de votre espace de travail. 


### <a name="disable-process-identification"></a>Désactiver l’identification du processus

>[!NOTE]
>
> En préversion, Azure Machine Learning ne prend pas en charge la fonctionnalité de sécurité d’identification du processus activée par défaut avec IoT Edge.
> Voici les étapes permettant de la désactiver. Elles ne sont toutefois pas appropriées pour une utilisation en production. Ces étapes ne doivent être effectuées que pour les appareils Linux. 

Pour désactiver l’identification du processus sur votre appareil IoT Edge, vous devez fournir l’adresse IP et le port pour **workload_uri** et **management_uri** dans la section **connect** de la configuration du démon IoT Edge.

Récupérez d’abord l’adresse IP. Entrez `ifconfig` dans votre ligne de commande et copiez l’adresse IP de l’interface **docker0**.

Modifiez le fichier de configuration du démon IoT Edge :

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

Mettez à jour la section **Connecter** de la configuration avec votre adresse IP. Par exemple :
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Entrez les mêmes adresses dans la section **Écouter** de la configuration. Par exemple : 

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Enregistrez et fermez le fichier de configuration.

Créez une variable d’environnement IOTEDGE_HOST avec l’adresse management_uri (pour la définir définitivement, ajoutez-la à `/etc/environment`). Par exemple : 

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```

Pour que les modifications prennent effet, redémarrez le service IoT Edge.

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="create-and-deploy-azure-machine-learning-module"></a>Créer et déployer un module Azure Machine Learning

Dans cette section, vous allez convertir des fichiers de modèles Machine Learning entraînés en un conteneur Azure Machine Learning Service. Tous les composants requis pour l’image Docker se trouvent dans le référentiel Git [AI Toolkit pour Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Effectuez les étapes suivantes pour charger ce dépôt dans Microsoft Azure Notebooks, afin de créer le conteneur et de l’envoyer (push) vers Azure Container Registry.


1. Accédez à vos projets Azure Notebooks. Vous pouvez y accéder à partir de votre espace de travail Azure Machine Learning Service sur le [portail Azure](https://portal.azure.com), ou en vous connectant à [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) avec votre compte Azure.

2. Sélectionnez **Charger un dépôt GitHub**.

3. Indiquez le nom de dépôt GitHub suivant : `Azure/ai-toolkit-iot-edge`. Si vous souhaitez que votre projet soit privé, décochez la case **Public**. Sélectionnez **Importer**. 

4. Une fois l’importation terminée, accédez au nouveau projet **ai-toolkit-iot-edge**, puis ouvrez le dossier **IoT Edge anomaly detection tutorial** (Tutoriel sur la détection d’anomalie dans IoT Edge). 

5. Vérifiez que votre projet est en cours d’exécution. Dans le cas contraire, sélectionnez **Run on Free Compute** (Exécuter sur le calcul gratuit).

   ![Exécuter sur le calcul gratuit](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Ouvrez le fichier **aml_config/config.json**.

7. Dans le fichier config, ajoutez les valeurs de votre ID d’abonnement Azure, d’un groupe de ressources de votre abonnement ainsi que le nom de l’espace de travail Azure Machine Learning Service. Vous pouvez retrouver toutes ces valeurs dans la section **Vue d’ensemble** de votre espace de travail dans Azure. 

8. Enregistrez le fichier de configuration.

9. Ouvrez le fichier **00-anomaly-detection-tutorial.ipynb**.

10. Lorsque vous y êtes invité, sélectionnez le noyau **Python 3.6**, puis sélectionnez **Set Kernel** (Définir le noyau).

11. Modifiez la première cellule du notebook selon les instructions des commentaires. Utilisez le groupe de ressources, l’ID d’abonnement et le nom d’espace de travail que vous avez ajoutés au fichier config.

12. Exécutez les cellules du notebook en les sélectionnant, puis en sélectionnant **Exécuter** ou en appuyant sur `Shift + Enter`.

    >[!TIP]
    >Dans le notebook du tutoriel sur la détection des anomalies, certaines cellules sont facultatives, car elles créent des ressources que les utilisateurs peuvent déjà avoir, comme un hub IoT. Si vous placez vos informations de ressources existantes dans la première cellule, l’exécution des cellules qui créent des ressources entraînera des erreurs, car Azure ne peut pas créer de doublons de ressources. Toutefois, il ne s’agit pas là d’un problème. Vous pouvez donc ignorer l’intégralité des erreurs et des sections facultatives. 

Une fois que vous aurez effectué toutes les étapes dans le notebook, vous aurez entraîné un modèle de détection des anomalies, vous l’aurez généré sous la forme d’une image conteneur Docker et vous aurez envoyé (push) cette image vers Azure Container Registry. Ensuite, vous aurez testé ce modèle et l’aurez déployé sur votre appareil IoT Edge. 

## <a name="view-container-repository"></a>Afficher le référentiel d’images conteneur

Vérifiez que votre image conteneur a bien été créée et qu’elle est stockée dans le registre de conteneurs Azure qui est associé à votre environnement Machine Learning. Le notebook que vous avez utilisé dans la section précédente a automatiquement fourni l’image conteneur et les informations d’identification du registre à votre appareil IoT Edge. Toutefois, vous avez besoin de savoir où elles sont stockées afin de pouvoir les retrouver ultérieurement. 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre espace de travail Azure Machine Learning Service. 

2. La section **Vue d’ensemble** regroupe les informations concernant l’espace de travail et les ressources associées. Sélectionnez la valeur de **Registre**, qui doit être composée du nom de votre espace de travail, suivi de nombres aléatoires. 

3. Dans le registre de conteneurs, sélectionnez **Référentiels**. Vous devez voir le référentiel nommé **tempanomalydetection**, qui a été créé par le notebook que vous avez exécuté dans la section précédente. 

4. Sélectionnez **tempanomalydetection**. Vous devez voir que le référentiel a une étiquette : **1**. 

   Maintenant que vous connaissez le nom du registre, le nom du référentiel et l’étiquette, vous connaissez le chemin complet de l’image conteneur. Les chemins d’image ressemblent à ceci **\<nom_registre\>.azurecr.io/tempanomalydetection:1**. Vous pouvez utiliser le chemin de l’image pour déployer ce conteneur sur les appareils IoT Edge. 

5. Dans le registre de conteneurs, sélectionnez **Clés d’accès**. Vous devez voir des informations d’identification d’accès, notamment les informations du **serveur de connexion**, ainsi que le **nom d’utilisateur** et le **mot de passe** de l’administrateur.

   Ces informations d’identification peuvent être ajoutées au manifeste de déploiement pour permettre à votre appareil IoT Edge de tirer (pull) des images conteneurs à partir du registre. 

Maintenant, vous savez où est stockée l’image conteneur Machine Learning. La section suivante décrit les étapes permettant de voir comment elle fonctionne en tant que module déployé sur votre appareil IoT Edge. 

## <a name="view-generated-data"></a>Afficher les données générées

Vous pouvez afficher les messages générés par chaque module IoT Edge, et vous pouvez afficher les messages envoyés à votre hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Afficher les données sur votre appareil IoT Edge

Vous pouvez afficher les messages envoyés par chaque module individuel sur votre appareil IoT Edge.

Vous devrez peut-être utiliser `sudo` afin de bénéficier des autorisations élevées permettant d’exécuter les commandes `iotedge`. Il vous suffit de vous déconnecter puis de vous reconnecter à votre appareil pour que vos autorisations soient mises à jour automatiquement.

1. Affichez tous les modules sur votre appareil IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Affichez les messages envoyés par un appareil spécifique. Utilisez le nom du module de la sortie de la commande précédente.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Afficher les données reçues par votre hub IoT

Vous pouvez afficher les messages appareil-à-cloud reçus par votre hub IoT à l’aide de [l’extension Azure IoT Hub Toolkit pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anciennement l’extension Azure IoT Toolkit).

Les étapes suivantes montrent comment configurer Visual Studio Code pour surveiller les messages appareil-à-cloud reçus par votre hub IoT.

1. Dans Visual Studio Code, sélectionnez **Appareils IoT Hub**.

2. Sélectionnez **...**, puis **Définir la chaîne de connexion IoT Hub** dans le menu.

   ![Définir la chaîne de connexion IoT Hub](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Dans la zone de texte qui s’ouvre en haut de la page, entrez la chaîne de connexion iothubowner de votre hub IoT. Votre appareil IoT Edge devrait apparaître dans la liste Appareils IoT Hub.

4. Sélectionnez à nouveau **...**, puis **Commencer le monitoring du message D2C**.

5. Observez les messages en provenance de tempSensor toutes les cinq secondes. Le corps du message contient une propriété appelée **anomaly**, qui est fournie par machinelearningmodule avec la valeur true ou false. La propriété **AzureMLResponse** contient la valeur « OK » si le modèle a été exécuté correctement.

   ![Réponse du service Azure Machine Learning dans le corps du message](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test.

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé un module IoT Edge optimisé par Azure Machine Learning. Vous pouvez continuer avec l’un des autres didacticiels pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Classer des images avec le service Vision personnalisée](tutorial-deploy-custom-vision.md)

