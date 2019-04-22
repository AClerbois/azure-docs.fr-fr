---
title: Guide pratique pour utiliser des stratégies d’allocation personnalisées avec le service Azure IoT Hub Device Provisioning | Microsoft Docs
description: Guide pratique pour utiliser des stratégies d’allocation personnalisées avec le service Azure IoT Hub Device Provisioning
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: f0eb2f7358e8fb1564275e1de510f302d2eef90b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59500938"
---
# <a name="how-to-use-custom-allocation-policies"></a>Comment utiliser des stratégies d’allocation personnalisées


Une stratégie d’allocation personnalisée vous permet de mieux contrôler le processus d’assignation des appareils à un hub IoT. Cette assignation s’effectue par l’ajout de code personnalisé dans une solution [Azure Functions](../azure-functions/functions-overview.md). Le service Device provisioning appelle votre code de fonction Azure en fournissant toutes les informations appropriées sur l’appareil et l’inscription. Votre code de fonction est exécuté et retourne les informations sur les hubs IoT utilisés pour le provisionnement de l’appareil.

Avec les stratégies d’allocation personnalisées, vous définissez vos propres stratégies d’allocation quand les stratégies fournies par le service Device Provisioning ne sont pas adaptées aux besoins de votre scénario.

Par exemple, vous souhaitez peut-être examiner le certificat utilisé par un appareil pendant le provisionnement et assigner l’appareil à un hub IoT en fonction d’une propriété du certificat. Vous avez peut-être une base de données contenant des informations sur vos appareils et souhaitez interroger la base de données pour déterminer à quel hub IoT un appareil doit être assigné.


Cet article présente une stratégie d’allocation personnalisée qui utilise du code Azure Functions en C#. Deux nouveaux hubs IoT sont créés pour les divisions *Contoso Toasters Division* et *Contoso Heat Pumps Division*. Les appareils à provisionner doivent avoir un ID d’inscription contenant l’un des suffixes suivants pour être éligibles au provisionnement :

- **-contoso-tstrsd-007** : Contoso Toasters Division
- **-contoso-hpsd-088** : Contoso Heat Pumps Division

Les appareils seront provisionnés sur la base de la présence d’un de ces suffixes obligatoires dans l’ID d’inscription. Ces appareils seront simulés à l’aide d’un exemple de provisionnement inclus dans le [SDK Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c). 

Dans cet article, vous allez effectuer les étapes suivantes :

* Utiliser Azure CLI pour créer deux hubs IoT destinés aux divisions **Contoso Toasters Division** et **Contoso Heat Pumps Division**
* Créer une inscription de groupe en ajoutant du code Azure Functions pour la stratégie d’allocation personnalisée
* Créer des clés d’appareil pour les deux simulations d’appareils
* Configurer l’environnement de développement nécessaire au SDK Azure IoT pour C
* Simuler les appareils pour vérifier qu’ils sont provisionnés selon l’exemple de code de la stratégie d’allocation personnalisée


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Conditions préalables

