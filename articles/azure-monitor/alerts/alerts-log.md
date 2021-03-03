---
title: Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure Monitor | Microsoft Docs
description: Użyj Azure Monitor, aby tworzyć i wyświetlać reguły alertów dzienników oraz nimi zarządzać
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 2cff0773883f7de5788bca5e4e3f680818e2bd77
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734880"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure Monitor

## <a name="overview"></a>Omówienie

Alerty dzienników umożliwiają użytkownikom użycie zapytania [log Analytics](../logs/log-analytics-tutorial.md) w celu obliczenia dzienników zasobów co określoną częstotliwość i wyzwolenia alertu na podstawie wyników. Reguły mogą wyzwalać jedną lub więcej akcji przy użyciu [grup akcji](./action-groups.md). [Dowiedz się więcej o funkcjach i terminologii alertów dzienników](./alerts-unified-log.md).

W tym artykule pokazano, jak tworzyć alerty dzienników i zarządzać nimi za pomocą Azure Monitor. Reguły alertów są definiowane przez trzy składniki:
- Cel: określony zasób platformy Azure do monitorowania.
- Kryteria: Logika do obliczenia. W przypadku spełnienia alertu.  
- Akcja: powiadomienia lub Automatyzacja poczty e-mail, wiadomości SMS, elementu webhook itd.

Reguły alertów dzienników można także tworzyć przy użyciu szablonów Azure Resource Manager, które są opisane w [osobnym artykule](../alerts/alerts-log-create-templates.md).

