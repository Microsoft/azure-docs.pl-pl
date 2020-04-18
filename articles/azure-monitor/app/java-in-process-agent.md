---
title: Monitorowanie aplikacji Java w dowolnym środowisku — usługa Azure Monitor Application Insights
description: Monitorowanie wydajności aplikacji Java działających w dowolnym środowisku bez instrumentowania aplikacji. Rozproszone śledzenie i mapa aplikacji.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 5706d5a74bd6850a237f7418b1a86a8e9c7762e1
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604575"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Aplikacja bezkodowa Java monitorująca usługę Azure Monitor Application Insights — publiczna wersja zapoznawcza

Monitorowanie aplikacji bezkodowych Java polega na prostocie - nie ma żadnych zmian w kodzie, agent Java można włączyć za pomocą zaledwie kilku zmian konfiguracji.

 Agent Java działa w każdym środowisku i umożliwia monitorowanie wszystkich aplikacji Java. Innymi słowy, niezależnie od tego, czy używasz aplikacji Java na maszynach wirtualnych, lokalnie, w aks, w systemie Windows, Linux - nazwa, agent Java 3.0 będzie monitorować aplikację.

Dodawanie SDK Java usługi Application Insights do aplikacji nie jest już wymagane, ponieważ agent 3.0 automatycznie zleca żądania, zależności i dzienniki samodzielnie.

Nadal można wysyłać niestandardowe dane telemetryczne z aplikacji. Agent 3.0 będzie śledzić i skorelować go wraz ze wszystkimi automatycznie chwytowane dane telemetryczne.

## <a name="quickstart"></a>Szybki start

**1. Pobierz agenta**

Pobierz [aplikacjęinsights-agent-3.0.0-PREVIEW.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.3/applicationinsights-agent-3.0.0-PREVIEW.3.jar)

**2. Skieruj JVM na agenta**

Dodaj `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.3.jar` do argumentów JVM aplikacji

Typowe args JVM obejmują `-Xmx512m` i `-XX:+UseG1GC`. Więc jeśli wiesz, gdzie je dodać, to już wiesz, gdzie to dodać.

Aby uzyskać dodatkową pomoc dotyczącą konfigurowania argumentów JVM aplikacji, zobacz [3.0 Preview: Porady dotyczące aktualizowania argumentów JVM](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args).

**3. Wskaż agenta do zasobu usługi Application Insights**

Jeśli nie masz jeszcze zasobu usługi Application Insights, możesz utworzyć nowy, wykonując kroki opisane w [przewodniku tworzenia zasobów](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

Wskaż agenta do zasobu usługi Application Insights, ustawiając zmienną środowiskową:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Lub przez utworzenie pliku `ApplicationInsights.json`konfiguracyjnego o nazwie i `applicationinsights-agent-3.0.0-PREVIEW.3.jar`umieszczenie go w tym samym katalogu co , z następującą zawartością:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Parametry połączenia można znaleźć w zasobie usługi Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Ciąg połączenia usługi Application Insights":::

**4. To wszystko!**

Teraz uruchom aplikację i przejdź do zasobu usługi Application Insights w witrynie Azure portal, aby wyświetlić dane monitorowania.

> [!NOTE]
> Może upłynąć kilka minut, aby dane monitorowania pojawiły się w portalu.


## <a name="configuration-options"></a>Opcje konfiguracji

W `ApplicationInsights.json` pliku można dodatkowo skonfigurować:

* Nazwa roli w chmurze
* Wystąpienie roli w chmurze
* Przechwytywanie dziennika aplikacji
* Metryki JMX
* Mikrometr
* Puls
* Próbkowanie
* HTTP Proxy
* Diagnostyka własna

Zobacz szczegóły w [3.0 Public Preview: Opcje konfiguracji](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Automatycznie śkolone żądania, zależności, dzienniki i dane

### <a name="requests"></a>Żądania

* Konsumenci JMS
* Konsumenci platformy Kafka
* Netty/WebFlux
* Servlets
* Wiosenne planowanie

### <a name="dependencies-with-distributed-trace-propagation"></a>Zależności z rozproszoną propagacją śledzenia

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
* MongoDB (asynchron i synchronizacja)
* Redis (Sałata i Jedis)

### <a name="logs"></a>Dzienniki

* java.util.logging
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>Metryki

* Mikrometr
* Wskaźniki JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Wysyłanie niestandardowych danych telemetrycznych z aplikacji

Naszym celem w 3.0+ jest umożliwienie wysyłania niestandardowych danych telemetrycznych przy użyciu standardowych interfejsów API.

Obsługujemy Micrometer, OpenTelemetry API i popularnych platform rejestrowania. Aplikacja Application Insights Java 3.0 automatycznie przechwytuje dane telemetryczne i skoreluje ją wraz ze wszystkimi automatycznie ślikwidowanymi daneami telemetrycznymi.

Z tego powodu nie planujemy wydania SDK z application insights 3.0 w tej chwili.

Aplikacja Application Insights Java 3.0 już nasłuchuje danych telemetrycznych wysyłanych do sdk Java 2.x usługi Application Insights. Ta funkcja jest ważną częścią wątku uaktualniania dla istniejących użytkowników 2.x i wypełnia ważną lukę w naszej niestandardowej obsłudze telemetrii, dopóki interfejs API OpenTelemetry jest GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Wysyłanie niestandardowych danych telemetrycznych przy użyciu usługi Application Insights Java SDK 2.x

Dodaj `applicationinsights-core-2.6.0.jar` do aplikacji (wszystkie wersje 2.x są obsługiwane przez application insights Java 3.0, ale warto użyć najnowszych, jeśli masz wybór):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Utwórz telemetrię:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

i użyj go do wysyłania niestandardowych danych telemetrycznych.

### <a name="events"></a>Zdarzenia

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Metryki

Dane telemetryczne można wysyłać za pośrednictwem [mikrometru:](https://micrometer.io)

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Możesz też użyć aplikacji Application Insights Java SDK 2.x:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Zależności

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

### <a name="logs"></a>Dzienniki
Można wysłać dane telemetryczne dziennika niestandardowego za pośrednictwem ulubionej struktury rejestrowania.

Możesz też użyć aplikacji Application Insights Java SDK 2.x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Wyjątki
Można wysłać dane telemetryczne wyjątków niestandardowych za pośrednictwem ulubionej struktury rejestrowania.

Możesz też użyć aplikacji Application Insights Java SDK 2.x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Uaktualnianie z usługi Application Insights Java SDK 2.x

Jeśli używasz już aplikacji Application Insights Java SDK 2.x w aplikacji, nie ma potrzeby, aby go usunąć. Agent Java 3.0 wykryje go, przechwytuje i skoreluje wszelkie niestandardowe dane telemetryczne wysyłane za pośrednictwem java SDK 2.x, pomijając jednocześnie wszelkie autokolekcje wykonywane przez java SDK 2.x, aby zapobiec przechwytywaniu duplikatów.

> [!NOTE]
> Uwaga: Java SDK 2.x TelemetryInilizatory i telemetryProcesory nie będą uruchamiane podczas korzystania z agenta 3.0.
