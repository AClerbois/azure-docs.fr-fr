---
title: Alias de noms de serveurs Azure Analysis Services | Microsoft Docs
description: Explique comment créer et utiliser des alias de noms de serveurs.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 161df354bae350df533f3991e551401fd56a4a65
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301078"
---
# <a name="alias-server-names"></a>Alias de noms de serveurs

Grâce aux alias de noms de serveurs, les utilisateurs peuvent se connecter à votre serveur Azure Analysis Services avec un *alias* plus court que le nom du serveur. En cas de connexion à partir d’une application cliente, l’alias est spécifié comme point de terminaison suivant le format de protocole **link://** . Le point de terminaison retourne ensuite le nom réel du serveur pour permettre la connexion.

Les alias de noms de serveurs sont utiles pour :

- migrer des modèles d’un serveur à un autre sans affecter les utilisateurs ; 
- proposer aux utilisateurs des noms conviviaux de serveurs, plus faciles à mémoriser ; 
- rediriger les utilisateurs vers différents serveurs selon les moments de la journée ; 
- rediriger les utilisateurs de différentes régions vers les instances les plus proches géographiquement, comme avec Azure Traffic Manager. 

Tout point de terminaison HTTP qui retourne un nom de serveur Azure Analysis Services valide peut servir d’alias. Le point de terminaison doit prendre en charge HTTPS sur le port 443, et le port ne doit pas être spécifié dans l’URI.

![Alias au format link](media/analysis-services-alias/aas-alias-browser.png)

En cas de connexion à partir d’un client, l’alias de nom de serveur est entré suivant le format de protocole **link://** . Par exemple, dans Power BI Desktop :

![Connexion Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Créer un alias

Pour créer un alias de point de terminaison, vous pouvez utiliser la méthode de votre choix, à condition qu’elle retourne un nom de serveur Azure Analysis Services valide : par exemple, une référence à un fichier dans le Stockage Blob Azure contenant le nom réel du serveur ; vous pouvez également créer et publier une application Web Forms ASP.NET.

Cet exemple crée une application Web Forms ASP.NET dans Visual Studio. Le contrôle utilisateur et la référence de la page maître sont supprimés de la page Default.aspx. Le contenu de Default.aspx correspond simplement à la directive Page suivante :

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

L’événement Page_Load de Default.aspx.cs utilise la méthode Response.Write() pour retourner le nom du serveur Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Voir aussi

[Bibliothèques clientes](analysis-services-data-providers.md)   
[Se connecter à partir de Power BI Desktop](analysis-services-connect-pbi.md)
