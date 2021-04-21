---
title: Azure Monitor Application Insights Java
description: Monitorowanie wydajności aplikacji dla aplikacji Java działających w dowolnym środowisku bez konieczności modyfikowania kodu. Śledzenie rozproszone i mapa aplikacji.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3f22e165fe4a3f86ecce8b1e307b19fae0eeac81
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812052"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Monitorowanie aplikacji bez kodu w języku Java Azure Monitor Application Insights

Monitorowanie aplikacji bez kodu w języku Java jest proste — nie ma żadnych zmian w kodzie. Agenta języka Java można włączyć za pomocą zaledwie kilku zmian konfiguracji.

 Agent języka Java działa w dowolnym środowisku i umożliwia monitorowanie wszystkich aplikacji Java. Innymi słowy, bez względu na to, czy aplikacje Java są uruchomione na maszynach wirtualnych, lokalnie, w UKS, w systemach Windows i Linux — nadaj jej nazwę, agent języka Java 3.0 będzie monitorować aplikację.

Dodanie zestawu SDK Application Insights Java do aplikacji nie jest już wymagane, ponieważ agent w wersji 3.0 automatycznie zbiera żądania, zależności i dzienniki samodzielnie.

Nadal możesz wysyłać niestandardowe dane telemetryczne z aplikacji. Agent 3.0 będzie śledzić i korelować go wraz ze wszystkimi automatycznie zebranymi telemetriami.

Agent w wersji 3.0 obsługuje środowisko Java 8 i więcej.

## <a name="quickstart"></a>Szybki start

**1. Pobieranie agenta**

> [!WARNING]
> **W przypadku uaktualniania z wersji 3.0 (wersja zapoznawcza)**
>
> Dokładnie przejrzyj wszystkie [opcje](./java-standalone-config.md) konfiguracji, ponieważ struktura JSON została całkowicie zmieniona, oprócz samej nazwy pliku, która zawierała tylko małe litery.

Pobierz [plik applicationinsights-agent-3.0.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.3/applicationinsights-agent-3.0.3.jar)

**2. Wskaż agentowi JVM**

Dodawanie `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` dorgs JVM aplikacji

Typowe args JVM obejmują `-Xmx512m` i `-XX:+UseG1GC` . Jeśli więc wiesz, gdzie je dodać, wiesz już, gdzie je dodać.

Aby uzyskać dodatkową pomoc w konfigurowaniu args JVM aplikacji, zobacz Porady dotyczące aktualizowania [argS JVM.](./java-standalone-arguments.md)

**3. Wskaż agentowi swój Application Insights zasobów**

Jeśli nie masz jeszcze zasobu Application Insights, możesz utworzyć nowy zasób, korzystając z instrukcji w [przewodniku tworzenia zasobu](./create-new-resource.md).

Wskaż agentowi zasób Application Insights, ustawiając zmienną środowiskową:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Lub tworząc plik konfiguracji o nazwie i umieszczając go w tym samym katalogu, w którym znajduje się `applicationinsights.json` `applicationinsights-agent-3.0.3.jar` plik o następującej zawartości:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Możesz znaleźć swoje parametrów połączenia w zasobie Application Insights zasobów:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights parametrów połączenia":::

**4. To wszystko!**

Teraz uruchom aplikację i przejdź do zasobu Application Insights w Azure Portal, aby wyświetlić dane monitorowania.

> [!NOTE]
> Wyświetlanie danych monitorowania w portalu może potrwać kilka minut.


## <a name="configuration-options"></a>Opcje konfiguracji

W `applicationinsights.json` pliku można dodatkowo skonfigurować:

* Nazwa roli w chmurze
* Wystąpienie roli w chmurze
* Próbkowanie
* Metryki JMX
* Wymiary niestandardowe
* Procesory telemetrii (wersja zapoznawcza)
* Automatyczne zbieranie rejestrowania
* Automatycznie zbierane metryki mikrometrów (w tym metryki Spring Boot wydajności)
* Puls
* HTTP Proxy
* Samodzielna diagnostyka

Zobacz [opcje konfiguracji,](./java-standalone-config.md) aby uzyskać szczegółowe informacje.

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Automatycznie zbierane żądania, zależności, dzienniki i metryki

### <a name="requests"></a>Żądania

* Użytkownicy JMS
* Konsumenci platformy Kafka
* Netty/WebFlux
* Servlets
* Planowanie spring

