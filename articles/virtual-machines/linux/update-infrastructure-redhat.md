---
title: Infrastructure de mise à jour Red Hat | Microsoft Docs
description: Découvrez l’infrastructure de mise à jour Red Hat pour les instances Red Hat Enterprise Linux à la demande dans Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 1/7/2019
ms.author: borisb
ms.openlocfilehash: 7d5c0a2a05bbdb19681641f53fb4e1cfcf30f6ca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519967"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infrastructure de mise à jour Red Hat pour machines virtuelles Red Hat Enterprise Linux à la demande dans Azure
 [Infrastructure de mise à jour Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) permet aux fournisseurs de cloud, par exemple Azure, de mettre en miroir le contenu du référentiel hébergé par Red Hat, de créer des référentiels personnalisés avec du contenu spécifique à Azure et de rendre ces référentiels accessibles aux machines virtuelles des utilisateurs finaux.

Les images Red Hat Enterprise Linux (RHEL) Pay-As-You-Go (PAYG, paiement à l’utilisation) sont préconfigurées pour accéder à Azure RHUI. Aucune configuration supplémentaire n’est nécessaire. Pour obtenir les dernières mises à jour, exécutez `sudo yum update` lorsque votre instance RHEL est prête. Ce service est inclus dans le cadre des frais de logiciel RHEL PAYG.

Des informations supplémentaires sur les images RHEL dans Azure, y compris les stratégies de publication et de conservation, sont disponibles [ici](./rhel-images.md).

Vous trouverez des informations sur les stratégies de prise en charge de Red Hat pour toutes les versions de RHEL sur la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

