---
title: Śledzenie zależności w usłudze Azure Application Insights przy użyciu języka Python OpenCensus | Microsoft Docs
description: Monitoruj wywołania zależności dla aplikacji w języku Python za pośrednictwem języka Python OpenCensus.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: abfd5e104bd4854781a0d3c9d08544506279518a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850087"
---
# <a name="track-dependencies-with-opencensus-python"></a>Śledzenie zależności przy użyciu języka Python OpenCensus

Zależność to składnik zewnętrzny wywoływany przez aplikację. Dane zależności są zbierane przy użyciu języka Python OpenCensus i różnych integracji. Dane są następnie wysyłane do Application Insights w obszarze Azure Monitor jako dane `dependencies` telemetryczne.

Najpierw Instrumentacja aplikacji w języku Python przy użyciu najnowszego [zestawu SDK języka Python OpenCensus](./opencensus-python.md).

## <a name="in-process-dependencies"></a>Zależności w procesie

OpenCensus Python SDK dla Azure Monitor umożliwia wysyłanie danych telemetrycznych zależności w procesie (informacji i logiki, które są wykonywane w aplikacji). Zależności w procesie będą mieć takie samo `type` pole jak `INPROC` w analizie.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Zależności z integracją "requests"

Śledź żądania wychodzące z `requests` integracją OpenCensus.

Pobierz i zainstaluj `opencensus-ext-requests` program z [PyPI](https://pypi.org/project/opencensus-ext-requests/) i dodaj go do integracji śledzenia. Żądania wysyłane za pomocą biblioteki [żądań](https://pypi.org/project/requests/) języka Python będą śledzone.

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

## <a name="dependencies-with-httplib-integration"></a>Zależności z integracją "httplib"

Śledź żądania wychodzące z `httplib` integracją OpenCensus.

Pobierz i zainstaluj `opencensus-ext-httplib` program z [PyPI](https://pypi.org/project/opencensus-ext-httplib/) i dodaj go do integracji śledzenia. Żądania wysyłane przy użyciu [protokołu HTTP. Client](https://docs.python.org/3.7/library/http.client.html) dla python3 lub [httplib](https://docs.python.org/2/library/httplib.html) dla python2 będą śledzone.

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

## <a name="dependencies-with-django-integration"></a>Zależności z integracją "Django"

Śledź wychodzące żądania Django z `django` integracją OpenCensus.

> [!NOTE]
> Jedyne wychodzące żądania Django są wywołaniami wykonywanymi w bazie danych. W przypadku żądań wysyłanych do aplikacji Django zobacz [przychodzące żądania](./opencensus-python-request.md#tracking-django-applications).

Pobierz i zainstaluj `opencensus-ext-django` z programu [PyPI](https://pypi.org/project/opencensus-ext-django/) i Dodaj następujący wiersz do `MIDDLEWARE` sekcji w pliku Django `settings.py` .

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Można podać dodatkową konfigurację, aby [dowiedzieć się, jak uzyskać pełne](https://github.com/census-instrumentation/opencensus-python#customization) odwołanie.

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
            connection_string="InstrumentationKey=<your-ikey-here>"
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>Zależności z integracją "MySQL"

Śledź zależności MYSQL z `mysql` integracją OpenCensus. Ta integracja obsługuje bibliotekę [łącznika MySQL](https://pypi.org/project/mysql-connector-python/) .

Pobierz i zainstaluj `opencensus-ext-mysql` z programu [PyPI](https://pypi.org/project/opencensus-ext-mysql/) i Dodaj do kodu następujące wiersze.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Zależności z integracją "pymysql"

Śledź zależności PyMySQL z `pymysql` integracją OpenCensus.

Pobierz i zainstaluj `opencensus-ext-pymysql` z programu [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) i Dodaj do kodu następujące wiersze.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Zależności z integracją "PostgreSQL"

Śledź zależności PostgreSQL z `postgresql` integracją OpenCensus. Ta integracja obsługuje bibliotekę [psycopg2](https://pypi.org/project/psycopg2/) .

Pobierz i zainstaluj `opencensus-ext-postgresql` z programu [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) i Dodaj do kodu następujące wiersze.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Zależności z integracją "pymongo"

Śledź zależności MongoDB z `pymongo` integracją OpenCensus. Ta integracja obsługuje bibliotekę [pymongo](https://pypi.org/project/pymongo/) .

Pobierz i zainstaluj `opencensus-ext-pymongo` z programu [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) i Dodaj do kodu następujące wiersze.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Zależności z integracją "SQLAlchemy"

Śledź zależności przy użyciu usługi SQLAlchemy przy użyciu `sqlalchemy` integracji OpenCensus. Ta integracja śledzi użycie pakietu [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) niezależnie od podstawowej bazy danych.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Następne kroki

* [Mapa aplikacji](./app-map.md)
* [Dostępność](./monitor-web-app-availability.md)
* [Wyszukiwanie](./diagnostic-search.md)
* [Zapytanie log (Analytics)](../log-query/log-query-overview.md)
* [Diagnostyka transakcji](./transaction-diagnostics.md)

