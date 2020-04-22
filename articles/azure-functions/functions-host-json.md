---
title: odwołanie host.json dla usługi Azure Functions 2.x
description: Dokumentacja referencyjna dla pliku host.json usługi Azure functions ze środowiska wykonawczego w wersji 2.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 7967cdc7f5f7cbb92c12de15d31471fda8aa6569
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758836"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>odwołanie host.json dla usługi Azure Functions 2.x i nowszych 

> [!div class="op_single_selector" title1="Wybierz wersję używanego środowiska wykonawczego usługi Azure Functions: "]
> * [Wersja 1](functions-host-json-v1.md)
> * [Wersja 2+](functions-host-json.md)

Plik metadanych *host.json* zawiera globalne opcje konfiguracji, które wpływają na wszystkie funkcje aplikacji funkcji. W tym artykule wymieniono ustawienia, które są dostępne począwszy od wersji 2.x środowiska wykonawczego usługi Azure Functions.  

> [!NOTE]
> Ten artykuł dotyczy usługi Azure Functions 2.x i nowszych wersji.  Aby uzyskać odwołanie do pliku host.json w usługach 1.x, zobacz [odwołanie do host.json dla usługi Azure Functions 1.x](functions-host-json-v1.md).

Inne opcje konfiguracji aplikacji funkcji są zarządzane w [ustawieniach aplikacji](functions-app-settings.md) (dla wdrożonych aplikacji) lub [pliku local.settings.json](functions-run-local.md#local-settings-file) (dla lokalnego rozwoju).

Konfiguracje w host.json związane z powiązaniami są stosowane jednakowo do każdej funkcji w aplikacji funkcji. 

## <a name="sample-hostjson-file"></a>Przykładowy plik hosta.json

Poniższy przykładowy plik *host.json* dla wersji 2.x+ ma wszystkie możliwe opcje określone (z wyłączeniem wszelkich, które są tylko do użytku wewnętrznego).

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

Poniżej dalsza część artykułu Wyjaśnij każdą właściwość najwyższego poziomu. Wszystkie są opcjonalne, chyba że wskazano inaczej.

## <a name="aggregator"></a>Agregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>ApplicationInsights

To ustawienie jest dziestawem [rejestrowania](#logging).

Steruje opcjami wglądu w aplikacje, w tym [opcjami próbkowania](./functions-monitoring.md#configure-sampling).

Aby uzyskać pełną strukturę JSON, zobacz wcześniejszy [przykład pliku host.json](#sample-hostjson-file).

> [!NOTE]
> Próbkowanie dziennika może spowodować, że niektóre wykonania nie są wyświetlane w bloku monitora usługi Application Insights. Aby uniknąć próbkowania dziennika, dodaj `excludedTypes: "Request"` do `samplingSettings` wartości.

| Właściwość | Domyślne | Opis |
| --------- | --------- | --------- | 
| pobieranie próbekSzeczenie | Nie dotyczy | Zobacz [applicationInsights.samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics (włącz metryki na żywo) | true | Włącza kolekcję metryk na żywo. |
| enableDependencyTracking | true | Włącza śledzenie zależności. |
| enablePerformanceCountersCollection | true | Włącza kolekcję liczników wydajności Kudu. |
| liveMetricsInitializationDelay | 00:00:15 | Tylko do użytku wewnętrznego. |
| httpAutoCollectionOptions | Nie dotyczy | Zobacz [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguracja | Nie dotyczy | Zobacz [applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>aplikacjaInsights.samplingSettings

|Właściwość | Domyślne | Opis |
| --------- | --------- | --------- | 
| Isenabled | true | Włącza lub wyłącza próbkowanie. | 
| maxTelemetryItemsPerSekunda | 20 | Docelowa liczba elementów telemetrycznych rejestrowanych na sekundę na każdym hoście serwera. Jeśli aplikacja działa na wielu hostach, zmniejsz tę wartość, aby pozostała w ogólnej docelowej szybkości ruchu. | 
| ewaluacjaInterwał | 01:00:00 | Interwał, w którym bieżąca szybkość telemetrii jest ponownie wyceniana. Ocena jest przeprowadzana jako średnia ruchoma. Można skrócić ten interwał, jeśli dane telemetryczne jest odpowiedzialny za nagłe wybuchy. |
| inicjałSamplingPercentage| 1.0 | Początkowa wartość procentowa próbkowania zastosowana na początku procesu pobierania próbek w celu dynamicznej zmiany wartości procentowej. Nie zmniejszaj wartości podczas debugowania. |
| samplingPercentageIncreaseTimeout | 00:00:01 | Gdy wartość procentowa próbkowania zmienia się, ta właściwość określa, jak szybko po aplikacji Usługa Insights może ponownie podnieść procent próbkowania ponownie, aby przechwycić więcej danych. |
| samplingPercentageDecreaseTimeout | 00:00:01 | Gdy wartość procentowa próbkowania zmienia się, ta właściwość określa, jak szybko po aplikacji Usługa Insights może ponownie obniżyć procent próbkowania ponownie, aby przechwycić mniej danych. |
| minSamplingPercentage | 0.1 | Ponieważ wartość procentowa próbkowania jest różna, ta właściwość określa minimalną dozwoloną wartość procentową próbkowania. |
| maxSamplingPercentage | 0.1 | Ponieważ wartość procentowa próbkowania jest różna, ta właściwość określa maksymalną dozwoloną wartość procentową próbkowania. |
| przeprowadzkaAverageRatio | 1.0 | Przy obliczaniu średniej ruchomej waga przypisana do najnowszej wartości. Użyj wartości równej lub mniejszej niż 1. Mniejsze wartości sprawiają, że algorytm jest mniej reaktywny na nagłe zmiany. |
| wykluczone typy | wartość null | Średnik rozdzielona lista typów, które nie mają być próbkowane. Rozpoznane typy `Event` `Exception`to: `Request` `Dependency`, `Trace`, `PageView`, , i . Wszystkie wystąpienia określonych typów są przesyłane; typy, które nie są określone są próbkowania. |
| includedTypes (includedTypes) | wartość null | Średnik rozdzielona lista typów, które mają być próbkowane; pusta lista oznacza wszystkie typy. Wpisz wymienione `excludedTypes` w typach zastępowania wymienionych w tym miejscu. Rozpoznane typy `Event` `Exception`to: `Request` `Dependency`, `Trace`, `PageView`, , i . Przykłady określonych typów są próbkowania; typy, które nie są określone lub dorozumiane są przesyłane bez pobierania próbek. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|Właściwość | Domyślne | Opis |
| --------- | --------- | --------- | 
| włączhttptriggerextendedinfocollection | true | Włącza lub wyłącza rozszerzone informacje o żądaniu HTTP dla wyzwalaczy HTTP: nagłówki korelacji żądań przychodzących, obsługa kluczy wieloprzyrządowania, metoda HTTP, ścieżka i odpowiedź. |
| enableW3CDistributedTracing | true | Włącza lub wyłącza obsługę rozproszonego protokołu śledzenia W3C (i włącza schemat korelacji starszej wersji). Domyślnie włączone, `enableHttpTriggerExtendedInfoCollection` jeśli jest true. Jeśli `enableHttpTriggerExtendedInfoCollection` jest false, ta flaga dotyczy tylko żądań wychodzących, a nie żądań przychodzących. |
| enableResponseHeaderInjection | true | Włącza lub wyłącza wtrysk wieloskładnikowych nagłówków korelacji do odpowiedzi. Włączenie iniekcji umożliwia usłudze Application Insights konstruowanie mapy aplikacji do momentu użycia kilku kluczy instrumentacji. Domyślnie włączone, `enableHttpTriggerExtendedInfoCollection` jeśli jest true. To ustawienie nie ma `enableHttpTriggerExtendedInfoCollection` zastosowania, jeśli jest false. |

### <a name="applicationinsightssnapshotconfiguration"></a>aplikacjaInsights.snapshotKonfiguracja

Aby uzyskać więcej informacji na temat migawek, zobacz [Debugowanie migawek dotyczących wyjątków w aplikacjach platformy .NET](/azure/azure-monitor/app/snapshot-debugger) i [rozwiązywanie problemów z włączaniem debugera migawek usługi Application Insights lub wyświetlania migawek](/azure/azure-monitor/app/snapshot-debugger-troubleshoot).

|Właściwość | Domyślne | Opis |
| --------- | --------- | --------- | 
| agentEndpoint | wartość null | Punkt końcowy używany do łączenia się z usługą debugerowania migawek aplikacji aplikacji. Jeśli null, używany jest domyślny punkt końcowy. |
| captureSnapshotMemoryWeight | 0,5 | Waga nadana bieżącemu rozmiarowi pamięci procesu podczas sprawdzania, czy jest wystarczająco dużo pamięci, aby zrobić migawkę. Oczekiwana wartość jest większa niż 0 właściwej frakcji (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | Limit liczby żądań żądań żądań migawek przed wyłączeniem procesora telemetrii.|
| handleUntrackedExceptions | true | Włącza lub wyłącza śledzenie wyjątków, które nie są śledzone przez dane telemetryczne usługi Application Insights. |
| Isenabled | true | Włącza lub wyłącza zbieranie migawek | 
| isEnabledInDeveloperMode | false | Włącza lub wyłącza kolekcję migawek jest włączona w trybie dewelopera. |
| isEnabledWhenProfiling | true | Włącza lub wyłącza tworzenie migawki, nawet jeśli profiler usługi Application Insights zbiera szczegółową sesję profilowania. |
| isExceptionSnappointsEnabled | false | Włącza lub wyłącza filtrowanie wyjątków. |
| isLowPrioritySnapshotUploader | true | Określa, czy proces SnapshotUploader ma być uruchamiany poniżej normalnego priorytetu. |
| maksymalnyKolejny PlanSize | 50 | Maksymalna liczba problemów, które możemy śledzić w dowolnym momencie w zakresie od jednego do 9999. |
| maksymalna Wymagane zdjęcia | 3 | Maksymalna liczba migawek zebranych dla pojedynczego problemu, w zakresie od jednego do 999. Problem może być traktowane jako indywidualne throw instrukcji w aplikacji. Gdy liczba migawek zebranych dla problemu osiągnie tę wartość, nie więcej migawek będą zbierane `problemCounterResetInterval`dla `thresholdForSnapshotting` tego problemu, dopóki liczniki problemów są resetowane (zobacz ) i limit zostanie osiągnięty ponownie. |
| problemCounterResetInterval | 24:00:00 | Jak często resetować liczniki problemu w zakresie od jednej minuty do siedmiu dni. Po osiągnięciu tego interwału wszystkie liczby problemów są resetowane do zera. Istniejące problemy, które osiągnęły już próg wykonywania migawek, ale nie wygenerowały jeszcze liczby migawek w `maximumSnapshotsRequired`, pozostają aktywne. |
| provideAnonymousTelemetry | true | Określa, czy do firmy Microsoft ma być wysyłana anonimowa funkcja użycia i dane telemetryczne dotyczące błędów. Ta telemetria może być używana, jeśli skontaktujesz się z firmą Microsoft w celu rozwiązania problemów z debugerem migawek. Służy również do monitorowania wzorców użycia. |
| ponowne połączenieInterwal | 00:15:00 | Jak często ponownie połączyć się z punktem końcowym debugera migawek. Dopuszczalny zakres wynosi od jednej minuty do jednego dnia. |
| shadowCopyFolder | wartość null | Określa folder, który ma być używany do kopiowania plików binarnych w tle. Jeśli nie jest ustawiona, foldery określone przez następujące zmienne środowiskowe są wypróbowywalane w kolejności: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| proces shareUploaderprocess | true | Jeśli true, tylko jedno wystąpienie SnapshotUploader będzie zbierać i przekazywania migawek dla wielu aplikacji, które współużytkują InstrumentationKey. Jeśli ustawiona na false, SnapshotUploader będzie unikatowy dla każdej krotki (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | true | Określa, czy migawki mają być przetwarzane w wątku o niskim priorytecie we/wy. Tworzenie migawki jest szybką operacją, ale w celu przekazania migawki do usługi debuger migawki, musi najpierw być zapisywane na dysku jako minidump. Dzieje się tak w procesie SnapshotUploader. Ustawienie tej wartości true używa we/wy niskiego priorytetu do zapisu minidump, który nie będzie konkurować z aplikacji dla zasobów. Ustawienie tej wartości na false przyspiesza tworzenie minidump kosztem spowolnienia aplikacji. |
| migawkiPerDayLimit | 30 | Maksymalna liczba migawek dozwolonych w ciągu jednego dnia (24 godziny). Ten limit jest również wymuszany po stronie usługi Usługi Application Insights. Przesyłanie jest ograniczone do 50 dziennie na aplikację (czyli na klucz instrumentacji). Ta wartość pomaga zapobiec tworzeniu dodatkowych migawek, które ostatecznie zostaną odrzucone podczas przekazywania. Wartość zero całkowicie usuwa limit, co nie jest zalecane. |
| snapshotsPerTenMinutesLimit | 1 | Maksymalna liczba migawek dozwolona w ciągu 10 minut. Chociaż nie ma górnej granicy tej wartości, należy zachować ostrożność zwiększając ją na obciążeń produkcyjnych, ponieważ może to mieć wpływ na wydajność aplikacji. Tworzenie migawki jest szybkie, ale tworzenie minidump migawki i przekazywanie go do usługi debuger migawki jest znacznie wolniejsze operacji, która będzie konkurować z aplikacji dla zasobów (zarówno procesora CPU i we/wy). |
| tempFolder (folder tempfolder) | wartość null | Określa folder do pisania plików dziennika minidumps i uploader. Jeśli nie jest ustawiona, używana jest *%TEMP%\Dumps.* |
| thresholdForSnapshotting | 1 | Ile razy usługa Application Insights musi zobaczyć wyjątek, zanim poprosi o migawki. |
| uploaderProxy | wartość null | Zastępuje serwer proxy używany w procesie przekazywania migawek. Może być konieczne użycie tego ustawienia, jeśli aplikacja łączy się z Internetem za pośrednictwem serwera proxy. Moduł zbierający migawki działa w ramach procesu aplikacji i będzie używać tych samych ustawień serwera proxy. Jednak program przekazywania migawek działa jako oddzielny proces i może być konieczne ręczne skonfigurowanie serwera proxy. Jeśli ta wartość jest null, a następnie Snapshot Collector spróbuje automatycznie wyekslić adres serwera proxy, sprawdzając System.Net.WebRequest.DefaultWebProxy i przekazywania wartości do programu Snapshot Uploader. Jeśli ta wartość nie jest null, a następnie autodetection nie jest używany i serwer proxy określony w tym miejscu będą używane w przesyłacz migawek. |

## <a name="cosmosdb"></a>cosmosDb

Ustawienie konfiguracji można znaleźć w [wyzwalaczach i powiązaniach usługi Cosmos DB](functions-bindings-cosmosdb-v2-output.md#host-json).

## <a name="durabletask"></a>trwałeZadań

Ustawienie konfiguracji można znaleźć w [powiązaniach dla funkcji trwałych](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Ustawienia konfiguracji można znaleźć w [wyzwalaczach i powiązaniach Centrum zdarzeń](functions-bindings-event-hubs-trigger.md#host-json). 

## <a name="extensions"></a>Rozszerzenia

Właściwość, która zwraca obiekt, który zawiera wszystkie ustawienia specyficzne dla powiązania, takie jak [http](#http) i [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Pakiety rozszerzeń umożliwiają dodawanie zgodnego zestawu rozszerzeń powiązania funkcji do aplikacji funkcji. Aby dowiedzieć się więcej, zobacz [Pakiety rozszerzeń dla rozwoju lokalnego](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a> — funkcje

Lista funkcji uruchamianych przez hosta zadań. Pusta tablica oznacza uruchomienie wszystkich funkcji. Przeznaczone do użytku tylko wtedy, gdy [działa lokalnie](functions-run-local.md). W aplikacjach funkcji na platformie Azure zamiast tego należy wykonać kroki w [jak wyłączyć funkcje w usłudze Azure Functions,](disable-function.md) aby wyłączyć określone funkcje, a nie przy użyciu tego ustawienia.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>funkcjaCzas

Wskazuje czas trwania limitu czasu dla wszystkich funkcji. Wynika z formatu ciągu timespan. W planie zużycia bez użycia serwera prawidłowy zakres wynosi od 1 sekundy do 10 minut, a wartość domyślna to 5 minut.  

W planie Premium prawidłowy zakres wynosi od 1 sekundy do 60 minut, a wartość domyślna to 30 minut.

W planie dedykowane (usługa aplikacji) nie ma ogólnego limitu, a wartość domyślna wynosi 30 minut. Wartość `-1` wskazuje wykonanie bez ograniczeń, ale zaleca się zachowanie stałej górnej granicy.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>monitor zdrowia

Ustawienia konfiguracji [monitora kondycji hosta](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|enabled|true|Określa, czy funkcja jest włączona. | 
|healthCheckInterval|10 sekund|Przedział czasu między okresowymi kontrolami kondycji w tle. | 
|healthCheckWindow|2 min.|Przesuwane okno czasu używane w `healthCheckThreshold` połączeniu z ustawieniem.| 
|zdrowieWykonańce|6|Maksymalna liczba razy sprawdzanie kondycji może zakończyć się niepowodzeniem przed rozpoczęciem odtwarzania hosta.| 
|licznikTrzymek|0,80|Próg, przy którym licznik wydajności będą uważane za niezdrowe.| 

## <a name="http"></a>http

Ustawienia konfiguracji można znaleźć w [wyzwalaczach i powiązaniach http](functions-bindings-http-webhook-output.md#hostjson-settings).

## <a name="logging"></a>rejestrowanie

Steruje zachowaniami rejestrowania aplikacji funkcji, w tym usługi Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|tryb rejestrowania plików|debugOnly|Określa, jaki poziom rejestrowania plików jest włączony.  Dostępne `never`opcje `always` `debugOnly`to , , . |
|Loglevel|Nie dotyczy|Obiekt definiujący filtrowanie kategorii dziennika dla funkcji w aplikacji. Wersje 2.x i nowsze są zgodne z układem ASP.NET Core do filtrowania kategorii dziennika. To ustawienie umożliwia filtrowanie rejestrowania dla określonych funkcji. Aby uzyskać więcej informacji, zobacz [Filtrowanie dziennika](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) w dokumentacji ASP.NET Core. |
|console|Nie dotyczy| Ustawienie rejestrowania [konsoli.](#console) |
|ApplicationInsights|Nie dotyczy| Ustawienie [ApplicationInsights.](#applicationinsights) |

## <a name="console"></a>console

To ustawienie jest dziestawem [rejestrowania](#logging). Steruje rejestrowaniem konsoli, gdy nie jest w trybie debugowania.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|Isenabled|false|Włącza lub wyłącza rejestrowanie konsoli.| 

## <a name="manageddependency"></a>zarządzaneZależność

Zależność zarządzana jest funkcją, która jest obecnie obsługiwana tylko za pomocą funkcji opartych na programie PowerShell. Umożliwia zależności, które mają być automatycznie zarządzane przez usługę. Gdy `enabled` właściwość jest `true`ustawiona na `requirements.psd1` , plik jest przetwarzany. Zależności są aktualizowane po wydaniu wszystkich wersji pomocniczych. Aby uzyskać więcej informacji, zobacz [zależność zarządzana](functions-reference-powershell.md#dependency-management) w artykule programu PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>Kolejek

Ustawienia konfiguracji można znaleźć w [wyzwalaczach i powiązaniach kolejki magazynu](functions-bindings-storage-queue-output.md#host-json).  

## <a name="sendgrid"></a>sendGrid (sendGrid)

Ustawienie konfiguracji można znaleźć w [wyzwalaczach i powiązaniach SendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>usługiBus

Ustawienie konfiguracji można znaleźć w [wyzwalaczach i powiązaniach usługi Service Bus](functions-bindings-service-bus-output.md#host-json).

## <a name="singleton"></a>Singleton

Ustawienia konfiguracji zachowania blokady singleton. Aby uzyskać więcej informacji, zobacz [GitHub problem dotyczący obsługi singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|lockPeriod|00:00:15|Okres, dla który blokuje poziom funkcji. Blokady automatycznie odnawiają.| 
|odbiornikLockPeriod|00:01:00|Okres, dla który są brane zablokowania odbiornika.| 
|odbiornikLockRecoveryPollingInterval|00:01:00|Przedział czasu używany do odzyskiwania blokady odbiornika, jeśli blokada odbiornika nie może zostać nabyta podczas uruchamiania.| 
|lockAcquisitionTimeout|00:01:00|Maksymalny czas, przez który środowisko wykonawcze spróbuje uzyskać blokadę.| 
|lockAcquisitionPollingInterval|Nie dotyczy|Interwał między próbami nabycia blokady.| 

## <a name="version"></a>version

Ta wartość wskazuje wersję schematu host.json. Ciąg `"version": "2.0"` wersji jest wymagany dla aplikacji funkcji, która jest przeznaczona dla środowiska wykonawczego w wersji 2 lub nowszej wersji. Nie ma żadnych zmian schematu host.json między v2 i v3.

## <a name="watchdirectories"></a>watchKatary

Zestaw [katalogów kodu udostępnionego,](functions-reference-csharp.md#watched-directories) które powinny być monitorowane pod kątem zmian.  Zapewnia, że po zmianie kodu w tych katalogach zmiany są pobierane przez funkcje.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaktualizować plik host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zobacz ustawienia globalne w zmiennych środowiskowych](functions-app-settings.md)
