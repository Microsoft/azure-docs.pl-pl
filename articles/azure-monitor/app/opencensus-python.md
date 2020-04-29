---
title: Monitoruj aplikacje języka Python za pomocą Azure Monitor (wersja zapoznawcza) | Microsoft Docs
description: Zawiera instrukcje dotyczące szkieletu OpenCensus Python z Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537112"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Konfigurowanie Azure Monitor dla aplikacji języka Python

Azure Monitor obsługuje śledzenie rozproszone, zbieranie metryk i rejestrowanie aplikacji języka Python poprzez integrację z usługą [OpenCensus](https://opencensus.io). Ten artykuł przeprowadzi Cię przez proces konfigurowania OpenCensus dla języka Python i wysyłania danych monitorowania do Azure Monitor.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
- Instalacja języka Python. W tym artykule jest stosowany [3.7.0 języka Python](https://www.python.org/downloads/), chociaż starsze wersje będą prawdopodobnie działały z drobnymi zmianami.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Tworzenie zasobu Application Insights w Azure Monitor

Najpierw należy utworzyć zasób Application Insights w Azure Monitor, co spowoduje wygenerowanie klucza Instrumentacji (iKey). IKey jest następnie używany do konfigurowania zestawu SDK OpenCensus w celu wysyłania danych telemetrycznych do Azure Monitor.

1. Wybierz pozycję **Utwórz zasób** > **Narzędzia** > deweloperskie**Application Insights**.

   ![Dodawanie zasobu Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Zostanie wyświetlone okno Konfiguracja. Skorzystaj z poniższej tabeli, aby wypełnić pola wejściowe.

   | Ustawienie        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Globalnie unikatowa wartość | Nazwa identyfikująca monitorowaną aplikację |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów do hostowania danych Application Insights |
   | **Lokalizacja** | Wschodnie stany USA | Lokalizacja w sąsiedztwie lub w sąsiedztwie, gdzie aplikacja jest hostowana |

1. Wybierz przycisk **Utwórz**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrument z zestawem SDK OpenCensus języka Python dla Azure Monitor

Zainstaluj OpenCensus Azure Monitor eksportu:

```console
python -m pip install opencensus-ext-azure
```

Aby uzyskać pełną listę pakietów i integracji, zobacz [OpenCensus Packages](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> W `python -m pip install opencensus-ext-azure` przypadku polecenia przyjęto założenie `PATH` , że masz zmienną środowiskową ustawioną dla instalacji języka Python. Jeśli ta zmienna nie została skonfigurowana, należy podać pełną ścieżkę do katalogu, w którym znajduje się plik wykonywalny języka Python. Wynikiem jest następujące polecenie: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

Zestaw SDK używa trzech Azure Monitor eksportujących do wysyłania różnych typów danych telemetrycznych do Azure Monitor: śledzenia, metryk i dzienników. Aby uzyskać więcej informacji na temat tych typów telemetrii, zobacz [Omówienie platformy danych](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Aby wysłać te typy telemetrii za pośrednictwem trzech eksporterów, należy wykonać poniższe instrukcje.

## <a name="telemetry-type-mappings"></a>Mapowania typów telemetrii

Poniżej przedstawiono eksporterów, którzy OpenCensus są zamapowane na typy danych telemetrycznych, które będą widoczne w Azure Monitor.

![Zrzut ekranu przedstawiający Mapowanie typów telemetrii z OpenCensus do Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Ślad

> [!NOTE]
> `Trace`w programie OpenCensus odnosi się do [śledzenia rozproszonego](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). Komunikaty `AzureExporter` `requests` i `dependency` dane telemetryczne do Azure monitor.

1. Najpierw wygenerujmy dane śledzenia lokalnie. W bezczynnym języku Python lub wybranym przez Ciebie edytorze wpisz poniższy kod.

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

2. Uruchomienie kodu spowoduje wielokrotne wyświetlenie monitu o wprowadzenie wartości. W przypadku każdego wpisu wartość zostanie wydrukowany w powłoce, a moduł OpenCensus języka Python wygeneruje odpowiadający element `SpanData`. Projekt OpenCensus definiuje [śledzenie jako drzewo zakresów](https://opencensus.io/core-concepts/tracing/).
    
    ```
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

3. Chociaż wprowadzanie wartości jest przydatne w celach demonstracyjnych, ostatecznie chcemy emitować `SpanData` do Azure monitor. Przekaż parametry połączenia bezpośrednio do eksportera lub możesz określić je w zmiennej `APPLICATIONINSIGHTS_CONNECTION_STRING`środowiskowej. Zmodyfikuj swój kod z poprzedniego kroku w oparciu o następujący przykład kodu:

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

4. Teraz po uruchomieniu skryptu języka Python nadal powinien zostać wyświetlony monit o wprowadzenie wartości, ale tylko wartość jest drukowana w powłoce. Utworzony `SpanData` zostanie wysłany do Azure monitor. Wyemitowane dane zakresu można znaleźć w sekcji `dependencies`. Aby uzyskać więcej informacji na temat żądań wychodzących, zobacz OpenCensus Python — [zależności](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency).
Aby uzyskać więcej informacji na temat żądań przychodzących, zobacz OpenCensus Python — [żądania](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request).

#### <a name="sampling"></a>Próbkowanie

Aby uzyskać informacje na temat próbkowania w OpenCensus, spójrz na [próbkowanie w OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Korelacja śledzenia

Aby uzyskać szczegółowe informacje na temat korelacji telemetrii w danych śledzenia, zapoznaj się z tematem [korelacji telemetrii](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)języka Python.

#### <a name="modify-telemetry"></a>Modyfikuj dane telemetryczne

Aby uzyskać szczegółowe informacje na temat modyfikowania śledzonych danych [telemetrycznych](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)przed ich wysłaniem do Azure monitor, zobacz OpenCensus.

### <a name="metrics"></a>Metryki

1. Najpierw wygenerujmy pewne dane lokalnej metryki. Utworzymy prostą metrykę do śledzenia liczby prób wprowadzenia przez użytkownika.

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
2. Uruchomienie kodu będzie kilkukrotnie monitować o naciśnięcie klawisza ENTER. Zostanie utworzona Metryka umożliwiająca śledzenie liczby godzin naciśnięcia klawisza ENTER. W przypadku każdego wpisu wartość zostanie zwiększona, a informacje o metrykach będą wyświetlane w konsoli programu. Informacje obejmują bieżącą wartość i bieżącą sygnaturę czasową, gdy Metryka została zaktualizowana.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Chociaż wprowadzanie wartości jest przydatne w celach demonstracyjnych, ostatecznie chcemy emitować dane metryk do Azure Monitor. Przekaż parametry połączenia bezpośrednio do eksportera lub możesz określić je w zmiennej `APPLICATIONINSIGHTS_CONNECTION_STRING`środowiskowej. Zmodyfikuj swój kod z poprzedniego kroku w oparciu o następujący przykład kodu:

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

4. Eksporter wyśle dane metryk do Azure Monitor w stałym interwale. Wartość domyślna to co 15 sekund. Śledzimy jedną metrykę, więc dane metryk, z dowolną wartością i sygnaturą czasową, będą wysyłane każdego interwału. Dane można znaleźć w sekcji `customMetrics`.

#### <a name="standard-metrics"></a>Metryki standardowe

Domyślnie eksporter metryk wyśle zestaw metryk standardowych do Azure Monitor. Można ją wyłączyć przez ustawienie `enable_standard_metrics` flagi `False` w konstruktorze konstruktora metryk.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Poniżej znajduje się lista standardowych metryk, które są obecnie wysyłane:

- Dostępna pamięć (w bajtach)
- Czas procesora CPU (w procentach)
- Szybkość żądań przychodzących (na sekundę)
- Średni czas wykonywania żądania przychodzącego (w milisekundach)
- Szybkość żądania wychodzącego (na sekundę)
- Użycie procesora CPU przez proces (procent)
- Prywatne bajty procesu (w bajtach)

Te metryki powinny być dostępne w programie `performanceCounters`. Przychodząca stawka żądania `customMetrics`byłaby poniżej. Aby uzyskać więcej informacji, zobacz [liczniki wydajności](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Modyfikuj dane telemetryczne

Aby uzyskać szczegółowe informacje na temat modyfikowania śledzonych danych [telemetrycznych](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)przed ich wysłaniem do Azure monitor, zobacz OpenCensus.

### <a name="logs"></a>Dzienniki

1. Najpierw wygenerujmy niektóre lokalne dane dziennika.

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

2.  Kod będzie w sposób ciągły proszony o wprowadzenie wartości. Wpis dziennika jest emitowany dla każdej wprowadzonej wartości.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Chociaż wprowadzanie wartości jest przydatne w celach demonstracyjnych, ostatecznie chcemy emitować dane dziennika do Azure Monitor. Przekaż parametry połączenia bezpośrednio do eksportera lub możesz określić je w zmiennej `APPLICATIONINSIGHTS_CONNECTION_STRING`środowiskowej. Zmodyfikuj swój kod z poprzedniego kroku w oparciu o następujący przykład kodu:

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

4. Eksporter wyśle dane dziennika do Azure Monitor. Dane można znaleźć w sekcji `traces`. 

> [!NOTE]
> `traces`w tym kontekście nie jest taka sama jak `Tracing`. `traces`odnosi się do typu danych telemetrycznych, które będą widoczne w Azure Monitor podczas `AzureLogHandler`używania. `Tracing`odwołuje się do koncepcji w OpenCensus i odnosi się do [śledzenia rozproszonego](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Aby sformatować komunikaty dziennika, można użyć `formatters` wbudowanego [interfejsu API rejestrowania](https://docs.python.org/3/library/logging.html#formatter-objects)języka Python.

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. Możesz również dodać właściwości niestandardowe do wiadomości dziennika w argumencie słowa kluczowego *ekstra* przy użyciu pola custom_dimensions. Zostaną one wyświetlone jako pary klucz-wartość w `customDimensions` w Azure monitor.
> [!NOTE]
> Aby ta funkcja działała, należy przekazać słownik do pola custom_dimensions. Jeśli przejdziesz argumenty dowolnego innego typu, rejestrator zignoruje je.

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

#### <a name="sending-exceptions"></a>Wysyłanie wyjątków

OpenCensus Python nie automatycznie śledzi i nie wysyła `exception` danych telemetrycznych. Są one wysyłane za pośrednictwem programu `AzureLogHandler` przy użyciu wyjątków za pośrednictwem biblioteki rejestrowania języka Python. Właściwości niestandardowe można dodawać podobnie jak w przypadku normalnego rejestrowania.

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
Ponieważ należy rejestrować wyjątki jawnie, jest on przeznaczony dla użytkowników, w jaki chcą rejestrować Nieobsłużone wyjątki. OpenCensus nie umieszcza ograniczeń w sposobie, w jaki użytkownik chce wykonać tę czynność, o ile jawnie rejestruje dane telemetryczne wyjątku.

#### <a name="sampling"></a>Próbkowanie

Aby uzyskać informacje na temat próbkowania w OpenCensus, spójrz na [próbkowanie w OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Korelacja dzienników

Aby uzyskać szczegółowe informacje na temat wzbogacania dzienników przy użyciu danych kontekstu śledzenia, zobacz [integracja dzienników](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)OpenCensus języka Python.

#### <a name="modify-telemetry"></a>Modyfikuj dane telemetryczne

Aby uzyskać szczegółowe informacje na temat modyfikowania śledzonych danych [telemetrycznych](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)przed ich wysłaniem do Azure monitor, zobacz OpenCensus.

## <a name="view-your-data-with-queries"></a>Wyświetlanie danych za pomocą zapytań

Dane telemetryczne, które zostały wysłane z aplikacji, można wyświetlić za pomocą karty **dzienniki (analiza)** .

![Zrzut ekranu przedstawiający okienko przegląd z opcją "dzienniki (analiza)" wybraną w czerwonym polu](./media/opencensus-python/0010-logs-query.png)

Na liście w obszarze **aktywne**:

- W przypadku danych telemetrycznych wysyłanych za pomocą eksportu śledzenia Azure Monitor `requests`w obszarze znajdują się żądania przychodzące. W obszarze `dependencies`są wyświetlane żądania wychodzące lub w procesie.
- W przypadku danych telemetrycznych wysyłanych za pomocą eksportu metryk Azure Monitor `customMetrics`są wyświetlane metryki.
- W przypadku danych telemetrycznych wysyłanych z wyeksportowanymi `traces`dziennikami Azure monitor dzienniki są wyświetlane poniżej. Wyjątki są wyświetlane `exceptions`w sekcji.

Aby uzyskać szczegółowe informacje na temat korzystania z zapytań i dzienników, zobacz [dzienniki w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Dowiedz się więcej na temat OpenCensus for Python

* [OpenCensus Python w witrynie GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Dostosowywanie](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor eksporterów w usłudze GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integracje OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor przykładowe aplikacje](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Następne kroki

* [Śledzenie żądań przychodzących](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Śledzenie żądań wychodzących](./../../azure-monitor/app/opencensus-python-request.md)
* [Mapa aplikacji](./../../azure-monitor/app/app-map.md)
* [Kompleksowe monitorowanie wydajności](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alerty

* [Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md): Utwórz testy, aby upewnić się, że Twoja witryna jest widoczna w sieci Web.
* [Inteligentne diagnostyki](../../azure-monitor/app/proactive-diagnostics.md): Te testy są uruchamiane automatycznie, więc nie trzeba wykonywać żadnych czynności, aby je skonfigurować. Ta funkcja powiadomi Cię, jeśli w aplikacji występuje nietypowa liczba nieudanych żądań.
* [Alerty metryk](../../azure-monitor/app/alerts.md): Ustaw alerty, aby ostrzec użytkownika, gdy Metryka przekroczy próg. Możesz je ustawić dla metryk niestandardowych, które zakodujesz w aplikacji.
