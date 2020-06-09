---
title: Śledzenie żądań przychodzących na platformie Azure Application Insights przy użyciu języka Python OpenCensus | Microsoft Docs
description: Monitoruj wywołania żądań dla aplikacji w języku Python za pośrednictwem języka Python OpenCensus.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: tracking-python
ms.openlocfilehash: 10d54088859332ad986dc642247c6af96b378978
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84553904"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Śledź przychodzące żądania przy użyciu języka Python OpenCensus

Dane żądania przychodzącego są zbierane przy użyciu języka Python OpenCensus i różnych integracji. Śledź przychodzące dane żądania wysyłane do aplikacji sieci Web utworzonych na podstawie popularnych struktur sieci Web `django` `flask` i `pyramid` . Dane są następnie wysyłane do Application Insights w obszarze Azure Monitor jako dane `requests` telemetryczne.

Najpierw Instrumentacja aplikacji w języku Python przy użyciu najnowszego [zestawu SDK języka Python OpenCensus](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Śledzenie aplikacji Django

1. Pobierz i zainstaluj `opencensus-ext-django` program [PyPI](https://pypi.org/project/opencensus-ext-django/) i instrumentację aplikacji za pomocą `django` oprogramowania pośredniczącego. Żądania przychodzące wysyłane do `django` aplikacji będą śledzone.

2. Dołącz `opencensus.ext.django.middleware.OpencensusMiddleware` do `settings.py` pliku pod `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Upewnij się, że AzureExporter jest prawidłowo skonfigurowany w `settings.py` ramach użytkownika `OPENCENSUS` .

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

4. Możesz również dodać adresy URL w `settings.py` obszarze `BLACKLIST_PATHS` dla żądań, które nie mają być śledzone.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Śledzenie aplikacji kolb

1. Pobierz i zainstaluj `opencensus-ext-flask` program [PyPI](https://pypi.org/project/opencensus-ext-flask/) i instrumentację aplikacji za pomocą `flask` oprogramowania pośredniczącego. Żądania przychodzące wysyłane do `flask` aplikacji będą śledzone.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. `flask`Oprogramowanie pośredniczące można skonfigurować bezpośrednio w kodzie. W przypadku żądań z adresów URL, które nie mają być śledzone, Dodaj je do programu `BLACKLIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Śledzenie aplikacji ostrosłupa

1. Pobierz i zainstaluj `opencensus-ext-django` program [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) i instrumentację aplikacji za pomocą `pyramid` animacji. Żądania przychodzące wysyłane do `pyramid` aplikacji będą śledzone.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Możesz skonfigurować `pyramid` animację bezpośrednio w kodzie. W przypadku żądań z adresów URL, które nie mają być śledzone, Dodaj je do programu `BLACKLIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Następne kroki

* [Mapa aplikacji](../../azure-monitor/app/app-map.md)
* [Dostępność](../../azure-monitor/app/monitor-web-app-availability.md)
* [Wyszukiwanie](../../azure-monitor/app/diagnostic-search.md)
* [Zapytanie log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostyka transakcji](../../azure-monitor/app/transaction-diagnostics.md)
