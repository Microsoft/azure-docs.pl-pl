---
title: Filtrowanie danych telemetrycznych usługi Azure Application Insights w aplikacji sieci Web Java
description: Zmniejsz ruch telemetrii, filtrując zdarzenia, które nie muszą być monitorowane.
ms.topic: conceptual
ms.date: 3/14/2019
ms.custom: devx-track-java
ms.openlocfilehash: e450cf0de7dce4f626fd41252bfeed5fba294c70
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761009"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrowanie danych telemetrycznych w aplikacji sieci Web Java

Filtry umożliwiają wybranie danych telemetrycznych [wysyłanych przez aplikację internetową Java do Application Insights](java-get-started.md). Istnieje kilka wbudowanych filtrów, których można użyć, i można również pisać własne filtry niestandardowe.

Dostępne są następujące filtry:

* Poziom ważności śledzenia
* Określone adresy URL, słowa kluczowe lub kody odpowiedzi
* Szybkie odpowiedzi — czyli żądania, do których aplikacja szybko odpowiedziała
* Nazwy określonych zdarzeń

> [!NOTE]
> Filtry pochylają metryki aplikacji. Na przykład można zdecydować, że w celu zdiagnozowania powolnych odpowiedzi należy ustawić filtr w celu odrzucania czasów szybkiego reagowania. Należy jednak pamiętać, że średni czas odpowiedzi raportowany przez Application Insights będzie wtedy wolniejszy od prawdziwej szybkości, a liczba żądań będzie mniejsza niż rzeczywista liczba.
> Jeśli jest to problem, użyj polecenia [próbkowania](./sampling.md) .

## <a name="setting-filters"></a>Filtry ustawień

W ApplicationInsights.xml Dodaj `TelemetryProcessors` sekcję podobną do tego przykładu:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```

[Sprawdź pełen zestaw wbudowanych procesorów](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal).

## <a name="built-in-filters"></a>Filtry wbudowane

### <a name="metric-telemetry-filter"></a>Filtr telemetrii metryki

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` -Rozdzielana przecinkami lista nazw metryk niestandardowych.


### <a name="page-view-telemetry-filter"></a>Filtr telemetrii widoku strony

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` -Czas trwania oznacza czas potrzebny do załadowania strony. Jeśli ta wartość jest ustawiona, strony ładowane szybciej niż ten czas nie są zgłaszane.
* `NotNeededNames` — Rozdzielana przecinkami lista nazw stron.
* `NotNeededUrls` -Rozdzielana przecinkami lista fragmentów adresów URL. Na przykład `"home"` filtry wszystkie strony, które mają "Home" w adresie URL.


### <a name="request-telemetry-filter"></a>Filtr telemetryczny żądania


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filtr źródła syntetycznego

Filtruje wszystkie dane telemetryczne, które mają wartości we właściwości SyntheticSource. Obejmują one żądania z botów, pająków i testów dostępności.

Odfiltruj dane telemetryczne dla wszystkich żądań syntetycznych:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Odfiltruj dane telemetryczne dla określonych źródeł syntetycznych:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` -Rozdzielana przecinkami lista nazw źródeł syntetycznych.

### <a name="telemetry-event-filter"></a>Filtr zdarzeń telemetrii

Filtruje zdarzenia niestandardowe (rejestrowane przy użyciu [poleceń trackEvent ()](./api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` — Rozdzielana przecinkami lista nazw zdarzeń.


### <a name="trace-telemetry-filter"></a>Filtr telemetrii śledzenia

Filtruje ślady dziennika (rejestrowane przy użyciu [TrackTrace ()](./api-custom-events-metrics.md#tracktrace) lub [modułu zbierającego platformy rejestrowania](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` prawidłowe wartości to:
  *  Wyłącz filtrowanie wszystkich śladów
  *  Śledzenie — brak filtrowania. równa się na poziomie śledzenia
  *  INFO — Filtruj poziom śledzenia
  *  OSTRZEGAj — Filtruj dane śledzenia i informacje
  *  BŁĄD — filtrowanie ostrzeżeń, informacji, śledzenia
  *  KRYTYCZNE — Filtruj wszystkie, ale krytyczne


## <a name="custom-filters"></a>Filtry niestandardowe

### <a name="1-code-your-filter"></a>1. Zakoduj filtr

W kodzie, Utwórz klasę, która implementuje `TelemetryProcessor` :

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Wywołaj filtr w pliku konfiguracji

W ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. Wywołaj filtr (Sprężyna Java)

W przypadku aplikacji opartych na strukturze wiosny niestandardowe procesory telemetrii muszą być zarejestrowane w głównej klasie aplikacji jako element. Po uruchomieniu aplikacji zostanie ona przetransmitowana ponownie.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Aby przekazać te parametry do niestandardowego filtru, należy utworzyć własne parametry filtru w `application.properties` i wykorzystać zewnętrzną strukturę rozruchu sprężyny. 


## <a name="troubleshooting"></a>Rozwiązywanie problemów

*Mój filtr nie działa.*

* Sprawdź, czy podano prawidłowe wartości parametrów. Na przykład czasy trwania powinny być liczbami całkowitymi. Nieprawidłowe wartości spowodują, że filtr zostanie zignorowany. Jeśli filtr niestandardowy zgłosi wyjątek z konstruktora lub metody Set, zostanie zignorowany.

## <a name="next-steps"></a>Następne kroki

* [Próbkowanie](./sampling.md) — należy rozważyć próbkowanie jako alternatywę, która nie pochyla metryk.