### <a name="dependencies-with-distributed-trace-propagation"></a>Zależności z rozproszoną propagację śledzenia

* Apache HttpClient i HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* Jms
* Kafka
* Klient netty
* OkHttp

### <a name="other-dependencies"></a>Inne zależności

* Cassandra
* JDBC
* MongoDB (asynchroniczne i synchronizowane)
* Redis (Lettuce i Jedis)

### <a name="logs"></a>Dzienniki

* java.util.logging
* Log4j (w tym właściwości MDC)
* SLF4J/Logback (w tym właściwości MDC)

### <a name="metrics"></a>Metryki

* Mikrometry (w tym metryki Spring Boot metryki)
* Metryki JMX

### <a name="azure-sdks-preview"></a>Zestawy Azure SDK (wersja zapoznawcza)

Zobacz opcje [konfiguracji, aby](./java-standalone-config.md#auto-collected-azure-sdk-telemetry-preview) włączyć tę funkcję w wersji zapoznawczej i przechwycić dane telemetryczne emitowane przez te zestawy Azure SDK:

* [App Configuration](https://docs.microsoft.com/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [Cognitive Search](https://docs.microsoft.com/java/api/overview/azure/search-documents-readme) 11.3.0+
* [Communication Chat](https://docs.microsoft.com/java/api/overview/azure/communication-chat-readme) 1.0.0+
* [Komunikacja wspólna](https://docs.microsoft.com/java/api/overview/azure/communication-common-readme) 1.0.0+
* [Communication Identity](https://docs.microsoft.com/java/api/overview/azure/communication-identity-readme) 1.0.0+
* [Komunikacja sms](https://docs.microsoft.com/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Cosmos DB](https://docs.microsoft.com/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Event Grid](https://docs.microsoft.com/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [Event Hubs](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [Event Hubs — Azure Blob Storage Checkpoint Store](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [Rozpoznawanie formularzy](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [Tożsamość](https://docs.microsoft.com/java/api/overview/azure/identity-readme) 1.2.4+
* [Key Vault — certyfikaty](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [Key Vault — klucze](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+
* [Key Vault — wpisy tajne](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [Service Bus](https://docs.microsoft.com/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [analiza tekstu](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "powyższe nazwy i linki zdjętą z https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # "Synchronizacja wersji i zostałaby ręcznie zsynchronizowana z najstarszą wersją oprogramowania Maven Central zbudowaną na platformie azure-core 1.14.0"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "for (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    Kontynuować"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="send-custom-telemetry-from-your-application"></a>Wysyłanie niestandardowych danych telemetrycznych z aplikacji

Naszym celem w wersji 3.0+ jest umożliwienie wysyłania niestandardowych danych telemetrycznych przy użyciu standardowych interfejsów API.

Obsługujemy mikrometry, popularne struktury rejestrowania i Application Insights Java 2.x SDK.
Application Insights Java 3.0 automatycznie przechwytuje dane telemetryczne wysyłane za pośrednictwem tych interfejsów API i koreluje je z automatycznie zebranymi telemetriami.

### <a name="supported-custom-telemetry"></a>Obsługiwane niestandardowe dane telemetryczne

W poniższej tabeli przedstawiono obecnie obsługiwane niestandardowe typy telemetrii, które można włączyć w celu uzupełnienia agenta java 3.0. Podsumowując, metryki niestandardowe są obsługiwane za pośrednictwem mikrometrów, niestandardowe wyjątki i ślady można włączyć za pomocą platform rejestrowania, a każdy typ niestandardowej telemetrii jest obsługiwany za pośrednictwem zestawu [SDK java 2.x](#send-custom-telemetry-using-the-2x-sdk)języka Application Insights.

|                     | Mikrometr | Log4j, logback, JULIA | Zestaw SDK 2.x |
|---------------------|------------|---------------------|---------|
| **Zdarzenia niestandardowe**   |            |                     |  Tak    |
| **Metryki niestandardowe**  |  Tak       |                     |  Tak    |
| **Zależności**    |            |                     |  Tak    |
| **Wyjątki**      |            |  Tak                |  Tak    |
| **Wyświetlenia stron**      |            |                     |  Tak    |
| **Żądania**        |            |                     |  Tak    |
| **Ślady**          |            |  Tak                |  Tak    |

Obecnie nie planujemy wydania zestawu SDK z Application Insights 3.0.

Application Insights java 3.0 już nasłuchuje danych telemetrycznych wysyłanych do zestawu SDK java 2.x Application Insights java 2.x. Ta funkcja jest ważną częścią historii uaktualniania dla istniejących użytkowników wersji 2.x i wypełnia ważną lukę w naszej obsługi telemetrii niestandardowej do momentu, gdy interfejs API OpenTelemetry będzie w wersji gałędowej.

### <a name="send-custom-metrics-using-micrometer"></a>Wysyłanie metryk niestandardowych przy użyciu mikrometru

Dodaj mikrometryk do aplikacji:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Użyj rejestru [globalnego Micrometer,](https://micrometer.io/docs/concepts#_global_registry) aby utworzyć miernik:

```java
static final Counter counter = Metrics.counter("test_counter");
```

i użyj tej funkcji do nagrywania metryk:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Wysyłanie niestandardowych śladów i wyjątków przy użyciu ulubionej struktury rejestrowania

Log4j, Logback i java.util.logging są instrumentowane automatycznie, a rejestrowanie wykonywane za pośrednictwem tych platform rejestrowania jest automatycznie zbierane jako dane telemetryczne śledzenia i wyjątków.

Domyślnie rejestrowanie jest zbierane tylko wtedy, gdy rejestrowanie jest wykonywane na poziomie INFO lub powyżej.
Zobacz opcje [konfiguracji, aby](./java-standalone-config.md#auto-collected-logging) dowiedzieć się, jak zmienić ten poziom.

Jeśli chcesz dołączyć wymiary niestandardowe do dzienników, możesz użyć [log4j 1.2 MDC,](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html) [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)lub [Logback MDC,](http://logback.qos.ch/manual/mdc.html)a środowisko Application Insights Java 3.0 automatycznie przechwyci te właściwości MDC jako wymiary niestandardowe w telemetrii śledzenia i wyjątków.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Wysyłanie niestandardowych danych telemetrycznych przy użyciu zestawu SDK 2.x

Dodaj do aplikacji (wszystkie wersje 2.x są obsługiwane przez środowisko Application Insights Java 3.0, ale jeśli masz wybór, warto użyć `applicationinsights-core-2.6.2.jar` najnowszej wersji):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

Tworzenie klienta TelemetryClient:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

i użyj ich do wysyłania niestandardowych danych telemetrycznych:

##### <a name="events"></a>Zdarzenia

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Metryki

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Zależności

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Dzienniki

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Wyjątki

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Dodawanie wymiarów niestandardowych żądania przy użyciu zestawu SDK 2.x

> [!NOTE]
> Ta funkcja jest dostępna tylko w programie 3.0.2 lub nowszym

Dodaj do aplikacji (wszystkie wersje 2.x są obsługiwane przez środowisko Application Insights Java 3.0, ale jeśli masz wybór, warto użyć `applicationinsights-web-2.6.2.jar` najnowszej wersji):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

i dodaj wymiary niestandardowe w kodzie:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>Ustawianie telemetrii żądania user_Id użyciu zestawu SDK 2.x

> [!NOTE]
> Ta funkcja jest dostępna tylko w programie 3.0.2 lub nowszym

Dodaj do aplikacji (wszystkie wersje 2.x są obsługiwane przez środowisko Application Insights Java 3.0, ale jeśli masz wybór, warto użyć `applicationinsights-web-2.6.2.jar` najnowszej wersji):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

i ustaw `user_Id` wartość w kodzie:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>Zastępowanie nazwy telemetrii żądania przy użyciu zestawu SDK 2.x

> [!NOTE]
> Ta funkcja jest dostępna tylko w programie 3.0.2 lub nowszym

Dodaj do aplikacji (wszystkie wersje 2.x są obsługiwane przez środowisko Application Insights Java 3.0, ale jeśli masz wybór, warto użyć `applicationinsights-web-2.6.2.jar` najnowszej wersji):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

i ustaw nazwę w kodzie:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>Uzyskiwanie identyfikatora telemetrii żądania i identyfikatora operacji przy użyciu zestawu SDK 2.x

> [!NOTE]
> Ta funkcja jest dostępna tylko w programie 3.0.3 lub nowszym

Dodaj do aplikacji (wszystkie wersje 2.x są obsługiwane przez środowisko Application Insights Java 3.0, ale jeśli masz wybór, warto użyć `applicationinsights-web-2.6.2.jar` najnowszej wersji):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

i pobierz identyfikator telemetrii żądania oraz identyfikator operacji w kodzie:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```
