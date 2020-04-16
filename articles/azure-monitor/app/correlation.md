---
title: Korelacja telemetrii usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Korelacja danych telemetrycznych usługi Application Insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 2e862410e2bf12e09e1a6388bbb6f7105b5b2edf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405265"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelacja telemetrii w usłudze Application Insights

W świecie mikrousług każda operacja logiczna wymaga pracy do wykonania w różnych składnikach usługi. Każdy z tych składników można monitorować oddzielnie za pomocą [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md). Usługa Application Insights obsługuje korelację danych telemetrycznych rozproszonych, która służy do wykrywania, który składnik jest odpowiedzialny za błędy lub obniżenie wydajności.

W tym artykule opisano model danych używany przez usługa Application Insights do skorelowania danych telemetrycznych wysyłanych przez wiele składników. Obejmuje techniki propagacji kontekstu i protokoły. Obejmuje ona również wdrażanie taktyk korelacji na różnych językach i platformach.

## <a name="data-model-for-telemetry-correlation"></a>Model danych dla korelacji telemetrii

Usługa Application Insights definiuje [model danych](../../azure-monitor/app/data-model.md) dla korelacji danych telemetrycznych rozproszonych. Aby skojarzyć dane telemetryczne z operacją logiczną, każdy element telemetrii ma pole kontekstu o nazwie `operation_Id`. Ten identyfikator jest współużytkowany przez każdy element telemetrii w rozproszonym śledzenia. Tak więc nawet w przypadku utraty danych telemetrycznych z pojedynczej warstwy, nadal można skojarzyć dane telemetryczne zgłaszane przez inne składniki.

Rozproszona operacja logiczna zazwyczaj składa się z zestawu mniejszych operacji, które są żądaniami przetwarzanym przez jeden ze składników. Te operacje są definiowane przez [dane telemetryczne żądania](../../azure-monitor/app/data-model-request-telemetry.md). Każdy element telemetrii `id` żądania ma swój własny, który identyfikuje go jednoznacznie i globalnie. Wszystkie elementy telemetryczne (takie jak ślady i wyjątki), które `operation_parentId` są skojarzone z `id`żądaniem, powinny ustawić wartość żądania.

Każda operacja wychodząca, taka jak wywołanie HTTP do innego składnika, jest reprezentowana przez [telemetrię zależności](../../azure-monitor/app/data-model-dependency-telemetry.md). Telemetria zależności definiuje również `id` własną, która jest unikatowa globalnie. Dane telemetryczne żądania, zainicjowane przez to `id` wywołanie zależności, używa tego jako jego `operation_parentId`.

Można utworzyć widok rozproszonej operacji logicznej `operation_Id` `operation_parentId`za `request.id` pomocą `dependency.id`programu , i z . Pola te definiują również kolejność przyczynowości wywołań telemetrycznych.

W środowisku mikrousług ślady ze składników można przejść do różnych elementów magazynu. Każdy składnik może mieć swój własny klucz instrumentacji w usłudze Application Insights. Aby uzyskać dane telemetryczne dla operacji logicznej, usługa Application Insights wysyła zapytania do danych z każdego elementu magazynu. Gdy liczba elementów magazynu jest duża, musisz wskazówkę, gdzie szukać dalej. Model danych usługi Application Insights definiuje dwa `request.source` pola, aby rozwiązać ten problem: i `dependency.target`. Pierwsze pole identyfikuje składnik, który zainicjował żądanie zależności. Drugie pole identyfikuje składnik, który zwrócił odpowiedź wywołania zależności.

## <a name="example"></a>Przykład

Spójrzmy na przykład. Aplikacja o nazwie Stock Prices pokazuje aktualną cenę rynkową akcji za pomocą zewnętrznego interfejsu API o nazwie Stock. Aplikacja Ceny akcji ma stronę o nazwie Stock page, `GET /Home/Stock`którą przeglądarka internetowa klienta otwiera za pomocą programu . Aplikacja wysyła zapytanie do interfejsu API `GET /api/stock/value`stock przy użyciu wywołania HTTP .

Wynikową dane telemetryczne można analizować, uruchamiając kwerendę:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

