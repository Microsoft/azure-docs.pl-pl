---
title: Korelacja telemetrii Application Insights platformy Azure | Microsoft Docs
description: Application Insights korelacji telemetrii
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: fd9299d49f42eb021d64ae25447fd13e7378ff3f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447869"
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

Przyjrzyjmy się przykładowi. Aplikacja o nazwie ceny giełdowe pokazuje aktualną cenę rynkową przy użyciu zewnętrznego interfejsu API o nazwie Stock. Aplikacja do cen giełdowych ma stronę o nazwie Strona giełdowa, którą otwiera przeglądarka klienta sieci Web za pomocą programu `GET /Home/Stock` . Aplikacja wysyła zapytanie do interfejsu API spisu przy użyciu wywołania HTTP `GET /api/stock/value` .

Dane telemetryczne mogą być analizowane przez uruchomienie zapytania:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Zwróć uwagę, że wszystkie elementy telemetrii korzystają z katalogu głównego `operation_Id` . Po wykonaniu wywołania AJAX ze strony nowy unikatowy identyfikator ( `qJSXU` ) jest przypisywany do telemetrii zależności, a identyfikator pageView jest używany jako `operation_ParentId` . Żądanie serwera używa identyfikatora AJAX jako `operation_ParentId` .

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
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

Najnowsza wersja zestawu SDK Application Insights obsługuje protokół śledzenia-kontekstu, ale może być konieczne zachodzenie do niego. (Zgodność z poprzednimi wersjami z powyższym protokołem korelacji obsługiwanym przez zestaw Application Insights SDK zostanie zachowana).

[Protokół http korelacji, nazywany również identyfikatorem żądania](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), jest przestarzały. Ten protokół definiuje dwa nagłówki:

- `Request-Id`: Przenosi unikatowy identyfikator globalny wywołania.
- `Correlation-Context`: Przenosi kolekcję par nazwa-wartość dla właściwości rozproszonego śledzenia.

Application Insights również definiuje [rozszerzenie](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) dla protokołu HTTP korelacji. Używa `Request-Context` par nazwa-wartość do propagowania kolekcji właściwości używanych przez bezpośredni obiekt wywołujący lub wywoływany. Zestaw SDK Application Insights używa tego nagłówka do ustawiania `dependency.target` pól i `request.source` .

