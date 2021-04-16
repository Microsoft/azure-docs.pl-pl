---
title: Monitorowanie aplikacji języka Python za pomocą Azure Monitor | Microsoft Docs
description: Zawiera instrukcje dotyczące dokańczania środowiska OpenCensus Python przy użyciu Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 92d954a865a2d4a8c55177b132139dcd7d0444ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515927"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Konfigurowanie Azure Monitor dla aplikacji w języku Python

Azure Monitor obsługuje śledzenie rozproszone, zbieranie metryk i rejestrowanie aplikacji języka Python dzięki integracji z [usługą OpenCensus.](https://opencensus.io) Ten artykuł zawiera informacje na temat procesu konfigurowania środowiska OpenCensus dla języka Python i wysyłania danych monitorowania do Azure Monitor.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- Instalacja języka Python. W tym artykule [jest używany język Python 3.7.0,](https://www.python.org/downloads/release/python-370/)chociaż inne wersje będą prawdopodobnie działać z niewielkimi zmianami. Zestaw SDK obsługuje tylko język Python w wersji 2.7 i 3.4-3.7.
- Utwórz zasób Application Insights [zasobów.](./create-new-resource.md) Do zasobu zostanie przypisany własny klucz instrumentacji (ikey).

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrument przy użyciu zestawu SDK języka Python OpenCensus dla Azure Monitor

Zainstaluj eksporterów Azure Monitor OpenCensus:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> W `python -m pip install opencensus-ext-azure` poleceniu założono, że masz `PATH` zmienną środowiskową ustawioną dla instalacji języka Python. Jeśli ta zmienna nie została skonfigurowana, musisz podać pełną ścieżkę katalogu do miejsca, w którym znajduje się plik wykonywalny języka Python. Wynikiem jest polecenie podobne do tego: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure` .

Zestaw SDK używa trzech Azure Monitor do wysyłania różnych typów danych telemetrycznych do Azure Monitor. Są to ślady, metryki i dzienniki. Aby uzyskać więcej informacji na temat tych typów telemetrii, zobacz [omówienie platformy danych](../data-platform.md). Użyj poniższych instrukcji, aby wysłać te typy danych telemetrycznych za pośrednictwem trzech eksporterów.

## <a name="telemetry-type-mappings"></a>Mapowania typów telemetrii

Oto eksporterzy udostępniani przez OpenCensus zamapowane na typy danych telemetrycznych, które są Azure Monitor.

| Filar możliwości obserwacji | Typ telemetrii w Azure Monitor    | Wyjaśnienie                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Dzienniki                    | Ślady, wyjątki, customEvents   | Telemetria dziennika, telemetria wyjątków, telemetria zdarzeń |
| Metryki                 | customMetrics, performanceCounters | Liczniki wydajności metryk niestandardowych                |
| Śledzenie                 | Żąda zależności             | Żądania przychodzące, żądania wychodzące                |

### <a name="logs"></a>Dzienniki

1. Najpierw wygenerujmy dane dziennika lokalnego.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Kod stale pyta o wartość, która ma zostać wprowadzona. Wpis dziennika jest emitowany dla każdej wprowadzonej wartości.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. Chociaż wprowadzanie wartości jest przydatne w celach demonstracyjnych, ostatecznie chcemy emitować dane dziennika do Azure Monitor. Przekaż swoje ciągi połączenia bezpośrednio do eksportera. Można też określić ją w zmiennej środowiskowej `APPLICATIONINSIGHTS_CONNECTION_STRING` . Zmodyfikuj kod z poprzedniego kroku, bazując na następującym przykładzie kodu:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Eksport wysyła dane dziennika do Azure Monitor. Dane można znaleźć w obszarze `traces` . 

    > [!NOTE]
    > W tym kontekście `traces` nie jest tożsamy z . `tracing` W tym `traces` miejscu odnosi się do typu telemetrii, która będzie Azure Monitor podczas korzystania z usługi `AzureLogHandler` . Jednak `tracing` odnosi się do koncepcji w OpenCensus i odnosi się do [śledzenia rozproszonego](./distributed-tracing.md).

    > [!NOTE]
    > Główny rejestrator jest skonfigurowany z poziomem OSTRZEŻENIE. Oznacza to, że wszystkie wysyłane dzienniki o mniejszej ważności są ignorowane i z kolei nie są wysyłane do Azure Monitor. Aby uzyskać więcej informacji, zobacz [dokumentację](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel).

1. Możesz również dodać właściwości niestandardowe do  komunikatów dziennika w dodatkowym argumentze słowa kluczowego, używając custom_dimensions pola. Te właściwości są wyświetlane jako pary klucz-wartość w `customDimensions` Azure Monitor.
    > [!NOTE]
    > Aby ta funkcja działała, należy przekazać słownik do pola custom_dimensions danych. Jeśli przekażemy argumenty dowolnego innego typu, rejestrator je zignoruje.

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>Konfigurowanie rejestrowania dla aplikacji Django

Możesz jawnie skonfigurować rejestrowanie w kodzie aplikacji, tak jak powyżej, dla aplikacji Django lub określić je w konfiguracji rejestrowania Django. Ten kod może trafić do dowolnego pliku, który jest konfiguracyjny dla ustawień Django. Aby dowiedzieć się, jak skonfigurować ustawienia Django, zobacz [Ustawienia Django](https://docs.djangoproject.com/en/3.0/topics/settings/). Aby uzyskać więcej informacji na temat konfigurowania rejestrowania, zobacz [Rejestrowanie Django](https://docs.djangoproject.com/en/3.0/topics/logging/).

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

Upewnij się, że używasz rejestratora o takiej samej nazwie jak określona w konfiguracji.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Wysyłanie wyjątków

Język OpenCensus Python nie śledzi automatycznie i nie wysyła danych `exception` telemetrycznych. Są one wysyłane przy użyciu `AzureLogHandler` wyjątków za pośrednictwem biblioteki rejestrowania języka Python. Właściwości niestandardowe można dodawać tak samo jak w przypadku normalnego rejestrowania.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
Ponieważ wyjątki muszą być jawnie rejestrowane, użytkownik chce rejestrować nieobsługiwane wyjątki. OpenCensus nie nakłada ograniczeń na sposób, w jaki użytkownik chce to zrobić, o ile jawnie rejestruje telemetrię wyjątku.

#### <a name="send-events"></a>Wysyłanie zdarzeń

Telemetrię można wysłać dokładnie w taki sam sposób, jak w przypadku wysyłania `customEvent` `trace` danych telemetrycznych, z wyjątkiem `AzureEventHandler` użycia.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>Próbkowanie

Aby uzyskać informacje na temat próbkowania w opencensus, zobacz [próbkowanie w opencensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Korelacja dzienników

Aby uzyskać szczegółowe informacje na temat wzbogacania dzienników o dane kontekstu śledzenia, zobacz Integracja dzienników OpenCensus [języka](./correlation.md#log-correlation)Python .

#### <a name="modify-telemetry"></a>Modyfikowanie telemetrii

Aby uzyskać szczegółowe informacje na temat modyfikowania śledzone dane telemetryczne przed ich wysłaniem do usługi Azure Monitor, zobacz OpenCensus Python [telemetry processors](./api-filtering-sampling.md#opencensus-python-telemetry-processors)(Procesory telemetrii Języka Python w języku OpenCensus).


### <a name="metrics"></a>Metryki

OpenCensus.stats obsługuje 4 metody agregacji, ale zapewnia częściową obsługę Azure Monitor:

- **Liczba:** Liczba punktów pomiarów. Wartość jest kumulowana, można ją zwiększyć i zresetować do wartości 0 tylko po ponownym uruchomieniu. 
- **Suma:** Suma punktów pomiarów. Wartość jest kumulowana, można ją zwiększyć i zresetować do wartości 0 tylko po ponownym uruchomieniu. 
- **LastValue:** Zachowuje ostatnią zanotowane wartość, porzuca wszystkie inne.
- **Dystrybucja:** Rozkład histogramu punktów pomiarów. Ta metoda NIE **jest obsługiwana przez eksportera platformy Azure.**

### <a name="count-aggregation-example"></a>Przykład agregacji liczby

1. Najpierw wygenerujmy kilka lokalnych danych metryk. Utworzymy prostą metrykę do śledzenia, ile razy użytkownik wybierze klawisz **Enter.**

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. Wielokrotne uruchamianie kodu monituje o wybranie **klawisza Enter**. Metryka jest tworzona w celu śledzenia, ile **razy wybrano** klawisz Enter. W przypadku każdego wpisu wartość jest zwiększana, a informacje o metrykach są wyświetlane w konsoli programu . Informacje obejmują bieżącą wartość i bieżący znacznik czasu podczas aktualizowania metryki.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Chociaż wprowadzanie wartości jest przydatne w celach demonstracyjnych, ostatecznie chcemy emitować dane metryk do Azure Monitor. Przekaż swoje ciągi połączenia bezpośrednio do eksportera. Możesz też określić ją w zmiennej środowiskowej `APPLICATIONINSIGHTS_CONNECTION_STRING` . Zmodyfikuj kod z poprzedniego kroku na podstawie następującego przykładu kodu:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. Eksporter wysyła dane metryk do Azure Monitor w ustalonym interwale. Wartość domyślna to co 15 sekund. Śledzimy pojedynczą metrykę, więc te dane metryki, niezależnie od ich wartości i sygnatury czasowej, są wysyłane co interwał. Wartość jest skumulowana, można ją zwiększyć i zresetować do wartości 0 po ponownym uruchomieniu. Dane można znaleźć w obszarze , ale właściwości `customMetrics` `customMetrics` valueCount, valueSum, valueMin, valueMax i valueStdDev nie są efektywnie używane.

### <a name="setting-custom-dimensions-in-metrics"></a>Ustawianie wymiarów niestandardowych w metrykach

Zestaw SDK Opencensus dla języka Python umożliwia dodawanie wymiarów niestandardowych do telemetrii metryk za pomocą parametru , który zasadniczo jest słownikiem par `tags` klucz/wartość. 

1. Wstaw tagi, których chcesz użyć, do mapy tagów. Mapa tagów działa jak rodzaj "puli" wszystkich dostępnych tagów, których można użyć.

```python
...
tmap = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
...
```

1. Dla określonego parametru określ tagi, które mają być używane podczas rejestrowania metryk w `View` tym widoku za pośrednictwem klucza tagu.

```python
...
prompt_view = view_module.View("prompt view",
                               "number of prompts",
                               ["url"], # <-- A sequence of tag keys used to specify which tag key/value to use from the tag map
                               prompt_measure,
                               aggregation_module.CountAggregation())
...
```

1. Pamiętaj, aby użyć mapy tagów podczas rejestrowania na mapie pomiarów. Klucze tagów określone w pliku `View` muszą znajdować się na mapie tagów używanej do nagrywania.

```python
...
mmap = stats_recorder.new_measurement_map()
mmap.measure_int_put(prompt_measure, 1)
mmap.record(tmap) # <-- pass the tag map in here
...
```

1. W tabeli wszystkie rekordy metryk emitowane przy użyciu funkcji `customMetrics` będą mieć wymiary niestandardowe `prompt_view` `{"url":"http://example.com"}` .

1. Aby utworzyć tagi z różnymi wartościami przy użyciu tych samych kluczy, utwórz dla nich nowe mapy tagów.

```python
...
tmap = tag_map_module.TagMap()
tmap2 = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
tmap2.insert("url", "https://www.wikipedia.org/wiki/")
...
```

#### <a name="performance-counters"></a>Liczniki wydajności

Domyślnie eksporter metryk wysyła zestaw liczników wydajności do Azure Monitor. Można to wyłączyć, ustawiając `enable_standard_metrics` flagę na `False` w konstruktorze eksportera metryk.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Te liczniki wydajności są obecnie wysyłane:

- Dostępna pamięć (w bajtach)
- Czas procesora CPU (wartość procentowa)
- Szybkość żądań przychodzących (na sekundę)
- Średni czas wykonywania żądania przychodzącego (w milisekundach)
- Użycie procesora CPU procesu (wartość procentowa)
- Przetwarzanie bajtów prywatnych (w bajtach)

Te metryki powinny być dostępne w programie `performanceCounters` . Aby uzyskać więcej informacji, zobacz [liczniki wydajności](./performance-counters.md).

#### <a name="modify-telemetry"></a>Modyfikowanie telemetrii

Aby uzyskać informacje na temat modyfikowania śledzone dane telemetryczne przed ich wysłaniiem do usługi Azure Monitor, zobacz Procesory telemetrii Języka Python w języku [OpenCensus](./api-filtering-sampling.md#opencensus-python-telemetry-processors).

### <a name="tracing"></a>Śledzenie

> [!NOTE]
> W openCensus `tracing` odnosi się do [śledzenia rozproszonego](./distributed-tracing.md). Wysyła `AzureExporter` dane `requests` `dependency` telemetryczne do Azure Monitor.

1. Najpierw wygenerujmy lokalnie dane śledzenia. W środowisku IDLE języka Python lub w edytorze wprowadź następujący kod:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Uruchomienie kodu wielokrotnie monituje o wprowadzenie wartości. Przy każdym wpisie wartość jest drukowana w powłokie. Moduł OpenCensus języka Python generuje odpowiedni element `SpanData` . Projekt OpenCensus definiuje ślad [jako drzewo zakresów](https://opencensus.io/core-concepts/tracing/).
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. Chociaż wprowadzanie wartości jest przydatne w celach demonstracyjnych, ostatecznie chcemy emitować `SpanData` Azure Monitor. Przekaż swoje ciągi połączenia bezpośrednio do eksportera. Możesz też określić ją w zmiennej środowiskowej `APPLICATIONINSIGHTS_CONNECTION_STRING` . Zmodyfikuj kod z poprzedniego kroku na podstawie następującego przykładu kodu:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Teraz po uruchomieniu skryptu języka Python nadal powinien zostać wyświetlony monit o wprowadzenie wartości, ale tylko wartość jest drukowana w powłoki. Utworzone zostaną `SpanData` wysłane do Azure Monitor. Emitowane dane zakresu można znaleźć w obszarze `dependencies` . Aby uzyskać więcej informacji na temat żądań wychodzących, zobacz OpenCensus Python dependencies (Zależności języka Python w [języku](./opencensus-python-dependency.md)OpenCensus).
Aby uzyskać więcej informacji na temat żądań przychodzących, zobacz OpenCensus Python requests (Żądania OpenCensus w [języku](./opencensus-python-request.md)Python).

#### <a name="sampling"></a>Próbkowanie

Aby uzyskać informacje na temat próbkowania w OpenCensus, zobacz próbkowanie w [OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Korelacja śledzenia

Aby uzyskać więcej informacji na temat korelacji telemetrii w danych śledzenia, zobacz OpenCensus Python [telemetry correlation](./correlation.md#telemetry-correlation-in-opencensus-python)(Korelacja telemetrii języka Python w języku OpenCensus).

#### <a name="modify-telemetry"></a>Modyfikowanie telemetrii

Aby uzyskać więcej informacji na temat modyfikowania śledzone dane telemetryczne przed ich wysłaniiem do usługi Azure Monitor, zobacz Procesory telemetrii Języka Python w języku [OpenCensus](./api-filtering-sampling.md#opencensus-python-telemetry-processors).

## <a name="configure-azure-monitor-exporters"></a>Konfigurowanie Azure Monitor eksportu

Jak pokazano, istnieją trzy różne Azure Monitor, które obsługują OpenCensus. Każdy z nich wysyła różne typy danych telemetrycznych do Azure Monitor. Aby zobaczyć, jakie typy danych telemetrycznych wysyła każdy eksporter, zobacz następującą listę.

Każdy eksporter akceptuje te same argumenty dla konfiguracji przekazywane przez konstruktory. Szczegółowe informacje o każdym z nich można znaleźć tutaj:

- `connection_string`: ciąg połączenia używany do nawiązywania połączenia z Azure Monitor zasobem. Ma priorytet nad `instrumentation_key` .
- `enable_standard_metrics`: używany `AzureMetricsExporter` dla . Sygnalizuje eksporterowi automatyczne wysyłanie metryk [liczników](../essentials/app-insights-metrics.md#performance-counters) wydajności do Azure Monitor. Wartość domyślna to `True` .
- `export_interval`: służy do określania częstotliwości eksportowania w sekundach.
- `instrumentation_key`: klucz instrumentacji używany do nawiązywania połączenia z Azure Monitor zasobem.
- `logging_sampling_rate`: używany `AzureLogHandler` dla . Udostępnia częstotliwość próbkowania [0,1.0] dla eksportowania dzienników. Wartość domyślna to 1.0.
- `max_batch_size`: określa maksymalny rozmiar danych telemetrycznych, które są eksportowane jednocześnie.
- `proxies`: określa sekwencję serwerów proxy do użycia w celu wysyłania danych do Azure Monitor. Aby uzyskać więcej informacji, zobacz [proxy](https://requests.readthedocs.io/en/master/user/advanced/#proxies).
- `storage_path`: ścieżka do lokalizacji, w której znajduje się folder magazynu lokalnego (niezasyłana telemetria). Od wersji `opencensus-ext-azure` 1.0.3 domyślną ścieżką jest katalog tymczasowy systemu operacyjnego + `opencensus-python`  +  `your-ikey` . Przed wersjami 1.0.3 ścieżka domyślna to $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Wyświetlanie danych za pomocą zapytań

Dane telemetryczne wysłane z aplikacji można wyświetlić za pomocą karty **Dzienniki (analiza).**

![Zrzut ekranu przedstawiający okienko przeglądu z wybraną oknie "Dzienniki (analiza)" w czerwonym polu](./media/opencensus-python/0010-logs-query.png)

Na liście w obszarze **Aktywne**:

- W przypadku telemetrii wysyłanej z Azure Monitor śledzenia żądania przychodzące są wyświetlane w obszarze `requests` . Żądania wychodzące lub w trakcie przetwarzania są wyświetlane w obszarze `dependencies` .
- W przypadku telemetrii wysyłanej z Azure Monitor eksportu metryki wysłane metryki są wyświetlane w obszarze `customMetrics` .
- W przypadku danych telemetrycznych wysyłanych z Azure Monitor eksportu dzienniki są wyświetlane w obszarze `traces` . Wyjątki są wyświetlane w obszarze `exceptions` .

Aby uzyskać bardziej szczegółowe informacje na temat używania zapytań i dzienników, zobacz [Dzienniki w Azure Monitor](../logs/data-platform-logs.md).

## <a name="learn-more-about-opencensus-for-python"></a>Dowiedz się więcej o środowisku OpenCensus for Python

* [OpenCensus Python w witrynie GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Dostosowywanie](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor w witrynie GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integracje OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor przykładowych aplikacji](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Następne kroki

* [Śledzenie żądań przychodzących](./opencensus-python-dependency.md)
* [Śledzenie wychodzącego żądania](./opencensus-python-request.md)
* [Mapa aplikacji](./app-map.md)
* [End-to-end performance monitoring (Monitorowanie wydajności na wszystkich urządzeniach)](../app/tutorial-performance.md)

### <a name="alerts"></a>Alerty

* [Testy dostępności](./monitor-web-app-availability.md): Utwórz testy, aby upewnić się, że Twoja witryna jest widoczna w sieci Web.
* [Inteligentne diagnostyki](./proactive-diagnostics.md): Te testy są uruchamiane automatycznie, więc nie trzeba wykonywać żadnych czynności, aby je skonfigurować. Ta funkcja powiadomi Cię, jeśli w aplikacji występuje nietypowa liczba nieudanych żądań.
* [Alerty dotyczące metryk:](../alerts/alerts-log.md)ustaw alerty, aby ostrzec, jeśli metryka przekroczy próg. Możesz je ustawić dla metryk niestandardowych, które zakodujesz w aplikacji.

