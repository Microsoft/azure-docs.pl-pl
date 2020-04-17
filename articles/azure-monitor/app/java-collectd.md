---
title: Monitorowanie wydajności aplikacji java w systemie Linux — Azure | Dokumenty firmy Microsoft
description: Rozszerzone monitorowanie wydajności aplikacji w witrynie java za pomocą wtyczki CollectD dla usługi Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 62a723dad7e9f6c2bfdabde159968d507d2d5d41
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537529"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: metryki wydajności systemu Linux w usłudze Application Insights


Aby eksplorować metryki wydajności systemu Linux w [usłudze Application Insights,](../../azure-monitor/app/app-insights-overview.md)zainstaluj [kolekcjonowane](https://collectd.org/)wraz z wtyczką usługi Application Insights. To rozwiązanie typu open source gromadzi różne statystyki systemowe i sieciowe.

Zazwyczaj będziesz używać kolekcjonowane, jeśli masz już [instrumentowane usługi sieci Web Java z Application Insights][java]. Zapewnia więcej danych, które pomogą Ci zwiększyć wydajność aplikacji lub zdiagnozować problemy. 

## <a name="get-your-instrumentation-key"></a>Pobierz klucz oprzyrządowania
W [witrynie Microsoft Azure portal](https://portal.azure.com)otwórz zasób [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md) w miejscu, w którym mają być wyświetlane dane. (Lub [utworzyć nowy zasób](../../azure-monitor/app/create-new-resource.md ).)

Weź kopię klucza instrumentacji, który identyfikuje zasób.

![Przeglądaj wszystkie, otwórz zasób, a następnie w obszarze rozwijany Essentials wybierz i skopiuj klucz instrumentacji](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Zainstaluj zebrać i wtyczkę
Na komputerach z serwerem Linux:

1. Zainstaluj [kolekcjonowane](https://collectd.org/) wersje 5.4.0 lub nowszej.
2. Pobierz [wtyczkę application insights collectd writer](https://github.com/microsoft/ApplicationInsights-Java/tree/master/collectd/src/main/java/com/microsoft/applicationinsights/collectd/internal). Zanotuj numer wersji.
3. Skopiuj `/usr/share/collectd/java`plugin JAR do .
4. Edycja `/etc/collectd/collectd.conf`:
   * Upewnij się, że [wtyczka Java](https://collectd.org/wiki/index.php/Plugin:Java) jest włączona.
   * Zaktualizuj JVMArg dla java.class.path, aby uwzględnić następujący jar. Zaktualizuj numer wersji, aby dopasować go do pobranego numeru:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Dodaj ten fragment kodu przy użyciu klucza instrumentacji z zasobu:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Oto część przykładowego pliku konfiguracyjnego:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Skonfiguruj inne [zebrane wtyczki,](https://collectd.org/wiki/index.php/Table_of_Plugins)które mogą zbierać różne dane z różnych źródeł.

Uruchom ponownie zbierane zgodnie z [jego instrukcją](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Wyświetlanie danych w usłudze Application Insights
W zasobie usługi Application Insights otwórz [metryki i dodaj wykresy][metrics], wybierając metryki, które chcesz wyświetlić z kategorii Niestandardowe.

Domyślnie metryki są agregowane na wszystkich komputerach hosta, z których zostały zebrane metryki. Aby wyświetlić metryki na hoście, w bloku Szczegóły wykresu włącz grupowanie, a następnie wybierz grupowanie według collectD-hosta.

## <a name="to-exclude-upload-of-specific-statistics"></a>Aby wykluczyć przesyłanie określonych statystyk
Domyślnie wtyczka usługi Application Insights wysyła wszystkie dane zebrane przez wszystkie włączone zebrane wtyczki "odczytu". 

Aby wykluczyć dane z określonych wtyczek lub źródeł danych:

* Edytuj plik konfiguracyjny. 
* W `<Plugin ApplicationInsightsWriter>`, dodaj linie dyrektywy w ten sposób:

| Dyrektywy | Efekt |
| --- | --- |
| `Exclude disk` |Wyklucz wszystkie dane `disk` zebrane przez wtyczkę |
| `Exclude disk:read,write` |Wyklucz źródła o `read` `write` nazwie `disk` i z wtyczki. |

Oddzielne dyrektywy z nową linię.

## <a name="problems"></a>Problemy?
*Nie widzę danych w portalu*

* Otwórz [wyszukiwanie,][diagnostic] aby sprawdzić, czy nieprzetworzone zdarzenia dotarły. Czasami trwa dłużej, aby pojawić się w Eksploratorze metryk.
* Może być konieczne [ustawienie wyjątków zapory dla danych wychodzących](../../azure-monitor/app/ip-addresses.md)
* Włącz śledzenie w usłudze Application Insights. Dodaj ten `<Plugin ApplicationInsightsWriter>`wiersz w obrębie :
  * `SDKLogger true`
* Otwórz terminal i uruchom zbieranie w trybie szczegółowym, aby zobaczyć wszelkie problemy, które zgłasza:
  * `sudo collectd -f`

## <a name="known-issue"></a>Znany problem

Wtyczka Application Insights Write jest niezgodna z niektórymi wtyczkami Odczytu. Niektóre wtyczki czasami wysyłają "NaN", gdzie wtyczka usługi Application Insights oczekuje numeru zmiennoprzecinkowego.

Symptom: Zebrać dziennik pokazuje błędy, które obejmują "AI: ... SyntaxError: Nieoczekiwany token N".

Obejście: Wyklucz dane zebrane przez problem Napisz wtyczki. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md