W wynikach należy zauważyć, że wszystkie `operation_Id`elementy telemetrii współużytkują katalog główny . Gdy połączenie Ajax jest na wykonane ze strony,`qJSXU`nowy unikatowy identyfikator ( ) jest przypisany do telemetrii zależności, a identyfikator pageView jest używany jako `operation_ParentId`. Żądanie serwera następnie używa ajax id jako `operation_ParentId`.

| Itemtype   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| Odsłona strony   | Strona zapasów                |              | Okręg wyborczy STYz               | Okręg wyborczy STYz         |
| Zależności | GET /Strona główna/Zapasy           | qJSXU        | Okręg wyborczy STYz               | Okręg wyborczy STYz         |
| Żądanie    | GET Strona główna/Zapasy            | KqKwlrSt9PA= | qJSXU              | Okręg wyborczy STYz         |
| Zależności | POBIERZ /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | Okręg wyborczy STYz         |

Po nawoływania `GET /api/stock/value` do usługi zewnętrznej, należy znać tożsamość tego serwera, dzięki czemu można ustawić `dependency.target` pole odpowiednio. Gdy usługa zewnętrzna nie obsługuje `target` monitorowania, jest ustawiona na nazwę `stock-prices-api.com`hosta usługi (na przykład). Ale jeśli usługa identyfikuje się przez zwrócenie wstępnie zdefiniowanego `target` nagłówka HTTP, zawiera tożsamość usługi, która umożliwia aplikacji Insights do tworzenia śledzenia rozproszonego przez zapytanie danych telemetrycznych z tej usługi.

## <a name="correlation-headers"></a>Nagłówki korelacji

