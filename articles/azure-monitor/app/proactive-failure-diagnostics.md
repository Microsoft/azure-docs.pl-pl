---
title: Inteligentne wykrywanie — anomalie błędów w usłudze Application Insights | Dokumenty firmy Microsoft
description: Ostrzega o nietypowych zmianach w szybkości żądań nie powiodło się do aplikacji sieci web i zapewnia analizę diagnostyczną. Konfiguracja nie jest wymagana.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: a1bce3ab86748d8247a72da3bd70e0f2e8155dbf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536815"
---
# <a name="smart-detection---failure-anomalies"></a>Inteligentne wykrywanie — anomalie błędów
[Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) automatycznie ostrzega użytkownika w czasie zbliżonym do rzeczywistego, jeśli aplikacja sieci web doświadcza nieprawidłowego wzrostu liczby żądań, które nie powiodły się. Wykrywa nietypowy wzrost liczby żądań HTTP lub wywołań zależności, które są zgłaszane jako nieudane. W przypadku żądań żądania nie powiodły się zwykle mają kody odpowiedzi 400 lub wyższe. Aby ułatwić klasyfikowanie i diagnozowanie problemu, w szczegółach alertu znajduje się analiza cech błędów i powiązanych danych aplikacji. Istnieją również łącza do portalu usługi Application Insights w celu dalszej diagnostyki. Funkcja nie wymaga konfiguracji ani konfiguracji, ponieważ używa algorytmów uczenia maszynowego do przewidywania normalnego współczynnika awarii.

Ta funkcja działa dla dowolnej aplikacji sieci web, hostowanej w chmurze lub na własnych serwerach, które generują żądania aplikacji lub dane zależności. Jeśli na przykład masz rolę procesu roboczego, która wywołuje [trackrequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) lub [TrackDependency().](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)

Po skonfigurowaniu [usługi Application Insights dla projektu](../../azure-monitor/app/app-insights-overview.md)i jeśli aplikacja generuje pewną minimalną ilość danych, inteligentne wykrywanie anomalii awarii trwa 24 godziny, aby dowiedzieć się normalne zachowanie aplikacji, zanim zostanie włączona i może wysyłać alerty.

Oto przykładowy alert:

