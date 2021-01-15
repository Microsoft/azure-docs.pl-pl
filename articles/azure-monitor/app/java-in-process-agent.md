---
title: Azure Monitor Application Insights Java
description: Monitorowanie wydajności aplikacji dla aplikacji Java działających w dowolnym środowisku bez konieczności modyfikacji kodu. Śledzenie rozproszone i mapa aplikacji.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 32d906bf96a0ad5cf798f68bf83f2d6af1064361
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231744"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Monitorowanie aplikacji bezkodu Java Azure Monitor Application Insights

Monitorowanie aplikacji bez kodu Java ma wszystkie informacje o prostotie — nie ma żadnych zmian w kodzie, a agent Java można włączyć za pomocą tylko kilku zmian konfiguracji.

 Agent Java działa w dowolnym środowisku i umożliwia monitorowanie wszystkich aplikacji Java. Innymi słowy, niezależnie od tego, czy aplikacje języka Java są uruchamiane na maszynach wirtualnych, lokalnie, w AKS, w systemie Windows, Linux — nazwa użytkownika, Agent Java 3,0 będzie monitorować aplikację.

Dodawanie Application Insights Java SDK do aplikacji nie jest już wymagane, ponieważ agent 3,0 automatycznie zbiera żądania, zależności i dzienniki.

Nadal możesz wysyłać niestandardowe dane telemetryczne z aplikacji. Agent 3,0 będzie śledził i skorelowany wraz ze wszystkimi danymi telemetrycznymi, które są zbierane.

Agent 3,0 obsługuje środowisko Java 8 i nowsze.

## <a name="quickstart"></a>Szybki start

**1. Pobierz agenta**

> [!WARNING]
> **W przypadku uaktualniania programu z wersji zapoznawczej 3,0**
>
> Uważnie Przejrzyj wszystkie [Opcje konfiguracji](./java-standalone-config.md) , ponieważ struktura JSON została całkowicie zmieniona, oprócz samej nazwy pliku, która wystąpiła tylko małymi literami.

Pobierz plik [ApplicationInsights-Agent-3.0.1. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.1/applicationinsights-agent-3.0.1.jar)

