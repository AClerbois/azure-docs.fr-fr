---
title: Suivre les modifications avec Azure Automation
description: La solution Change Tracking permet d’identifier les modifications apportées aux logiciels et au service Windows dans votre environnement.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4f917c45030ad70a2ab76fed877bd822d1902f82
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927281"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Suivre les modifications apportées à votre environnement grâce à la solution Suivi des modifications

Cet article vous aide à utiliser la solution Change Tracking pour identifier facilement les modifications apportées dans votre environnement. La solution suit les modifications apportées aux logiciels Windows et Linux, aux fichiers Windows et Linux, aux clés de Registre Windows, aux services Windows et aux démons Linux. Identifier les modifications de configuration peut vous aider à identifier les problèmes opérationnels.

Les modifications apportées aux logiciels installés, aux services Windows, aux fichiers et au Registre Windows ainsi qu’aux démons Linux sur les serveurs supervisés sont envoyées au service cloud Azure Monitor pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement voir les modifications apportées à votre infrastructure de serveur.

> [!NOTE]
> Azure Automation suivi effectue le suivi des modifications dans les machines virtuelles. Pour effectuer le suivi des modifications apportées aux propriétés de Azure Resource Manager, consultez Azure ressource Graph [l’historique des modifications](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="supported-windows-operating-systems"></a>Systèmes d’exploitation Windows pris en charge

Les versions suivantes du système d’exploitation Windows sont officiellement prises en charge pour l’agent Windows :

* Windows Server 2008 R2 ou version ultérieure

## <a name="supported-linux-operating-systems"></a>Systèmes d’exploitation Linux pris en charge

Les distributions Linux suivantes sont officiellement prises en charge. Toutefois, l’agent Linux peut également s’exécuter sur d’autres distributions, qui ne se trouvent pas dans la liste. Sauf indication contraire, toutes les versions mineures sont prises en charge pour chaque version majeure répertoriée.  

### <a name="64-bit"></a>64 bits

* CentOS 6 et 7
* Amazon Linux 2017.09
* Oracle Linux 6 et 7
* Red Hat Enterprise Linux Server 6 et 7
* Debian GNU/Linux 8 et 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS et 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 bits

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 et 9
* Ubuntu Linux 14.04 LTS et 16.04 LTS

## <a name="onboard"></a>Activer Change Tracking et l’inventaire

Pour commencer à suivre les modifications, vous devez activer la solution Change Tracking et Inventory. Il existe de nombreuses façons d’intégrer les machines à Change Tracking et Inventory. Voici des méthodes recommandées et prises en charge pour intégrer la solution.

* [Depuis une machine virtuelle](automation-onboard-solutions-from-vm.md)
* [Depuis plusieurs machines](automation-onboard-solutions-from-browse.md)
* [Depuis votre compte Automation](automation-onboard-solutions-from-automation-account.md)
* [Avec un runbook Azure Automation](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Configuration de Change Tracking et Inventory

Pour découvrir comment intégrer des ordinateurs à la solution, visitez : [Intégration de solutions Automation](automation-onboard-solutions-from-automation-account.md). Une fois que vous avez une machine intégrée à la solution Change Tracking and Inventory, vous pouvez configurer les éléments à suivre. Quand vous activez le suivi d’un nouveau fichier ou d’une nouvelle clé de Registre, ceux-ci sont activés à la fois pour Change Tracking et Inventory.

Pour suivre les modifications apportées à des fichiers sur Windows et Linux, les hachages MD5 de fichiers sont utilisés. Ces hachages sont ensuite utilisés pour détecter si une modification a été apportée depuis le dernier inventaire.

### <a name="configure-linux-files-to-track"></a>Configuration des fichiers Linux à suivre

Utilisez les étapes ci-dessous pour configurer le suivi des fichiers sur des ordinateurs Linux :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **GESTION DE LA CONFIGURATION**. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
2. Dans la page **Change Tracking**, sélectionnez **Fichiers Linux**, puis cliquez sur **+ Ajouter** pour ajouter un nouveau fichier à suivre.
3. Dans la fenêtre **Ajouter le fichier Linux pour le suivi des modifications**, entrez les informations du fichier ou du répertoire à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Entrer le chemin     | Chemin dans lequel rechercher le fichier. Par exemple : « /etc/* .conf »       |
|Type de chemin     | Type d’élément à suivre. Valeurs possibles : fichier et répertoire.        |
|Récursivité     | Détermine si la récursivité est utilisée lorsque vous recherchez l’élément à suivre.        |
|Utiliser sudo     | Ce paramètre détermine si sudo est utilisé lorsque vous vérifiez l’élément.         |
|Liens     | Ce paramètre détermine le traitement des liens symboliques lorsque vous parcourez les répertoires.<br> **Ignorer** : ignore les liens symboliques et n'inclut pas les fichiers/répertoires référencés.<br>**Suivre** : suit les liens symboliques pendant les opérations de récursivité et inclut aussi les fichiers/répertoires référencés.<br>**Gérer** : suit les liens symboliques et autorise la modification du contenu retourné.     |
|Télécharger le contenu du fichier pour tous les paramètres| Active ou désactive le chargement du contenu du fichier pour le suivi des modifications. Options disponibles : **True** ou **False**.|

> [!NOTE]
> L’option permettant de « Gérer » les liens n’est pas recommandée. L’extraction du contenu du fichier n’est pas prise en charge.

### <a name="configure-windows-files-to-track"></a>Configuration des fichiers Windows à suivre

Utilisez les étapes suivantes pour configurer le suivi des fichiers sur des ordinateurs Windows :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **GESTION DE LA CONFIGURATION**. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
2. Dans la page **Change Tracking**, sélectionnez **Fichiers Windows**, puis cliquez sur **+ Ajouter** pour ajouter un nouveau fichier à suivre.
3. Dans la fenêtre **Ajouter le fichier Windows pour le suivi des modifications**, entrez les informations du fichier à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial du fichier à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des fichiers.        |
|Entrer le chemin     | Chemin d’accès pour rechercher le fichier. Exemple : « c:\temp\\\*.txt »<br>Vous pouvez également utiliser des variables d’environnement telles que « %winDir%\System32\\\*.* »       |
|Récursivité     | Détermine si la récursivité est utilisée lorsque vous recherchez l’élément à suivre.        |
|Télécharger le contenu du fichier pour tous les paramètres| Active ou désactive le chargement du contenu du fichier pour le suivi des modifications. Options disponibles : **True** ou **False**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Caractère générique, récursivité et paramètres d’environnement

La récursivité vous permet de spécifier des caractères génériques pour simplifier le suivi entre les répertoires, et des variables d’environnement pour vous permettre d’effectuer le suivi de fichiers entre les environnements avec plusieurs noms de lecteurs ou des noms de lecteurs dynamiques. La liste suivante répertorie les informations courantes que vous devez connaître lors de la configuration de la récursivité :

* Les caractères génériques sont requis pour effectuer le suivi de plusieurs fichiers
* Si vous utilisez des caractères génériques, ceux-ci ne peuvent être utilisés que dans le dernier segment du chemin d’accès. (par exemple, `c:\folder\*file*` ou `/etc/*.conf`)
* Si le chemin d’accès d’une variable d’environnement n’est pas valide, la validation réussit, mais ce chemin d’accès échoue lors de l’exécution de l’inventaire.
* Évitez les chemins d’accès généraux comme `c:\*.*` lors de la définition du chemin d’accès, auquel cas un trop grand nombre de dossiers sont parcourus.

## <a name="configure-file-content-tracking"></a>Configurer le suivi de contenu de fichier

Avec File Content Change Tracking, vous pouvez voir le contenu d’un fichier avant et après modification. Cette fonctionnalité est disponible pour les fichiers Windows et Linux. À chaque modification, le contenu du fichier est stocké dans un compte de stockage, avec présentation de celui-ci avant et après la modification, à la suite ou côte à côte. Pour plus d’informations, consultez [Afficher le contenu d’un fichier suivi](change-tracking-file-contents.md).

![Afficher les modifications d’un fichier](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurer les clés de Registre Windows pour effectuer le suivi

Utilisez les étapes suivantes pour configurer le suivi des clés de Registre sur des ordinateurs Windows :

1. Dans votre compte Automation, sélectionnez **Change Tracking** sous **GESTION DE LA CONFIGURATION**. Cliquez sur **Modifier les paramètres** (le symbole engrenage).
2. Dans la page **Change Tracking**, sélectionnez **Registre Windows**, puis cliquez sur **+ Ajouter** pour ajouter une nouvelle clé de Registre à suivre.
3. Dans la fenêtre **Ajouter le Registre Windows pour le suivi des modifications**, entrez les informations correspondant à la clé à suivre et cliquez sur **Enregistrer**.

|Propriété  |Description  |
|---------|---------|
|activé     | Détermine si le paramètre est appliqué.        |
|Item Name     | Nom convivial de la clé de Registre à suivre.        |
|Groupe     | Nom de groupe pour le regroupement logique des clés de Registre.        |
|Clé de Registre Windows   | Chemin dans lequel rechercher la clé de Registre. Par exemple :  « HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup »      |

## <a name="limitations"></a>Limites

Actuellement, la solution Change Tracking ne prend pas en charge les éléments suivants :

* Récursivité pour le suivi du Registre Windows
* Systèmes de fichiers réseau

Autres limitations :

* La colonne **Taille maximale des fichiers** et ses valeurs ne sont pas utilisées dans l’implémentation actuelle.
* Si vous collectez plus de 2500 fichiers dans le cycle de collecte de 30 minutes, les performances de la solution peuvent être dégradées.
* Lorsque le trafic réseau est élevé, l’affichage des enregistrements de modifications peut prendre jusqu’à six heures.
* Si vous modifiez la configuration lorsqu’un ordinateur est arrêté, l’ordinateur risque de publier des modifications appartenant à la configuration précédente.

## <a name="known-issues"></a>Problèmes connus

La solution Change Tracking connaît les problèmes suivants :

* Les mises à jour de correctif logiciel ne sont pas collectées sur les machines Windows 2016 Core RS3.
* Démons Linux peut indiquer un état modifié même si aucune modification n’est survenu. Cela est dû à la façon dont le `SvcRunLevels` champ est capturé.

## <a name="change-tracking-data-collection-details"></a>Détails de la collecte de données de suivi des modifications

Le tableau suivant indique la fréquence de collecte de données selon les types de modification. Pour chaque type de capture instantanée de données, l’état actuel est également actualisé au moins toutes les 24 heures :

| **Type de modification** | **Fréquence** |
| --- | --- |
| Registre Windows | 50 minutes |
| Fichier Windows | 30 minutes |
| Fichier Linux | 15 minutes |
| Services Windows | 10 secondes à 30 minutes</br> Valeur par défaut : 30 minutes |
| Démons Linux | 5 minutes |
| Logiciels Windows | 30 minutes |
| Logiciels Linux | 5 minutes |

Le tableau suivant montre les limites des éléments suivis par machine pour Change Tracking.

| **Ressource** | **Limite**| **Remarques** |
|---|---|---|
|Fichier|500||
|Registre|250||
|Logiciels Windows|250|N'inclut pas les mises à jour logicielles|
|Packages Linux|1250||
|Services|250||
|Daemon|250||

La consommation moyenne de données Log Analytics d'une machine utilisant la solution Change Tracking and Inventory est d'environ 40 Mo par mois. Cette valeur est approximative et sujette à modification en fonction de votre environnement. Nous vous recommandons de surveiller votre environnement pour connaître votre consommation exacte.

### <a name="windows-service-tracking"></a>Suivi du service Windows

Pour les services Windows, la fréquence de collecte par défaut est de 30 minutes. Pour configurer la fréquence, accédez à **Change Tracking**. Sous l'option **Modifier les paramètres** de l'onglet **Services Windows**, un curseur vous permet de modifier la fréquence de collecte des services Windows (de 10 secondes à 30 minutes). Déplacez la barre du curseur sur la fréquence de votre choix (celle-ci est automatiquement enregistrée).

![Curseur des services Windows](./media/change-tracking/windowservices.png)

L’agent effectue uniquement le suivi des modifications, ce qui permet d’optimiser ses performances. Lorsqu'un seuil élevé est défini, certaines modifications peuvent être omises si le service est rétabli à son état d'origine. Le fait de définir une fréquence moins élevée vous permet d’intercepter les modifications susceptibles d’être omises.

> [!NOTE]
> Même si l’agent peut enregistrer les modifications toutes les 10 secondes, les données mettent quelques minutes à s’afficher dans le portail. Les modifications effectuées pendant l’affichage des données dans le portail continuent d’être suivies et enregistrées.
  
### <a name="registry-key-change-tracking"></a>Suivi des modifications des clés de Registre

Le contrôle des modifications apportées aux clés de Registre a pour objectif d’identifier les points d’extension où peuvent s’activer du code tiers et des logiciels malveillants. La liste suivante présente les clés de Registre préconfigurées. Ces clés sont configurées, mais pas activées. Pour suivre ces clés de Registre, vous devez les activer.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les scripts qui s’exécutent au démarrage.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les scripts qui s’exécutent à l’arrêt.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les clés qui sont chargées avant que l’utilisateur ne se connecte à son compte Windows. La clé est utilisée pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les modifications apportées aux paramètres d’application.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille l’inscription du gestionnaire de superposition d’image sur une icône.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille l’inscription du gestionnaire de superposition d’image sur une icône pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisé pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisé pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les nouvelles extensions Internet Explorer, notamment les menus d’outils et boutons de barre d’outils personnalisés.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les nouvelles extensions Internet Explorer, notamment les menus d’outils et boutons de barre d’outils personnalisés pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc. Analogue à la section [pilotes] du fichier SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits. Analogue à la section [pilotes] du fichier SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille la liste des DLL système connues ou couramment utilisées. Ce système empêche quiconque d’exploiter des autorisations faibles de répertoire d’application en déposant des versions de type cheval de Troie des DLL système.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Surveille la liste des packages capables de recevoir des notifications d’événements de la part de Winlogon, le modèle de prise en charge de l’ouverture de session interactive du système d’exploitation Windows.|

## <a name="network-requirements"></a>Configuration requise pour le réseau

Les adresses suivantes sont exigées particulièrement pour Change Tracking. La communication avec ces adresses s’effectue par le biais du port 443.

|Azure (public)  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|* .azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Utilisation du suivi des modifications

Une fois que la solution est activée, vous pouvez afficher le résumé des modifications de vos ordinateurs surveillés en sélectionnant **Change Tracking** sous **GESTION DE LA CONFIGURATION** dans votre compte Automation.

Vous pouvez afficher les modifications apportées à vos ordinateurs, puis explorer les détails de chaque événement. Des listes déroulante sont disponibles en haut du graphique pour les informations en fonction du type de modification et des intervalles de temps. Vous pouvez également cliquer et faire glisser le curseur sur le graphique pour sélectionner un intervalle de temps personnalisé. **Modifier le Type** aura l’une des valeurs suivantes **événements**, **démons**, **fichiers**, **Registre**,  **Logiciel**, **Windows Services**. Catégorie affiche le type de modification et peut être **Added**, **Modified**, ou **supprimé**.

![image du tableau de bord de suivi des modifications](./media/change-tracking/change-tracking-dash01.png)

Cliquer sur une modification ou un événement permet de faire apparaître des informations détaillées s’y rapportant. Comme vous pouvez le voir à partir de l’exemple, le type de démarrage du service est passé de Manuel à Auto.

![image des détails du suivi des modifications](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Rechercher des journaux d’activité

En plus des détails fournis dans le portail, des recherches peuvent être effectuées dans les journaux d’activité. Avec le **Change Tracking** page, cliquez sur **Analytique de journal**, cette opération ouvre le **journaux** page.

### <a name="sample-queries"></a>Exemples de requêtes

Le tableau suivant fournit des exemples de recherches dans les journaux d’enregistrements de modification collectés par cette solution :

|Requête  |Description  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Affiche les enregistrements d’inventaire les plus récents des services Windows qui ont été définis sur Auto, mais qui ont été signalés comme étant arrêtés.<br>Les résultats se limitent à l’enregistrement le plus récent pour ce SoftwareName et ce Computer.      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Affiche les enregistrements de modification des logiciels supprimés.|

## <a name="alert-on-changes"></a>Alerte sur les modifications

L'une des principales caractéristiques de la solution Change Tracking and Inventory est sa capacité à alerter sur l'état de la configuration et sur les modifications apportées à l'état de la configuration de votre environnement hybride.  

Dans l'exemple suivant, la capture d'écran montre que le fichier `C:\windows\system32\drivers\etc\hosts` a été modifié sur une machine. Ce fichier est important car le fichier Hosts est utilisé par Windows pour associer des noms d'hôtes à des adresses IP et il prime même sur le DNS, ce qui a pu entraîner des problèmes de connectivité ou la redirection du trafic vers des sites web malveillants ou dangereux.

![Graphique montrant la modification du fichier Hosts](./media/change-tracking/changes.png)

Pour analyser cette modification plus en détail, accédez à Recherche dans les journaux en cliquant sur **Log Analytics**. Une fois dans Recherche dans les journaux, recherchez les modifications apportées au contenu du fichier Hosts à l'aide de la requête `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Cette requête permet de rechercher les modifications apportées au contenu des fichiers dont le chemin d'accès complet contient le mot « hosts ». Vous pouvez également rechercher un fichier spécifique en remplaçant le chemin d'accès par sa forme complète (par exemple, `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Une fois que la requête a renvoyé les résultats souhaités, cliquez sur le bouton **Nouvelle règle d'alerte** de l'expérience Recherche dans les journaux pour ouvrir la page de création d'alerte. Vous pouvez également accéder à cette expérience via **Azure Monitor** sur le portail Azure. Dans l'expérience de création d'alerte, vérifiez à nouveau notre requête et modifiez la logique d'alerte. Pour cet exemple, vous souhaitez que l'alerte soit déclenchée à la moindre modification détectée sur l'ensemble des machines de l'environnement.

![Image illustrant la requête de modification pour le suivi des modifications apportées au fichier Hosts](./media/change-tracking/change-query.png)

Une fois la logique de condition définie, attribuez des groupes d'actions pour répondre à l'alerte déclenchée. J'ai ici configuré les actions suivantes : envoi d'e-mails et création d'un ticket ITSM.  De nombreuses autres actions utiles peuvent également être entreprises, telles que le déclenchement d’une fonction Azure, d’un runbook Automation, d’un webhook ou d’une application logique.

![Image illustrant la configuration d'un groupe d'actions pour alerter de la modification](./media/change-tracking/action-groups.png)

Une fois tous les paramètres et la logique définis, nous pouvons appliquer l'alerte à l'environnement.

### <a name="alert-suggestions"></a>Suggestions d'alertes

L'alerte relative aux modifications apportées au fichier Hosts est une bonne application des alertes disponibles pour les données Change Tracking or Inventory, mais il existe de nombreux autres scénarios d'alerte, y compris les cas définis dans la section ci-dessous ainsi que leurs exemples de requêtes.

|Requête  |Description  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|Utile pour le suivi des modifications apportées aux fichiers critiques du système|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|Utile pour le suivi des modifications apportées aux fichiers de configuration de clés|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|Utile pour le suivi des modifications apportées aux services critiques du système|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState != "Running"|Utile pour le suivi des modifications apportées aux services critiques du système|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|Utile pour les environnements nécessitant des configurations logicielles verrouillées|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion != "8.0.11081.0"|Utile pour voir sur quelles machines une version obsolète ou non conforme d'un logiciel est installée. Présente le dernier état de configuration signalé, pas les modifications.|
|ConfigurationChange <br>&#124; where RegistryKey == "HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Utile pour le suivi des modifications apportées aux clés antivirus essentielles|
|ConfigurationChange <br>&#124; where RegistryKey contains "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Utile pour le suivi des modifications apportées aux paramètres de pare-feu|

## <a name="next-steps"></a>Étapes suivantes

Consultez le didacticiel sur Change Tracking pour en savoir plus sur l’utilisation de la solution :

> [!div class="nextstepaction"]
> [Dépanner les modifications apportées à votre environnement](automation-tutorial-troubleshoot-changes.md)

* Utilisez [recherches de journal dans les journaux Azure Monitor](../log-analytics/log-analytics-log-searches.md) pour afficher les données de suivi détaillé.