---
title: Analiza zdarzeń Service Fabric platformy Azure z Application Insights
description: Dowiedz się więcej na temat wizualizacji i analizowania zdarzeń przy użyciu Application Insights do monitorowania i diagnostyki klastrów Service Fabric platformy Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: a912b7a6d918a40aaae54c9b177250dc3c30c84d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256564"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analiza zdarzeń i wizualizacja przy użyciu Application Insights

Częścią Azure Monitor, Application Insights to rozszerzalna platforma do monitorowania i diagnostyki aplikacji. Obejmuje zaawansowane narzędzie do analizy i wykonywania zapytań, dostosowywalne pulpity nawigacyjne i wizualizacje oraz dalsze opcje, w tym automatyczne alerty. Integracja Application Insights z Service Fabric obejmuje środowiska narzędziowe dla programu Visual Studio i Azure Portal, a także Service Fabric określone metryki, co zapewnia kompleksowe środowisko rejestrowania. Chociaż wiele dzienników jest automatycznie tworzonych i zbieranych za pomocą Application Insights, zalecamy dodanie do aplikacji dodatkowego rejestrowania niestandardowego w celu utworzenia bogatszego środowiska diagnostyki.

Ten artykuł pomaga w rozwiązywaniu następujących typowych pytań:

* Jak mogę wiesz, co się dzieje w mojej aplikacji i usługach i Zbieraj dane telemetryczne?
* Jak mogę Rozwiązywanie problemów z moją aplikacją, szczególnie w przypadku komunikacji między sobą?
* Jak mogę uzyskać metryki dotyczące sposobu, w jaki działają usługi, na przykład czas ładowania strony, żądania HTTP?

