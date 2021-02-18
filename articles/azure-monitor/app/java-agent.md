---
title: Monitorowanie wydajności aplikacji sieci Web Java — Application Insights platformy Azure
description: Rozszerzone monitorowanie wydajności i użycia witryny sieci Web w języku Java za pomocą Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: c753e4e254890f9198da9bc913b29bdaae335b78
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573840"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorowanie zależności, przechwycone wyjątki i czasy wykonywania metod w aplikacjach sieci Web Java

> [!IMPORTANT]
> Nie zaleca się już podejścia opisanego w tym dokumencie.
>
> Zalecanym podejściem do monitorowania aplikacji Java jest użycie autoinstrumentacji bez zmiany kodu. Postępuj zgodnie z wytycznymi dla [Application Insights Java 3,0 Agent](./java-in-process-agent.md).

Jeśli masz [instrumentację aplikacji sieci Web w języku Java za pomocą zestawu SDK Application Insights][java], możesz użyć agenta Java, aby uzyskać dokładniejszy wgląd, bez wprowadzania żadnych zmian w kodzie:

* **Zależności:** Dane dotyczące wywołań, które aplikacja wykonuje w innych składnikach, w tym:
  * **Wychodzące wywołania http** wykonywane za pośrednictwem Apache HTTPClient, OkHttp i `java.net.HttpURLConnection` są przechwytywane.
  * Przechwycono **wywołania Redis** wykonywane za pośrednictwem klienta Jedis.
  * **JDBC zapytania** — dla obiektów MySQL i PostgreSQL, jeśli wywołanie trwa dłużej niż 10 sekund, Agent zgłosi plan zapytania.

* **Rejestrowanie aplikacji:** Przechwytywanie i skorelowanie dzienników aplikacji przy użyciu żądań HTTP i innych danych telemetrycznych
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Lepsza nazwa operacji:** (używana do agregowania żądań w portalu)
  *  Na podstawie `@RequestMapping` .
  * **Jax-RS** — na podstawie `@Path` . 

Aby korzystać z agenta Java, należy zainstalować go na serwerze. Aplikacje sieci Web muszą być Instrumentacją [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Zainstaluj agenta Application Insights dla środowiska Java
1. Na maszynie, na której działa serwer Java, [Pobierz agenta 2. x](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/2.6.2). Upewnij się, że używana wersja agenta Java programu 2. x jest zgodna z wersją zestawu SDK 2. x Application Insights używanym przez program.
2. Edytuj skrypt uruchamiania serwera aplikacji i Dodaj następujący argument JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Na przykład w Tomcat na komputerze z systemem Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Uruchom ponownie serwer aplikacji.

## <a name="configure-the-agent"></a>Konfigurowanie agenta
Utwórz plik o nazwie `AI-Agent.xml` i umieść go w tym samym folderze, w którym znajduje się plik JAR agenta.

Ustaw zawartość pliku XML. Edytuj Poniższy przykład, aby dołączyć lub pominąć wybrane funkcje.

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

## <a name="additional-config-spring-boot"></a>Dodatkowa konfiguracja (sprężynowy rozruch)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

W przypadku usługi Azure App Services wykonaj następujące czynności:

* Wybierz kolejno pozycje Ustawienia > Ustawienia aplikacji
* W obszarze Ustawienia aplikacji dodaj nową parę klucz-wartość:

Klucz: `JAVA_OPTS` wartość: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.6.2.jar`

Agent musi być spakowany jako zasób w projekcie, w taki sposób, aby kończył się w sieci D:/Home/site/wwwroot/katalogu. Można potwierdzić, że Agent znajduje się w poprawnym katalogu App Service, przechodząc do konsoli debugowania narzędzia **deweloperskie narzędzia programistyczne**  >    >   i sprawdzając zawartość katalogu witryn.    

* Zapisz ustawienia i ponownie uruchom aplikację. (Te kroki dotyczą tylko App Services uruchomionych w systemie Windows).

> [!NOTE]
> AI-Agent.xml, a plik JAR agenta powinien znajdować się w tym samym folderze. Są one często umieszczane w `/resources` folderze projektu.  

#### <a name="enable-w3c-distributed-tracing"></a>Włącz śledzenie rozproszone W3C

Dodaj następujące elementy do AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Tryb zgodności z poprzednimi wersjami jest domyślnie włączony, a parametr enableW3CBackCompat jest opcjonalny i powinien być używany tylko wtedy, gdy chcesz go wyłączyć. 

W idealnym przypadku, gdy wszystkie usługi zostały zaktualizowane do nowszej wersji zestawów SDK obsługujących protokół W3C. Zdecydowanie zaleca się przejście do nowszej wersji zestawów SDK z obsługą W3C tak szybko, jak to możliwe.

Upewnij się, że **konfiguracje [przychodzące](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) i wychodzące (Agent)** są dokładnie takie same.

## <a name="view-the-data"></a>Wyświetlanie danych
W zasobie Application Insights zagregowane czasy wykonywania zdalnych zależności i metod są wyświetlane [w obszarze kafelka wydajność][metrics].

Aby wyszukać poszczególne wystąpienia zależności, wyjątków i raportów metod, Otwórz [Wyszukiwanie][diagnostic].

[Diagnozowanie problemów z zależnościami — Dowiedz się więcej](./asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Masz pytania? Problemy?
* Brak danych? [Ustawianie wyjątków zapory](./ip-addresses.md)
* [Rozwiązywanie problemów z technologią Java](java-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md

