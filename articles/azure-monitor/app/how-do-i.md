---
title: Jak mogę ... w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Często zadawane pytania w usłudze Application Insights.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 8d4b1e79c48b14ed7dce756468e4c48d633c3f04
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536866"
---
# <a name="how-do-i--in-application-insights"></a>Jak mogę (...) w usłudze Application Insights?
## <a name="get-an-email-when-"></a>Otrzymaj wiadomość e-mail, gdy ...
### <a name="email-if-my-site-goes-down"></a>Wysyłanie wiadomości e-mail w przypadku upadku mojej witryny
Ustaw [test sieci web dostępności](../../azure-monitor/app/monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Wysyłanie wiadomości e-mail, jeśli moja witryna jest przeciążona
Ustaw [alert](../../azure-monitor/app/alerts.md) na **czas odpowiedzi serwera**. Próg od 1 do 2 sekund powinien działać.

![](./media/how-do-i/030-server.png)

Aplikacja może również wykazywać oznaki odkształcenia przez zwracanie kodów błędów. Ustawianie alertu w sprawie **żądań nieudanych**.

Jeśli chcesz ustawić alert na **wyjątki serwera,** może być konieczne kilka [dodatkowych ustawień,](../../azure-monitor/app/asp-net-exceptions.md) aby zobaczyć dane.

### <a name="email-on-exceptions"></a>Wysyłanie wiadomości e-mail z wyjątkami
1. [Konfigurowanie monitorowania wyjątków](../../azure-monitor/app/asp-net-exceptions.md)
2. [Ustawianie alertu](../../azure-monitor/app/alerts.md) na metrykę Liczba wyjątków

### <a name="email-on-an-event-in-my-app"></a>Wysyłanie wiadomości e-mail z wydarzeniem w mojej aplikacji
Załóżmy, że chcesz otrzymać wiadomość e-mail, gdy wystąpi określone zdarzenie. Usługa Application Insights nie udostępnia tej funkcji bezpośrednio, ale może [wysyłać alerty, gdy metryka przekroczy próg.](../../azure-monitor/app/alerts.md)

Alerty można ustawić na [metryki niestandardowe,](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)choć nie zdarzenia niestandardowe. Napisz kod, aby zwiększyć metrykę, gdy wystąpi zdarzenie:

    telemetry.TrackMetric("Alarm", 10);

lub:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Ponieważ alerty mają dwa stany, należy wysłać niską wartość, jeśli wziąć pod uwagę alert, który został zakończony:

    telemetry.TrackMetric("Alarm", 0.5);

Utwórz wykres w [Eksploratorze metryk,](../../azure-monitor/platform/metrics-charts.md) aby wyświetlić alarm:

![](./media/how-do-i/010-alarm.png)

Teraz ustaw alert do ognia, gdy metryka przekracza wartość średnią przez krótki okres:

![](./media/how-do-i/020-threshold.png)

Ustaw minimalny okres uśredniania.

Otrzymasz e-maile zarówno wtedy, gdy dane przekroczyć i poniżej progu.

Oto niektóre ważne kwestie:

* Alert ma dwa stany ("alert" i "zdrowy"). Stan jest oceniany tylko po odebraniu metryki.
* Wiadomość e-mail jest wysyłana tylko wtedy, gdy stan się zmienia. Dlatego musisz wysłać zarówno metryki o wysokiej, jak i niskiej wartości.
* Aby ocenić alert, średnia jest pobierana z odebranych wartości w poprzednim okresie. Dzieje się tak za każdym razem, gdy metryka jest odbierana, więc wiadomości e-mail mogą być wysyłane częściej niż ustawiony okres.
* Ponieważ wiadomości e-mail są wysyłane zarówno na "alert" i "zdrowy", warto rozważyć ponowne przemyślenie zdarzenia jednego strzału jako warunek dwustanowy. Na przykład zamiast zdarzenia "zadanie ukończone" masz warunek "zadanie w toku", w którym otrzymasz wiadomości e-mail na początku i na końcu zadania.

### <a name="set-up-alerts-automatically"></a>Automatyczne konfigurowanie alertów
[Tworzenie nowych alertów za pomocą programu PowerShell](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Zarządzanie aplikacjami za pomocą programu PowerShell
* [Tworzenie nowych zasobów](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Tworzenie nowych alertów](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Oddzielanie danych telemetrycznych od różnych wersji

* Wiele ról w aplikacji: użyj jednego zasobu usługi Application Insights i [filtruj cloud_Rolename](../../azure-monitor/app/app-map.md).
* Oddzielanie wersji deweloperskich, testowych i release: użyj różnych zasobów usługi Application Insights. Odbierz klucze oprzyrządowania z web.config. [Dowiedz się więcej](../../azure-monitor/app/separate-resources.md)
* Raportowanie wersji kompilacji: Dodaj właściwość przy użyciu inicjatora telemetrii. [Dowiedz się więcej](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorowanie serwerów zaplecza i aplikacji klasycznych
[Użyj modułu SDK systemu Windows Server](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Wizualizowanie danych
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Pulpit nawigacyjny z danymi z wielu aplikacji
* W [Eksploratorze metryk](../../azure-monitor/platform/metrics-charts.md)dostosuj wykres i zapisz go jako ulubiony. Przypnij go do pulpitu nawigacyjnego platformy Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Pulpit nawigacyjny z danymi z innych źródeł i usługą Application Insights
* [Eksportowanie danych telemetrycznych do usługi Power BI](../../azure-monitor/app/export-power-bi.md ).

Lub

* Użyj programu SharePoint jako pulpitu nawigacyjnego, wyświetlając dane w składnikach Web Part programu SharePoint. [Użyj ciągłego eksportu i usługi Stream Analytics, aby eksportować do programu SQL](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Użyj programu PowerView, aby sprawdzić bazę danych i utworzyć składnik Web Part programu SharePoint dla programu PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Odfiltruj anonimowych lub uwierzytelnionych użytkowników
Jeśli użytkownicy się zalogują, możesz ustawić [uwierzytelniony identyfikator użytkownika](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users). (To nie dzieje się automatycznie.)

Następnie można wykonywać czynności takie jak:

* Wyszukiwanie określonych identyfikatorów użytkowników

![](./media/how-do-i/110-search.png)

* Filtrowanie danych dla użytkowników anonimowych lub uwierzytelnionych

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modyfikowanie nazw lub wartości właściwości
Tworzenie [filtru](../../azure-monitor/app/api-filtering-sampling.md#filtering). Dzięki temu można modyfikować lub filtrować dane telemetryczne przed wysłaniem z aplikacji do usługi Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Lista określonych użytkowników i ich użycie
Jeśli chcesz tylko [wyszukać konkretnych użytkowników,](#search-specific-users)możesz ustawić [uwierzytelniony identyfikator użytkownika](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Jeśli chcesz mieć listę użytkowników z danymi, takimi jak strony, na których patrzą lub jak często się logują, masz dwie opcje:

* [Ustaw uwierzytelniony identyfikator użytkownika,](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) [eksportuj do bazy danych](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) i używaj odpowiednich narzędzi do analizowania tam danych użytkownika.
* Jeśli masz tylko niewielką liczbę użytkowników, wyślij niestandardowe zdarzenia lub metryki, używając danych zainteresowania jako wartości metryki lub nazwy zdarzenia i ustawiając identyfikator użytkownika jako właściwość. Aby analizować widoki strony, zastąp standardowe wywołanie trackPageView w języku JavaScript. Aby przeanalizować dane telemetryczne po stronie serwera, użyj inicjatora telemetrii, aby dodać identyfikator użytkownika do wszystkich danych telemetrycznych serwera. Następnie można filtrować i segmentować metryki i wyszukiwania według identyfikatora użytkownika.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Zmniejszanie ruchu z aplikacji do usługi Application Insights
* W [AplikacjiInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), wyłączyć wszystkie moduły, które nie są potrzebne, takie licznik wydajności kolektora.
* Użyj [próbkowania i filtrowania](../../azure-monitor/app/api-filtering-sampling.md) w sdk.
* Na stronach internetowych ogranicz liczbę połączeń Ajax zgłoszonych dla każdego widoku strony. We urywek skryptu `instrumentationKey:...` po `,maxAjaxCallsPerView:3` , wstawić: (lub odpowiednią liczbę).
* Jeśli używasz [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), obliczyć zagregowane partie wartości metryki przed wysłaniem wyniku. Istnieje przeciążenie TrackMetric(), które zapewnia, że.

Dowiedz się więcej o [cenach i przydziałach](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Wyłączanie danych telemetrycznych
Aby **dynamicznie zatrzymać i uruchomić** zbieranie i przesyłanie danych telemetrycznych z serwera:

### <a name="aspnet-classic-applications"></a>ASP.NET Aplikacje klasyczne

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Inne zastosowania
Nie zaleca się `TelemetryConfiguration.Active` używania singletona na konsoli lub ASP.NET aplikacji Core.
jeśli samodzielnie `TelemetryConfiguration` utworzono `DisableTelemetry` `true`wystąpienie - ustaw na .

W przypadku aplikacji ASP.NET Core `TelemetryConfiguration` można uzyskać dostęp do wystąpienia przy użyciu [ASP.NET iniekcji zależności rdzenia](/aspnet/core/fundamentals/dependency-injection/). Więcej szczegółów można znaleźć w [artykule ApplicationInsights for ASP.NET Core applications.](../../azure-monitor/app/asp-net-core.md)

## <a name="disable-selected-standard-collectors"></a>Wyłączanie wybranych standardowych modułów zbierających
Można wyłączyć standardowe moduły zbierające (na przykład liczniki wydajności, żądania HTTP lub zależności)

* **ASP.NET aplikacji** - Usuń lub skomentuj odpowiednie wiersze w [AplikacjiInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **ASP.NET Podstawowe aplikacje** — śledzenie opcji konfiguracji modułów telemetrycznych w [aplikacji ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Wyświetlanie liczników wydajności systemu
Wśród metryk, które można wyświetlić w Eksploratorze metryk, znajduje się zestaw liczników wydajności systemu. Istnieje wstępnie zdefiniowany blok zatytułowany **Serwery,** który wyświetla kilka z nich.

![Otwórz zasób usługi Application Insights i kliknij pozycję Serwery](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Jeśli nie widzisz danych licznika wydajności
* **Serwer usług IIS** na własnym komputerze lub na maszynie wirtualnej. [Zainstaluj Monitor stanu](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Witryna sieci Web platformy Azure** — nie obsługujemy jeszcze liczników wydajności. Istnieje kilka metryk, które można uzyskać jako standardową część panelu sterowania witryny sieci Web platformy Azure.
* **Instalacja serwera** - Uniksa[zbierana](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Aby wyświetlić więcej liczników wydajności
* Najpierw [dodaj nowy wykres](../../azure-monitor/platform/metrics-charts.md) i sprawdź, czy licznik znajduje się w podstawowym zestawie, który oferujemy.
* Jeśli nie, [dodaj licznik do zestawu zebranego przez moduł licznika wydajności](../../azure-monitor/app/performance-counters.md).
