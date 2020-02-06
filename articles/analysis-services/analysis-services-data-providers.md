---
title: Bibliothèques clientes Azure Analysis Services | Microsoft Docs
description: Décrit les bibliothèques clientes requises pour que les applications clientes et les outils se connectent à Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 006e1fb3fcef1ebcd0e09dffb79149e75eec622a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713177"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliothèques clientes pour la connexion à Azure Analysis Services

Les bibliothèques clientes sont nécessaires pour que les applications clientes et les outils se connectent aux serveurs Analysis Services. Les applications clientes Microsoft telles que Power BI Desktop, Excel, SQL Server Management Studio (SSMS), ainsi que les extensions de projets Analysis Services pour Visual Studio installent les trois bibliothèques clientes et les mettent à jour avec des mises à jour régulières des applications. Dans certains cas, vous devrez peut-être installer des versions plus récentes des bibliothèques clientes. Les applications clientes personnalisées nécessitent également l’installation de bibliothèques clientes.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Télécharger les bibliothèques clientes plus récentes (Windows Installer)  

|Téléchargement  |Version du produit  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.21.27    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.21.27       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.4.0.5    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.4.0.5     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO et ADOMD (packages NuGet)

Les bibliothèques clientes AMO et ADOMD sont disponibles sous forme de packages d’installation sur [NuGet.org](https://www.nuget.org/). Il est recommandé de migrer vers des références NuGet au lieu d’utiliser Windows Installer. 

|Package  | Version du produit  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.4.0.5     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.4.0.5      |

Les assemblys de package NuGet AssemblyVersion respectent la gestion sémantique de version suivante : MAJEURE.MINEURE.CORRECTIF. Les références NuGet chargent la version attendue, même s’il existe une version différente dans le Global Assembly Cache (résultant de l’installation MSI). Le numéro de correctif est incrémenté à chaque version. Les versions AMO et ADOMD sont constamment synchronisées.

## <a name="understanding-client-libraries"></a>Présentation des bibliothèques clientes

Analysis Services utilise trois bibliothèques clientes, également appelées fournisseurs de données. ADOMD.NET et Analysis Services Management Objects (AMO) sont des bibliothèques clientes managées. Le fournisseur Analysis Services OLE DB (MSOLAP DLL) est une bibliothèque cliente native. En général, les trois bibliothèques sont installées en même temps. **Azure Analysis Services nécessite les dernières versions des trois bibliothèques**. 

Les applications clientes Microsoft telles que Power BI Desktop et Excel installent les trois bibliothèques clientes et les mettent à jour quand de nouvelles versions sont disponibles. Selon la version ou la fréquence des mises à jour, certaines bibliothèques clientes peuvent ne pas être les dernières versions requises par Azure Analysis Services. Il en va de même pour les applications personnalisées ou d’autres interfaces telles que AsCmd, TOM, ADOMD.NET. Ces applications nécessitent l’installation manuelle ou par programmation des bibliothèques. Les bibliothèques clientes pour l’installation manuelle sont incluses dans les packs de fonctionnalités SQL Server sous forme de packages distribuables. Toutefois, elles sont liées à la version de SQL Server et il se peut qu’elles ne soient pas à jour.  

Les bibliothèques clientes pour les connexions clientes sont différentes des fournisseurs de données requis pour connecter un serveur Azure Analysis Services à une source de données. Pour plus d’informations sur les connexions aux sources de données, consultez [Connexions à des sources de données](analysis-services-datasource.md).

## <a name="client-library-types"></a>Types de bibliothèques clientes

### <a name="analysis-services-ole-db-provider-msolap"></a>Fournisseur OLE DB pour Analysis Services (MSOLAP) 

 Le fournisseur Analysis Services OLE DB (MSOLAP) est la bibliothèque cliente native pour les connexions de base de données Analysis Services. Il est utilisé indirectement par ADOMD.NET et AMO, les demandes de connexion étant déléguées au fournisseur de données. Vous pouvez également appeler le fournisseur OLE DB directement à partir de code d’application.  
  
 Le fournisseur Analysis Services OLE DB est installé automatiquement par la plupart des outils et des applications clientes utilisés pour accéder aux bases de données Analysis Services. Il doit être installé sur les ordinateurs utilisés pour accéder aux données Analysis Services.  
  
 Les fournisseurs OLE DB sont souvent spécifiés dans les chaînes de connexion. Une chaîne de connexion Analysis Services utilise une nomenclature différente pour faire référence au fournisseur OLE DB : MSOLAP.\<version>.dll.

### <a name="amo"></a>AMO  

 AMO est une bibliothèque cliente managée utilisée pour l’administration serveur et la définition de données. Elle est installée et utilisée par des outils et des applications clientes. Par exemple, SQL Server Management Studio (SSMS) utilise AMO pour se connecter à Analysis Services. Une connexion utilisant AMO se résume généralement à `"data source=\<servername>"`. Une fois une connexion établie, vous utilisez l’API pour recourir à des collections de bases de données et à des objets principaux. Visual Studio et SSMS utilisent AMO pour se connecter à une instance Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET est une bibliothèque cliente de données managée utilisée pour interroger des données Analysis Services. Elle est installée et utilisée par des outils et des applications clientes. 
  
 Durant la connexion à une base de données, les propriétés de chaîne de connexion pour les trois bibliothèques sont similaires. Pratiquement toute chaîne de connexion que vous définissez pour ADOMD.NET à l’aide de [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) fonctionne également pour AMO et le fournisseur OLE DB Analysis Services (MSOLAP). Pour plus d’informations, consultez [Propriétés des chaînes de connexion &#40;Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Comment déterminer la version de la bibliothèque cliente   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Atteindre `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Si vous avez plusieurs dossiers, choisissez le numéro le plus élevé.
  
2.  Cliquez avec le bouton droit sur **msolap.dll** > **Propriétés** > **Détails**. Si le nom de fichier est msolap140.dll, la dll est plus ancienne que la version la plus récente et doit donc être mise à niveau.
    
    ![Détails de la bibliothèque cliente](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Atteindre `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Si vous avez plusieurs dossiers, choisissez le numéro le plus élevé.
2. Cliquez avec le bouton droit sur **Microsoft.AnalysisServices** > **Propriétés** > **Détails**.  

### <a name="adomd"></a>ADOMD

1. Atteindre `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Si vous avez plusieurs dossiers, choisissez le numéro le plus élevé.
2. Cliquez avec le bouton droit sur **Microsoft.AnalysisServices.AdomdClient** > **Propriétés** > **Détails**.  


## <a name="next-steps"></a>Étapes suivantes
[Connexion avec Excel](analysis-services-connect-excel.md)    
[Connexion avec Power BI](analysis-services-connect-pbi.md)