## <a name="important-information-about-azure-rhui"></a>Informations importantes concernant Azure RHUI
* Azure RHUI prend actuellement en charge uniquement la dernière version mineure de chaque famille RHEL (RHEL6 ou RHEL7). Pour mettre à jour une instance de machine virtuelle RHEL connectée à RHUI avec la dernière version secondaire, exécutez `sudo yum update`.

    Par exemple, si vous approvisionnez une machine virtuelle à partir d’une image RHEL 7.4 PAYG et exécutez `sudo yum update`, vous vous retrouvez avec une machine virtuelle RHEL 7.6 (dernière version secondaire dans la famille RHEL7).

    Pour éviter ce problème, vous devez créer votre propre image comme décrit dans l’article [Créer et charger une machine virtuelle Red Hat pour Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vous avez besoin de la connecter à une infrastructure de mise à jour différente ([directement aux serveurs de distribution de contenu Red Hat](https://access.redhat.com/solutions/253273) ou à un [serveur Satellite Red Hat](https://access.redhat.com/products/red-hat-satellite)).

* L’accès à l’infrastructure RHUI hébergée sur Azure est inclus dans le prix de l’image RHEL PAYG. Si vous annulez l’inscription à l’infrastructure RHUI hébergée sur Azure d’une machine virtuelle RHEL avec paiement à l’utilisation, cela ne convertit pas la machine virtuelle en une machine virtuelle de type BYOL (apportez votre propre licence). Si vous inscrivez la même machine virtuelle avec une autre source de mises à jour, vous pouvez être confronté à des frais doubles _indirects_. Vous êtes facturés une première fois pour les frais du logiciel RHEL de Azure. Vous êtes facturé une deuxième fois pour les abonnements Red Hat achetés précédemment. Si vous devez systématiquement utiliser une infrastructure de mise à jour autre que l’infrastructure RHUI hébergée sur Azure, envisagez de créer et déployer vos propres images (de type BYOL). Ce processus est décrit dans l’article [Créer et télécharger une machine virtuelle basée sur Red Hat pour Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Les images RHEL SAP PAYG dans Azure (RHEL for SAP HANA, RHEL for SAP Business Applications) sont connectées aux canaux RHUI dédiés qui restent sur la version mineure RHEL spécifique nécessaire pour la certification SAP.

* L’accès à l’infrastructure RHUI hébergée sur Azure est limité aux machines virtuelles figurant dans les [plages IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653). Si vous redirigez à l’aide d’un proxy tout le trafic de la machine virtuelle via une infrastructure réseau locale, vous devrez peut-être configurer des itinéraires définis par l’utilisateur pour permettre aux machines virtuelles RHEL PAYG d’accéder à Azure RHUI.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>EUS RHEL et machines virtuelles RHEL avec verrouillage de version
Certains clients peuvent vouloir verrouiller leurs machines virtuelles RHEL sur une version mineure RHEL en particulier. Vous pouvez verrouiller la version de votre machine virtuelle RHEL sur une version mineure spécifique en mettant à jour les dépôts pour qu’ils pointent vers les dépôts Extended Update Support. Utilisez les instructions suivantes pour verrouiller une machine virtuelle RHEL sur une version mineure en particulier :

>[!NOTE]
> Ceci s’applique uniquement aux versions RHEL pour laquelle EUS est disponible. Au moment de rédiger cet article, cela inclut RHEL 7.2-7.6. Pour plus de détails, voir la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

1. Désactivez les dépôts non-EUS :
    ```bash
    sudo yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Ajoutez les dépôts EUS :
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Verrouillez la variable releasever :
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > L’instruction ci-dessus verrouille la version mineure de RHEL sur la version mineure actuelle. Entrez une version mineure spécifique si vous souhaitez mettre à niveau et verrouiller une version mineure ultérieure qui n’est pas la dernière version. Par exemple, `echo 7.5 > /etc/yum/vars/releasever` verrouille votre version RHEL sur RHEL 7.5

1. Mettre à jour votre machine virtuelle RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Adresses IP des serveurs de distribution de contenu RHUI

L’infrastructure RHUI est disponible dans toutes les régions où les images RHEL à la demande sont disponibles. Cela inclut actuellement toutes les régions publiques répertoriées sur la page [Tableau de bord Statut Azure](https://azure.microsoft.com/status/) et les régions Microsoft Azure du gouvernement des États-Unis et d’Allemagne.

Si vous utilisez une configuration du réseau pour restreindre davantage l’accès à partir de machines virtuelles RHEL PAYG, assurez-vous que les adresses IP suivantes sont autorisées pour que `yum update` fonctionne en fonction de votre environnement :


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Azure RHUI Infrastructure


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Mettre à jour le certificat de client RHUI arrivé à expiration sur une machine virtuelle

Si vous utilisez une ancienne image de machine virtuelle RHEL, par exemple, RHEL 7.4 (URN d’image : `RedHat:RHEL:7.4:7.4.2018010506`), vous rencontrerez des problèmes de connectivité à RHUI en raison d’un certificat de client SSL arrivé à expiration. L’erreur que vous voyez peut ressembler à _« L’homologue SSL a rejeté le certificat car il a expiré »_ ou _« Erreur : Impossible de récupérer les métadonnées du référentiel (repomd.xml) pour le référentiel :... Vérifiez son chemin d’accès et essayez à nouveau »_. Pour résoudre ce problème, mettez à jour le package client RHUI sur la machine virtuelle à l’aide de la commande suivante :

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Sinon, l’exécution de `sudo yum update` met aussi à jour le package de certificat client (en fonction de la version de votre RHEL) en dépit des erreurs « certificat SSL expiré » que vous voyez pour d’autres dépôts. Si la mise à jour fonctionne, une connectivité normale à d’autres dépôts RHUI doit être restaurée, vous pouvez donc exécuter `sudo yum update`.

Si vous rencontrez une erreur 404 lors de son exécution un `yum update`, essayez la procédure suivante pour actualiser votre cache yum :
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Résoudre des problèmes de connexion à Azure RHUI
Si vous rencontrez des problèmes de connexion à RHUI Azure à partir de votre machine virtuelle Azure RHEL PAYG, procédez comme suit :

1. Examinez la configuration de machine virtuelle pour le point de terminaison RHUI Azure :

    a. Vérifiez si le fichier `/etc/yum.repos.d/rh-cloud.repo` contient la référence à `rhui-[1-3].microsoft.com` dans la `baseurl` de la section `[rhui-microsoft-azure-rhel*]` du fichier. Si c’est le cas, vous utilisez la nouvelle version de RHUI Azure.

    b. Si elle pointe vers un emplacement avec le modèle suivant; `mirrorlist.*cds[1-4].cloudapp.net`, la mise à jour de la configuration est requise. Vous utilisez une ancienne capture instantanée de la machine virtuelle, et vous devez la mettre à jour pour pointer vers la nouvelle RHUI Azure.

1. L’accès à la RHUI hébergée par Azure est limité aux machines virtuelles dans les [plages d’adresses IP des centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Si vous utilisez la nouvelle configuration et que vous ne parvenez toujours pas à vous connecter à RHUI Azure après avoir vérifié que la machine virtuelle se connecte à l’aide de la plage d’adresses IP Azure, ouvrez une demande de support avec Microsoft ou Red Hat.

### <a name="infrastructure-update"></a>Mise à jour de l’infrastructure

En septembre 2016, nous avons déployé une mise à jour de RHUI Azure. En avril 2017, nous avons mis fin à l’ancien RHUI Azure. Si vous avez utilisé les images RHEL PAYG (ou leurs captures instantanées) à partir de septembre 2016, vous vous connectez automatiquement à la nouvelle RHUI Azure. Si toutefois vous avez des captures instantanées plus anciennes sur vos machines virtuelles, vous devez mettre à jour leur configuration manuellement pour accéder à la RHUI Azure, comme décrit dans la section suivante.

Les nouveaux serveurs RHUI Azure sont déployés avec [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). Dans Traffic Manager, un simple point de terminaison (rhui-1.micrsoft.com) peut être utilisé par n’importe quelle machine virtuelle, quelle que soit la région.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procédure de mise à jour manuelle pour utiliser les serveurs RHUI Azure
Cette procédure est fournie uniquement à titre de référence. Les images RHEL PAYG possèdent déjà la configuration correcte pour se connecter à la RHUI Azure. Pour mettre à jour manuellement la configuration afin d’utiliser les serveurs RHUI Azure, procédez comme suit :

1. Télécharger la signature de clé publique via curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc
   ```

1. Vérifier la validité de la clé téléchargée.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

1. Vérifiez la sortie et puis vérifiez `keyid` et `user ID packet`.

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

1. Installez la clé publique.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

1. Téléchargez, vérifiez et installez un package Gestionnaire de package RPM (RPM) client.

    >[!NOTE]
    >Les versions du package varient. Si vous vous connectez manuellement à la RHUI Azure, vous trouverez la dernière version du package client pour chaque famille RHEL en approvisionnant la dernière image de la galerie.

   a. Téléchargez.

    - Pour RHEL 6 :
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.2-74.noarch.rpm
        ```

    - Pour RHEL 7 :
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.2-74.noarch.rpm
        ```

   b. Vérifiez.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Vérifiez la sortie pour vous assurer que la signature du package est OK.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Installez le package RPM.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

1. Une fois l’installation terminée, vérifiez que vous pouvez accéder à l’écran RHUI Azure depuis la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes
* Pour créer une machine virtuelle Red Hat Enterprise Linux à partir d’une image PAYG dans le service Marketplace Azure et utiliser l’infrastructure RHUI hébergée sur Azure, voir [Place de marché Azure](https://azure.microsoft.com/marketplace/partners/redhat/).
* Pour en savoir plus sur les images Red Hat dans Azure, accédez à la [page de documentation](./rhel-images.md).
* Vous trouverez des informations sur les stratégies de prise en charge de Red Hat pour toutes les versions de RHEL sur la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