Celem tego artykułu jest przedstawienie informacji o sposobach uzyskiwania szczegółowych informacji i rozwiązywania problemów z poziomu Application Insights. Jeśli chcesz dowiedzieć się, jak skonfigurować i skonfigurować Application Insights przy użyciu Service Fabric, zapoznaj się z tym [samouczkiem](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitorowanie w Application Insights

W przypadku korzystania z Service Fabric Program Application Insights ma rozbudowane środowisko pracy. Na stronie Przegląd Application Insights zawiera najważniejsze informacje dotyczące usługi, takie jak czas odpowiedzi i liczba przetworzonych żądań. Klikając przycisk wyszukiwania u góry, zobaczysz listę ostatnich żądań w aplikacji. Ponadto w tym miejscu będzie można zobaczyć Nieudane żądania i zdiagnozować, jakie błędy mogły wystąpić.

![Przegląd Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

W prawym panelu na powyższym obrazie istnieją dwa główne typy wpisów: żądania i zdarzenia. Żądania są wywołaniami w interfejsie API aplikacji za pośrednictwem żądań HTTP w tym przypadku, a zdarzenia są zdarzeniami niestandardowymi, które działają jako dane telemetryczne, można dodać gdziekolwiek w kodzie. Możesz dodatkowo zbadać instrumentację aplikacji w [interfejsie API Application Insights, aby uzyskać niestandardowe zdarzenia i metryki](../azure-monitor/app/api-custom-events-metrics.md). Kliknięcie żądania spowoduje wyświetlenie dalszych informacji, jak pokazano na poniższej ilustracji, w tym danych specyficznych dla Service Fabric, które są zbierane w Application Insights Service Fabric pakiecie NuGet. Te informacje są przydatne do rozwiązywania problemów i znajomości stanu aplikacji, a wszystkie te informacje są przeszukiwane w ramach Application Insights

![Szczegóły żądania Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights ma wyznaczono widok do wykonywania zapytań dotyczących wszystkich danych, które znajdują się w. Kliknij pozycję "Eksplorator metryk" w górnej części strony przegląd, aby przejść do portalu Application Insights. W tym miejscu można uruchamiać zapytania względem zdarzeń niestandardowych wymienionych przed, żądania, wyjątki, liczniki wydajności i inne metryki przy użyciu języka zapytań Kusto. Poniższy przykład przedstawia wszystkie żądania w ciągu ostatniej godziny.

![Szczegóły żądania Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Aby dowiedzieć się więcej o możliwościach portalu Application Insights, przejdź do [dokumentacji portalu Application Insights](../azure-monitor/app/overview-dashboard.md).

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurowanie Application Insights przy użyciu użyciu struktury eventflow

Jeśli używasz użyciu struktury eventflow do agregowania zdarzeń, upewnij się, że zaimportowano `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` pakiet NuGet. Poniższy kod jest wymagany w sekcji dane *wyjściowe* *eventFlowConfig.jsw*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Upewnij się, że wprowadzono wymagane zmiany w filtrach, a także Dołącz wszelkie inne dane wejściowe (wraz z odpowiednimi pakietami NuGet).

## <a name="application-insights-sdk"></a>Zestaw SDK Application Insights

Zaleca się korzystanie z użyciu struktury eventflow i funkcji wad jako rozwiązań agregacji, ponieważ umożliwiają one bardziej modularne podejście do diagnostyki i monitorowania, tj. Jeśli chcesz zmienić dane wyjściowe z użyciu struktury eventflow, nie wymaga to zmiany rzeczywistej instrumentacji, a tylko prostej modyfikacji pliku konfiguracyjnego. Jeśli jednak użytkownik zdecyduje się na zainwestowanie przy użyciu Application Insights i nie będzie można go zmienić na inną platformę, należy przyjrzeć się użyciu Application Insights "nowy zestaw SDK do agregowania zdarzeń i wysłania ich do Application Insights. Oznacza to, że nie będzie już konieczne skonfigurowanie użyciu struktury eventflow do wysyłania danych do Application Insights, ale zamiast tego zostanie zainstalowany pakiet NuGet Service Fabric ApplicationInsight. Szczegółowe informacje o pakiecie można znaleźć [tutaj](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Obsługa Application Insights mikrousług i kontenerów](https://azure.microsoft.com/blog/app-insights-microservices/) zawiera niektóre nowe funkcje, nad którymi pracujesz (obecnie w wersji beta), co pozwala na bardziej zaawansowane opcje monitorowania z Application Insights. Obejmują one śledzenie zależności (używane w przypadku tworzenia AppMap wszystkich usług i aplikacji w klastrze oraz komunikacji między nimi) oraz lepszą korelację śladów pochodzących z usług (pomaga w lepszym wykorzystaniu problemu w przepływie pracy aplikacji lub usługi).

Jeśli opracowujesz program w programie .NET i prawdopodobnie będziesz używać niektórych modeli programowania Service Fabric i wolisz korzystać z Application Insights jako platformy do wizualizacji i analizowania danych dotyczących zdarzeń i dzienników, zalecamy przechodzenie przez Application Insights trasy zestawu SDK jako przepływu pracy monitorowania i diagnostyki. Zapoznaj się z [tym](../azure-monitor/app/asp-net-more.md) tematem [, aby rozpocząć](../azure-monitor/app/asp-net-trace-logs.md) pracę z usługą Application Insights w celu zbierania i wyświetlania dzienników.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Nawigowanie po zasobie Application Insights w Azure Portal

Po skonfigurowaniu Application Insights jako danych wyjściowych dla zdarzeń i dzienników informacje powinny zacząć pojawić się w zasobie Application Insights w ciągu kilku minut. Przejdź do zasobu Application Insights, co spowoduje przejście do pulpitu nawigacyjnego zasobów Application Insights. Kliknij przycisk **Wyszukaj** na pasku zadań Application Insights, aby zobaczyć najnowsze dane śledzenia odebrane i aby można było je filtrować.

*Eksplorator metryk* to przydatne narzędzie do tworzenia niestandardowych pulpitów nawigacyjnych opartych na metrykach, które mogą być raportowane przez aplikacje, usługi i klaster. Zobacz [Eksplorowanie metryk w Application Insights](../azure-monitor/platform/metrics-charts.md) , aby skonfigurować kilka wykresów dla siebie na podstawie zbieranych danych.

Kliknięcie pozycji **Analiza** spowoduje przejście do portalu analizy Application Insights, w którym można badać zdarzenia i ślady z większym zakresem i opcjonalnością. Więcej informacji na ten temat można znaleźć [w części analiza w Application Insights](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie alertów w programie AI](../azure-monitor/platform/alerts-log.md) do powiadamiania o zmianach wydajności lub użycia
* [Inteligentne wykrywanie w Application Insights](../azure-monitor/app/proactive-diagnostics.md) służy do aktywnej analizy danych telemetrycznych wysyłanych do Application Insights w celu ostrzegania o potencjalnych problemach z wydajnością
