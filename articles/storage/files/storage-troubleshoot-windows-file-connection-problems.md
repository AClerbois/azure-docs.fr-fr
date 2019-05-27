---
title: Résoudre les problèmes liés à Azure Files sous Windows | Microsoft Docs
description: Résoudre les problèmes liés à Azure Files sous Windows
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 9658ed46e1a46aa3fc2c7fe251fd73b2ef0a13dd
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991367"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Résoudre les problèmes liés à Azure Files sous Windows

Cet article liste les problèmes courants liés à Microsoft Azure Files en cas de connexion à partir de clients Windows. Il fournit également les causes possibles et les solutions de ces problèmes. En plus des étapes de résolution présentées dans cet article, vous pouvez utiliser [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)  pour vérifier que l’environnement du client Windows est configuré correctement. AzFileDiagnostics détecte automatiquement la plupart des problèmes mentionnés dans cet article et vous aide à configurer votre environnement pour que les performances soient optimales. Vous pouvez également trouver ces informations dans [l’utilitaire de résolution des problèmes de partages Azure Files](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares), qui vous guide dans les étapes de résolution des problèmes liés à la connexion, au mappage ou au montage de partages Azure Files.

<a id="error5"></a>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-5-when-you-mount-an-azure-file-share"></a>Error 5 quand vous montez un partage de fichiers Azure

Quand vous essayez de monter un partage de fichiers, vous pouvez recevoir l’erreur suivante :

- Erreur système 5. L’accès est refusé.

### <a name="cause-1-unencrypted-communication-channel"></a>Cause 1 : Canal de communication non chiffré

Pour des raisons de sécurité, les connexions aux partages de fichiers Azure sont bloquées si le canal de communication n’est pas chiffré et si la tentative de connexion n’est pas effectuée depuis le centre de données sur lequel résident les partages de fichiers Azure. Les connexions non chiffrées dans le même centre de données peuvent également être bloquées si le paramètre [Transfert sécurisé requis](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) est activé sur le compte de stockage. Un canal de communication chiffré est fourni uniquement si le système d’exploitation client de l’utilisateur prend en charge le chiffrement SMB.

Windows 8, Windows Server 2012 et les versions ultérieures de chaque demande de négociation système incluant SMB 3.0, prenant en charge le chiffrement.

### <a name="solution-for-cause-1"></a>Solution pour la cause 1

1. Connectez-vous à partir d’un client qui prend en charge le chiffrement SMB (Windows 8, Windows Server 2012 ou versions ultérieures), ou à partir d’une machine virtuelle se trouvant dans le même centre de données que le compte de stockage Azure utilisé pour le partage de fichiers Azure.
2. Vérifiez que le paramètre [Transfert sécurisé obligatoire](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) est désactivé sur le compte de stockage si le client ne prend pas en charge le chiffrement SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Cause 2 : Des règles de pare-feu ou de réseau virtuel sont activées sur le compte de stockage 

Si des règles de pare-feu et de réseau virtuel sont configurées sur le compte de stockage, le trafic réseau n’a pas d’accès, sauf si l’adresse IP du client ou un réseau virtuel est autorisé à accéder.

### <a name="solution-for-cause-2"></a>Solution pour la cause 2

