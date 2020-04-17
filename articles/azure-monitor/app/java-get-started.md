---
title: 'Szybki start: analiza aplikacji java w sieci Web za pomocą usługi Azure Application Insights'
description: 'Monitorowanie wydajności aplikacji internetowych w języku Java za pomocą usługi Application Insights. '
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: e56ba304d197984110de5127a0f163ac0accf1aa
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537512"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Szybki start: wprowadzenie do usługi Application Insights w projekcie sieci Web Java

W tym przewodniku Szybki start można użyć usługi Application Insights do automatycznego instruowania żądania, śledzenia zależności i zbierania liczników wydajności, diagnozowania problemów z wydajnością i wyjątków oraz pisania kodu w celu śledzenia, co użytkownicy robią z aplikacją.

Application Insights jest rozszerzalną usługą analizy dla deweloperów sieci Web, która ułatwia zrozumienie wydajności i użycia aktywnej aplikacji. Usługa Application Insights obsługuje aplikacje w języku Java działające w systemach Linux, Unix lub Windows.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Działająca aplikacja Java.

## <a name="get-an-application-insights-instrumentation-key"></a>Uzyskiwanie klucza instrumentacji usługi Application Insights

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W witrynie Azure Portalutwórz zasób usługi Application Insights. Jako typ aplikacji ustaw wartość Aplikacja internetowa Java.