[![](./media/proactive-failure-diagnostics/013.png "Sample smart detection alert showing cluster analysis around failure")](./media/proactive-failure-diagnostics/013.png#lightbox)

Szczegóły alertu podadzą:

* Wskaźnik awaryjności w porównaniu do normalnego zachowania aplikacji.
* Ilu użytkowników dotyczy - więc wiesz, jak bardzo się martwić.
* Charakterystyczny wzór związany z awariami. W tym przykładzie istnieje kod określonej odpowiedzi, nazwa żądania (operacja) i wersja aplikacji. To natychmiast informuje, od czego zacząć szukać w kodzie. Inne możliwości mogą być określonej przeglądarki lub systemu operacyjnego klienta.
* Wyjątek, dziennik śledzenia i awarii zależności (baz danych lub innych składników zewnętrznych), które wydają się być skojarzone z charakterystycznych błędów.
* Łącza bezpośrednio do odpowiednich wyszukiwań danych w usłudze Application Insights.

## <a name="benefits-of-smart-detection"></a>Zalety inteligentnego wykrywania
Zwykłe [alerty metryki](../../azure-monitor/app/alerts.md) informują, że może wystąpić problem. Ale inteligentne wykrywanie rozpoczyna pracę diagnostyczną dla Ciebie, wykonując wiele analizy, które w przeciwnym razie musiałbyś wykonać samodzielnie. Otrzymasz wyniki starannie zapakowane, pomagając szybko dostać się do źródła problemu.

## <a name="how-it-works"></a>Jak to działa
Inteligentne wykrywanie monitoruje dane odebrane z aplikacji, a w szczególności wskaźniki awarii. Ta reguła zlicza liczbę `Successful request` żądań, dla których właściwość jest false, a liczba wywołań zależności, dla których `Successful call` właściwość jest false. W przypadku żądań `Successful request == (resultCode < 400)` domyślnie (chyba że masz napisany kod niestandardowy do [filtrowania](../../azure-monitor/app/api-filtering-sampling.md#filtering) lub generowania własnych wywołań [TrackRequest).](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) 

Wydajność aplikacji ma typowy wzorzec zachowania. Niektóre żądania lub wywołania zależności będą bardziej podatne na niepowodzenie niż inne; a ogólny wskaźnik awaryjności może wzrosnąć wraz ze wzrostem obciążenia. Inteligentne wykrywanie używa uczenia maszynowego, aby znaleźć te anomalie.

Ponieważ dane są dostępne w usłudze Application Insights z aplikacji sieci Web, funkcja Inteligentne wykrywanie porównuje bieżące zachowanie z wzorcami widocznymi w ciągu ostatnich kilku dni. Jeśli nieprawidłowy wzrost wskaźnika awaryjności jest obserwowany w porównaniu z poprzednią wydajnością, wyzwalana jest analiza.

Po wyzwoleniu analizy usługa wykonuje analizę klastra na żądanie nie powiodło się, aby spróbować zidentyfikować wzorzec wartości, które charakteryzują błędy. 

W powyższym przykładzie wykryła, że większość błędów dotyczy określonego kodu wynikowego, nazwy żądania, hosta adresu URL serwera i wystąpienia roli. 

Gdy usługa jest instrumentowane z tych wywołań, analizator szuka wyjątku i awarii zależności, które są skojarzone z żądaniami w klastrze, który zidentyfikował, wraz z przykładem dzienników śledzenia skojarzonych z tymi żądaniami.

Wynikowa analiza jest wysyłana jako alert, chyba że nie skonfigurowano go.

Podobnie jak [alerty ustawione ręcznie,](../../azure-monitor/app/alerts.md)można sprawdzić stan odpalony alert, który można rozwiązać, jeśli problem został rozwiązany. Skonfiguruj reguły alertów na stronie Alerty zasobu usługi Application Insights. Jednak w przeciwieństwie do innych alertów nie trzeba konfigurować ani konfigurować funkcji Wykrywania inteligentnego. Jeśli chcesz, możesz go wyłączyć lub zmienić jego docelowe adresy e-mail.

### <a name="alert-logic-details"></a>Szczegóły logiki alertu

Alerty są wyzwalane przez nasz zastrzeżony algorytm uczenia maszynowego, więc nie możemy udostępnić dokładnych szczegółów implementacji. Z powiedział, że rozumiemy, że czasami trzeba wiedzieć więcej o tym, jak działa logika podstawowa. Podstawowe czynniki, które są oceniane w celu określenia, czy alert powinien być wyzwalany są: 

* Analiza procentu błędów żądań/zależności w oknie czasu kroczącego 20 minut.
* Porównanie procentu awarii z ostatnich 20 minut do szybkości w ciągu ostatnich 40 minut i ostatnich siedmiu dni i szuka znaczących odchyleń, które przekraczają x-times, że odchylenie standardowe.
* Przy użyciu limitu adaptacyjnego dla minimalnej wartości procentowej awarii, która różni się w zależności od liczby żądań/zależności aplikacji.
* Istnieje logika, która może automatycznie rozwiązać warunek monitora alertu, jeśli problem nie zostanie wykryty przez 8-24 godziny.

## <a name="configure-alerts"></a>Konfigurowanie alertów

Regułę alertu wykrywania inteligentnego można wyłączyć z portalu lub za pomocą usługi Azure Resource Manager[(zobacz przykład szablonu).](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)

Ta reguła alertu jest tworzona przy zastosowaniu skojarzonej [grupy akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) o nazwie "Inteligentne wykrywanie usługi Application Insights", która zawiera akcje poczty e-mail i elementu webhook i może zostać rozszerzona w celu wyzwolenia dodatkowych akcji po uruchomieniu alertu.

> [!NOTE]
> Powiadomienia e-mail wysyłane z tej reguły alertów są teraz wysyłane domyślnie do użytkowników skojarzonych z rolami czytnika monitorowania i monitorowania współautora subskrypcji. Więcej informacji na ten temat można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
> Powiadomienia wysyłane z tej reguły alertu są zgodne ze [wspólnym schematem alertów](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
>

Otwórz stronę Alerty. Reguły alertów anomalie awarii są uwzględniane wraz z alertami, które zostały ustawione ręcznie i można sprawdzić, czy jest obecnie w stanie alertu.

[![](./media/proactive-failure-diagnostics/021.png "On the Application Insights resource page, click 'Alerts' tile, then 'Manage alert rules'")](./media/proactive-failure-diagnostics/021.png#lightbox)

Kliknij alert, aby go skonfigurować.

[![](./media/proactive-failure-diagnostics/032.png "Rule configuration screen")](./media/proactive-failure-diagnostics/032.png#lightbox)

Należy zauważyć, że można wyłączyć lub usunąć regułę alertu anomalie awarii, ale nie można utworzyć innego w tym samym zasobie usługi Application Insights.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Przykład błędu Anomalie alert ładunku elementu webhook

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [["2019-10-30T05:20:00Z",
            0],
            ["2019-10-30T05:40:00Z",
            100],
            ["2019-10-30T06:00:00Z",
            0],
            ["2019-10-30T06:20:00Z",
            0],
            ["2019-10-30T06:40:00Z",
            100],
            ["2019-10-30T07:00:00Z",
            0],
            ["2019-10-30T07:20:00Z",
            0],
            ["2019-10-30T07:40:00Z",
            100],
            ["2019-10-30T08:00:00Z",
            0],
            ["2019-10-30T08:20:00Z",
            0],
            ["2019-10-30T08:40:00Z",
            100],
            ["2019-10-30T17:00:00Z",
            0],
            ["2019-10-30T17:20:00Z",
            0],
            ["2019-10-30T09:00:00Z",
            0],
            ["2019-10-30T09:20:00Z",
            0],
            ["2019-10-30T09:40:00Z",
            100],
            ["2019-10-30T10:00:00Z",
            0],
            ["2019-10-30T10:20:00Z",
            0],
            ["2019-10-30T10:40:00Z",
            100],
            ["2019-10-30T11:00:00Z",
            0],
            ["2019-10-30T11:20:00Z",
            0],
            ["2019-10-30T11:40:00Z",
            100],
            ["2019-10-30T12:00:00Z",
            0],
            ["2019-10-30T12:20:00Z",
            0],
            ["2019-10-30T12:40:00Z",
            100],
            ["2019-10-30T13:00:00Z",
            0],
            ["2019-10-30T13:20:00Z",
            0],
            ["2019-10-30T13:40:00Z",
            100],
            ["2019-10-30T14:00:00Z",
            0],
            ["2019-10-30T14:20:00Z",
            0],
            ["2019-10-30T14:40:00Z",
            100],
            ["2019-10-30T15:00:00Z",
            0],
            ["2019-10-30T15:20:00Z",
            0],
            ["2019-10-30T15:40:00Z",
            100],
            ["2019-10-30T16:00:00Z",
            0],
            ["2019-10-30T16:20:00Z",
            0],
            ["2019-10-30T16:40:00Z",
            100],
            ["2019-10-30T17:30:00Z",
            50]],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [{
                "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
            }],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [{
                "rootJsonNode": null,
                "sectionName": null,
                "displayControls": [{
                    "property": "DetectionSummary",
                    "displayName": "What was detected?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "FormattedOccurenceTime",
                    "displayName": "When did this occur?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "DetectedFailureRate",
                    "displayName": "Detected failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "NormalFailureRate",
                    "displayName": "Normal failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "chartType": "Line",
                    "xAxisType": "Date",
                    "yAxisType": "Percentage",
                    "xAxisName": "",
                    "yAxisName": "",
                    "property": "FailureRateChart",
                    "displayName": "Failure rate over last 12 hours",
                    "type": "Chart",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "defaultLoad": true,
                    "displayConfig": [{
                        "rootJsonNode": null,
                        "sectionName": null,
                        "displayControls": [{
                            "showHeader": false,
                            "columns": [{
                                "property": "Name",
                                "displayName": "Name"
                            },
                            {
                                "property": "Value",
                                "displayName": "Value"
                            }],
                            "property": "tables[0].rows[0][0]",
                            "displayName": "All of the failed requests had these characteristics:",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": true
                        }]
                    }],
                    "property": "ArmSystemEventsRequest",
                    "displayName": "",
                    "type": "ARMRequest",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "showHeader": false,
                    "columns": [{
                        "property": "Link",
                        "displayName": "Link"
                    }],
                    "property": "LinksTable",
                    "displayName": "Links",
                    "type": "Table",
                    "isOptional": false,
                    "isPropertySerialized": false
                }]
            }]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Klasyfikowanie i diagnozowanie alertu

Alert wskazuje, że wykryto nieprawidłowy wzrost szybkości żądania, który nie powiódł się. Jest prawdopodobne, że istnieje jakiś problem z aplikacją lub jej środowiskiem.

Aby zbadać dalej, kliknij "Wyświetl pełne szczegóły w usłudze Application Insights" linki na tej stronie spowoduje, że bezpośrednio do [strony wyszukiwania](../../azure-monitor/app/diagnostic-search.md) filtrowane do odpowiednich żądań, wyjątku, zależności lub śladów. 

Można również otworzyć [witrynę Azure portal](https://portal.azure.com), przejdź do zasobu usługi Application Insights dla aplikacji i otwórz stronę Błędy.

Kliknięcie na "Diagnozowanie awarii" pomoże Ci uzyskać więcej szczegółów i rozwiązać problem.

[![](./media/proactive-failure-diagnostics/051.png "Diagnostic search")](./media/proactive-failure-diagnostics/051.png#lightbox)

Z procentu żądań i liczby użytkowników, których dotyczy problem, możesz zdecydować, jak pilny jest problem. W powyższym przykładzie wskaźnik awaryjności wynoszący 78,5% porównuje się z normalną szybkością 2,2%, wskazuje, że dzieje się coś złego. Z drugiej strony, tylko 46 użytkowników zostało dotkniętych. Jeśli była to Twoja aplikacja, możesz ocenić, jak poważna jest to aplikacja.

W wielu przypadkach będzie można szybko zdiagnozować problem od nazwy żądania, wyjątku, awarii zależności i danych śledzenia dostarczonych.

W tym przykładzie wystąpił wyjątek z bazy danych SQL z powodu osiągnięcia limitu żądań.

[![](./media/proactive-failure-diagnostics/052.png "Failed request details")](./media/proactive-failure-diagnostics/052.png#lightbox)

## <a name="review-recent-alerts"></a>Przeglądanie ostatnich alertów

Kliknij **pozycję Alerty** na stronie zasobu usługi Application Insights, aby przejść do najnowszych alertów zwolnionych:

[![](./media/proactive-failure-diagnostics/070.png "Alerts summary")](./media/proactive-failure-diagnostics/070.png#lightbox)

## <a name="whats-the-difference-"></a>Jaka jest różnica ...
Inteligentne wykrywanie anomalii awarii uzupełnia inne podobne, ale odrębne funkcje usługi Application Insights.

* [Alerty metryki](../../azure-monitor/app/alerts.md) są ustawiane przez Ciebie i mogą monitorować szeroki zakres metryk, takich jak obłożenie procesora CPU, szybkość żądania, czas ładowania strony i tak dalej. Można ich użyć, aby ostrzec, na przykład, jeśli chcesz dodać więcej zasobów. Z drugiej strony inteligentne wykrywanie anomalii awarii obejmuje niewielki zakres krytycznych metryk (obecnie tylko nie powiodło się szybkość żądania), przeznaczone do powiadamiania w sposób w czasie zbliżonym do rzeczywistego, gdy nie powiodło się stawki żądania aplikacji sieci web zwiększa się w porównaniu do normalnego zachowania aplikacji sieci web. W przeciwieństwie do alertów metryk inteligentne wykrywanie automatycznie ustawia i aktualizuje progi w odpowiedzi zmiany w zachowaniu. Inteligentne wykrywanie uruchamia również pracę diagnostyczną, oszczędzając czas w rozwiązywaniu problemów.

* [Inteligentne wykrywanie anomalii wydajności](proactive-performance-diagnostics.md) używa również analizy maszynowej do odnajdywanie nietypowych wzorców w metrykach i nie jest wymagana żadna konfiguracja. Jednak w przeciwieństwie do inteligentnego wykrywania anomalii awarii, celem inteligentnego wykrywania anomalii wydajności jest znalezienie segmentów kolektora użycia, które mogą być źle obsługiwane - na przykład przez określone strony w określonym typie przeglądarki. Analiza jest wykonywana codziennie, a jeśli zostanie znaleziony jakikolwiek wynik, może to być znacznie mniej pilne niż alert. Z drugiej strony analiza anomalii awarii jest wykonywana w sposób ciągły na przychodzących danych aplikacji i zostanie powiadomiony w ciągu kilku minut, jeśli wskaźniki awarii serwera są większe niż oczekiwano.

## <a name="if-you-receive-a-smart-detection-alert"></a>Jeśli zostanie wyświetlony alert inteligentnego wykrywania
*Dlaczego otrzymałem ten alert?*

* Firma Wekryliśmy nieprawidłowy wzrost wskaźnika liczby żądań zakończonych niepowodzeniem w porównaniu z normalną wartością bazową poprzedniego okresu. Po analizie błędów i skojarzonych danych aplikacji uważamy, że istnieje problem, który należy przeanalizować.

*Czy powiadomienie oznacza, że na pewno mam problem?*

* Staramy się ostrzegać o zakłóceniu lub degradacji aplikacji, ale tylko Ty możesz w pełni zrozumieć semantytykę i wpływ na aplikację lub użytkowników.

*Tak, patrzysz na moje dane aplikacji?*

* Nie. Usługa jest całkowicie automatyczna. Tylko Ty otrzymujesz powiadomienia. Twoje dane są [prywatne](../../azure-monitor/app/data-retention-privacy.md).

*Czy muszę subskrybować ten alert?*

* Nie. Każda aplikacja, która wysyła dane żądania ma regułę alertu inteligentnego wykrywania.

*Czy mogę zrezygnować z subskrypcji lub otrzymywać powiadomienia wysyłane do moich współpracowników?*

* Tak, w regułach alertów kliknij regułę inteligentnego wykrywania, aby ją skonfigurować. Można wyłączyć alert lub zmienić adresatów dla alertu.

*Zgubiłem wiadomość e-mail. Gdzie mogę znaleźć powiadomienia w portalu?*

* W dziennikach aktywności. Na platformie Azure otwórz zasób usługi Application Insights dla aplikacji, a następnie wybierz dzienniki aktywności.

*Niektóre alerty dotyczą znanych problemów i nie chcę ich otrzymywać.*

* Można użyć funkcji pomijania [reguł akcji alertów.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules)

## <a name="next-steps"></a>Następne kroki
Te narzędzia diagnostyczne ułatwiają sprawdzanie danych z aplikacji:

* [Eksplorator metryki](../../azure-monitor/platform/metrics-charts.md)
* [Eksplorator wyszukiwania](../../azure-monitor/app/diagnostic-search.md)
* [Analytics - potężny język zapytań](../../azure-monitor/log-query/get-started-portal.md)

Inteligentne wykrywanie jest automatyczne. Ale może chcesz skonfigurować kilka alertów więcej?

* [Ręcznie skonfigurowane alerty metryk](../../azure-monitor/app/alerts.md)
* [Testy dostępności sieci Web](../../azure-monitor/app/monitor-web-app-availability.md)
