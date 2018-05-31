---
title: Optimiser les coûts pour SQL Server sur les machines virtuelles Azure | Microsoft Docs
description: Fournit des meilleures pratiques pour choisir le bon modèle de tarification de machine virtuelle SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2018
ms.author: jroth
ms.openlocfilehash: 71c86af9d4dcdf1026b4f539574b9932ef1cfc89
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32767798"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Tarification des machines virtuelles SQL Server Azure

Cet article fournit des informations de tarification pour les [machines virtuelles SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) dans Azure. Plusieurs facteurs ont un impact sur le coût, et il est important de choisir le bon équilibre entre les coûts et les besoins de l’entreprise.

> [!TIP]
> Si vous cherchez seulement à obtenir une estimation de coût pour une combinaison spécifique d’édition de SQL Server et de taille de machine virtuelle, consultez la page des tarifs pour [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Sélectionnez votre plateforme et l’édition de SQL Server dans la liste **Système d’exploitation/Logiciel**.
>
> ![Interface utilisateur sur la page de tarification des machines virtuelles](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Ou utilisez la [Calculatrice de prix](https://azure.microsoft.com/pricing/#explore-cost) pour ajouter et configurer une machine virtuelle. 

## <a name="free-licensed-sql-server-editions"></a>Éditions SQL Server sous licence libre

Si vous souhaitez développer, tester ou générer une preuve de concept, utilisez l’édition **SQL Server Développeur** sous licence libre. Cette édition offre toutes les fonctionnalités de SQL Server Entreprise, ce qui vous permet de générer et de tester n’importe quel type d’application. Toutefois, vous ne pouvez pas exécuter l’édition Développeur en production. Une machine virtuelle exécutant SQL Server Développeur engendre uniquement des frais liés au coût de la machine virtuelle, car aucun coût de licence n’est associé à cette édition.

Si vous souhaitez exécuter une charge de travail légère en production (< 4 cœurs, < 1 Go de mémoire, < 10 Go/base de données), utilisez l’édition **SQL Server Express** sous licence libre. Là encore, une machine virtuelle exécutant SQL Server Express engendre uniquement des frais liés au coût de la machine virtuelle.

Pour ces charges de travail de développement/test et de production légère, vous pouvez également faire des économies en choisissant une machine virtuelle plus petite qui correspond à ces charges de travail. DS1v2 peut s’avérer un bon choix dans certains scénarios.

Pour créer une machine virtuelle SQL Server 2017 Azure avec l’une de ces images, consultez les liens suivants :

| Plateforme | Images sous licence libre |
|---|---|
| Windows Server 2016 | [Machine virtuelle Azure SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[Machine virtuelle Azure SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [Machine virtuelle Azure SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[Machine virtuelle Azure SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Machine virtuelle Azure SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[Machine virtuelle Azure SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [Machine virtuelle Azure SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[Machine virtuelle Azure SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Éditions SQL Server payantes

Si votre charge de travail de production n’est pas légère, utilisez l’une des éditions suivantes de SQL Server :

| Édition SQL Server | Charge de travail |
|-----|-----|
| Web | Petits sites web |
| standard | Charges de travail faibles ou moyennes |
| Entreprise | Charges de travail volumineuses ou critiques|

Pour ces éditions, vous pouvez vous acquitter de la licence SQL Server de deux manières : par un *paiement à l’utilisation* ou par la méthode *BYOL (apportez votre propre licence)*.

## <a name="pay-per-usage"></a>Paiement à l’utilisation

**Le paiement d’une licence SQL Server à l’utilisation** signifie que le coût par seconde de l’exécution de la machine virtuelle Azure comprend le coût de la licence SQL Server. Les tarifs des différentes éditions de SQL Server (Web, Standard, Entreprise) sont indiqués dans la page des tarifs des machines virtuelles Azure pour [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Le coût est le même pour toutes les versions de SQL Server (de 2012 SP3 à 2017). Les frais de licence par seconde varient en fonction du nombre de cœurs de la machine virtuelle, ce qui est la norme pour toutes les licences SQL Server.

Le paiement de la licence SQL Server à l’utilisation est recommandé pour :

- **Les charges de travail temporaires ou périodiques**. Par exemple, une application qui doit soutenir un événement pendant quelques mois chaque année, ou une analyse métier réalisée tous les lundis.

- **Les charges de travail dont la durée de vie ou la mise à l’échelle ne sont pas connues**. Par exemple, une application qui peut ne pas être requise dans quelques mois, ou qui peut nécessiter plus ou moins de puissance de calcul, en fonction de la demande.

Pour créer une machine virtuelle SQL Server 2017 Azure avec l’une de ces images de paiement à l’utilisation, consultez les liens suivants :

| Plateforme | Images sous licence |
|---|---|
| Windows Server 2016 | [Machine virtuelle Azure Web SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[Machine virtuelle Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Machine virtuelle Azure Entreprise SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [Machine virtuelle Azure Web SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[Machine virtuelle Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Machine virtuelle Azure Entreprise SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Machine virtuelle Azure Web SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[Machine virtuelle Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Machine virtuelle Azure Entreprise SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Machine virtuelle Azure Web SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[Machine virtuelle Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Machine virtuelle Azure Entreprise SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Lorsque vous créez une machine virtuelle SQL Server dans le portail, la fenêtre **Choisir une taille** affiche le coût estimé. Il est important de noter que cette estimation correspond uniquement aux frais de calcul liés à l’exécution de la machine virtuelle, ainsi qu’aux coûts de licence du système d’exploitation (Windows ou système d’exploitation Linux tiers).
>
> ![Panneau Choisir la taille de la machine virtuelle](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Elle n’inclut pas les coûts de licence SQL Server supplémentaires pour les éditions Web, Standard et Enterprise. Pour obtenir une estimation plus précise des tarifs, sélectionnez votre système d’exploitation et l’édition SQL Server dans la page des tarifs pour [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="bring-your-own-license-byol"></a>BYOL (apportez votre propre licence)

L’**apport de votre propre licence SQL Server par le biais de License Mobility**, également appelé **BYOL**, implique l’utilisation d’une licence en volume SQL Server existante avec Software Assurance dans une machine virtuelle Azure. Une machine virtuelle SQL Server qui a recours à la méthode BYOL facture uniquement le coût d’exécution de la machine virtuelle, et non la licence SQL Server, étant donné que vous avez déjà acquis les licences et Software Assurance dans le cadre d’un programme de licence en volume.

> [!NOTE]
> Les images BYOL ne sont actuellement disponibles que pour les machines virtuelles Windows. Toutefois, vous pouvez installer manuellement SQL Server sur une machine virtuelle Linux uniquement. Consultez les instructions dans le [Forum Aux Questions sur la machine virtuelle SQL Linux](../../linux/sql/sql-server-linux-faq.md).

L’apport de votre propre licence SQL par le biais de Licence Mobility est recommandé pour :

- **Les charges de travail continues**. Par exemple, une application qui doit prendre en charge les opérations de l’entreprise 24 h/24, 7 j/7.

- **Les charges de travail dont la durée de vie et la mise à l’échelle sont connues**. Par exemple, une application qui sera requise pendant toute l’année et dont la demande a été prévue.

Pour utiliser la méthode BYOL avec une machine virtuelle SQL Server, vous devez posséder une licence SQL Server Standard ou Enterprise ainsi que [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), qui est une option obligatoire pour certains programmes de licence en volume, et une option facultative pour d’autres. Le niveau de tarification fourni par les programmes de licence en volume varie selon le type de contrat et la quantité et/ou l’engagement vis-à-vis de SQL Server. Mais en règle générale, l’apport de votre propre licence pour les charges de travail continues présente les avantages suivants :

| Avantages de la méthode BYOL | Description |
|-----|-----|
| **Réduction des coûts** | L’apport de votre propre licence SQL Server est plus économique que le paiement à l’utilisation si une charge de travail doit exécuter en permanence SQL Server Standard ou Enterprise pendant *plus de 10 mois*. |
| **Économies sur le long terme** | En moyenne, l’achat ou le renouvellement d’une licence SQL Server coûte *30 % moins cher par an* pendant les trois premières années. En outre, après 3 ans, vous n’avez plus besoin de renouveler la licence. Il ne vous reste qu’à payer Software Assurance. À ce stade, l’opération revient *200 % moins cher*. |
| **Réplica secondaire passif gratuit** | Autre avantage de l’apport de votre propre licence : la [licence gratuite pour un réplica secondaire passif](https://azure.microsoft.com/pricing/licensing-faq/) par SQL Server à des fins de haute disponibilité. Cela réduit de moitié le coût de licence d’un déploiement SQL Server à haute disponibilité (par exemple, à l’aide de groupes de disponibilité AlwaysOn). Les droits d’exécution du réplica secondaire passif sont fournis au travers des avantages procurés par Software Assurance pour les serveurs de basculement. |

Pour créer une machine virtuelle SQL Server 2017 Azure avec l’une de ces images BYOL, reportez-vous aux machines virtuelles contenant le préfixe « {BYOL} » :

- [Machine virtuelle Azure Entreprise SQL Server 2017](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [Machine virtuelle Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Veuillez nous indiquer sous 10 jours le nombre de licences SQL Server que vous utilisez dans Azure. Les liens vers les images précédentes comportent des instructions sur la manière de procéder.

> [!NOTE]
> Il n’est pas possible de modifier le modèle de licence d’une machine virtuelle SQL Server payée à la seconde pour utiliser votre propre licence. Dans ce cas, vous devez créer une nouvelle machine virtuelle BYOD et migrer vos bases de données vers la nouvelle machine virtuelle.

## <a name="reduce-costs"></a>Réduire les coûts

Pour éviter des coûts inutiles, choisissez une taille de machine virtuelle optimale et envisagez des arrêts intermittents pour les charges de travail non continues.

### <a id="machinesize"></a> Dimensionner correctement votre machine virtuelle

Le coût de la licence SQL Server est directement lié au nombre de cœurs. Choisissez une taille de machine virtuelle qui correspond à vos besoins en termes de processeur, mémoire, stockage et bande passante d’E/S. Pour obtenir la liste complète des options de taille de machine virtuelle, consultez [Tailles de machine virtuelle Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) et [Tailles de machine virtuelle Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Il existe de nouvelles tailles de machine virtuelle qui fonctionnent correctement avec certains types de charges de travail SQL Server. Ces tailles de machine virtuelle ont des niveaux élevés de mémoire, de stockage et de bande passante d’E/S, mais elles possèdent un nombre de cœurs virtualisés inférieur. Considérez l’exemple suivant :

| Taille de la machine virtuelle | Processeurs virtuels | Mémoire | Disques max | Débit d’E/S max | Coûts de licence SQL | Total des coûts (calcul + licence) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 Go | 32 | 51 200 IOPS ou 768 Mo/s | | |
| **Standard_DS14-4v2** | 4 | 112 Go | 32 | 51 200 IOPS ou 768 Mo/s | 75 % inférieur | 57 % inférieur |

> [!IMPORTANT]
> Il s’agit d’un exemple ponctuel. Pour les spécifications les plus récentes, consultez les articles sur les tailles de machine virtuelle et la page de tarification Azure pour [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) et [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Dans l’exemple précédent, vous pouvez voir que les spécifications de **Standard_DS14v2** et **Standard_DS14-4v2** sont identiques à l’exception des processeurs virtuels. Le suffixe **-4v2** à la fin de la taille de machine **Standard_DS14-4v2** indique le nombre de processeurs virtuels actifs. Étant donné que les coûts de licence SQL Server sont liés au nombre de cœurs, cela réduit considérablement le coût de la machine virtuelle dans les scénarios où les processeurs virtuels supplémentaires ne sont pas nécessaires. Il s’agit d’un seul exemple. Il existe de nombreuses tailles de machine avec des processeurs virtuels limités qui sont identifiées par ce modèle de suffixe. Pour plus d’informations, consultez le billet de blog [Announcing new Azure VM sizes for more cost-effective database work](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Arrêter votre machine virtuelle lorsque cela est possible

Si vous utilisez des charges de travail qui ne s’exécutent pas en continu, envisagez d’arrêter la machine virtuelle pendant les périodes d’inactivité. Vous paierez uniquement pour ce que vous utiliserez.

Par exemple, si vous testez simplement SQL Server sur une machine virtuelle Azure, vous ne souhaitez pas engendrer de coûts en le laissant accidentellement s’exécuter pendant des semaines. Pour remédier à ce problème, il est possible de recourir à la [fonction d’arrêt automatique](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Arrêt automatique des machines virtuelles SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

L’arrêt automatique fait partie d’un ensemble plus important de fonctionnalités similaires fournies par [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Pour les autres flux de travail, envisagez l’arrêt et le redémarrage automatiques des machines virtuelles Azure avec une solution de script, comme [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> L’arrêt et la libération de votre machine virtuelle constituent la seule façon d’éviter des frais. Le simple arrêt ou l’utilisation des options d’alimentation pour arrêter la machine virtuelle entraînent toujours des frais d’utilisation.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une assistance globale sur la tarification Azure, consultez [Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure](../../../billing/billing-getting-started.md). Pour obtenir les tarifs des machines virtuelles les plus récents, notamment ceux de SQL Server, consultez la page des tarifs des machines virtuelles Azure pour [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) et [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Pour obtenir une vue d’ensemble de SQL Server sur des machines virtuelles Azure, consultez les articles suivants :

- [Vue d’ensemble de SQL Server sur des machines virtuelles Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Vue d’ensemble de SQL Server sur des machines virtuelles Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)