**2. wskaż JVM do agenta**

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.1.jar` do ARGUMENTÓW JVM aplikacji

Typowe argumenty JVM obejmują `-Xmx512m` i `-XX:+UseG1GC` . Jeśli wiesz, gdzie je dodać, już wiesz, gdzie je dodać.

Aby uzyskać dodatkową pomoc dotyczącą konfigurowania argumentów JVM aplikacji, zobacz [porady dotyczące aktualizowania ARGUMENTÓW JVM](./java-standalone-arguments.md).

**3. wskaż agenta Application Insights zasobem**

Jeśli nie masz jeszcze zasobu Application Insights, możesz utworzyć nowy, wykonując czynności opisane w [przewodniku tworzenia zasobów](./create-new-resource.md).

Wskaż agenta Application Insights zasobem, ustawiając zmienną środowiskową:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Lub tworząc plik konfiguracji o nazwie `applicationinsights.json` i umieszczając go w tym samym katalogu, co `applicationinsights-agent-3.0.1.jar` , z następującą zawartością:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Parametry połączenia można znaleźć w zasobie Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights parametry połączenia":::

**4. to wszystko!**

Teraz uruchom aplikację i przejdź do zasobu Application Insights w Azure Portal, aby wyświetlić dane monitorowania.

> [!NOTE]
> Wyświetlenie danych monitorowania w portalu może potrwać kilka minut.


## <a name="configuration-options"></a>Opcje konfiguracji

W `applicationinsights.json` pliku można dodatkowo skonfigurować:

* Nazwa roli w chmurze
* Wystąpienie roli w chmurze
* Próbkowanie
* Metryki JMX
* Wymiary niestandardowe
* Procesory telemetrii (wersja zapoznawcza)
* Rejestrowanie z autozbieraniem
* Zbierane metryki Micrometer (w tym metryki uruchamiającego rozruch z sprężyną)
* Puls
* Serwer proxy HTTP
* Samodiagnostyka

Aby uzyskać szczegółowe informacje, zobacz [Opcje konfiguracji](./java-standalone-config.md) .

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Aplikacje zbierane z autogromadzeniem, zależności, dzienniki i metryki

### <a name="requests"></a>Żądania

* JMS konsumenci
* Kafka konsumenci
* Sieci i strumień sieci
* Serwletów
* Planowanie wiosny

### <a name="dependencies-with-distributed-trace-propagation"></a>Zależności z propagacją rozproszonego śledzenia

* Apache HttpClient i HttpAsyncClient
* gRPC
* Java. NET. HttpURLConnection
* JMS
* Kafka
* Klient z sieciami
* OkHttp

### <a name="other-dependencies"></a>Inne zależności

* Cassandra
* JDBC
* MongoDB (asynchroniczne i synchroniczne)
* Redis (sałaty i Jedis)

### <a name="logs"></a>Dzienniki

* Java. util. Logging
* Log4J (łącznie z właściwościami MDC)
* SLF4J/Logback (łącznie z właściwościami MDC)

### <a name="metrics"></a>Metryki

* Micrometer (w tym metryki uruchamiającego uruchamianie sprężynowe)
* Metryki JMX

## <a name="send-custom-telemetry-from-your-application"></a>Wyślij niestandardową telemetrię z aplikacji

Naszym celem w programie 3.0 + jest umożliwienie wysyłania niestandardowych danych telemetrycznych przy użyciu standardowych interfejsów API.

Obsługujemy Micrometer, popularne struktury rejestrowania oraz zestaw Application Insights Java 2. x.
Application Insights Java 3,0 automatycznie przechwytuje dane telemetryczne wysyłane za pomocą tych interfejsów API i skorelowane z automatycznie zebraną telemetrię.

### <a name="supported-custom-telemetry"></a>Obsługiwana niestandardowa Telemetria

Poniższa tabela przedstawia obecnie obsługiwane typy niestandardowych danych telemetrycznych, które można włączyć, aby uzupełnić agenta Java 3,0. Podsumowując, metryki niestandardowe są obsługiwane za pomocą micrometer, niestandardowe wyjątki i ślady mogą być włączane za pomocą platform rejestrowania, a dowolny typ telemetrii niestandardowej jest obsługiwany za pomocą [Application Insights Java 2. x SDK](#send-custom-telemetry-using-the-2x-sdk).

|                     | Mikrometr | Log4J, logback, lip | zestaw SDK 2. x |
|---------------------|------------|---------------------|---------|
| **Zdarzenia niestandardowe**   |            |                     |  Tak    |
| **Metryki niestandardowe**  |  Tak       |                     |  Tak    |
| **Zależności**    |            |                     |  Tak    |
| **Wyjątki**      |            |  Tak                |  Tak    |
| **Wyświetlenia stron**      |            |                     |  Tak    |
| **Żądania**        |            |                     |  Tak    |
| **Ślady**          |            |  Tak                |  Tak    |

W tej chwili nie planujemy zwolnić zestawu SDK z Application Insights 3,0.

Application Insights Java 3,0 już nasłuchuje na danych telemetrycznych wysyłanych do Application Insights Java 2. x SDK. Ta funkcja jest ważną częścią wątku uaktualnienia dla istniejących użytkowników w wersji 2. x i pełni ważną lukę w naszej niestandardowej pomocy technicznej telemetrii do momentu, w którym interfejs API OpenTelemetry jest w całości.

### <a name="send-custom-metrics-using-micrometer"></a>Wysyłanie metryk niestandardowych przy użyciu Micrometer

Dodaj Micrometer do aplikacji:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Użyj [rejestru globalnego](https://micrometer.io/docs/concepts#_global_registry) Micrometer do utworzenia miernika:

```java
static final Counter counter = Metrics.counter("test_counter");
```

i Użyj tego do rejestrowania metryk:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Wysyłanie niestandardowych śladów i wyjątków przy użyciu ulubionej struktury rejestrowania

Log4J, Logback i Java. util. Logging są autoinstrumentami, a rejestrowanie wykonywane za pośrednictwem tych platform rejestrowania jest zbierane w sposób autozbierany jako dane telemetryczne śledzenia i wyjątków.

Domyślnie rejestrowanie jest zbierane tylko wtedy, gdy rejestrowanie odbywa się na poziomie informacji lub wyższym.
Zobacz [Opcje konfiguracji](./java-standalone-config.md#auto-collected-logging) dotyczące zmiany tego poziomu.

Jeśli chcesz dołączyć niestandardowe wymiary do dzienników, możesz użyć [Log4j 1,2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4J 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)lub [Logback MDC](http://logback.qos.ch/manual/mdc.html), a Application Insights Java 3,0 automatycznie przechwytuje te właściwości MDC jako wymiary niestandardowe na danych telemetrycznych śledzenia i wyjątków.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Wyślij niestandardową telemetrię przy użyciu zestawu SDK 2. x

Dodaj `applicationinsights-core-2.6.2.jar` do swojej aplikacji (wszystkie wersje 2. x są obsługiwane przez Application Insights Java 3,0, ale warto użyć najnowszej wersji, jeśli masz wybór):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

Utwórz TelemetryClient:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

i Użyj, aby wysłać niestandardową telemetrię:

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

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Dodawanie niestandardowych wymiarów żądania przy użyciu zestawu SDK 2. x

> [!NOTE]
> Ta funkcja jest tylko w 3.0.1 i nowszych

Dodaj `applicationinsights-web-2.6.2.jar` do swojej aplikacji (wszystkie wersje 2. x są obsługiwane przez Application Insights Java 3,0, ale warto użyć najnowszej wersji, jeśli masz wybór):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

i Dodaj niestandardowe wymiary w kodzie:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>Ustawianie user_Id danych telemetrycznych żądania przy użyciu zestawu SDK 2. x

> [!NOTE]
> Ta funkcja jest tylko w 3.0.1 i nowszych

Dodaj `applicationinsights-web-2.6.2.jar` do swojej aplikacji (wszystkie wersje 2. x są obsługiwane przez Application Insights Java 3,0, ale warto użyć najnowszej wersji, jeśli masz wybór):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

i ustaw `user_Id` w kodzie:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>Zastępowanie nazwy telemetrii żądania przy użyciu zestawu SDK 2. x

> [!NOTE]
> Ta funkcja jest tylko w 3.0.1 i nowszych

Dodaj `applicationinsights-web-2.6.2.jar` do swojej aplikacji (wszystkie wersje 2. x są obsługiwane przez Application Insights Java 3,0, ale warto użyć najnowszej wersji, jeśli masz wybór):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

i Ustaw nazwę w kodzie:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

> [!NOTE]
> Wszystkie inne operacje `RequestTelemetry` pobrane z programu `ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry()` poza tymi opisanymi powyżej będą działać szybko i zgłosić wyjątek informujący o tym, że jest to niezdefiniowane zachowanie w ramach agenta 3,0.
>
> Jeśli potrzebujesz współdziałania z innymi metodami, `RequestTelemetry` skontaktuj się z nami, otwierając problem https://github.com/microsoft/ApplicationInsights-Java/issues .