3. Znajdź klucz instrumentacji nowego zasobu. Wkrótce będzie trzeba wkleić ten klucz do projektu kodu.

    ![W opisie nowego zasobu kliknij opcję Właściwości i skopiuj klucz instrumentacji](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Dodawanie zestawu SDK usługi Application Insights dla środowiska Java do projektu

*Wybierz typ projektu.*

# <a name="maven"></a>[Maven](#tab/maven)

Jeśli projekt jest już skonfigurowany do używania maven do kompilacji, scal następujący kod do pliku *pom.xml.*

Następnie odśwież zależności projektu, aby pliki binarne zostały pobrane.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Jeśli projekt jest już skonfigurowany do używania Gradle for build, scal następujący kod z plikiem *build.gradle.*

Następnie odśwież zależności projektu, aby pliki binarne zostały pobrane.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[Inne typy](#tab/other)

Pobierz [najnowszą wersję](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) i skopiuj niezbędne pliki do projektu, zastępując wszystkie poprzednie wersje.

---

### <a name="questions"></a>Pytania
* *Jaki jest związek między `-web-auto` `-web` składnikiem , a `-core` komponentami?*
  * `applicationinsights-web-auto`udostępnia metryki, które śledzą liczbę żądań serwera HTTP i czasy odpowiedzi, automatycznie rejestrując filtr serwletu usługi Application Insights w czasie wykonywania.
  * `applicationinsights-web`udostępnia również metryki, które śledzą liczbę żądań serwera HTTP i czasy odpowiedzi, ale wymaga ręcznej rejestracji filtru serwletu usługi Application Insights w aplikacji.
  * `applicationinsights-core`daje tylko gołe interfejsu API, na przykład, jeśli aplikacja nie jest oparta na serwlet.
  
* *Jak zaktualizować zestaw SDK do najnowszej wersji?*
  * Jeśli używasz Gradle lub Maven ...
    * Zaktualizuj plik kompilacji, aby określić najnowszą wersję.
  * Jeśli ręcznie zarządzasz zależnościami...
    * Pobierz najnowszy [Zestaw SDK usługi Application Insights dla środowiska Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) i zastąp nim stary. Zmiany są opisane w [informacjach o wersji zestawu SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="add-an-applicationinsightsxml-file"></a>Dodawanie pliku *ApplicationInsights.xml*
Dodaj *ApplicationInsights.xml* do folderu zasobów w projekcie lub upewnij się, że został dodany do ścieżki klasy wdrażania projektu. Skopiuj do niego następujący kod XML.

Zastąp klucz instrumentacji na ten, który został eś wyposażony w witrynę Azure portal.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Opcjonalnie plik konfiguracji może znajdować się w dowolnej lokalizacji dostępnej dla aplikacji.  Właściwość `-Dapplicationinsights.configurationDirectory` systemowa określa katalog zawierający *plik ApplicationInsights.xml*. Na przykład plik konfiguracji znajdujący się w lokalizacji `E:\myconfigs\appinsights\ApplicationInsights.xml` można skonfigurować za pomocą właściwości `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* Klucz instrumentacji jest wysyłany wraz z każdym elementem telemetrii i dzięki temu te elementy mogą być wyświetlane dla odpowiedniego zasobu usługi Application Insights.
* Składnik żądań HTTP jest opcjonalny. Powoduje automatyczne wysyłanie telemetrii dotyczącej żądań i czasów odpowiedzi do portalu.
* Korelacja zdarzenia jest dodatkiem do składnika żądań HTTP. Przypisuje identyfikator do każdego żądania odebranego przez serwer. Następnie dodaje ten identyfikator jako właściwość do każdego elementu telemetrii jako właściwości "Operation.Id". Umożliwia korelowanie telemetrii skojarzonej z każdym żądaniem przez ustawienie filtru w [wyszukiwaniu diagnostycznym][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Alternatywne sposoby ustawienia klucza instrumentacji
Zestaw SDK usługi Application Insights szuka klucza w następującej kolejności:

1. Właściwość systemu: -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. Zmienna środowiskowa: APPINSIGHTS_INSTRUMENTATIONKEY
3. Plik konfiguracji: *ApplicationInsights.xml*

Możesz również [ustawić klucz w kodzie](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Dodaj agenta

[Zainstaluj Agenta Java](java-agent.md) do przechwytywania wychodzących wywołań HTTP, zapytań JDBC, rejestrowania aplikacji i lepszego nazewnictwa operacji.

## <a name="run-your-application"></a>Uruchamianie aplikacji
Uruchom aplikację w trybie debugowania na komputerze deweloperskim albo opublikuj na serwerze.

## <a name="view-your-telemetry-in-application-insights"></a>Wyświetlanie telemetrii w usłudze Application Insights
Wróć do zasobu usługi Application Insights w witrynie [Microsoft Azure Portal](https://portal.azure.com).

W bloku przeglądu zostaną wyświetlone dane żądań HTTP. (Jeśli ich tam nie ma, odczekaj kilka sekund, a następnie kliknij przycisk Odśwież).

![Zrzut ekranu przedstawiający przykładowe dane dotyczące przeglądu](./media/java-get-started/overview-graphs.png)

[Dowiedz się więcej o metrykach.][metrics]

Klikaj elementy wykresów, aby wyświetlać bardziej szczegółowe metryki zagregowane.

![Okienko błędów usługi Application Insights z wykresami](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Dane wystąpienia
Kliknij określony typ żądania, aby wyświetlić poszczególne wystąpienia.

![Przechodzenie do szczegółów w określonym widoku próbki](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analiza: zaawansowany język zapytań
W miarę zgromadzenia większej ilości danych można uruchamiać zapytania zarówno w celu agregowania danych, jak i w celu znajdowania poszczególnych wystąpień.  [Analiza](../../azure-monitor/app/analytics.md) jest zaawansowanym narzędziem, którego można używać zarówno w celu poznania wydajności i użycia, jak i do celów diagnostycznych.

![Przykład analizy](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Instalowanie aplikacji na serwerze
Teraz opublikuj aplikację na serwerze, pozwól z niej korzystać innym osobom, a następnie obejrzyj telemetrię wyświetlaną w portalu.

* Upewnij się, że zapora pozwala aplikacji na wysłanie telemetrii do tych portów:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Jeśli ruch wychodzący ma być kierowany przez zaporę, zdefiniuj właściwości systemu `http.proxyHost` i `http.proxyPort`.

* Na serwerach systemu Windows zainstaluj:

  * [Pakiet Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    Ten składnik umożliwia działanie liczników wydajności.

## <a name="azure-app-service-config-spring-boot"></a>Usługa Azure App Service config (wiosenny rozruch)

Aplikacje Spring Boot uruchomione w systemie Windows wymagają dodatkowej konfiguracji do uruchomienia w usługach Azure App Services. Zmodyfikuj **plik web.config** i dodaj następującą konfigurację:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Wyjątki i błędy żądań
Nieobsłużone wyjątki i błędy żądań są automatycznie zbierane przez filtr sieci web usługi Application Insights.

Aby zbierać dane dotyczące innych wyjątków, można [wstawić wywołania trackException() w kodzie][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorowanie wywołań metod i zależności zewnętrznych
[Zainstaluj agenta programu Java](java-agent.md) w celu rejestrowania określonych metod wewnętrznych i wywołań za pośrednictwem JDBC z danymi chronometrażu.

I do automatycznego nazewnictwa operacji.

## <a name="w3c-distributed-tracing"></a>Śledzenie rozproszone W3C

Zestaw Java SDK usługi Application Insights obsługuje teraz [śledzenie rozproszone W3C.](https://w3c.github.io/trace-context/)

Przychodzące konfiguracji SDK jest wyjaśnione w dalszej części naszego artykułu na [korelacji](correlation.md).

Wychodząca konfiguracja SDK jest zdefiniowana w pliku [AI-Agent.xml.](java-agent.md)

## <a name="performance-counters"></a>Liczniki wydajności
Otwórz **badanie**, **Metryki**, aby wyświetlić zakres liczników wydajności.

![Zrzut ekranu przedstawiający okienko metryki z zaznaczonymi bajtami prywatnymi procesami](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Dostosowywanie zbierania danych liczników wydajności
Aby wyłączyć zbieranie standardowych liczników wydajności, dodaj następujący kod w węźle głównym pliku *ApplicationInsights.xml:*

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Zbieranie danych dodatkowych liczników wydajności
Możesz określić dodatkowe liczniki wydajności do zbierania danych.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Liczniki JMX (udostępniane przez maszynę wirtualną Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` — nazwa wyświetlana w portalu Application Insights.
* `objectName` — nazwa obiektu JMX.
* `attribute` — atrybut nazwy obiektu JMX do pobrania.
* `type`(opcjonalnie) - Typ atrybutu obiektu JMX:
  * wartość domyślna: typ prosty, np. int lub long.
  * `composite`: dane licznika wydajności są w formacie „Atrybut.Dane”
  * `tabular`: dane licznika wydajności są w formacie wiersza tabeli

#### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows
Każdy [licznik wydajności systemu Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) jest składową kategorii (w taki sam sposób, w jaki pole jest składową klasy). Kategorie mogą być globalne lub mogą mieć wystąpienia numerowane lub nazwane.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName — nazwa wyświetlana w portalu Application Insights.
* categoryName — kategoria licznika wydajności (obiekt wydajności), z którą skojarzony jest ten licznik wydajności.
* counterName — nazwa licznika wydajności.
* instanceName — nazwa wystąpienia kategorii licznika wydajności lub ciąg pusty (""), jeśli kategoria zawiera jedno wystąpienie. Jeśli categoryName to Proces, a licznik wydajności, którego dane chcesz zbierać, pochodzi z bieżącego procesu maszyny JVM, na której jest uruchomiona aplikacja, podaj wartość `"__SELF__"`.

### <a name="unix-performance-counters"></a>Liczniki wydajności sytemu Unix
* [Zainstaluj program collectd z wtyczką Application Insights](java-collectd.md), aby uzyskać szeroką gamę danych na temat systemu i sieci.

## <a name="get-user-and-session-data"></a>Pobieranie danych użytkownika i sesji
Wysyłasz już telemetrię z serwera sieci Web. Teraz, aby zyskać pełny wgląd w dane aplikacji, możesz dodać więcej opcji monitorowania:

* [Dodaj telemetrię do stron sieci Web][usage], aby monitorować wyświetlenia stron i metryki użytkowników.
* [Konfigurowanie testów sieci Web][availability], aby upewnić się, że aplikacja stale działa i odpowiada.

## <a name="send-your-own-telemetry"></a>Wysyłanie własnej telemetrii
Teraz, po zainstalowaniu zestawu SDK, możesz użyć interfejsu API do wysyłania własnej telemetrii.

* [Śledź niestandardowe zdarzenia i metryki][api], aby dowiedzieć się, jak użytkownicy korzystają z aplikacji.
* [Wyszukiwanie zdarzeń i dzienników][diagnostic], aby łatwiej diagnozować problemy.

## <a name="availability-web-tests"></a>Testy dostępności sieci Web
Usługa Application Insights może służyć do testowania witryny sieci Web w regularnych odstępach czasu, aby sprawdzić, czy witryna działa i odpowiada poprawnie.

[Dowiedz się więcej o konfigurowaniu testów sieci Web dostępności.][availability]

## <a name="questions-problems"></a>Pytania? Problemy?
[Rozwiązywanie problemów z technologią Java](java-troubleshoot.md)

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie wywołań zależności](java-agent.md)
* [Monitorowanie liczników wydajności sytemu Unix](java-collectd.md)
* Dodawanie [monitorowania do stron sieci Web](javascript.md) w celu monitorowania czasów ładowania stron, wywołań AJAX i wyjątków przeglądarki
* Zapisywanie [niestandardowych danych telemetrycznych](../../azure-monitor/app/api-custom-events-metrics.md) w celu śledzenia użycia w przeglądarce lub na serwerze.
* Używanie [analytics do](../../azure-monitor/app/analytics.md) zaawansowanych zapytań dotyczących danych telemetrycznych z aplikacji
* Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[usage]: javascript.md
