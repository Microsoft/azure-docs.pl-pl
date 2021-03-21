---
title: Anomalie wykrywania inteligentnego, w Application Insights | Microsoft Docs
description: Ostrzega o nietypowych zmianach w przypadku żądań zakończonych niepowodzeniem w aplikacji sieci Web i umożliwia analizę diagnostyki. Nie jest wymagana żadna konfiguracja.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 0f4de3aaba4acf86df37048134089326196e87ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587549"
---
# <a name="smart-detection---failure-anomalies"></a>Wykrywanie inteligentne — anomalie błędów
[Application Insights](./app-insights-overview.md) automatycznie alarmuje użytkownika w czasie niemal rzeczywistym, jeśli aplikacja sieci Web napotyka nietypowy wzrost liczby żądań zakończonych niepowodzeniem. Wykrywa nietypowy wzrost liczby żądań HTTP lub wywołań zależności zgłaszanych jako zakończone niepowodzeniem. W przypadku żądań żądania zakończone niepowodzeniem zazwyczaj mają kody odpowiedzi 400 lub wyższe. Aby ułatwić Klasyfikacja i zdiagnozowanie problemu, analiza charakterystyk błędów i powiązanych danych aplikacji znajduje się w szczegółach alertu. Istnieją także linki do portalu Application Insights w celu przeprowadzenia dalszej diagnostyki. Ta funkcja nie wymaga konfigurowania ani konfiguracji, ponieważ używa algorytmów uczenia maszynowego do przewidywania normalnego współczynnika niepowodzeń.