> [!NOTE]
> Dane dziennika z [obszaru roboczego log Analytics](../logs/log-analytics-tutorial.md) mogą być wysyłane do magazynu metryk Azure monitor. Alerty metryk mają [różne zachowanie](./alerts-metric-overview.md), co może być bardziej odpowiednie w zależności od danych, z którymi pracujesz. Aby uzyskać informacje na temat tego, co i jak można kierować dzienniki do metryk, zobacz [alert metryki dla dzienników](./alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Tworzenie reguły alertu dziennika przy użyciu Azure Portal

Poniżej przedstawiono procedurę tworzenia zapytań dotyczących alertów:

1. Przejdź do zasobu, dla którego chcesz otrzymywać alerty. Należy rozważyć skonfigurowanie reguł alertów dla wielu zasobów, w miarę możliwości zaznaczając subskrypcję lub zakres grupy zasobów. Alerty dotyczące wielu zasobów zmniejszają koszty i muszą zarządzać wieloma regułami alertów.
1. W obszarze **monitor** wybierz pozycję **dzienniki**.
1. Wykonaj zapytanie o dane dziennika, które mogą wskazywać na problem. Możesz użyć [przykładu zapytania dotyczącego alertu](../logs/example-queries.md) , aby zrozumieć, co można odkryć lub [zacząć pisać przy tworzeniu własnych zapytań](../logs/log-analytics-tutorial.md). Ponadto [dowiedz się, jak tworzyć zoptymalizowane zapytania alertów](alerts-log-query.md).
1. Naciśnij przycisk "+ Nowa reguła alertu", aby uruchomić przepływ tworzenia alertu.

    ![Alert Log Analytics — Ustawianie](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> Zaleca się tworzenie alertów na dużą skalę w przypadku korzystania z trybu dostępu do zasobów dzienników, które są uruchamiane na wielu zasobach przy użyciu grupy zasobów lub zakresu subskrypcji. Generowanie alertów w skali zmniejsza koszty związane z zarządzaniem regułami. Aby możliwe było kierowanie zasobów, w wynikach należy uwzględnić kolumnę identyfikator zasobu. [Dowiedz się więcej na temat dzielenia alertów według wymiarów](./alerts-unified-log.md#split-by-alert-dimensions).

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Alert dziennika dla Log Analytics i Application Insights

1. Jeśli Składnia zapytania jest poprawna, dane historyczne zapytania są wyświetlane jako Graf z opcją dostosowywania okresu wykresu z ostatnich sześciu godzin do ostatniego tygodnia.
 
    Jeśli wyniki zapytania zawierają dane sumaryczne lub kolumny specyficzne dla [projektu](/azure/kusto/query/projectoperator) bez kolumny czas, wykres pokazuje pojedynczą wartość.

    ![Konfigurowanie reguły alertu](media/alerts-log/AlertsPreviewAlertLog.png)

1. Wybierz przedział czasu, do którego chcesz ocenić określony warunek, przy użyciu opcji [**okres**](./alerts-unified-log.md#query-time-range) .

1. Alerty dzienników mogą opierać się na dwóch typach [**miar**](./alerts-unified-log.md#measure):
    1. **Liczba wyników** — liczba rekordów zwróconych przez zapytanie.
    1. **Pomiar**  -  metryki *Wartość zagregowana* jest obliczana przy użyciu podsumowania pogrupowane według wybranych wyrażeń i zaznaczania [bin ()](/azure/kusto/query/binfunction) . Na przykład:

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. W przypadku logiki alertów pomiarów metryk można opcjonalnie określić sposób [dzielenia alertów według wymiarów](./alerts-unified-log.md#split-by-alert-dimensions) przy użyciu opcji **Agreguj dla** . Wyrażenie grupowania wierszy musi być unikatowe i posortowane.

    > [!NOTE]
    > Ponieważ funkcja [bin ()](/azure/kusto/query/binfunction) może powodować nierówne przedziały czasu, usługa alertów automatycznie konwertuje funkcję [bin ()](/azure/kusto/query/binfunction) na funkcję [bin_at ()](/azure/kusto/query/binatfunction) na odpowiednim czasie w czasie wykonywania, aby zapewnić wyniki ze stałym punktem.

    > [!NOTE]
    > Podział według wymiarów alertów jest dostępny tylko dla bieżącego interfejsu API scheduledQueryRules. W przypadku korzystania z starszego [interfejsu API log Analytics alertów](./api-alerts.md)należy przełączyć. [Dowiedz się więcej o przełączaniu](./alerts-log-api-switch.md). Generowanie alertów zasobów na dużą skalę jest obsługiwane tylko w wersji interfejsu API `2020-05-01-preview` i nowszych.

    ![Agreguj dla opcji](media/alerts-log/aggregate-on.png)

1. Następnie na podstawie danych w wersji zapoznawczej Ustaw [ **operatora**, **wartość progową**](./alerts-unified-log.md#threshold-and-operator)i [**częstotliwość**](./alerts-unified-log.md#frequency).

1. Opcjonalnie można również ustawić [liczbę naruszeń wyzwalających alert](./alerts-unified-log.md#number-of-violations-to-trigger-alert) przy użyciu **łącznych lub kolejnych naruszeń**.

1. Kliknij **Gotowe**. 

1. Zdefiniuj nazwę i **Opis** **reguły alertu**, a następnie wybierz **ważność** alertu. Te szczegóły są używane we wszystkich akcjach alertów. Ponadto możesz zrezygnować z aktywacji reguły alertu podczas tworzenia, wybierając pozycję **Włącz regułę podczas tworzenia**.

1. Wybierz, jeśli chcesz pominąć akcje reguły przez czas po uruchomieniu alertu, użyj opcji [**pomijania alertów**](./alerts-unified-log.md#state-and-resolving-alerts) . Zasada będzie nadal działać i tworzyć alerty, ale akcje nie zostaną wyzwolone w celu uniemożliwienia szumu. Wartość wyciszania akcji musi być większa niż częstotliwość obowiązywania alertu.

    ![Pomijaj alerty dla alertów dziennika](media/alerts-log/AlertsPreviewSuppress.png)

1. Określ, czy reguła alertu powinna wyzwalać co najmniej jedną [**grupę akcji**](./action-groups.md#webhook) po spełnieniu warunku alertu.

    > [!NOTE]
    > Ograniczenia dotyczące działań, które można wykonać, zapoznaj się z [limitami usługi subskrypcji platformy Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) .  

1. Opcjonalnie można dostosować akcje w regułach alertów dziennika:

    - **Niestandardowy temat wiadomości e-mail**: przesłania *wiadomość e-mail* dotyczącą akcji poczty e-mail. Nie można modyfikować treści poczty, a to pole **nie jest przeznaczone do adresów e-mail**.
    - **Uwzględnij niestandardowy ładunek JSON**: przesłania kod JSON elementu webhook używany przez grupy akcji, przy założeniu, że grupa akcji zawiera akcję elementu webhook. Dowiedz się więcej o [akcji elementu webhook dla alertów dziennika](./alerts-log-webhook.md).

    ![Zastąpienia akcji dla alertów dziennika](media/alerts-log/AlertsPreviewOverrideLog.png)

1. Jeśli wszystkie pola są prawidłowo ustawione, można kliknąć przycisk **Utwórz regułę alertu** i utworzyć alert.

    W ciągu kilku minut alert jest aktywny i wyzwalacze opisane wcześniej.

    ![Tworzenie reguły](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>Tworzenie alertów dziennika dla Log Analytics i Application Insights z zarządzania alertami

> [!NOTE]
> Tworzenie z poziomu zarządzania alertami nie jest obecnie obsługiwane w przypadku dzienników zorientowanych na zasoby

1. W [portalu](https://portal.azure.com/)wybierz pozycję **Monitoruj** , a następnie wybierz pozycję **alerty**.

    ![Monitorowanie](media/alerts-log/AlertsPreviewMenu.png)

1. Wybierz pozycję **Nowa reguła alertu**. 

    ![Dodawanie alertu](media/alerts-log/AlertsPreviewOption.png)

1. Zostanie wyświetlone okienko **Tworzenie alertu** . Ma cztery części: 
    - Zasób, którego dotyczy alert.
    - Warunek do sprawdzenia.
    - Akcje do wykonania, jeśli warunek ma wartość true.
    - Szczegóły do nazwy i opisujące alert. 

    ![Tworzenie reguły](media/alerts-log/AlertsPreviewAdd.png)

1. Naciśnij przycisk **Wybierz zasób** . Filtruj, wybierając pozycję *subskrypcja*, *Typ zasobu* i wybierz zasób. Upewnij się, że zasób ma dostępne dzienniki.

   ![Wybierz zasób](media/alerts-log/Alert-SelectResourceLog.png)

1. Następnie użyj przycisku Dodaj **warunek** , aby wyświetlić listę opcji sygnałów dostępnych dla zasobu. Wybierz opcję **Wyszukiwanie w dzienniku niestandardowym** .

   ![Wybierz zasób — wyszukiwanie w dzienniku niestandardowym](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > W portalu alertów są wyświetlane zapisane zapytania z Log Analytics i Application Insights i mogą być używane jako zapytania dotyczące alertów szablonu.

1. Po wybraniu Zapisz, wklej lub Edytuj zapytanie dotyczące alertów w polu **zapytania wyszukiwania** .

1. Przejdź do następnych kroków opisanych w [ostatniej sekcji](#log-alert-for-log-analytics-and-application-insights).

### <a name="log-alert-for-all-other-resource-types"></a>Alert dotyczący dziennika dla wszystkich innych typów zasobów

> [!NOTE]
> Obecnie nie ma dodatkowych opłat za wersję interfejsu API `2020-05-01-preview` i alerty dzienników zorientowanych na zasoby.  Cennik funkcji, które są w wersji zapoznawczej, zostanie ogłoszony w przyszłości, a powiadomienie podane przed rozpoczęciem rozliczania. W przypadku wybrania opcji kontynuowania korzystania z nowej wersji interfejsu API i zdarzeń dzienników skoncentrowanych na zasobach po upływie okresu wypowiedzenia zostanie naliczona stawka mająca zastosowanie.

1. Rozpocznij od karty **warunek** :

    1. Sprawdź, czy wartość [**miary**](./alerts-unified-log.md#measure), [**typ agregacji**](./alerts-unified-log.md#aggregation-type)i [**stopień szczegółowości agregacji**](./alerts-unified-log.md#aggregation-granularity) są poprawne. 
        1. Domyślnie reguła zlicza liczbę wyników w ciągu ostatnich 5 minut.
        1. Jeśli wykryjesz podsumowanie wyników zapytania, reguła zostanie automatycznie zaktualizowana w ciągu kilku sekund w celu przechwycenia.

    1. W razie konieczności wybierz pozycję [podział alertów według wymiarów](./alerts-unified-log.md#split-by-alert-dimensions): 
       - **Kolumna Identyfikator zasobu** jest wybierana automatycznie, jeśli zostanie wykryta, i zmieni kontekst wywoływanego alertu na zasób rekordu. 
       - **Kolumnę identyfikator zasobu** można usunąć z alertów dotyczących subskrypcji lub grup zasobów. Cofanie wyboru jest przydatne, gdy wyniki zapytania są oparte na zasobach krzyżowych. Na przykład zapytanie sprawdzające, czy 80% maszyn wirtualnych grupy zasobów ma duże użycie procesora CPU.
       - Można również wybrać maksymalnie sześć kolejnych podziałów dla dowolnego typu kolumn liczbowych lub tekstowych przy użyciu tabeli Dimensions.
       - Alerty są uruchamiane oddzielnie, zgodnie z podziałem na podstawie unikatowych kombinacji i ładunku alertów zawiera te informacje.
    
        ![Wybieranie parametrów agregacji i ich podział](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. Wykres **podglądu** pokazuje wyniki oceny zapytania w czasie. Możesz zmienić okres wykresu lub wybrać inną serię czasową, która wynika z unikatowego podziału alertu według wymiarów.

        ![Podgląd wykresu](media/alerts-log/preview-chart.png)

    1. Następnie na podstawie danych w wersji zapoznawczej Ustaw **logikę alertów**. [ **Operator**, **wartość progowa**](./alerts-unified-log.md#threshold-and-operator)i [**częstotliwość**](./alerts-unified-log.md#frequency).

        ![Podgląd wykresu z logiką progu i alertu](media/alerts-log/chart-and-alert-logic.png)

    1. Opcjonalnie możesz ustawić [**liczbę naruszeń, aby wyzwolić alert**](./alerts-unified-log.md#number-of-violations-to-trigger-alert) w sekcji **Opcje zaawansowane** .
    
        ![Opcje zaawansowane](media/alerts-log/advanced-options.png)

1. Na karcie **Akcje** wybierz lub Utwórz wymagane [grupy akcji](./action-groups.md).

    ![Karta akcje](media/alerts-log/actions-tab.png)

1. Na karcie **szczegóły** Zdefiniuj **szczegóły reguły alertu** i **szczegóły projektu**. Opcjonalnie można określić, czy nie **uruchamiać teraz** ani [**wyciszać akcji**](./alerts-unified-log.md#state-and-resolving-alerts) przez okres po uruchomieniu reguły alertu.

    > [!NOTE]
    > Reguły alertów dziennika są obecnie bezstanowe i wyzwalają akcję za każdym razem, gdy zostanie utworzony alert, chyba że zostanie zdefiniowany wyciszenie.

    ![Karta Szczegóły](media/alerts-log/details-tab.png)

1. Na karcie **Tagi** Ustaw wszystkie wymagane znaczniki w zasobie reguły alertu.

    ![Karta Tagi](media/alerts-log/tags-tab.png)

1. Na karcie **Recenzja + tworzenie** zostanie uruchomiona weryfikacja i będzie ona informować o ewentualnych problemach. Przejrzyj i zatwierdź definicję reguły.
1. Jeśli wszystkie pola są poprawne, wybierz przycisk **Utwórz** i Ukończ Tworzenie reguły alertu. Wszystkie alerty można wyświetlić z poziomu zarządzania alertami.
 
    ![Przejrzyj i Utwórz kartę](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Wyświetlanie & Zarządzanie alertami dzienników w programie Azure Portal

1. W [portalu](https://portal.azure.com/)wybierz odpowiedni zasób lub usługę **monitorowania** . Następnie wybierz pozycję **alerty** w sekcji monitorowanie.

1. W obszarze Zarządzanie alertami są wyświetlane wszystkie alerty, które zostały wyzwolone. [Dowiedz się więcej o zarządzaniu alertami](alerts-managing-alert-instances.md).

    > [!NOTE]
    > Reguły alertów dziennika są obecnie [bezstanowe i nie są rozwiązywane](./alerts-unified-log.md#state-and-resolving-alerts).

1. Aby edytować reguły, kliknij przycisk **Zarządzaj regułami alertów** na górnym pasku.

    ![ Zarządzaj regułami alertów](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>Zarządzanie alertami dzienników przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Program PowerShell nie jest obecnie obsługiwany w wersji interfejsu API `2020-05-01-preview`

Polecenia cmdlet programu PowerShell wymienione poniżej umożliwiają zarządzanie regułami przy użyciu [interfejsu API zaplanowanych reguł zapytań](/rest/api/monitor/scheduledqueryrules/).

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : polecenie cmdlet programu PowerShell, aby utworzyć nową regułę alertu dziennika.
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : polecenie cmdlet programu PowerShell służące do aktualizowania istniejącej reguły alertu dziennika.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry źródłowe alertu dziennika. Używane jako dane wejściowe za pomocą polecenia cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): polecenie cmdlet programu PowerShell do tworzenia lub aktualizowania obiektu określającego parametry harmonogramu dla alertu dziennika. Używane jako dane wejściowe za pomocą polecenia cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry akcji dla alertu dziennika. Używane jako dane wejściowe za pomocą polecenia cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry grup akcji dla alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) .
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry warunku wyzwalacza dla alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) .
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry warunku wyzwalacza metryki dla [alertu dziennika typu pomiaru metryki](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value). Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) .
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : polecenie cmdlet programu PowerShell, aby wyświetlić listę istniejących reguł alertów dziennika lub konkretnej reguły alertu dziennika
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : polecenie cmdlet programu PowerShell do włączania lub wyłączania reguły alertów dziennika
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): polecenie cmdlet programu PowerShell, aby usunąć istniejącą regułę alertu dziennika

> [!NOTE]
> Polecenia cmdlet programu PowerShell ScheduledQueryRules mogą zarządzać tylko regułami utworzonymi w bieżącym [interfejsie API reguł zaplanowanych zapytań](/rest/api/monitor/scheduledqueryrules/). Reguły alertów dziennika utworzone przy użyciu starszego [interfejsu API alertów log Analytics](./api-alerts.md) mogą być zarządzane tylko przy użyciu programu PowerShell tylko po [przełączeniu do interfejsu API zaplanowanych reguł zapytania](../alerts/alerts-log-api-switch.md)

Poniżej przedstawiono przykładowe kroki tworzenia reguły alertu dziennika przy użyciu programu PowerShell:

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

Poniżej przedstawiono przykładowe kroki tworzenia reguły alertu dziennika przy użyciu programu PowerShell z kwerendami między zasobami:

```powershell
$authorized = @ ("/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicewsCrossExample", "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceAppInsights")

$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews" -AuthorizedResource $authorized

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition
New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name" 
```

Możesz również utworzyć alert dziennika przy użyciu plików [szablonów i parametrów](./alerts-log-create-templates.md) przy użyciu programu PowerShell:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>Zarządzanie alertami dzienników przy użyciu interfejsu wiersza polecenia

> [!NOTE]
> Obsługa interfejsu wiersza polecenia platformy Azure jest dostępna tylko w przypadku wersji API scheduledQueryRules `2020-05-01-preview` i nowszych. Wersja interfejsu API poprzedniej może używać interfejsu wiersza polecenia Azure Resource Manager z szablonami, zgodnie z poniższym opisem. W przypadku korzystania ze starszego [interfejsu API log Analytics alertów](./api-alerts.md)należy przełączyć się do korzystania z interfejsu wiersza polecenia. [Dowiedz się więcej o przełączaniu](./alerts-log-api-switch.md).

W poprzednich sekcjach opisano sposób tworzenia i wyświetlania reguł alertów dzienników oraz zarządzania nimi przy użyciu Azure Portal. W tej sekcji opisano, jak wykonać to samo przy użyciu wieloplatformowego [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli). Najszybszym sposobem na rozpoczęcie korzystania z interfejsu wiersza polecenia platformy Azure jest [Azure Cloud Shell](../../cloud-shell/overview.md). W tym artykule będziemy używać Cloud Shell.

1. Przejdź do Azure Portal, wybierz pozycję **Cloud Shell**.

1. W wierszu polecenia można użyć poleceń z opcją, ``--help`` Aby dowiedzieć się więcej o poleceniu i sposobach ich użycia. Na przykład następujące polecenie pokazuje listę poleceń dostępnych do tworzenia i wyświetlania alertów dzienników oraz zarządzania nimi:

    ```azurecli
    az monitor scheduled-query --help
    ```

1. Można utworzyć regułę alertu dziennika, która monitoruje liczbę błędów zdarzeń systemowych:

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. Wszystkie alerty dzienników można wyświetlić w grupie zasobów za pomocą następującego polecenia:

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. Szczegóły konkretnej reguły alertu dziennika można zobaczyć przy użyciu nazwy lub identyfikatora zasobu reguły:

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. Regułę alertu dziennika można wyłączyć przy użyciu następującego polecenia:

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. Regułę alertu dziennika można usunąć za pomocą następującego polecenia:

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

Interfejs wiersza polecenia Azure Resource Manager można również użyć z plikami [szablonów](./alerts-log-create-templates.md) :

```azurecli
az login

az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

Po pomyślnym utworzeniu zostanie zwrócona 201. Po pomyślnym wykonaniu aktualizacji zostanie zwrócona 200.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [alertach dzienników](./alerts-unified-log.md).
* Tworzenie alertów dziennika przy użyciu [szablonów Azure Resource Manager](./alerts-log-create-templates.md).
* Informacje [o akcjach elementu webhook dla alertów dziennika](./alerts-log-webhook.md).
* Dowiedz się więcej o [zapytaniach dziennika](../logs/log-query-overview.md).