Vérifiez que les règles de pare-feu et de réseau virtuel sont configurées correctement sur le compte de stockage. Pour vérifier si des règles de pare-feu ou de réseau virtuel sont à l’origine du problème, définissez temporairement le paramètre du compte de stockage sur **Autoriser l’accès à partir de tous les réseaux**. Pour plus d’informations, consultez [Configurer les pare-feu et les réseaux virtuels dans le Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Les messages « Erreur 53 », « Erreur 67 » ou « Erreur 87 » s’affichent lorsque vous montez ou démontez un partage de fichiers Azure

Lorsque vous essayez de monter un partage de fichiers depuis un centre de données local ou un centre de données différent, les erreurs suivantes pourraient survenir :

- Erreur système 53. Le chemin d’accès réseau est introuvable.
- Erreur système 67. Le nom du réseau est introuvable.
- Erreur système 87. Le paramètre est incorrect.

### <a name="cause-1-port-445-is-blocked"></a>Cause 1 : Le port 445 est bloqué

Une erreur système 53 ou 67 peut se produire si la communication sortante du port 445 vers le centre de données Azure Files est bloquée. Pour afficher le récapitulatif des FAI qui autorisent ou interdisent l’accès depuis le port 445, consultez [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Pour vérifier si votre pare-feu ou votre ISP bloque le port 445, utilisez l’outil [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) ou l’applet de commande `Test-NetConnection`. 

Pour utiliser le `Test-NetConnection` applet de commande Azure PowerShell module doit être installé, consultez [installer Azure PowerShell module](/powershell/azure/install-Az-ps) pour plus d’informations. N’oubliez pas de remplacer `<your-storage-account-name>` et `<your-resource-group-name>` avec les noms appropriés de votre compte de stockage.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Si la connexion a réussi, vous devez voir la sortie suivante :
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> La commande ci-dessus retourne l’adresse IP actuelle du compte de stockage. Cette adresse IP est susceptible de changer à tout moment. Ne codez pas cette adresse IP en dur dans un script ou dans une configuration de pare-feu.

### <a name="solution-for-cause-1"></a>Solution pour la cause 1

#### <a name="solution-1---use-azure-file-sync"></a>Solution 1 - utiliser Azure File Sync
Azure File Sync peut transforme votre serveur de Windows en local dans un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Azure File Sync fonctionne sur le port 443 et peut donc être utilisé comme solution de contournement pour accéder Azure Files à partir de clients qui ont le port 445 bloqué. [Découvrez comment configurer Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Solution 2 : utilisation VPN
En configurant un VPN à votre compte de stockage spécifique, le trafic passe par un tunnel sécurisé par opposition à via internet. Suivez le [obtenir des instructions sur la configuration du VPN](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
) pour accéder aux fichiers Azure à partir de Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Solution 3 : débloquer le port 445 à l’aide de votre fournisseur de services / administrateur informatique
Travailler avec votre service informatique ou le fournisseur de services Internet pour ouvrir le port 445 sortant aux [plages IP Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Solution 4 : utiliser l’API REST en fonction des outils tels que Storage Explorer ou de Powershell
Azure Files prend également en charge REST en plus de SMB. L’accès REST fonctionne sur le port 443 (tcp standard). Il existe différents outils qui sont écrits à l’aide de l’API REST qui permettent l’expérience d’interface utilisateur riche. [Explorateur de stockage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) est un d’eux. [Télécharger et installer l’Explorateur de stockage](https://azure.microsoft.com/features/storage-explorer/) et connectez-vous à votre partage de fichiers grâce à Azure Files. Vous pouvez également utiliser [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) qui également utilisateur API REST.


### <a name="cause-2-ntlmv1-is-enabled"></a>Cause 2 : NTLMv1 est activé

Les messages Erreur système 53 ou Erreur système 87 peuvent également s’afficher si la communication NTLMv1 est activée sur le client. Azure Files prend uniquement en charge l’authentification NTLMv2. Le fait d’activer NTLMv1 crée un client moins sécurisé. Par conséquent, les communications sont bloquées pour Azure Files. 

Pour déterminer s’il s’agit de la cause de l’erreur, vérifiez que la sous-clé de Registre suivante est définie sur une valeur de 3 :

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa &gt; LmCompatibilityLevel**

Pour plus d’informations, consultez la rubrique [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) sur TechNet.

### <a name="solution-for-cause-2"></a>Solution pour la cause 2

Rétablissez la valeur **LmCompatibilityLevel** à la valeur par défaut 3 dans la sous-clé de Registre suivante :

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Le message Erreur 1816 « Quota disponible insuffisant pour effectuer cette commande » s’affiche lorsque vous copiez vers un partage de fichiers Azure.

### <a name="cause"></a>Cause :

L’erreur 1816 se produit lorsque vous atteignez la limite autorisée de descripteurs ouverts simultanément pour un fichier sur l’ordinateur où le partage de fichiers est monté.

### <a name="solution"></a>Solution

Réduisez le nombre de handles ouverts simultanément en fermant certains d’entre eux, puis réessayez. Pour plus d’informations, consultez [Liste de contrôle des performances et de l’extensibilité de Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="accessdeniedportal"></a>
## <a name="error-access-denied-when-browsing-to-an-azure-file-share-in-the-portal"></a>Erreur « Accès refusé » quand vous accédez à un partage de fichiers Azure dans le portail

Quand vous accédez à un partage de fichiers Azure dans le portail, vous pouvez recevoir l’erreur suivante :

Accès refusé  
Vous n’avez pas accès  
Apparemment, vous n’avez pas accès à ce contenu. Pour obtenir l’accès, contactez le propriétaire.  

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Cause 1 : Votre compte d’utilisateur n’a pas accès au compte de stockage

### <a name="solution-for-cause-1"></a>Solution pour la cause 1

Accédez au compte de stockage où se trouve le partage de fichiers Azure, cliquez sur **Contrôle d’accès (IAM)** et vérifiez que votre compte d’utilisateur a accès au compte de stockage. Pour en savoir plus, consultez [Guide pratique pour sécuriser votre compte de stockage avec le contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Cause 2 : Des règles de pare-feu ou de réseau virtuel sont activées sur le compte de stockage

### <a name="solution-for-cause-2"></a>Solution pour la cause 2

Vérifiez que les règles de pare-feu et de réseau virtuel sont configurées correctement sur le compte de stockage. Pour vérifier si des règles de pare-feu ou de réseau virtuel sont à l’origine du problème, définissez temporairement le paramètre du compte de stockage sur **Autoriser l’accès à partir de tous les réseaux**. Pour plus d’informations, consultez [Configurer les pare-feu et les réseaux virtuels dans le Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Ralentissement des copies de fichiers vers et à partir d’Azure Files sous Windows

Les performances peuvent être ralenties lorsque vous essayez de transférer des fichiers vers le service Azure File.

- Si vous n’avez pas d’exigence de taille d’E/S minimum spécifique, nous vous recommandons d’utiliser une taille d’E/S de 1 Mio pour des performances optimales.
-   Si vous connaissez la taille finale d’un fichier que vous étendez avec des écritures, et si votre logiciel ne présente aucun problème de compatibilité lorsque la fin non écrite du fichier contient des zéros, définissez la taille du fichier à l’avance au lieu que chaque écriture soit une écriture d’extension.
-   Utilisez la méthode de copie appropriée :
    -   Utilisez [AZCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pour les transferts entre deux partages de fichiers.
    -   Utilisez [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre des partages de fichiers sur un ordinateur local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Informations pour Windows 8.1 ou Windows Server 2012 R2

Pour les clients qui exécutent Windows 8.1 ou Windows Server 2012 R2, assurez-vous que le correctif logiciel [KB3114025](https://support.microsoft.com/help/3114025) est installé. Ce correctif logiciel améliore les performances de création et d’ouverture des handles.

Vous pouvez exécuter le script suivant pour vérifier si le correctif logiciel a été installé :

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Si le correctif logiciel est installé, la sortie suivante s’affiche :

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Les images de Windows Server 2012 R2 dans Azure Marketplace ont le correctif logiciel KB3114025 installé par défaut à compter de décembre 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Aucun dossier avec une lettre de lecteur dans **Poste de travail**

Si vous mappez un partage de fichiers Azure en tant qu’administrateur via Net use, le partage n’apparaît pas.

### <a name="cause"></a>Cause :

Par défaut, l’Explorateur Windows ne s’exécute pas en tant qu’administrateur. Si vous exécutez Net use depuis une invite de commandes administrateur, vous mappez le lecteur réseau en tant qu’administrateur. Étant donné que les lecteurs mappés sont centrés sur l’utilisateur, le compte d’utilisateur qui est connecté n’affiche pas les lecteurs s’ils sont montés sous un compte d’utilisateur différent.

### <a name="solution"></a>Solution
Montez le partage à partir d’une ligne de commande non administrateur. Vous pouvez également suivre [cette rubrique TechNet](https://technet.microsoft.com/library/ee844140.aspx) pour configurer la valeur de Registre **EnableLinkedConnections**.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>La commande Net use échoue si le compte de stockage contient une barre oblique

### <a name="cause"></a>Cause :

La commande Net use interprète une barre oblique (/) comme une option de ligne de commande. Si le nom de votre compte utilisateur commence par une barre oblique, le mappage du lecteur échoue.

### <a name="solution"></a>Solution

Vous pouvez utiliser l’une des étapes suivantes pour contourner le problème :

- Exécutez la commande PowerShell suivante :

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Depuis un fichier de commandes, vous pouvez exécuter la commande de cette façon :

  `Echo new-smbMapping ... | powershell -command –`

- Placez des guillemets doubles autour de la clé pour résoudre ce problème, sauf si la barre oblique est le premier caractère. Si c’est le cas, utilisez le mode interactif et entrez votre mot de passe séparément, ou régénérez vos clés pour obtenir une clé qui ne commence pas par une barre oblique.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>L’application ou service ne peut pas accéder à un lecteur Azure Files monté

### <a name="cause"></a>Cause :

Les lecteurs sont montés par l’utilisateur. Si vous n’exécutez pas l’application ou service depuis le compte utilisateur ayant monté le lecteur, l’application ou service ne verra pas le lecteur.

### <a name="solution"></a>Solution

Utilisez l'une des solutions suivantes :

-   Montez le lecteur depuis le compte utilisateur qui dispose de l’application. Vous pouvez utiliser un outil tel que PsExec.
- Transmettez le nom et la clé du compte de stockage dans les paramètres de nom d’utilisateur et de mot de passe de la commande Net use.
- Utilisez la commande cmdkey pour ajouter les informations d’identification dans le Gestionnaire d’informations d’identification. Effectuez cette procédure à partir d’une ligne de commande dans le contexte du compte de service, via une connexion interactive ou avec RunAs.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mappez le partage directement sans l’aide d’une lettre de lecteur mappée. Certaines applications ne se reconnectent pas correctement à la lettre de lecteur. Il est donc conseillé d’utiliser le chemin d’accès UNC complet qui est plus fiable. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Après avoir suivi ces instructions, vous pouvez recevoir le message d’erreur suivant en exécutant net use pour le compte de service réseau ou système : « Erreur système 1312. Une session ouverte spécifiée n’existe pas. Il se peut qu’elle ait été déjà fermée. » Si cela se produit, vérifiez que le nom d’utilisateur transmis à Net use inclut des informations de domaine (par exemple : « [nom du compte de stockage].file.core.windows.net »).

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erreur « Vous copiez un fichier vers une destination qui ne prend pas en charge le chiffrement »

Lorsqu’un fichier est copié sur le réseau, le fichier est déchiffré sur l’ordinateur source, transmis en clair, puis de nouveau chiffré une fois à destination. Toutefois, vous pouvez voir l’erreur suivante quand vous essayez de copier un fichier chiffré : « Vous copiez le fichier vers une destination qui ne prend pas en charge le chiffrement. »

### <a name="cause"></a>Cause :
Ce problème peut se survenir si vous utilisez le système de fichiers EFS (Encrypting File System). Les fichiers chiffrés BitLocker peuvent être copiés vers Azure Files. Toutefois, Azure Files ne prend pas en charge le système de fichiers EFS NTFS.

### <a name="workaround"></a>Solution de contournement
Pour copier un fichier sur le réseau, vous devez d’abord le déchiffrer. Utilisez l’une des méthodes suivantes :

- Utilisez la commande **copy /d**. Les fichiers chiffrés peuvent ainsi être enregistrés comme fichiers déchiffrés une fois à destination.
- Définissez la clé de Registre suivante :
  - Chemin d’accès = HKLM\Software\Policies\Microsoft\Windows\System
  - Type de valeur = DWORD
  - Nom = CopyFileAllowDecryptedRemoteDestination
  - Valeur = 1

Notez bien que la définition de la clé de Registre affecte toutes les opérations de copie sur les partages réseau.

## <a name="slow-enumeration-of-files-and-folders"></a>Lenteur de l’énumération des fichiers et dossiers

### <a name="cause"></a>Cause :

Ce problème peut se produire quand il n’y a pas suffisamment de mémoire cache sur la machine cliente pour les grands répertoires.

### <a name="solution"></a>Solution

Pour résoudre ce problème, ajustez la valeur de Registre **DirectoryCacheEntrySizeMax** pour permettre la mise en cache des listes de répertoires plus grands sur la machine cliente :

- Localisation : HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Nom de la valeur : DirectoryCacheEntrySizeMax 
- Type de valeur : DWORD
 
 
Par exemple, spécifiez la valeur 0x100000 pour voir si les performances sont meilleures.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Erreur AadDsTenantNotFound dans l’activation de l’authentification Azure Active Directory pour Azure Files « Impossible de localiser des locataires actifs avec l’ID aad-tenant-id »

### <a name="cause"></a>Cause :

L’erreur AadDsTenantNotFound se produit lorsque vous tentez d’[activer l’authentification Azure Active Directory (AAD) pour Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) sur un compte de stockage où un [Service de domaine AAD (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) n’est pas créé sur le locataire AAD de l’abonnement associé.  

### <a name="solution"></a>Solution

Activez AAD DS sur le locataire AAD de l’abonnement sur lequel votre compte de stockage est déployé. Pour créer un domaine managé, vous devez disposer des privilèges d’administrateur du locataire AAD. Si vous n’êtes pas l’administrateur du locataire Azure AD, contactez-le et suivez les instructions pas à pas pour [Activer Azure Active Directory Domain Services à l’aide du portail Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez encore besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour résoudre rapidement votre problème.