Ta funkcja działa w przypadku dowolnej aplikacji sieci Web, hostowanej w chmurze lub na własnych serwerach, która generuje żądanie aplikacji lub dane zależności. Na przykład jeśli masz rolę procesu roboczego, która wywołuje [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) lub [TrackDependency ()](./api-custom-events-metrics.md#trackdependency).

Po skonfigurowaniu [Application Insights dla projektu](./app-insights-overview.md), a jeśli aplikacja generuje pewną minimalną ilość danych, Inteligentne wykrywanie anomalii awarii trwa 24 godziny, aby poznać normalne zachowanie aplikacji, zanim zostanie ona przełączona i będzie mogła wysyłać alerty.

Oto przykładowy Alert:

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="Przykładowy alert wykrywania inteligentnego pokazujący, że analiza klastra nie powiodła się." lightbox="./media/proactive-failure-diagnostics/013.png":::

Szczegóły alertu pomogą Ci:

* Współczynnik błędów w porównaniu z normalnym zachowaniem aplikacji.
* Liczba użytkowników, których to dotyczy, aby wiedzieć, jak się martwić.
* Wzorzec charakterystyczny skojarzony z błędami. W tym przykładzie istnieje określony kod odpowiedzi, nazwa żądania (operacja) i wersja aplikacji. To natychmiast informuje o tym, gdzie rozpocząć wyszukiwanie kodu. Inne możliwości mogą być konkretnymi przeglądarkami lub systemami operacyjnymi klienta.
* Wyjątek, ślady dziennika i awaria zależności (bazy danych lub inne składniki zewnętrzne), które są wyświetlane do skojarzenia z błędami.
* Łączy bezpośrednio z odpowiednimi wyszukiwaniami danych w Application Insights.

## <a name="benefits-of-smart-detection"></a>Zalety wykrywania inteligentnego
Zwykłe [alerty dotyczące metryk](../alerts/alerts-log.md) informują o problemie. Jednak funkcja inteligentnego wykrywania uruchamia pracę diagnostykę, wykonując wiele analiz, które w przeciwnym razie trzeba wykonać samodzielnie. Wyniki są zapakowane w sposób uporządkowany, co pomaga szybko uzyskać dostęp do katalogu głównego problemu.

## <a name="how-it-works"></a>Jak to działa
Inteligentne wykrywanie monitoruje dane otrzymane z aplikacji, a w szczególności współczynnik awarii. Ta zasada zlicza liczbę żądań, dla których `Successful request` Właściwość ma wartość false, oraz liczbę wywołań zależności, dla których `Successful call` Właściwość ma wartość false. W przypadku żądań domyślnie `Successful request == (resultCode < 400)` (chyba że kod niestandardowy nie został zapisany do [filtrowania](./api-filtering-sampling.md#filtering) lub generowania własnych wywołań [TrackRequest](./api-custom-events-metrics.md#trackrequest) ). 

Wydajność aplikacji ma typowy wzorzec zachowania. Niektóre żądania lub wywołania zależności będą bardziej podatne na awarie niż inne; a ogólna częstotliwość niepowodzeń może być większa w miarę wzrostu obciążenia. Funkcja inteligentnego wykrywania używa uczenia maszynowego, aby znaleźć te anomalie.

Gdy dane są dostarczane do Application Insights z aplikacji sieci Web, funkcja inteligentnego wykrywania porównuje bieżące zachowanie z wzorcami widocznymi w ciągu ostatnich kilku dni. W przypadku nienormalnego wzrostu współczynnika błędów w porównaniu z poprzednią wydajnością jest wyzwalana analiza.

Po wyzwoleniu analizy usługa wykonuje analizę klastra dla żądania zakończonego niepowodzeniem, aby spróbować zidentyfikować wzorzec wartości, które opisują błędy. 

W powyższym przykładzie analiza wykryła, że większość błędów dotyczy określonego kodu wynikowego, nazwy żądania, hosta adresu URL serwera i wystąpienia roli. 

Gdy usługa jest Instrumentacją tych wywołań, Analizator szuka wyjątku i awarii zależności, które są skojarzone z żądaniami w klastrze, który zidentyfikował, wraz z przykładem wszystkich dzienników śledzenia skojarzonych z tymi żądaniami.

Wyniki analizy są wysyłane do Ciebie jako alert, chyba że skonfigurowano go w taki sposób, aby nie został skonfigurowany.

Podobnie jak w przypadku [alertów ustawionych ręcznie](../alerts/alerts-log.md), można sprawdzić stan wygenerowanego alertu, który można rozwiązać, jeśli problem został rozwiązany. Skonfiguruj reguły alertów na stronie Alerty zasobu Application Insights. Ale w przeciwieństwie do innych alertów nie trzeba konfigurować ani konfigurować inteligentnego wykrywania. Jeśli chcesz, możesz ją wyłączyć lub zmienić docelowe adresy e-mail.

### <a name="alert-logic-details"></a>Szczegóły logiki alertu

Alerty są wyzwalane przez swój własny algorytm uczenia maszynowego, dlatego nie można udostępnić dokładnych szczegółów implementacji. Dzięki temu wiemy, że czasami trzeba dowiedzieć się więcej o tym, jak działa podstawowa logika. Podstawowe czynniki, które są oceniane, aby określić, czy alert powinien być wyzwalany: 

* Analiza procentu niepowodzenia żądań/zależności w przedziale czasu na 20 minut.
* Porównanie procentu niepowodzenia z ostatnich 20 minut z szybkością w ciągu ostatnich 40 minut i ostatnich siedmiu dni i wyszukiwanie znaczących odchyleń, które przekraczają X-Times w przypadku odchylenia standardowego.
* Użycie limitu adaptacyjnego dla procentu minimalnej awarii, który różni się w zależności od liczby żądań/zależności aplikacji.
* Istnieje logika, która może automatycznie rozpoznać wygenerowanego stanu monitora alertów, jeśli problem nie zostanie już wykryty przez 8-24 godzin.
  Uwaga: w bieżącym projekcie. powiadomienie lub akcja nie będą wysyłane po rozwiązaniu alertu inteligentnego wykrywania. Możesz sprawdzić, czy w Azure Portal został rozwiązany alert wykrywania inteligentnego.

## <a name="configure-alerts"></a>Konfigurowanie alertów

Regułę alertu inteligentnego wykrywania można wyłączyć z poziomu portalu lub za pomocą Azure Resource Manager ([Zobacz przykład szablonu](./proactive-arm-config.md)).

Ta reguła alertu jest tworzona za pomocą skojarzonej [grupy akcji](../alerts/action-groups.md) o nazwie "Application Insights Smart Detection", która zawiera akcje poczty e-mail i elementu webhook, i można ją rozszerzyć, aby wyzwolić dodatkowe akcje po uruchomieniu alertu.

> [!NOTE]
> Powiadomienia e-mail wysyłane z tej reguły alertów są teraz domyślnie wysyłane do użytkowników skojarzonych z czytnikiem monitorowania subskrypcji i monitorowaniem ról współautor. Więcej informacji na ten temat można znaleźć [tutaj](./proactive-email-notification.md).
> Powiadomienia wysyłane z tej reguły alertu są zgodne ze [wspólnym schematem alertów](../alerts/alerts-common-schema.md).
>

Otwórz stronę alerty. Reguły alertów o anomalii błędów są uwzględniane wraz z alertami, które zostały ustawione ręcznie, i można sprawdzić, czy jest ona aktualnie w stanie alertu.

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="Na stronie zasób Application Insights kliknij kafelek alerty, a następnie Zarządzaj regułami alertów." lightbox="./media/proactive-failure-diagnostics/021.png":::

Kliknij alert, aby go skonfigurować.

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="Ekran konfiguracji reguły." lightbox="./media/proactive-failure-diagnostics/032.png":::

Należy zauważyć, że można wyłączyć lub usunąć regułę alertów o anomalii niepowodzeń, ale nie można utworzyć kolejnej na tym samym Application Insights zasobów.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Przykład ładunku elementu webhook alertu dotyczącego anomalii awarii

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
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
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
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Klasyfikacja i diagnozowanie alertu

Alert wskazuje, że wykryto nietypowy wzrost liczby żądań zakończonych niepowodzeniem. Prawdopodobnie wystąpił problem z aplikacją lub jej środowiskiem.

Aby dowiedzieć się więcej, kliknij pozycję "Wyświetl pełne szczegóły w Application Insights" linki na tej stronie spowoduje przejście do [strony wyszukiwania](./diagnostic-search.md) , która przefiltrowana do odpowiednich żądań, wyjątków, zależności lub śladów. 

Możesz również otworzyć [Azure Portal](https://portal.azure.com), przejdź do zasobu Application Insights dla aplikacji, a następnie otwórz stronę błędy.

Kliknięcie przycisku "Diagnozuj błędy" pomoże Ci uzyskać więcej szczegółów i rozwiązać problem.

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="Wyszukiwanie diagnostyczne." lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

Od procentu żądań i liczby użytkowników, których to dotyczy, możesz zdecydować, jak pilnuje problem. W powyższym przykładzie Współczynnik awaryjności 78,5% porównuje z normalną szybkością 2,2%, wskazuje, że wystąpił problem. Z drugiej strony dotyczyły tylko 46 użytkowników. Jeśli była Twoją aplikacją, możesz ocenić, jak poważny jest.

W wielu przypadkach będzie możliwe szybkie zdiagnozowanie problemu przy użyciu nazwy żądania, wyjątku, błędu zależności i danych śledzenia.

W tym przykładzie wystąpił wyjątek z SQL Database z powodu osiągnięcia limitu żądań.

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="Szczegóły żądania zakończonego niepowodzeniem." lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>Przejrzyj Ostatnie alerty

Kliknij pozycję **alerty** na stronie zasobów Application Insights, aby przejść do ostatnio wygenerowanego alertu:

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="Podsumowanie alertów." lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>Jaka jest różnica...
Inteligentne wykrywanie anomalii o awarii uzupełnia inne podobne, ale odrębne funkcje Application Insights.

* [alerty metryk](../alerts/alerts-log.md) są ustawiane przez użytkownika i mogą monitorować szeroką gamę metryk, takich jak użycie procesora CPU, stawki żądań, czasy ładowania stron itd. Można z nich skorzystać, aby ostrzec użytkownika, na przykład jeśli trzeba dodać więcej zasobów. Z kolei funkcja inteligentnego wykrywania anomalii o niepowodzeń obejmuje niewielki zakres metryk krytycznych (aktualnie nie zakończonych niepowodzeniem), dzięki czemu można powiadamiać niemal w czasie rzeczywistym o tym, kiedy szybkość żądań zakończonych niepowodzeniem w aplikacji sieci Web zostanie zwiększona w porównaniu z normalnym zachowaniem aplikacji sieci Web. W przeciwieństwie do alertów dotyczących metryk, funkcja inteligentnego wykrywania automatycznie ustawia i aktualizuje progi w reakcji na zmiany w zachowaniu. Funkcja inteligentnego wykrywania uruchamia również pracę diagnostyczną, oszczędzając czas rozwiązywania problemów.

* [Inteligentne wykrywanie anomalii wydajności](proactive-performance-diagnostics.md) korzysta również z analizy maszynowej w celu odnajdywania nietypowych wzorców w metrykach i nie wymaga konfiguracji. Jednak w przeciwieństwie do inteligentnego wykrywania anomalii związanych z awariami, cel inteligentnego wykrywania anomalii wydajności polega na znalezieniu segmentów przełożenia użycia, które mogą zostać nieprawidłowo obsłużone — na przykład przez określone strony w określonym typie przeglądarki. Analiza jest wykonywana codziennie i w przypadku znalezienia dowolnego wyniku prawdopodobnie będzie znacznie mniej pilna niż alert. Z drugiej strony analiza anomalii związanych z awariami jest wykonywana w sposób ciągły na przychodzących danych aplikacji, a użytkownik zostanie powiadomiony w ciągu kilku minut, jeśli stawki za awarie serwera są większe niż oczekiwano.

## <a name="if-you-receive-a-smart-detection-alert"></a>Jeśli otrzymasz alert Smart Detection
*Dlaczego otrzymuję ten alert?*

* Wykryliśmy nietypowy wzrost liczby żądań zakończonych niepowodzeniem w porównaniu z normalną linią bazową poprzedniego okresu. Po analizie błędów i skojarzonych z nią danych aplikacji uważamy, że występuje problem, którego należy szukać.

*Czy powiadomienie oznacza, że występuje problem?*

* Spróbujemy ostrzec o zakłóceniach aplikacji lub obniżeniu wydajności, ale tylko ty możesz zrozumieć semantykę i wpływ na aplikację lub użytkowników.

*Czy przeglądasz dane aplikacji?*

* Nie. Usługa jest całkowicie automatyczna. Tylko otrzymujesz powiadomienia. Twoje dane są [prywatne](./data-retention-privacy.md).

*Czy muszę subskrybować ten alert?*

* Nie. Każda aplikacja, która wysyła dane żądania ma regułę alertu inteligentnego wykrywania.

*Czy mogę anulować subskrypcję lub otrzymywać powiadomienia wysyłane do moich współpracowników?*

* Tak, w regułach alertów kliknij regułę wykrywania inteligentnego, aby ją skonfigurować. Można wyłączyć alert lub zmienić odbiorców alertu.

*Wiadomość e-mail została utracona. Gdzie mogę znaleźć powiadomienia w portalu?*

* W dziennikach aktywności. Na platformie Azure Otwórz zasób Application Insights dla aplikacji, a następnie wybierz pozycję Dzienniki aktywności.

*Niektóre alerty dotyczą znanych problemów i nie chcę ich otrzymywać.*

* Można użyć funkcji pomijania [reguł akcji alertów](../alerts/alerts-action-rules.md) .

## <a name="next-steps"></a>Następne kroki
Te narzędzia diagnostyczne ułatwiają inspekcję danych w aplikacji:

* [Eksplorator metryk](../essentials/metrics-charts.md)
* [Eksplorator wyszukiwania](./diagnostic-search.md)
* [Analiza — zaawansowany język zapytań](../logs/log-analytics-tutorial.md)

Inteligentne wykrywanie są automatyczne. Ale być może chcesz skonfigurować więcej alertów?

* [Ręcznie skonfigurowane alerty metryk](../alerts/alerts-log.md)
* [Testy dostępności sieci Web](./monitor-web-app-availability.md)