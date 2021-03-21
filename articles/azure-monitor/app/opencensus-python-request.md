---
title: Śledzenie żądań przychodzących na platformie Azure Application Insights przy użyciu języka Python OpenCensus | Microsoft Docs
description: Monitoruj wywołania żądań dla aplikacji w języku Python za pośrednictwem języka Python OpenCensus.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 3b029a9cb14a81c80072847dc17d6b71f480743f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585666"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Śledź przychodzące żądania przy użyciu języka Python OpenCensus

Dane żądania przychodzącego są zbierane przy użyciu języka Python OpenCensus i różnych integracji. Śledź przychodzące dane żądania wysyłane do aplikacji sieci Web utworzonych na podstawie popularnych struktur sieci Web `django` `flask` i `pyramid` . Dane są następnie wysyłane do Application Insights w obszarze Azure Monitor jako dane `requests` telemetryczne.

Najpierw Instrumentacja aplikacji w języku Python przy użyciu najnowszego [zestawu SDK języka Python OpenCensus](./opencensus-python.md).

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

3. Upewnij się, że AzureExporter jest prawidłowo skonfigurowany w `settings.py` ramach użytkownika `OPENCENSUS` . W przypadku żądań z adresów URL, które nie mają być śledzone, Dodaj je do programu `EXCLUDELIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
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

2. Możesz również skonfigurować `flask` aplikację za poorednictwem programu `app.config` . W przypadku żądań z adresów URL, które nie mają być śledzone, Dodaj je do programu `EXCLUDELIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
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

2. Możesz skonfigurować `pyramid` animację bezpośrednio w kodzie. W przypadku żądań z adresów URL, które nie mają być śledzone, Dodaj je do programu `EXCLUDELIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>Śledzenie aplikacji FastAPI

OpenCensus nie ma rozszerzenia FastAPI. Aby napisać własne oprogramowanie pośredniczące FastAPI, wykonaj następujące czynności:

1. Wymagane są następujące zależności: 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. Dodaj [oprogramowanie pośredniczące FastAPI](https://fastapi.tiangolo.com/tutorial/middleware/). Upewnij się, że ustawisz serwer rodzaju span: `span.span_kind = SpanKind.SERVER` .

3. Uruchom aplikację. Wywołania wykonane w aplikacji FastAPI powinny być automatycznie śledzone i dane telemetryczne powinny być rejestrowane bezpośrednio w Azure Monitor.

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>Następne kroki

* [Mapa aplikacji](./app-map.md)
* [Dostępność](./monitor-web-app-availability.md)
* [Wyszukiwanie](./diagnostic-search.md)
* [Zapytanie log (Analytics)](../logs/log-query-overview.md)
* [Diagnostyka transakcji](./transaction-diagnostics.md)

