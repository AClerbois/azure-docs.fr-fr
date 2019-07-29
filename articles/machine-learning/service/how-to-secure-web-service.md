---
title: Sécuriser des services web à l'aide de SSL
titleSuffix: Azure Machine Learning service
description: Découvrez comment sécuriser un service web déployé par le biais d'Azure Machine Learning service en activant HTTPS. HTTPS sécurise les données des clients à l’aide du protocole TLS (Transport Layer Security), une alternative au protocole SSL (Secure Socket Layer). Les clients utilisent également HTTPS pour vérifier l’identité du service web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 779a34800057284ce77b6d76e030ddca5fadc25f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393825"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Utiliser SSL pour sécuriser un service web par le biais d'Azure Machine Learning

Cet article vous expliquer comment sécuriser un service web déployé par le biais d'Azure Machine Learning service.

Vous utilisez [HTTPS](https://en.wikipedia.org/wiki/HTTPS) pour restreindre l’accès aux services web et sécuriser les données soumises par les clients. HTTPS permet de sécuriser les communications entre un client et un service web en chiffrant les communications entre les deux. Le chiffrement utilise [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS est toujours parfois appelé *SSL (Secure Sockets Layer)* , qui était le prédécesseur de TLS.

> [!TIP]
> Le kit de développement logiciel (SDK) Azure Machine Learning utilise le terme « SSL » pour les propriétés liées aux communications sécurisées. Cela ne signifie pas que votre service web n’utilise pas *TLS*. SSL est tout simplement un terme plus répandu.

TLS et SSL s'appuient tous deux sur des *certificats numériques* qui facilitent le chiffrement et la vérification d'identité. Pour plus d’informations sur le fonctionnement des certificats numériques, consultez l'article Wikipédia [Infrastructure à clé publique](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Si vous n’utilisez pas HTTPS pour votre service web, les données envoyées vers et depuis le service peuvent être visibles par d'autres personnes sur Internet.
>
> HTTPS permet également au client de vérifier l’authenticité du serveur auquel il se connecte. Cette fonctionnalité protège les clients des attaques [de l'intercepteur](https://en.wikipedia.org/wiki/Man-in-the-middle_attack).

Voici le processus général visant à sécuriser un service web :

1. Obtenir un nom de domaine.

2. Obtenir un certificat numérique.

3. Déployer ou mettre à jour le service web sur lequel SSL est activé.

4. Mettre à jour votre DNS afin qu’il pointe vers le service web.

> [!IMPORTANT]
> Si vous effectuez un déploiement vers Azure Kubernetes Service (AKS), vous pouvez acheter votre propre certificat ou utiliser un certificat fourni par Microsoft. Si vous utilisez un certificat fourni par Microsoft, vous n'êtes pas tenu d'obtenir un nom de domaine ou un certificat SSL. Pour plus d’informations, consultez la section [Activer le protocole SSL et le déployer](#enable) de cet article.

La sécurisation des services web sur les [cibles de déploiement](how-to-deploy-and-where.md) présente de légères différences.

## <a name="get-a-domain-name"></a>Obtenir un nom de domaine

Si vous ne possédez pas de nom de domaine, achetez-en-un auprès d’un *bureau d’enregistrement de noms de domaine*. Le processus et le prix varient selon les bureaux d’enregistrement. Le bureau d’enregistrement propose des outils pour gérer le nom de domaine. Vous utilisez ces outils pour mapper un nom de domaine complet (FQDN) (par exemple, www\.contoso.com) à l’adresse IP qui héberge votre service web.

## <a name="get-an-ssl-certificate"></a>Obtenir un certificat SSL

Il existe de nombreuses façons d’obtenir un certificat SSL (certificat numérique). Le moyen le plus courant consiste à en acheter un auprès d’une *autorité de certification* (CA). Quel que soit l’endroit où vous obtenez le certificat, vous avez besoin des fichiers suivants :

* Un **certificat**. Le certificat doit contenir la chaîne d’approbation et doit être « codé en PEM ».
* Une **clé**. La clé doit aussi être codée en PEM.

Lorsque vous demandez un certificat, vous devez fournir le nom de domaine complet (FQDN) de l’adresse que vous envisagez d’utiliser pour le service web (par exemple, www\.contoso.com). L’adresse estampillée sur le certificat et l’adresse utilisée par les clients sont comparées pour vérifier l’identité du service web. Si ces adresses ne correspondent pas, le client reçoit un message d’erreur.

> [!TIP]
> Si l’autorité de certification ne peut pas fournir le certificat et la clé sous forme de fichiers PEM, vous pouvez utiliser un utilitaire tel que [OpenSSL](https://www.openssl.org/) pour modifier le format.

> [!WARNING]
> Utilisez les certificats *auto-signés* uniquement pour le développement. Ne les utilisez pas dans des environnements de production. Les certificats autosignés peuvent entraîner des problèmes dans vos applications clientes. Pour plus d’informations, consultez la documentation relative aux bibliothèques réseau que votre application cliente utilise.

## <a id="enable"></a> Activer le protocole SSL et le déployer

Pour déployer (ou redéployer) le service avec le protocole SSL activé, définissez le paramètre *ssl_enabled* sur « True », si nécessaire. Définissez le paramètre *ssl_certificate* sur la valeur du fichier *certificate*. Définissez *ssl_key* sur la valeur du fichier *key*.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Déployer AKS et FPGA (Field-Programmable Gate Array)

  > [!NOTE]
  > Les informations contenues dans cette section s’appliquent également lorsque vous déployez un service web sécurisé pour l’interface visuelle. Si vous n’êtes pas habitué à utiliser le kit de développement logiciel (SDK) Python, consultez [Présentation du kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Lorsque vous déployez sur AKS, vous pouvez créer un cluster AKS ou attacher un cluster existant.
  
-  Si vous créez un cluster, vous utilisez **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-)** .
- Si vous attachez un cluster existant, vous utilisez **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-)** . Tous deux renvoient un objet de configuration présentant une méthode **enable_ssl**.

La méthode **enable_ssl** peut utiliser un certificat fourni par Microsoft ou un certificat que vous achetez.

  * Lorsque vous utilisez un certificat fourni par Microsoft, vous devez utiliser le paramètre *leaf_domain_label*. Ce paramètre génère le nom DNS du service. Par exemple, une valeur « monservice » crée un nom de domaine « monservice\<six-caracteres-aleatoires >.\<regionazure>. cloudapp.azure.com », où \<regionazure> correspond à la région contenant le service. Vous pouvez également utiliser le paramètre *overwrite_existing_domain* pour remplacer le paramètre *leaf_domain_label* existant.

    Pour déployer (ou redéployer) le service avec le protocole SSL activé, définissez le paramètre *ssl_enabled* sur « True », si nécessaire. Définissez le paramètre *ssl_certificate* sur la valeur du fichier *certificate*. Définissez *ssl_key* sur la valeur du fichier *key*.

    > [!IMPORTANT]
    > Lorsque vous utilisez un certificat fourni par Microsoft, vous n'êtes pas tenu d'acheter votre propre certificat ou nom de domaine.

    L’exemple suivant montre comment créer une configuration qui utilise un certificat SSL fourni par Microsoft :

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * Lorsque vous utilisez *un certificat que vous avez acheté*, vous utilisez les paramètres *ssl_cert_pem_file*, *ssl_key_pem_file* et *ssl_cname*. L’exemple suivant montre comment utiliser les fichiers *.pem* pour créer une configuration s'appuyant sur un certificat SSL que vous avez acheté :

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Pour plus d’informations sur *enable_ssl*, consultez [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) et [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Déployer sur Azure Container Instances

Lorsque vous déployez sur Azure Container Instances, vous fournissez des valeurs pour les paramètres SSL, comme l'illustre l’extrait de code suivant :

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Pour plus d’informations, consultez [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Mettre à jour votre DNS

Vous devez ensuite mettre à jour votre DNS afin qu’il pointe vers le service web.

+ **Pour Container Instances :**

  Utilisez les outils de votre bureau d’enregistrement de noms de domaine pour mettre à jour l’enregistrement DNS pour votre nom de domaine. L’enregistrement doit pointer vers l’adresse IP du service.

  Selon le bureau d’enregistrement et la durée de vie (TTL) configurée pour le nom de domaine, les clients peuvent avoir à patienter quelques minutes voire plusieurs heures avant de pouvoir résoudre le nom de domaine.

+ **Pour AKS :**

  > [!WARNING]
  > Si vous avez utilisé *leaf_domain_label* pour créer le service à l’aide d’un certificat fourni par Microsoft, ne mettez pas à jour manuellement la valeur DNS du cluster. Cette valeur doit être définie automatiquement.

  Mettez à jour le DNS sous l’onglet **Configuration** de l’adresse IP publique du cluster AKS. (Consultez l'image suivante.) L’adresse IP publique est un type de ressource créé sous le groupe de ressources qui contient les nœuds d’agent AKS et d’autres ressources de mise en réseau.

  ![Azure Machine Learning service : Sécurisation des services web avec SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment :
+ [Utiliser un modèle Machine Learning déployé en tant que service web](how-to-consume-web-service.md)
+ [Exécuter en toute sécurité des expériences et une inférence dans un réseau virtuel Azure](how-to-enable-virtual-network.md)
