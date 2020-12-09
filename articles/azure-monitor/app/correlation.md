---
title: Korelacja telemetrii Application Insights platformy Azure | Microsoft Docs
description: Application Insights korelacji telemetrii
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 7a206d61124b2524feae0cf155083c1939696e18
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853326"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelacja telemetrii w Application Insights

Na świecie mikrousług każda operacja logiczna wymaga wykonania pracy w różnych składnikach usługi. Każdy z tych składników można monitorować osobno przy użyciu [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights obsługuje korelację rozproszonej telemetrii, za pomocą której można wykryć, który składnik jest odpowiedzialny za błędy lub spadek wydajności.

W tym artykule opisano model danych używany przez Application Insights do skorelowania telemetrii wysyłanej przez wiele składników. Obejmuje to techniki i protokoły propagacji kontekstowej. Obejmuje to również implementację korelacji taktykę w różnych językach i platformach.

## <a name="data-model-for-telemetry-correlation"></a>Model danych korelacji telemetrii

Application Insights definiuje [model danych](../../azure-monitor/app/data-model.md) dla korelacji rozproszonej telemetrii. Aby skojarzyć dane telemetryczne z operacją logiczną, każdy element telemetrii ma pole kontekstu o nazwie `operation_Id` . Ten identyfikator jest współużytkowany przez każdy element telemetrii rozproszonego śledzenia. Nawet jeśli utracisz dane telemetryczne z pojedynczej warstwy, nadal możesz skojarzyć telemetrię zgłoszoną przez inne składniki.

Rozproszone operacje logiczne zwykle składają się z zestawu mniejszych operacji, które są żądaniami przetworzonymi przez jeden ze składników. Te operacje są definiowane przez dane [telemetryczne żądania](../../azure-monitor/app/data-model-request-telemetry.md). Każdy element telemetrii żądania jest własnym `id` , który jednoznacznie i globalnie identyfikuje. Wszystkie elementy telemetrii (takie jak ślady i wyjątki), które są skojarzone z żądaniem, powinny ustawiać `operation_parentId` wartość żądania `id` .

Każda operacja wychodząca, taka jak wywołanie HTTP w innym składniku, jest reprezentowana przez dane [telemetryczne zależności](../../azure-monitor/app/data-model-dependency-telemetry.md). Funkcja Telemetria zależności określa również własną `id` globalnie unikatową. Dane telemetryczne żądania inicjowane przez to wywołanie zależności używa tego elementu `id` jako jego elementu `operation_parentId` .

Możesz utworzyć widok rozproszonej operacji logicznej przy użyciu `operation_Id` , `operation_parentId` , i `request.id` z `dependency.id` . Te pola definiują również kolejność wywoływania wywołań telemetrycznych.

W środowisku mikrousług ślady składników mogą przechodzić do różnych elementów magazynu. Każdy składnik może mieć własny klucz Instrumentacji w Application Insights. Aby uzyskać dane telemetryczne dla operacji logicznej, Application Insights wysyła zapytanie do danych z każdego elementu magazynu. Gdy liczba elementów magazynu jest duża, musisz mieć wskazówkę dotyczącą miejsca, w którym będzie wyglądać dalej. Application Insights model danych definiuje dwa pola, aby rozwiązać ten problem: `request.source` i `dependency.target` . Pierwsze pole identyfikuje składnik inicjujący żądanie zależności. Drugie pole określa, który składnik zwrócił odpowiedź wywołania zależności.

## <a name="example"></a>Przykład

Spójrzmy na przykład. Aplikacja o nazwie ceny giełdowe pokazuje aktualną cenę rynkową przy użyciu zewnętrznego interfejsu API o nazwie Stock. Aplikacja do cen giełdowych ma stronę o nazwie Strona giełdowa, którą otwiera przeglądarka klienta sieci Web za pomocą programu `GET /Home/Stock` . Aplikacja wysyła zapytanie do interfejsu API spisu przy użyciu wywołania HTTP `GET /api/stock/value` .

Dane telemetryczne mogą być analizowane przez uruchomienie zapytania:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Zwróć uwagę, że wszystkie elementy telemetrii korzystają z katalogu głównego `operation_Id` . Po wykonaniu wywołania AJAX ze strony nowy unikatowy identyfikator ( `qJSXU` ) jest przypisywany do telemetrii zależności, a identyfikator pageView jest używany jako `operation_ParentId` . Żądanie serwera używa identyfikatora AJAX jako `operation_ParentId` .

| itemType   | name                      | ID (Identyfikator)           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Strona giełdowa                |              | STYz               | STYz         |
| zależności | Pobierz/Home/Stock           | qJSXU        | STYz               | STYz         |
| żądając    | Pobierz domowy/giełdowy            | KqKwlrSt9PA = | qJSXU              | STYz         |
| zależności | Pobierz/API/Stock/Value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Gdy wywołanie `GET /api/stock/value` jest nawiązywane w usłudze zewnętrznej, należy znać tożsamość tego serwera, aby można było `dependency.target` odpowiednio ustawić pole. Gdy usługa zewnętrzna nie obsługuje monitorowania, `target` jest ustawiona na nazwę hosta usługi (na przykład `stock-prices-api.com` ). Ale jeśli usługa identyfikuje siebie przez zwrócenie wstępnie zdefiniowanego nagłówka HTTP, `target` zawiera tożsamość usługi umożliwiającą Application Insights kompilowania rozproszonego śledzenia przez przeszukiwanie danych telemetrycznych z tej usługi.

## <a name="correlation-headers-using-w3c-tracecontext"></a>Nagłówki korelacji przy użyciu TraceContext — śledzenie W3C

Application Insights przechodzi do [kontekstu śledzenia W3C](https://w3c.github.io/trace-context/), który definiuje:

- `traceparent`: Przenosi unikatowy identyfikator operacji globalnego i unikatowy identyfikator wywołania.
- `tracestate`: Przenosi kontekst śledzenia specyficzny dla systemu.

Najnowsza wersja zestawu SDK Application Insights obsługuje protokół Trace-Context, ale może być konieczne zastąpienie do niego. (Zgodność z poprzednimi wersjami z powyższym protokołem korelacji obsługiwanym przez zestaw Application Insights SDK zostanie zachowana).

[Protokół http korelacji, nazywany również identyfikatorem żądania](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), jest przestarzały. Ten protokół definiuje dwa nagłówki:

- `Request-Id`: Przenosi unikatowy identyfikator globalny wywołania.
- `Correlation-Context`: Przenosi kolekcję par nazwa-wartość dla właściwości rozproszonego śledzenia.

Application Insights również definiuje [rozszerzenie](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) dla protokołu HTTP korelacji. Używa `Request-Context` par nazwa-wartość do propagowania kolekcji właściwości używanych przez bezpośredni obiekt wywołujący lub wywoływany. Zestaw SDK Application Insights używa tego nagłówka do ustawiania `dependency.target` pól i `request.source` .

Modele danych w [kontekście śledzenia W3C](https://w3c.github.io/trace-context/) i Application Insights są mapowane w następujący sposób:

| Application Insights                   | TraceContext — śledzenie W3C                                      |
|------------------------------------    |-------------------------------------------------|
| `Id` z `Request` i `Dependency`     | [Identyfikator elementu nadrzędnego](https://w3c.github.io/trace-context/#parent-id)                                     |
| `Operation_Id`                         | [Identyfikator śledzenia](https://w3c.github.io/trace-context/#trace-id)                                           |
| `Operation_ParentId`                   | [Identyfikator elementu nadrzędnego](https://w3c.github.io/trace-context/#parent-id) tego zakresu. Jeśli jest to zakres główny, to pole musi być puste.     |


Aby uzyskać więcej informacji, zobacz [Application Insights model danych telemetrii](../../azure-monitor/app/data-model.md).

### <a name="enable-w3c-distributed-tracing-support-for-net-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji platformy .NET

Śledzenie rozproszone oparte na TraceContext — śledzenieach jest domyślnie włączone we wszystkich najnowszych zestawach SDK .NET Framework/. NET Core oraz zgodność z poprzednimi wersjami przy użyciu starszego protokołu Request-Id.

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji Java

#### <a name="java-30-agent"></a>Agent Java 3,0

  Agent Java 3,0 obsługuje W3C z pudełka i nie jest wymagana żadna dodatkowa konfiguracja. 

#### <a name="java-sdk"></a>Zestaw SDK Java
- **Konfiguracja przychodząca**

  - W przypadku aplikacji Java EE Dodaj następujący kod do `<TelemetryModules>` znacznika w ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - W przypadku aplikacji do rozruchu sprężynowego Dodaj następujące właściwości:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Konfiguracja wychodząca**

  Dodaj następujące elementy do AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Tryb zgodności z poprzednimi wersjami jest domyślnie włączony, a `enableW3CBackCompat` parametr jest opcjonalny. Użyj go tylko wtedy, gdy chcesz wyłączyć zgodność z poprzednimi wersjami.
  >
  > Najlepiej ją wyłączyć, gdy wszystkie usługi zostały zaktualizowane do nowszych wersji zestawów SDK, które obsługują protokół W3C. Zdecydowanie zalecamy przechodzenie do tych nowszych zestawów SDK najszybciej, jak to możliwe.

> [!IMPORTANT]
> Upewnij się, że konfiguracje przychodzące i wychodzące są dokładnie takie same.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji sieci Web

Ta funkcja jest dostępna w programie `Microsoft.ApplicationInsights.JavaScript` . Jest on domyślnie wyłączony. Aby ją włączyć, użyj `distributedTracingMode` konfiguracji. AI_AND_W3C zapewnia zgodność z poprzednimi wersjami ze starszymi usługami przystosowanymi przez Application Insights.

- **[Konfiguracja oparta na npm](./javascript.md#npm-based-setup)**

Dodaj następującą konfigurację:
  ```JavaScript
    distributedTracingMode: DistributedTracingModes.W3C
  ```
  
- **[Konfiguracja oparta na fragmentach kodu](./javascript.md#snippet-based-setup)**

Dodaj następującą konfigurację:
  ```
      distributedTracingMode: 2 // DistributedTracingModes.W3C
  ```
> [!IMPORTANT] 
> Aby wyświetlić wszystkie konfiguracje wymagane do włączenia korelacji, zapoznaj się z [dokumentacją korelacji JavaScript](./javascript.md#enable-correlation).

## <a name="telemetry-correlation-in-opencensus-python"></a>Korelacja telemetrii w języku Python OpenCensus

OpenCensus Python obsługuje [kontekst śledzenia W3C](https://w3c.github.io/trace-context/) , bez konieczności dodatkowej konfiguracji.

Jako odwołanie można znaleźć w [tym miejscu](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace)model danych OpenCensus.

### <a name="incoming-request-correlation"></a>Korelacja żądań przychodzących

OpenCensus Python skorelowanie nagłówków W3C Trace-Context z żądań przychodzących do zakresów, które są generowane na podstawie samych żądań. OpenCensus automatycznie podejmie integrację dla tych popularnych platform aplikacji sieci Web: kolby, Django i ostrosłupowy. Wystarczy wypełnić nagłówki W3C Trace-Context [prawidłowym formatem](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) i wysłać je z żądaniem. Oto przykładowa aplikacja do kolby, która ilustruje:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Ten kod uruchamia przykładową aplikację do kolby na komputerze lokalnym, nasłuchuje na porcie `8080` . Aby skorelować kontekst śledzenia, Wyślij żądanie do punktu końcowego. W tym przykładzie można użyć `curl` polecenia:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Przeglądając [Format nagłówka-kontekstu śledzenia](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), można utworzyć następujące informacje:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Jeśli zobaczysz wpis żądania, który został wysłany do Azure Monitor, możesz zobaczyć pola wypełnione informacjami nagłówka śledzenia. Te dane można znaleźć w obszarze Dzienniki (analiza) w zasobie Azure Monitor Application Insights.

![Zażądaj danych telemetrycznych w dziennikach (analiza)](./media/opencensus-python/0011-correlation.png)

`id`Pole jest w formacie `<trace-id>.<span-id>` , gdzie `trace-id` jest pobierany z nagłówka śledzenia, który został przesłany w żądaniu, a `span-id` jest generowaną 8-bajtową tablicą dla tego zakresu.

`operation_ParentId`Pole jest w formacie `<trace-id>.<parent-id>` , gdzie oba `trace-id` i `parent-id` są pobierane z nagłówka śledzenia, który został przesłany w żądaniu.

### <a name="log-correlation"></a>Korelacja dzienników

OpenCensus Python umożliwia skorelowanie dzienników przez dodanie identyfikatora śledzenia, identyfikatora zakresu i flagi próbkowania w celu rejestrowania rekordów. Te atrybuty są dodawane przez zainstalowanie [integracji rejestrowania](https://pypi.org/project/opencensus-ext-logging/)OpenCensus. Następujące atrybuty zostaną dodane do `LogRecord` obiektów Python: `traceId` , `spanId` , i `traceSampled` . Należy zauważyć, że ta wartość obowiązuje tylko w przypadku rejestratorów utworzonych po integracji.

Oto przykładowa aplikacja, która ilustruje następujące:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Po uruchomieniu tego kodu następujące elementy są drukowane w konsoli programu:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Zwróć uwagę, że istnieje `spanId` komunikat dziennika, który znajduje się w ramach zakresu. Jest to taka sama, `spanId` która należy do zakresu o nazwie `hello` .

Dane dziennika można wyeksportować za pomocą polecenia `AzureLogHandler` . Aby uzyskać więcej informacji, zobacz [ten artykuł](./opencensus-python.md#logs).

## <a name="telemetry-correlation-in-net"></a>Korelacja telemetrii w programie .NET

Środowisko uruchomieniowe platformy .NET obsługuje dystrybucję z użyciem [działań](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) i [DiagnosticSource](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)

Application Insights .NET SDK używa `DiagnosticSource` i `Activity` do zbierania i skorelowania telemetrii.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Korelacja telemetrii w języku Java

Zarówno [Agent Java](./java-in-process-agent.md) , jak i [zestaw SDK Java](../../azure-monitor/app/java-get-started.md) w wersji 2.0.0 lub nowszej obsługują automatyczną korelację danych telemetrycznych. Automatycznie wypełnia `operation_id` wszystkie dane telemetryczne (takie jak ślady, wyjątki i zdarzenia niestandardowe), które zostały wystawione w zakresie żądania. Propaguje także nagłówki korelacji (opisane wcześniej) dla wywołań między usługami za pośrednictwem protokołu HTTP, jeśli skonfigurowano [agenta zestawu Java SDK](../../azure-monitor/app/java-agent.md) .

> [!NOTE]
> Application Insights Agent języka Java zbiera żądania i zależności dla JMS, Kafka, sieci i sieci webstrumień i nie tylko. Dla funkcji korelacji są obsługiwane tylko wywołania zestawu Java SDK nawiązywane za pośrednictwem platformy Apache HttpClient. Automatyczne propagowanie kontekstu w technologiach obsługi komunikatów (takich jak Kafka, RabbitMQ i Azure Service Bus) nie jest obsługiwane w zestawie SDK. 

> [!NOTE]
> Zbieranie niestandardowych danych telemetrycznych potrzebnych do Instrumentacji aplikacji za pomocą zestawu Java 2,6 SDK. 

### <a name="role-names"></a>Nazwy ról

Można dostosować sposób wyświetlania nazw składników na [mapie aplikacji](../../azure-monitor/app/app-map.md). Aby to zrobić, można ręcznie ustawić, `cloud_RoleName` wykonując jedną z następujących czynności:

- W przypadku programu Application Insights Java Agent 3,0 Ustaw nazwę roli w chmurze w następujący sposób:

    ```json
    {
      "role": {
        "name": "my cloud role name"
      }
    }
    ```
    Możesz również ustawić nazwę roli w chmurze przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_ROLE_NAME` .

- Za pomocą Application Insights Java SDK 2.5.0 i nowszych można określić `cloud_RoleName` przez dodanie `<RoleName>` do ApplicationInsights.xml pliku:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- W przypadku korzystania z sieci sprężynowej przy użyciu Application Insights sprężynowego startu, wystarczy ustawić niestandardową nazwę aplikacji w pliku Application. Properties:

  `spring.application.name=<name-of-app>`

  Rozruch sprężynowy startowy jest automatycznie przypisywany `cloudRoleName` do wartości wprowadzonej dla `spring.application.name` właściwości.

## <a name="next-steps"></a>Następne kroki

- Napisz [niestandardową telemetrię](../../azure-monitor/app/api-custom-events-metrics.md).
- Aby uzyskać zaawansowane scenariusze korelacji w ASP.NET Core i ASP.NET, zobacz [śledzenie operacji niestandardowych](custom-operations-tracking.md).
- Dowiedz się więcej o [ustawianiu cloud_RoleName](./app-map.md#set-or-override-cloud-role-name) dla innych zestawów SDK.
- Dołączanie wszystkich składników mikrousługi na Application Insights. Zapoznaj się z [obsługiwanymi platformami](./platforms.md).
- Zobacz [model danych](./data-model.md) dla typów Application Insights.
- Dowiedz się [, jak rozciągnąć i filtrować dane telemetryczne](./api-filtering-sampling.md).
- Przejrzyj [informacje dotyczące konfiguracji Application Insights](configuration-with-applicationinsights-config.md).