* Avoir effectué les étapes décrites dans le guide de démarrage rapide [Configurer le service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md).
* Visual Studio 2015 ou [Visual Studio 2017](https://www.visualstudio.com/vs/) avec la charge de travail [« Développement Desktop en C++ »](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) activée.
* Dernière version de [Git](https://git-scm.com/download/) installée.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Créer deux hubs IoT pour les divisions

Dans cette section, vous allez utiliser Azure Cloud Shell pour créer deux hubs IoT représentant les divisions **Contoso Toasters Division** et **Contoso Heat Pumps Division**.

1. Dans Azure Cloud Shell, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

    L’exemple suivant crée un groupe de ressources nommé *contoso-us-resource-group* dans la région *eastus*. Il est recommandé d’utiliser ce groupe pour toutes les ressources créées dans cet article. Cela facilitera la suppression des ressources quand vous aurez terminé l’article.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Dans Azure Cloud Shell, créez un hub IoT **Contoso Toasters Division** avec la commande [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Le hub IoT sera ajouté au groupe *contoso-us-resource-group*.

    L’exemple suivant crée un hub IoT nommé *contoso-toasters-hub-1098* dans la région *eastus*. Utilisez le nom unique de votre propre hub. Remplacez **1098** par votre propre suffixe dans le nom du hub. Dans l’exemple de code pour la stratégie d’allocation personnalisée, le nom du hub doit contenir `-toasters-`.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Cette commande peut prendre plusieurs minutes.

3. Dans Azure Cloud Shell, créez un hub IoT **Contoso Heat Pumps Division** avec la commande [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Ce hub IoT sera également ajouté au groupe *contoso-us-resource-group*.

    L’exemple suivant crée un hub IoT nommé *contoso-heatpumps-hub-1098* dans la région *eastus*. Utilisez le nom unique de votre propre hub. Remplacez **1098** par votre propre suffixe dans le nom du hub. Dans l’exemple de code pour la stratégie d’allocation personnalisée, le nom du hub doit contenir `-heatpumps-`.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Cette commande peut également prendre plusieurs minutes.




## <a name="create-the-enrollment"></a>Créer l’inscription

Dans cette section, vous allez créer un groupe d’inscriptions qui utilise la stratégie d’allocation personnalisée. Par souci de simplicité, cet article utilise [l’attestation de clé symétrique](concepts-symmetric-key-attestation.md) avec l’inscription. Pour sécuriser votre solution, utilisez plutôt [l’attestation de certificat X.509](concepts-security.md#x509-certificates) avec une chaîne d’approbation.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre instance du service Device Provisioning.

2. Sélectionnez l’onglet **Gérer les inscriptions**, puis cliquez sur le bouton **Ajouter un groupe d’inscriptions** en haut de la page. 

3. Dans **Ajouter un groupe d’inscriptions**, entrez les informations suivantes, puis cliquez sur le bouton **Enregistrer**.

    **Nom du groupe** : entrez **contoso-custom-allocated-devices**.

    **Type d’attestation** : sélectionnez **Clé symétrique**.

    **Générer automatiquement les clés** : cette case est normalement déjà cochée.

    **Sélectionner le mode d’affectation des appareils aux hubs** : sélectionnez **Personnalisé (utiliser la fonction Azure)**.

    ![Ajouter un groupe d’inscription d’allocation personnalisée pour l’attestation de clé symétrique](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. Sous **Ajouter un groupe d’inscriptions**, cliquez sur **Lier un nouveau hub IoT** pour lier les deux hubs IoT des divisions. 

    Vous devez exécuter cette étape pour vos deux hubs IoT de division.

    **Abonnement**: si vous avez plusieurs abonnements, choisissez celui où vous avez créé les hubs IoT des divisions.

    **Hub IoT** : sélectionnez un des hubs que vous avez créés pour les divisions.

    **Stratégie d’accès** : choisissez **iothubowner**.

    ![Lier les hubs IoT des divisions au service de provisionnement](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. Une fois que vous avez lié les deux hubs IoT des divisions, sous **Ajouter un groupe d’inscriptions**, sélectionnez-les afin de créer le groupe de hubs IoT pour l’inscription, comme ci-dessous :

    ![Créer le groupe de hubs des divisions pour l’inscription](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. Sous **Ajouter un groupe d’inscriptions**, faites défiler jusqu’à la section **Sélectionner Azure Functions** et cliquez sur **Créer une application de fonction**.

7. Dans la page **Function App** qui s’ouvre, entrez les paramètres suivants pour votre nouvelle fonction et cliquez sur **Créer** :

    **Nom de l’application** : entrez un nom unique pour votre application de fonction. **contoso-function-app-1098** est un exemple de nom.

    **Groupe de ressources** : sélectionnez **Utiliser existant** et **contoso-us-resource-group** pour conserver ensemble toutes les ressources créées dans cet article.

    **Application Insights** : dans cet exercice, vous pouvez désactiver cette option.

    ![Créer l’application de fonction](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. Revenez dans la page **Ajouter un groupe d’inscriptions** pour vérifier que votre nouvelle application de fonction est sélectionnée. Vous devrez peut-être resélectionner l’abonnement pour actualiser la liste des applications de fonction.

    Quand la nouvelle application de fonction est sélectionnée, cliquez sur **Créer une fonction**.

    ![Créer l’application de fonction](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    Votre nouvelle application de fonction s’ouvre.

9. Sur votre application de fonction, cliquez pour créer une fonction

    ![Créer l’application de fonction](./media/how-to-use-custom-allocation-policies/new-function.png)

    Pour la nouvelle fonction, utilisez les paramètres par défaut afin de créer une **API + Webhook** avec le langage **CSharp**. Cliquez sur **Créer cette fonction**.

    Cette opération crée une fonction C# nommée **HttpTriggerCSharp1**.

10. Remplacez le code de la nouvelle fonction C# par le code suivant, puis cliquez sur **Enregistrer** :    

    ```C#
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");    
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }    

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```


11. Revenez dans la page **Ajouter un groupe d’inscriptions** pour vérifier que la nouvelle fonction est sélectionnée. Vous devrez peut-être resélectionner l’application de fonction pour actualiser la liste des fonctions.

    Quand la nouvelle fonction est sélectionnée, cliquez sur **Enregistrer** pour enregistrer le groupe d’inscriptions.

    ![Enfin, enregistrez le groupe d’inscriptions](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. Après avoir enregistré l’inscription, rouvrez-la et notez la valeur de la **Clé primaire**. Vous devez d’abord enregistrer l’inscription pour permettre la génération des clés. Cette clé primaire servira ultérieurement à générer des clés uniques pour les appareils simulés.


## <a name="derive-unique-device-keys"></a>Dériver les clés d’appareil uniques

Dans cette section, vous allez créer deux clés d’appareil uniques. Une clé sera utilisée pour l’appareil simulé Toaster. L’autre clé sera utilisée pour l’appareil simulé Heat pump.

Pour générer la clé des deux appareils, utilisez la **clé primaire** que vous avez notée plus tôt pour calculer le code [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) de l’ID d’inscription de chacun des appareils et convertissez le résultat au format Base64. Pour plus d’informations sur la création de clés d’appareil dérivées avec des groupes d’inscription, consultez la section sur les inscriptions de groupes dans [Attestation de clé symétrique](concepts-symmetric-key-attestation.md).

Pour l’exemple de cet article, utilisez les ID d’inscription des deux appareils suivants et calculez une clé d’appareil pour chaque appareil. Les deux ID d’inscription ont un suffixe qui peut être utilisé dans l’exemple de code pour la stratégie d’allocation personnalisée :

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Stations de travail Linux

Si vous utilisez une station de travail Linux, utilisez openssl pour générer vos clés d’appareil dérivées, comme indiqué dans l’exemple suivant.

1. Remplacez la valeur **KEY** par la **clé primaire** que vous avez notée précédemment.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


#### <a name="windows-based-workstations"></a>Stations de travail Windows

Si vous utilisez une station de travail Windows, utilisez PowerShell pour générer votre clé d’appareil dérivée, comme indiqué dans l’exemple suivant.

1. Remplacez la valeur **KEY** par la **clé primaire** que vous avez notée précédemment.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


Les appareils simulés utiliseront les clés d’appareil dérivées avec chaque ID d’inscription pour effectuer l’attestation de clé symétrique.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Préparer un environnement de développement pour le SDK Azure IoT pour C

Dans cette section, vous allez préparer un environnement de développement pour générer le [SDK Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c). Le SDK inclut l’exemple de code pour l’appareil simulé. Cet appareil simulé tente le provisionnement durant la séquence de démarrage de l’appareil.

Cette section s’applique à une station de travail Windows. Pour obtenir un exemple sur Linux, consultez la section sur la configuration des machines virtuelles dans [Guide pratique du provisionnement pour la multilocation](how-to-provision-multitenant.md).

1. Téléchargez le [système de génération CMake](https://cmake.org/download/).

    Il est important que les composants requis Visual Studio (Visual Studio et la charge de travail « Développement Desktop en C++ ») soient installés sur votre machine, **avant** de commencer l’installation de l’élément `CMake`. Une fois les composants requis en place et le téléchargement effectué, installez le système de génération de CMake.

2. Ouvrez une invite de commandes ou l’interpréteur de commandes Git Bash. Exécutez la commande suivante pour cloner le dépôt GitHub du SDK Azure IoT pour C :
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Attendez-vous à ce que cette opération prenne plusieurs minutes.


3. Créez un sous-répertoire `cmake` dans le répertoire racine du référentiel Git et accédez à ce dossier. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Exécutez la commande suivante qui génère une version du SDK propre à votre plateforme cliente de développement. Une solution Visual Studio pour l’appareil simulé est générée dans le répertoire `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Si `cmake` ne trouve pas votre compilateur C++, vous obtiendrez peut-être des erreurs de build lors de l’exécution de la commande ci-dessus. Si cela se produit, essayez d’exécuter cette commande dans [l’invite de commandes de Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Une fois la génération terminée, les dernières lignes de sortie doivent ressembler à la sortie suivante :

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```




## <a name="simulate-the-devices"></a>Simuler les appareils

Dans cette section, vous allez mettre à jour un exemple de provisionnement nommé **prov\_dev\_client\_sample** provenant du SDK Azure IoT pour C que vous avez configuré précédemment. 

Cet exemple de code simule une séquence de démarrage d’un appareil qui envoie la demande de provisionnement à votre instance du service Device Provisioning. La séquence de démarrage entraîne la reconnaissance de l’appareil Toaster et son assignation au hub IoT défini par la stratégie d’allocation personnalisée.

1. Dans le portail Azure, sélectionnez l’onglet **Vue d’ensemble** de votre service Device Provisioning et notez les valeurs de **_Étendue de l’ID_**.

    ![Extraction des informations de point de terminaison du service Device Provisioning à partir du panneau du Portail](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Dans Visual Studio, ouvrez le fichier solution **azure_iot_sdks.sln** généré précédemment en exécutant CMake. Le fichier solution doit être à l’emplacement suivant :

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Dans la fenêtre *Explorateur de solutions* de Visual Studio, accédez au dossier **Provision\_Samples**. Développez l’exemple de projet nommé **prov\_dev\_client\_sample**. Développez **Fichiers sources**, puis ouvrez **prov\_dev\_client\_sample.c**.

4. Recherchez la constante `id_scope` et remplacez la valeur par la valeur **Étendue de l’ID** que vous avez copiée précédemment. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Recherchez la définition de la fonction `main()` dans le même fichier. Vérifiez que la variable `hsm_type` a la valeur `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, comme indiqué ci-dessous :

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Cliquez avec le bouton droit sur le projet **prov\_dev\_client\_sample** et sélectionnez **Définir comme projet de démarrage**. 


#### <a name="simulate-the-contoso-toaster-device"></a>Simuler l’appareil Toaster de Contoso

1. Pour simuler le toaster, recherchez l’appel à `prov_dev_set_symmetric_key_info()` dans **prov\_dev\_client\_sample.c**, qui est placé en commentaire.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Décommentez l’appel de fonction et remplacez les valeurs des espaces réservés (y compris les crochets) par l’ID d’inscription du toaster et la clé d’appareil dérivée que vous avez générée précédemment. La valeur **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** est donnée seulement à titre d’exemple.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    Enregistrez le fichier .

2. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Exécuter sans débogage** pour exécuter la solution. Dans l’invite pour régénérer le projet, cliquez sur **Oui** pour régénérer le projet avant l’exécution.

    La sortie suivante est un exemple illustrant le démarrage réussi de l’appareil Toaster simulé, et sa connexion à l’instance du service Device Provisioning à assigner au hub IoT Toasters à l’aide de la stratégie d’allocation personnalisée :

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Simuler l’appareil Heat pump de Contoso

1. Pour simuler l’appareil pompe à chaleur, mettez à jour l’appel à `prov_dev_set_symmetric_key_info()` dans **prov\_dev\_client\_sample.c** à nouveau avec l’ID d’inscription et la clé d’appareil dérivée de la pompe à chaleur générée précédemment. La valeur **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** est donnée seulement à titre d’exemple.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    Enregistrez le fichier .

2. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Exécuter sans débogage** pour exécuter la solution. Dans l’invite pour régénérer le projet, cliquez sur **Oui** pour régénérer le projet avant l’exécution.

    La sortie suivante est un exemple illustrant le démarrage réussi de l’appareil Heat pump simulé, et sa connexion à l’instance du service Device Provisioning à assigner au hub IoT Heat pumps de Contoso à l’aide de la stratégie d’allocation personnalisée :

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>Résolution des problèmes liés aux stratégies d’allocation personnalisées

Le tableau suivant présente les scénarios attendus et les codes d’erreur de résultats que vous pouvez rencontrer. Aidez-vous de ce tableau pour résoudre les problèmes de stratégie d’allocation personnalisée avec Azure Functions.


| Scénario | Résultat de l’inscription retourné par le service Provisioning | Résultat du provisionnement retourné par le SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Le webhook retourne 200 OK avec 'iotHubHostName' défini sur un nom d’hôte IoT Hub valide | État du résultat : Attribué  | Le SDK retourne PROV_DEVICE_RESULT_OK et des informations sur le hub |
| Le webhook retourne 200 OK avec 'iotHubHostName' présent dans la réponse, mais défini sur une chaîne vide ou null | État du résultat : Échec<br><br> Code d’erreur : CustomAllocationIotHubNotSpecified (400208) | Le SDK retourne PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Le webhook retourne 401 Unauthorized | État du résultat : Échec<br><br>Code d’erreur : CustomAllocationUnauthorizedAccess (400209) | Le SDK retourne PROV_DEVICE_RESULT_UNAUTHORIZED |
| Une inscription individuelle a été créée pour désactiver l’appareil | État du résultat : Désactivé | Le SDK retourne PROV_DEVICE_RESULT_DISABLED |
| Le webhook retourne le code d’erreur >= 429 | L’orchestration de DPS va effectuer plusieurs nouvelles tentatives. La stratégie de nouvelles tentatives est actuellement la suivante :<br><br>&nbsp;&nbsp;- Nombre de nouvelles tentatives : 10<br>&nbsp;&nbsp;- Intervalle initial : 1 s<br>&nbsp;&nbsp;- Incrément : 9 s | Le SDK ignore l’erreur et envoie un autre message d’état get dans le délai spécifié |
| Le webhook retourne un autre code d’état | État du résultat : Échec<br><br>Code d’erreur : CustomAllocationFailed (400207) | Le SDK retourne PROV_DEVICE_RESULT_DEV_AUTH_ERROR |


## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de continuer à utiliser les ressources créées dans cet article, vous pouvez les conserver. Sinon, effectuez les étapes suivantes pour supprimer toutes les ressources qui ont été créées dans le cadre de cet article, et ainsi éviter des frais inutiles.

Les étapes décrites supposent que vous avez créé toutes les ressources utilisées dans cet article dans le même groupe de ressources nommé **contoso-us-resource-group**, comme nous vous l’avions demandé.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. Si vous avez créé l’IoT Hub à l’intérieur d’un groupe de ressources existant qui concerne des ressources que vous souhaitez conserver, supprimer uniquement la ressource de l’ IoT Hub au lieu de supprimer le groupe de ressources.
>

Pour supprimer le groupe de ressources par nom :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Groupes de ressources**.

2. Dans la zone de texte **Filtrer par nom...**, tapez le nom du groupe de ressources contenant vos ressources (ici, **contoso-us-resource-group**). 

3. À droite de votre groupe de ressources dans la liste des résultats, cliquez sur **...**, puis sur **Supprimer le groupe de ressources**.

4. Il vous sera demandé de confirmer la suppression du groupe de ressources. Saisissez de nouveau le nom de votre groupe de ressources pour confirmer, puis cliquez sur **Supprimer**. Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus Reprovisioning, consultez [réapprovisionnement des concepts d’appareil IoT Hub](concepts-device-reprovision.md) 
- Pour en savoir plus Deprovisioning, consultez [Guide pratique pour déprovisionner des appareils qui ont été provisionnés automatiquement](how-to-unprovision-devices.md) 