Usługa Application Insights przechodzi do [kontekstu śledzenia W3C,](https://w3c.github.io/trace-context/)który definiuje:

- `traceparent`: Nosi unikatowy globalnie identyfikator operacji i unikatowy identyfikator wywołania.
- `tracestate`: Przenosi kontekst śledzenia specyficzny dla systemu.

Najnowsza wersja zestawu SDK usługi Application Insights obsługuje protokół Trace-Context, ale może być konieczne wybranie go. (Zgodność wsteczna z poprzednim protokołem korelacji obsługiwanym przez SDK usługi Application Insights zostanie zachowana).

Korelacja [protokołu HTTP, nazywana również identyfikatorem żądania,](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)jest przestarzała. Ten protokół definiuje dwa nagłówki:

- `Request-Id`: Nosi unikatowy na całym świecie identyfikator połączenia.
- `Correlation-Context`: Przenosi kolekcję par nazw i wartości właściwości śledzenia rozproszonego.

Usługa Application Insights definiuje również [rozszerzenie](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) dla protokołu HTTP korelacji. Używa pary `Request-Context` nazwa wartość propagacji kolekcji właściwości używanych przez bezpośredniego wywołującego lub wywoływanego. Zestaw SDK usługi Application Insights używa `dependency.target` `request.source` tego nagłówka do ustawiania pól i pól.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Włącz obsługę śledzenia rozproszonego W3C dla klasycznych aplikacji ASP.NET
 
  > [!NOTE]
  >  Począwszy `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.DependencyCollector`od i , nie jest wymagana konfiguracja.

Obsługa kontekstu śledzenia W3C jest implementowana w sposób zgodny z powrotem. Korelacja ma działać z aplikacjami, które są instrumentowane z poprzednimi wersjami SDK (bez obsługi W3C).

Jeśli chcesz nadal używać `Request-Id` starszego protokołu, możesz wyłączyć trace-context przy użyciu tej konfiguracji:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Jeśli uruchomisz starszą wersję SDK, zaleca się jej zaktualizowanie lub zastosowanie następującej konfiguracji, aby włączyć kontekst śledzenia.
Ta funkcja jest `Microsoft.ApplicationInsights.Web` dostępna `Microsoft.ApplicationInsights.DependencyCollector` w pakietach i, począwszy od wersji 2.8.0-beta1.
Jest domyślnie wyłączona. Aby ją włączyć, należy `ApplicationInsights.config`wprowadzić te zmiany w:

- W `RequestTrackingTelemetryModule`obszarze `EnableW3CHeadersExtraction` dodaj element i `true`ustaw jego wartość na .
- W `DependencyTrackingTelemetryModule`obszarze `EnableW3CHeadersInjection` dodaj element i `true`ustaw jego wartość na .
- Dodaj `W3COperationCorrelationTelemetryInitializer` `TelemetryInitializers`w obszarze . Będzie wyglądać podobnie do tego przykładu:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Włącz obsługę śledzenia rozproszonego W3C dla aplikacji ASP.NET Core

 > [!NOTE]
  > Począwszy `Microsoft.ApplicationInsights.AspNetCore` od wersji 2.8.0, nie jest wymagana żadna konfiguracja.
 
Obsługa kontekstu śledzenia W3C jest implementowana w sposób zgodny z powrotem. Korelacja ma działać z aplikacjami, które są instrumentowane z poprzednimi wersjami SDK (bez obsługi W3C).

Jeśli chcesz nadal używać `Request-Id` starszego protokołu, możesz wyłączyć trace-context przy użyciu tej konfiguracji:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Jeśli uruchomisz starszą wersję SDK, zaleca się jej zaktualizowanie lub zastosowanie następującej konfiguracji, aby włączyć kontekst śledzenia.

Ta funkcja `Microsoft.ApplicationInsights.AspNetCore` jest w wersji 2.5.0-beta1 i w `Microsoft.ApplicationInsights.DependencyCollector` wersji 2.8.0-beta1.
Jest domyślnie wyłączona. Aby ją włączyć, `true`ustaw na: `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Włącz obsługę śledzenia rozproszonego W3C dla aplikacji Java

#### <a name="java-30-agent"></a>Agent Java 3.0

  Agent Java 3.0 obsługuje W3C po wyjęciu z pudełka i nie jest wymagana żadna dodatkowa konfiguracja. 

#### <a name="java-sdk"></a>Zestaw SDK Java
- **Konfiguracja przychodząca**

  - W przypadku aplikacji Java EE `<TelemetryModules>` dodaj do tagu w pliku ApplicationInsights.xml następujące elementy:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - W przypadku aplikacji Spring Boot dodaj następujące właściwości:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Konfiguracja wychodząca**

  Dodaj następujące elementy do pliku AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Tryb zgodności z powrotem jest domyślnie `enableW3CBackCompat` włączony, a parametr jest opcjonalny. Używaj go tylko wtedy, gdy chcesz wyłączyć zgodność z powrotem.
  >
  > W idealnym przypadku można wyłączyć tę funkcję, gdy wszystkie usługi zostały zaktualizowane do nowszych wersji sdków obsługujących protokół W3C. Zdecydowanie zaleca się jak najszybsze przejście do tych nowszych sdków.

> [!IMPORTANT]
> Upewnij się, że konfiguracje przychodzące i wychodzące są dokładnie takie same.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Włącz obsługę śledzenia rozproszonego W3C dla aplikacji sieci Web

Ta funkcja `Microsoft.ApplicationInsights.JavaScript`znajduje się w pliku . Jest domyślnie wyłączona. Aby ją włączyć, użyj `distributedTracingMode` konfiguracji. AI_AND_W3C jest przewidziane dla zgodności z powrotem z wszelkich starszych usług instrumentowane przez application insights.

- **konfiguracja npm (ignoruj, jeśli używasz konfiguracji urywka)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Ustawienia urywka (ignoruj, jeśli używasz konfiguracji npm)**

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

## <a name="opentracing-and-application-insights"></a>OpenTracing i usługa Application Insights

[Specyfikacja modelu danych OpenTracing](https://opentracing.io/) i modele danych usługi Application Insights są mapowane w następujący sposób:

| Application Insights                   | Otwórz śledzenie                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `Span`Z`span.kind = server`                    |
| `Dependency`                           | `Span`Z`span.kind = client`                    |
| `Id`z `Request` i`Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `Reference`typu `ChildOf` (zakres nadrzędny)     |

Aby uzyskać więcej informacji, zobacz [Model danych telemetrycznych usługi Application Insights](../../azure-monitor/app/data-model.md).

Definicje pojęć OpenTracing można znaleźć w [specyfikacji](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing i [konwencjach semantycznych](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Korelacja telemetrii w języku Python OpenCensus

OpenCensus Python `OpenTracing` jest zgodny ze specyfikacjami modelu danych opisanymi wcześniej. Obsługuje również [W3C Trace-Context](https://w3c.github.io/trace-context/) bez konieczności konfiguracji.

### <a name="incoming-request-correlation"></a>Korelacja żądań przychodzących

OpenCensus Python koreluje nagłówki W3C Trace-Context z żądań przychodzących do zakresów, które są generowane z samych żądań. OpenCensus zrobi to automatycznie z integracji dla tych popularnych platform aplikacji internetowych: Flask, Django i Pyramid. Wystarczy wypełnić nagłówki W3C Trace-Context [poprawnym formatem](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) i wysłać je z żądaniem. Oto przykładowa aplikacja Flask, która pokazuje to:

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

Ten kod uruchamia przykładową aplikację Flask na `8080`komputerze lokalnym, nasłuchując portu . Aby skorelować kontekst śledzenia, należy wysłać żądanie do punktu końcowego. W tym przykładzie można `curl` użyć polecenia:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Patrząc na [format nagłówka kontekstu śledzenia,](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)można uzyskać następujące informacje:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Jeśli spojrzeć na wpis żądania, który został wysłany do usługi Azure Monitor, można zobaczyć pola wypełnione informacjami nagłówka śledzenia. Te dane można znaleźć w obszarze Dzienniki (Analytics) w zasobie usługi Azure Monitor Application Insights.

![Żądania danych telemetrycznych w dziennikach (Analytics)](./media/opencensus-python/0011-correlation.png)

Pole `id` jest w `<trace-id>.<span-id>`formacie , `trace-id` gdzie jest pobierana z nagłówka śledzenia, który został przekazany w żądaniu i `span-id` jest wygenerowaną tablicą 8-bajtową dla tego zakresu.

Pole `operation_ParentId` jest w `<trace-id>.<parent-id>`formacie , `trace-id` gdzie `parent-id` zarówno i są pobierane z nagłówka śledzenia, który został przekazany w żądaniu.

### <a name="log-correlation"></a>Korelacja dzienników

OpenCensus Python umożliwia skorelowanie dzienników przez dodanie identyfikatora śledzenia, identyfikatora zakresu i flagi próbkowania do rejestrowania rekordów. Dodaj te atrybuty, instalując [integrację rejestrowania](https://pypi.org/project/opencensus-ext-logging/)OpenCensus . Do obiektów Języka `LogRecord` Python zostaną `traceId`dodane `spanId`następujące `traceSampled`atrybuty: , i . Należy zauważyć, że to ma obowiązywać tylko dla rejestratorów, które są tworzone po integracji.

Oto przykładowa aplikacja, która pokazuje to:

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
Po uruchomieniu tego kodu w konsoli są drukowane następujące elementy:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Należy zauważyć, że `spanId` istnieje prezent dla komunikatu dziennika, który znajduje się w zakresie. Jest to `spanId` to samo, które `hello`należy do zakresu o nazwie .

Dane dziennika można wyeksportować za pomocą programu `AzureLogHandler`. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Korelacja telemetrii w .NET

Wraz z czasem .NET zdefiniował kilka sposobów skorelowania dzienników telemetrii i diagnostyki:

- `System.Diagnostics.CorrelationManager`umożliwia śledzenie [LogicalOperationStack i ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource`i śledzenie zdarzeń dla systemu Windows (ETW) definiują [metodę SetCurrentThreadActivityId.](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx)
- `ILogger`używa [zakresów dziennika](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) i HTTP wire up "bieżące" propagacji kontekstu.

Ale te metody nie umożliwiają automatyczną obsługę śledzenia rozproszonego. `DiagnosticSource`obsługuje automatyczną korelację między maszynami. Biblioteki .NET `DiagnosticSource` obsługują i umożliwiają automatyczne propagowanie między komputerami kontekstu korelacji za pośrednictwem transportu, takich jak HTTP.

Podręcznik [użytkownika](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) aktywności `DiagnosticSource` w artykule wyjaśniono podstawy działań związanych ze śledzeniem.

ASP.NET Core 2.0 obsługuje wyodrębnianie nagłówków HTTP i uruchamianie nowych działań.

`System.Net.Http.HttpClient`, począwszy od wersji 4.1.0, obsługuje automatyczne wtryszenie nagłówków HTTP korelacji i śledzenie wywołań HTTP jako działania.

Istnieje nowy moduł HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), dla klasycznych ASP.NET. Ten moduł implementuje korelację telemetrii przy użyciu programu `DiagnosticSource`. Uruchamia działanie na podstawie nagłówków żądań przychodzących. Koreluje również dane telemetryczne z różnych etapów przetwarzania żądań, nawet wtedy, gdy każdy etap przetwarzania internetowych usług informacyjnych (IIS) działa w innym wątku zarządzanym.

SDK usługi Application Insights, począwszy od wersji 2.4.0-beta1, używa `DiagnosticSource` i `Activity` do zbierania danych telemetrycznych i skojarzyć go z bieżącej działalności.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Korelacja telemetrii w języku Java

[Agent Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) oraz [zestaw Java SDK](../../azure-monitor/app/java-get-started.md) w wersji 2.0.0 lub nowszej obsługują automatyczną korelację danych telemetrycznych. Automatycznie wypełnia `operation_id` dla wszystkich danych telemetrycznych (takich jak ślady, wyjątki i zdarzenia niestandardowe) wydane w zakresie żądania. Propaguje również nagłówki korelacji (opisane wcześniej) dla wywołań usługi do usługi za pośrednictwem protokołu HTTP, jeśli [agent java SDK](../../azure-monitor/app/java-agent.md) jest skonfigurowany.

> [!NOTE]
> Agent Java usługi Application Insights automatycznie zbiera żądania i zależności dla JMS, Kafka, Netty/Webflux i innych. W przypadku java SDK obsługiwane są tylko wywołania wykonane za pośrednictwem protokołu Apache HttpClient dla funkcji korelacji. Automatyczne propagowanie kontekstu między technologiami obsługi wiadomości (takimi jak Kafka, RabbitMQ i usługa Azure Service Bus) nie jest obsługiwana w pliku SDK. 

> [!NOTE]
> Aby zebrać niestandardowe dane telemetryczne, należy przyswrzeć aplikację za pomocą zestawu Java 2.6 SDK. 

### <a name="role-names"></a>Nazwy ról

Można dostosować sposób wyświetlania nazw składników w [mapie aplikacji](../../azure-monitor/app/app-map.md). Aby to zrobić, można ręcznie `cloud_RoleName` ustawić, wykonując jedną z następujących czynności:

- W przypadku agenta Java usługi Application Insights 3.0 ustaw następującą nazwę roli w chmurze:

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    Nazwę roli w chmurze można również `APPLICATIONINSIGHTS_ROLE_NAME`ustawić przy użyciu zmiennej środowiskowej .

- Za pomocą aplikacji Application Insights Java SDK 2.5.0 lub nowszych można określić, `cloud_RoleName` dodając `<RoleName>` do pliku ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Jeśli używasz Spring Boot z aplikacją Insights Spring Boot Starter, wystarczy ustawić niestandardową nazwę aplikacji w pliku application.properties:

  `spring.application.name=<name-of-app>`

  Spring Boot Starter automatycznie `cloudRoleName` przypisuje do wartości wprowadzonej dla `spring.application.name` właściwości.

## <a name="next-steps"></a>Następne kroki

- [Zapisuj niestandardowe dane telemetryczne](../../azure-monitor/app/api-custom-events-metrics.md).
- Aby zapoznać się ze scenariuszami zaawansowanej korelacji w ASP.NET Core i ASP.NET, zobacz [Śledzenie operacji niestandardowych](custom-operations-tracking.md).
- Dowiedz się więcej o [ustawianiu cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) dla innych sdków.
- Dołączanie wszystkich składników mikrousługi w usłudze Application Insights. Sprawdź [obsługiwane platformy](../../azure-monitor/app/platforms.md).
- Zobacz [model danych](../../azure-monitor/app/data-model.md) dla typów usługi Application Insights.
- Dowiedz się, jak [rozszerzyć i filtrować dane telemetryczne](../../azure-monitor/app/api-filtering-sampling.md).
- Przejrzyj [odwołanie do konfiguracji usługi Application Insights](configuration-with-applicationinsights-config.md).
