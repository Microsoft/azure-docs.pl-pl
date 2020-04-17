---
title: Monitorowanie wydajności aplikacji sieci Web w języku Java — usługa Azure Application Insights
description: Rozszerzone monitorowanie wydajności i użycia witryny Java za pomocą usługi Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: b047a8dd8c67679a5cc8a45e8be82f9ab5227aa4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537546"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorowanie zależności, przechwyconych wyjątków i czasów wykonywania metody w aplikacjach java web


Jeśli aplikacja internetowa Java została [zaarządowana za pomocą aplikacji Application Insights,][java]możesz użyć agenta Java, aby uzyskać głębsze informacje, bez żadnych zmian w kodzie:

* **Zależności:** Dane dotyczące wywołań, które aplikacja wykonuje do innych składników, w tym:
  * **Wychodzące wywołania HTTP** wykonane za pośrednictwem Apache HttpClient, OkHttp i `java.net.HttpURLConnection` są przechwytywane.
  * **Połączenia Redis** wykonane za pośrednictwem klienta Jedis są przechwytywane.
  * **Zapytania JDBC** — dla MySQL i PostgreSQL, jeśli wywołanie trwa dłużej niż 10 sekund, agent zgłasza plan kwerend.

* **Rejestrowanie aplikacji:** Przechwytywanie i korelowanie dzienników aplikacji z żądaniami HTTP i innymi datami telemetrycznymi
  * **Log4j 1.2**
  * **Log4j2 ( Log4j2 )**
  * **Logowanie**

* **Lepsze nazewnictwo operacji:** (używane do agregowania żądań w portalu)
  * **Wiosna** - `@RequestMapping`na podstawie .
  * **JAX-RS** - `@Path`na podstawie . 

Aby użyć agenta Java, należy go zainstalować na serwerze. Aplikacje internetowe muszą być wyposażone w [zestaw Java SDK aplikacji Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalowanie agenta usługi Application Insights dla środowiska Java
1. Na maszynie z uruchomionym serwerem Java [pobierz agenta](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Upewnij się, że pobierasz tę samą wersję agenta Java co wersja pakietów podstawowego i internetowego zestawu Java SDK usługi Application Insights.
2. Edytuj skrypt startowy serwera aplikacji i dodaj następujący argument JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Na przykład w Tomcat na komputerze z systemem Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Uruchom ponownie serwer aplikacji.

## <a name="configure-the-agent"></a>Konfigurowanie agenta
Utwórz plik `AI-Agent.xml` o nazwie i umieść go w tym samym folderze co agent JAR.

Ustaw zawartość pliku xml. Edytuj poniższy przykład, aby uwzględnić lub pominąć żądane funkcje.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Dodatkowa config (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

W przypadku usług Azure App Services wykonaj następujące czynności:

* Wybierz kolejno pozycje Ustawienia > Ustawienia aplikacji
* W obszarze Ustawienia aplikacji dodaj nową parę klucz-wartość:

Klucz: `JAVA_OPTS` Wartość:`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Aby uzyskać najnowszą wersję agenta Java, sprawdź wersje [tutaj](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Agent musi być spakowany jako zasób w projekcie w taki sposób, aby kończył się w katalogu D:/home/site/wwwroot/. Możesz potwierdzić, że agent znajduje się w katalogu usługi App Service, przechodząc do**konsoli debugowania** narzędzi **programistycznych** > **i** > sprawdzając zawartość katalogu lokacji.    

* Zapisz ustawienia i ponownie uruchom aplikację. (Te kroki dotyczą tylko usług App Services działających w systemie Windows).

> [!NOTE]
> Plik AI-Agent.xml i plik jar agenta powinny znajdować się w tym samym folderze. Są one często umieszczane razem w folderze `/resources` projektu.  

#### <a name="enable-w3c-distributed-tracing"></a>Włącz śledzenie rozproszone W3C

Dodaj następujące elementy do pliku AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Tryb zgodności z przeszywem jest domyślnie włączony, a parametr enableW3CBackCompat jest opcjonalny i powinien być używany tylko wtedy, gdy chcesz go wyłączyć. 

Najlepiej byłoby to w przypadku, gdy wszystkie usługi zostały zaktualizowane do nowszej wersji SDK obsługujących protokół W3C. Zdecydowanie zaleca się jak najszybsze przejście do nowszej wersji sdk z obsługą W3C.

Upewnij się, że **konfiguracje [przychodzące](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) i wychodzące (agent)** są dokładnie takie same.

## <a name="view-the-data"></a>Wyświetlanie danych
W zasobie Usługi Application Insights zagregowane zdalne zależności i czasy wykonywania metody są wyświetlane [w kafelku Wydajność][metrics].

Aby wyszukać poszczególne wystąpienia raportów zależności, wyjątków i metod, otwórz [pole wyszukaj][diagnostic].

[Diagnozowanie problemów z zależnościami - dowiedz się więcej](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Pytania? Problemy?
* Brak danych? [Ustawianie wyjątków zapory](../../azure-monitor/app/ip-addresses.md)
* [Rozwiązywanie problemów z technologią Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
