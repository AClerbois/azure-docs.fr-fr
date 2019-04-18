---
title: Collecte des données JSON personnalisées dans Azure Monitor | Microsoft Docs
description: Les sources de données JSON personnalisées peuvent être collectées dans Azure Monitor à l’aide de l’agent Log Analytics pour Linux.  Ces sources de données personnalisées peuvent être de simples scripts qui renvoient JSON en tant que cURL ou l’un des 300 plug-ins de FluentD. Cet article décrit la configuration requise pour cette collecte de données.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 101719668fee155e84b7a767647a662ca845f0f2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848982"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Collecte des sources de données JSON personnalisées à l’aide de l’agent Log Analytics pour Linux dans Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Les sources de données JSON personnalisées peuvent être collectées dans [Azure Monitor](data-platform.md) à l'aide de l'agent Log Analytics pour Linux.  Ces sources de données personnalisées peuvent être des scripts simples qui renvoient JSON en tant que [cURL](https://curl.haxx.se/) ou l’un des [300 plug-ins de FluentD](https://www.fluentd.org/plugins/all). Cet article décrit la configuration requise pour cette collecte de données.


> [!NOTE]
> L’agent Log Analytics pour Linux v1.1.0-217 et ultérieur est requis pour les données JSON personnalisées.

## <a name="configuration"></a>Configuration

### <a name="configure-input-plugin"></a>Configuration du plug-in d’entrée

Pour collecter des données JSON dans Azure Monitor, ajoutez `oms.api.` au début d’une balise FluentD dans un plug-in d’entrée.

Par exemple, ceci est un fichier de configuration distinct `exec-json.conf` dans `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Cet exemple utilise le plug-in FluentD `exec` pour exécuter une commande cURL toutes les 30 secondes.  La sortie de cette commande est collectée par le plug-in de sortie JSON.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
La propriété du fichier de configuration ajouté sous `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` devra être modifiée à l’aide de la commande suivante.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Configuration du plug-in de sortie 
Ajouter la configuration de plug-in de sortie suivante à la configuration principale dans `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` ou en tant que fichier de configuration distinct placé dans `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Redémarrer l’agent Log Analytics pour Linux
Redémarrez l’agent Log Analytics pour le service Linux à l’aide de la commande suivante.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Sortie
Les données sont collectées dans Azure Monitor avec un enregistrement de type `<FLUENTD_TAG>_CL`.

Par exemple, la balise personnalisée `tag oms.api.tomcat` dans Azure Monitor avec un enregistrement de type `tomcat_CL`.  Vous pouvez extraire tous les enregistrements de ce type avec la requête de journal suivante.

    Type=tomcat_CL

Les sources de données JSON imbriquées sont prises en charge, mais sont indexées en fonction du champ parent. Par exemple, les données JSON suivantes sont retournées à partir d’une requête de journal en tant que `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Étapes suivantes
* Découvrez les [requêtes dans les journaux](../log-query/log-query-overview.md) pour analyser les données collectées à partir de sources de données et de solutions. 