Modele danych w [kontekście śledzenia W3C](https://w3c.github.io/trace-context/) i Application Insights są mapowane w następujący sposób:

| Application Insights                   | TraceContext — śledzenie W3C                                      |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `SpanKind` czy serwer jest synchroniczny; `SpanKind` jest konsumentem, jeśli asynchronicznie                    |
| `Dependency`                           | `SpanKind` jest klientem w przypadku synchronicznego; `SpanKind` jest producentem, jeśli asynchronicznie                   |
| `Id` z `Request` i `Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `SpanId` z zakresu nadrzędnego tego zakresu. Jeśli jest to zakres główny, to pole musi być puste.     |

Aby uzyskać więcej informacji, zobacz [Application Insights model danych telemetrii](../../azure-monitor/app/data-model.md).

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla klasycznych aplikacji ASP.NET
 
  > [!NOTE]
  >  Począwszy od `Microsoft.ApplicationInsights.Web` i `Microsoft.ApplicationInsights.DependencyCollector` , nie jest wymagana żadna konfiguracja.

Obsługa funkcji śledzenia W3C jest zaimplementowana w sposób zgodny z poprzednimi wersjami. Oczekuje się, że korelacja będzie współdziałać z aplikacjami, które są Instrumentacją z poprzednimi wersjami zestawu SDK (bez pomocy technicznej W3C).

Aby nadal korzystać z starszego `Request-Id` protokołu, można wyłączyć kontekst śledzenia przy użyciu tej konfiguracji:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Jeśli uruchamiasz starszą wersję zestawu SDK, zalecamy jej aktualizację lub zastosowanie poniższej konfiguracji w celu włączenia kontekstu śledzenia.
Ta funkcja jest dostępna w `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.DependencyCollector` pakietach i, począwszy od wersji 2.8.0-beta1.
Jest on domyślnie wyłączony. Aby je włączyć, wprowadź następujące zmiany `ApplicationInsights.config` :

- W obszarze `RequestTrackingTelemetryModule` Dodaj `EnableW3CHeadersExtraction` element i ustaw jego wartość na `true` .
- W obszarze `DependencyTrackingTelemetryModule` Dodaj `EnableW3CHeadersInjection` element i ustaw jego wartość na `true` .
- Dodaj `W3COperationCorrelationTelemetryInitializer` poniżej `TelemetryInitializers` . Będzie wyglądać podobnie do tego przykładu:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Włącz obsługę rozproszonego śledzenia W3C dla aplikacji ASP.NET Core

 > [!NOTE]
  > Począwszy od `Microsoft.ApplicationInsights.AspNetCore` wersji 2.8.0, nie jest wymagana żadna konfiguracja.
 
Obsługa funkcji śledzenia W3C jest zaimplementowana w sposób zgodny z poprzednimi wersjami. Oczekuje się, że korelacja będzie współdziałać z aplikacjami, które są Instrumentacją z poprzednimi wersjami zestawu SDK (bez pomocy technicznej W3C).

Aby nadal korzystać z starszego `Request-Id` protokołu, można wyłączyć kontekst śledzenia przy użyciu tej konfiguracji:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Jeśli uruchamiasz starszą wersję zestawu SDK, zalecamy jej aktualizację lub zastosowanie poniższej konfiguracji w celu włączenia kontekstu śledzenia.

Ta funkcja jest w `Microsoft.ApplicationInsights.AspNetCore` wersji 2.5.0-beta1 i w `Microsoft.ApplicationInsights.DependencyCollector` wersji 2.8.0-beta1.
Jest on domyślnie wyłączony. Aby je włączyć, ustaw opcję `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` na `true` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

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

- **Instalator npm (zignoruj, jeśli użyto Instalatora wstawek)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Instalator fragmentu kodu (zignoruj, jeśli jest używany Instalator npm)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="telemetry-correlation-in-opencensus-python"></a>Korelacja telemetrii w języku Python OpenCensus

OpenCensus Python obsługuje [kontekst śledzenia W3C](https://w3c.github.io/trace-context/) , bez konieczności dodatkowej konfiguracji.

Jako odwołanie można znaleźć w [tym miejscu](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace)model danych OpenCensus.

### <a name="incoming-request-correlation"></a>Korelacja żądań przychodzących

OpenCensus Python skorelowanie nagłówków kontekstu śledzenia W3C z żądań przychodzących do zakresów, które są generowane na podstawie samych żądań. OpenCensus automatycznie podejmie integrację dla tych popularnych platform aplikacji sieci Web: kolby, Django i ostrosłupowy. Wystarczy wypełnić nagłówki W3C śledzenia-kontekstu [prawidłowym formatem](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) i wysłać je z żądaniem. Oto przykładowa aplikacja do kolby, która ilustruje:

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

W miarę upływu czasu platforma .NET określiła kilka sposobów skorelowania dzienników telemetrii i diagnostyki:

- `System.Diagnostics.CorrelationManager` umożliwia śledzenie elementów [LogicalOperationStack i ActivityId](/dotnet/api/system.diagnostics.correlationmanager?view=netcore-3.1).
- `System.Diagnostics.Tracing.EventSource` i śledzenie zdarzeń systemu Windows (ETW) definiują metodę [SetCurrentThreadActivityId](/dotnet/api/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid?view=netcore-3.1#overloads) .
- `ILogger` używa [zakresów dzienników](/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) i HTTP — "bieżące" Propagacja kontekstu.

Jednak te metody nie umożliwiały automatycznej obsługi śledzenia rozproszonego. `DiagnosticSource` obsługuje automatyczną korelację między maszynami. Biblioteki .NET obsługują `DiagnosticSource` i zezwalają na automatyczne propagowanie między maszynami kontekstu korelacji przy użyciu transportu, takiego jak http.

[Podręcznik użytkownika działania](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) w programie `DiagnosticSource` objaśnia podstawy śledzenia działań.

ASP.NET Core 2,0 obsługuje wyodrębnianie nagłówków HTTP i uruchamianie nowych działań.

`System.Net.Http.HttpClient`począwszy od wersji 4.1.0, obsługuje automatyczne wstrzyknięcie nagłówków HTTP korelacji i śledzenie wywołań HTTP jako działania.

Istnieje nowy moduł HTTP, [Microsoft. ASPNET. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), dla klasycznego ASP.NET. Ten moduł implementuje korelację telemetrii za pomocą `DiagnosticSource` . Uruchamia działanie na podstawie przychodzących nagłówków żądań. Wiąże się to również z danymi telemetrycznymi z różnych etapów przetwarzania żądań, nawet gdy przetwarzanie poszczególnych etapów Internet Information Services (IIS) przebiega w innym zarządzanym wątku.

Zestaw Application Insights SDK, zaczynając od wersji 2.4.0-beta1, używa `DiagnosticSource` i `Activity` do zbierania danych telemetrycznych i kojarzenia go z bieżącym działaniem.

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
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
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
