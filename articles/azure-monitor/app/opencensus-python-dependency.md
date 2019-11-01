---
title: Suivi des dépendances dans Azure Application Insights avec OpenCensus Python | Microsoft Docs
description: Supervisez les appels de dépendances pour vos applications Python via OpenCensus Python.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 00ebf86bf6d0d57b29d660f78aae3dd828d44ded
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819143"
---
# <a name="track-dependencies-with-opencensus-python"></a>Effectuer le suivi des dépendances avec OpenCensus Python

Une dépendance est un composant externe appelé par votre application. Les données de dépendance sont collectées à l’aide d’OpenCensus Python et de ses diverses intégrations. Ces données sont ensuite envoyées à Application Insights dans Azure Monitor.

Commencez par instrumenter votre application Python avec le dernier [kit SDK OpenCensus Python](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Dépendances in-process

Le kit SDK OpenCensus Python pour Azure Monitor vous permet d’envoyer une télémétrie des dépendances « in-process » (informations et logiques présentes dans votre application). Le champ `type` des dépendances in-process a la valeur `INPROC` dans Analytics.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Dépendances avec intégration de « requests »

Effectuez le suivi de vos requêtes sortantes avec l’intégration de `requests` d’OpenCensus.

Téléchargez et installez `opencensus-ext-requests` à partir de [PyPI](https://pypi.org/project/opencensus-ext-requests/), puis ajoutez-le aux intégrations de trace. Les requêtes envoyées à l’aide de la bibliothèque de [requêtes](https://pypi.org/project/requests/) Python font l’objet d’un suivi.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Dépendances avec intégration de « httplib »

Effectuez le suivi de vos requêtes sortantes avec l’intégration de `httplib` d’OpenCensus.

Téléchargez et installez `opencensus-ext-httplib` à partir de [PyPI](https://pypi.org/project/opencensus-ext-httplib/), puis ajoutez-le aux intégrations de trace. Les requêtes envoyées à l’aide de [http.client](https://docs.python.org/3.7/library/http.client.html) pour Python3 ou de [httplib](https://docs.python.org/2/library/httplib.html) pour Python2 font l’objet d’un suivi.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Dépendances avec intégration de « django »

Effectuez le suivi de vos requêtes Django sortantes avec l’intégration de `django` d’OpenCensus.

Téléchargez et installez `opencensus-ext-django` à partir de [PyPI](https://pypi.org/project/opencensus-ext-django/), puis ajoutez la ligne suivante à la section `MIDDLEWARE` du fichier `settings.py` de Django.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Vous pouvez fournir une configuration supplémentaire. Lisez les [personnalisations](https://github.com/census-instrumentation/opencensus-python#customization) pour obtenir une référence complète.

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.ocagent.trace_exporter.TraceExporter(
            service_name='foobar',
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>Dépendances avec intégration de « mysql »

Effectuez le suivi de vos dépendances MYSQL avec l’intégration de `mysql` d’OpenCensus. Cette intégration prend en charge la bibliothèque [mysql-connector](https://pypi.org/project/mysql-connector-python/).

Téléchargez et installez `opencensus-ext-mysql` à partir de [PyPI](https://pypi.org/project/opencensus-ext-mysql/), puis ajoutez les lignes suivantes à votre code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Dépendances avec intégration de « pymysql »

Effectuez le suivi de vos dépendances PyMySQL avec l’intégration de `pymysql` d’OpenCensus.

Téléchargez et installez `opencensus-ext-pymysql` à partir de [PyPI](https://pypi.org/project/opencensus-ext-pymysql/), puis ajoutez les lignes suivantes à votre code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Dépendances avec intégration de « postgresql »

Effectuez le suivi de vos dépendances PostgreSQL avec l’intégration de `postgresql` d’OpenCensus. Cette intégration prend en charge la bibliothèque [psycopg2](https://pypi.org/project/psycopg2/).

Téléchargez et installez `opencensus-ext-postgresql` à partir de [PyPI](https://pypi.org/project/opencensus-ext-postgresql/), puis ajoutez les lignes suivantes à votre code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Dépendances avec intégration de « pymongo »

Effectuez le suivi de vos dépendances MongoDB avec l’intégration de `pymongo` d’OpenCensus. Cette intégration prend en charge la bibliothèque [pymongo](https://pypi.org/project/pymongo/).

Téléchargez et installez `opencensus-ext-pymongo` à partir de [PyPI](https://pypi.org/project/opencensus-ext-pymongo/), puis ajoutez les lignes suivantes à votre code.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Dépendances avec intégration de « sqlalchemy »

Effectuez le suivi de vos dépendances SQLAlchemy à l’aide de l’intégration `sqlalchemy` d’OpenCensus. Cette intégration effectue le suivi de l’utilisation du package [sqlalchemy](https://pypi.org/project/SQLAlchemy/), quelle que soit la base de données sous-jacente.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Étapes suivantes

* [Plan de l’application](../../azure-monitor/app/app-map.md)
* [Disponibilité](../../azure-monitor/app/monitor-web-app-availability.md)
* [action](../../azure-monitor/app/diagnostic-search.md)
* [Requête de journal (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostics de transaction](../../azure-monitor/app/transaction-diagnostics.md)