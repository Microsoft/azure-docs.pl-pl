---
title: Rozwiązywanie problemów z alertami dzienników w Azure Monitor | Microsoft Docs
description: Typowe problemy, błędy i rozwiązania dla reguł alertów dziennika na platformie Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: ec2ffe71a32781a855da258f3621738f1a5f6be4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294295"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Rozwiązywanie problemów z alertami dzienników w Azure Monitor  

W tym artykule opisano sposób rozwiązywania typowych problemów z alertami dzienników w Azure Monitor. Zawiera również rozwiązania typowych problemów z funkcjonalnością i konfiguracją alertów dzienników.

Alerty dzienników umożliwiają użytkownikom użycie zapytania [log Analytics](../log-query/get-started-portal.md) w celu obliczenia dzienników zasobów co określoną częstotliwość i wyzwolenia alertu na podstawie wyników. Reguły mogą wyzwalać jedną lub więcej akcji przy użyciu [grup akcji](./action-groups.md). [Dowiedz się więcej o funkcjach i terminologii alertów dzienników](alerts-unified-log.md).

> [!NOTE]
> W tym artykule nie są brane pod uwagę przypadki, w których Azure Portal pokazuje wyzwalaną regułę alertu, a powiadomienie nie jest wykonywane przez skojarzoną grupę akcji. W takich przypadkach należy zapoznać się z szczegółowymi informacjami na [temat rozwiązywania](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)problemów.

## <a name="log-alert-didnt-fire"></a>Alert dziennika nie został wywołany

### <a name="data-ingestion-time-for-logs"></a>Czas pozyskiwania danych dzienników

Azure Monitor przetwarza terabajtów dzienników klientów na całym świecie, co może spowodować opóźnienie pozyskiwania [dzienników](./data-ingestion-time.md).

Dzienniki to dane z częściową strukturą, które są z natury bardziej ukryte niż metryki. Jeśli występuje więcej niż 4 minuty opóźnienia w wyzwalanych alertach, należy rozważyć użycie [alertów metryk](alerts-metric-overview.md). Dane można wysyłać do magazynu metryk z dzienników przy użyciu [alertów metryk dla dzienników](alerts-metric-logs.md).

System ponawia próbę oceny alertów wiele razy, aby ograniczyć opóźnienia. Po nadejściu danych Alert zostanie wyzwolony, co w większości przypadków nie jest równe czasowi rejestrowania dziennika.

### <a name="incorrect-query-time-range-configured"></a>Skonfigurowano niepoprawny zakres czasu zapytania

Zakres czasu zapytania jest ustawiany w definicji warunku reguły. To pole jest nazywane **okresem** dla obszarów roboczych i Application Insights i nazywane **zakresem czasu zapytania przesłonięcia** dla wszystkich innych typów zasobów. Podobnie jak w przypadku usługi log Analytics, zakres czasu ogranicza dane zapytania do określonego okresu. Nawet jeśli **to** polecenie jest używane w zapytaniu, przedział czasu będzie miał zastosowanie. 

Na przykład zapytanie skanuje 60 minut, gdy zakres czasu wynosi 60 minut, nawet jeśli tekst zawiera **temu (1D)**. Zakres czasu i filtrowanie czasu zapytania muszą być zgodne. W przykładzie w przypadku zmiany **okresu**  /  **przesłonięcia zapytania** do jednego dnia program będzie działał zgodnie z oczekiwaniami.

![Okres](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>Akcje są wyciszone w regule alertu

Alerty dzienników zapewniają opcję wyciszenia wywołanych akcji alertów przez określony czas. To pole jest nazywane **pomijaniem alertów** w obszarach roboczych i Application Insights. We wszystkich innych typach zasobów jest wywoływana **Akcja wyciszenia**. 

Typowym problemem jest fakt, że alert nie wyzwolenie akcji z powodu problemu z usługą. Nawet trudniejsze jest wyciszone przez konfigurację reguły.

![Pomiń alerty](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>Reguła alertu pomiaru metryki z podziałem przy użyciu starszej wersji interfejsu API Log Analytics

[Pomiar metryki](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) to typ alertu dziennika, który jest oparty na wynikach podsumowania szeregów czasowych. Te reguły umożliwiają grupowanie według kolumn w celu [podziału alertów](alerts-unified-log.md#split-by-alert-dimensions). Jeśli używasz starszej wersji interfejsu API Log Analytics, podział nie będzie działał zgodnie z oczekiwaniami. Wybieranie grupowania w starszym interfejsie API nie jest obsługiwane.

Bieżący interfejs API ScheduledQueryRules pozwala na ustawienie **agregacji** w regułach [pomiaru metryk](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) , które będą działały zgodnie z oczekiwaniami. [Dowiedz się więcej na temat przełączania do bieżącego interfejsu API ScheduledQueryRules](alerts-log-api-switch.md).

## <a name="log-alert-fired-unnecessarily"></a>Alert dziennika jest niepotrzebny

Skonfigurowana [reguła alertu dziennika w Azure monitor](./alerts-log.md) może zostać nieoczekiwanie wyzwolona. W poniższych sekcjach opisano niektóre typowe przyczyny.

### <a name="alert-triggered-by-partial-data"></a>Alert wywołany przez częściowe dane

Azure Monitor przetwarza terabajtów dzienników klientów na całym świecie, co może spowodować opóźnienie pozyskiwania [dzienników](./data-ingestion-time.md).

Dzienniki to dane z częściową strukturą, które są z natury bardziej ukryte niż metryki. Jeśli masz wiele niestabilnych uruchomień w wyzwalanych alertach, należy rozważyć użycie [alertów metryk](alerts-metric-overview.md). Dane można wysyłać do magazynu metryk z dzienników przy użyciu [alertów metryk dla dzienników](alerts-metric-logs.md).

Alerty dzienników najlepiej sprawdzają się w przypadku próby wykrycia danych w dziennikach. Działa ono mniej dobrze podczas próby wykrywania braku danych w dziennikach. Na przykład alert dotyczący pulsu maszyny wirtualnej. 

Chociaż istnieją wbudowane funkcje, które uniemożliwiają fałszowanie alertów, nadal mogą wystąpić w przypadku bardzo ukrytych danych (ponad około 30 minut) i danych z opóźnieniami.

### <a name="query-optimization-issues"></a>Problemy z optymalizacją zapytań

Usługa alertów zmienia zapytanie w celu optymalizacji pod kątem mniejszego opóźnienia ładowania i generowania alertów. Przepływ alertów został utworzony w celu przekształcenia wyników wskazujących na problem z alertem. Na przykład w przypadku zapytania, takiego jak:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Jeśli zamiarem użytkownika jest alert, gdy ten typ zdarzenia wystąpi, logika alertów dołączy `count` do zapytania. Zostanie wykonane zapytanie:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Nie ma potrzeby dodawania logiki alertu do zapytania i wykonywania operacji, które mogą nawet powodować problemy. W powyższym przykładzie, jeśli dołączysz `count` do zapytania, zawsze będzie to wartość 1, ponieważ usługa alertu wykona `count` `count` .

Zoptymalizowane zapytanie jest wykonywane przez usługę alertów dziennika. Zmodyfikowane zapytanie można uruchomić w [portalu](../log-query/log-query-overview.md) log Analytics lub w [interfejsie API](/rest/api/loganalytics/).

W przypadku obszarów roboczych i Application Insights jest on nazywany **kwerendą wykonywaną** w okienku warunek. We wszystkich innych typach zasobów wybierz pozycję **Zobacz zapytanie dotyczące końcowego alertu** na karcie warunek.

![Zapytanie do wykonania](media/alert-log-troubleshoot/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>Alert dziennika został wyłączony

W poniższych sekcjach wymieniono przyczyny, dla których Azure Monitor mogą wyłączyć regułę alertu dziennika. Uwzględniono również [przykład dziennika aktywności, który jest wysyłany, gdy reguła jest wyłączona](#activity-log-example-when-rule-is-disabled).

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>Zakres alertu już nie istnieje lub został przeniesiony

Gdy zasoby zakresu reguły alertu nie są już prawidłowe, wykonywanie reguły kończy się niepowodzeniem. W takim przypadku rozliczenia również zatrzymają się.

Azure Monitor spowoduje wyłączenie alertu dziennika po tygodniu, jeśli zakończy się niepowodzeniem.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>Zapytanie użyte w alercie dziennika jest nieprawidłowe

Po utworzeniu reguły alertu dotyczącego dziennika zapytanie jest sprawdzane pod kątem poprawnej składni. Jednak czasami zapytanie podane w regule alertu dziennika może się nie powieść. Najczęstsze przyczyny są następujące:

- Zasady zostały utworzone za pośrednictwem interfejsu API i weryfikacja została pominięta przez użytkownika.
- Zapytanie [jest uruchamiane na wielu zasobach](../log-query/cross-workspace-query.md) , a co najmniej jeden z zasobów został usunięty lub przeniesiony.
- [Zapytanie nie powiedzie się](https://dev.loganalytics.io/documentation/Using-the-API/Errors) , ponieważ:
    - Rozwiązanie rejestrowania nie zostało [wdrożone w obszarze roboczym](../insights/solutions.md#install-a-monitoring-solution), dlatego tabele nie są tworzone.
    - Dane przepływają do tabeli w zapytaniu przez ponad 30 dni.
    - [Tabele dzienników niestandardowych](data-sources-custom-logs.md) nie zostały jeszcze utworzone, ponieważ przepływ danych nie został uruchomiony.
- Zmiany w [języku zapytań](/azure/kusto/query/) zawierają poprawiony format poleceń i funkcji. Dlatego podane wcześniej zapytanie nie jest już prawidłowe.

[Azure Advisor](../../advisor/advisor-overview.md) ostrzega o tym zachowaniu. Dodaje zalecenie dotyczące reguły alertu dotyczącego dziennika. Kategoria używana to "wysoka dostępność" z średnim wpływem i opis "Napraw regułę alertu dziennika w celu zapewnienia monitorowania".

## <a name="alert-rule-quota-was-reached"></a>Osiągnięto limit przydziału reguły alertów

Liczba reguł alertów dotyczących przeszukiwania dzienników na subskrypcję i zasób podlega limitom przydziału opisanym [tutaj](../service-limits.md).

### <a name="recommended-steps"></a>Zalecane kroki
    
Jeśli osiągnięto limit przydziału, następujące kroki mogą pomóc w rozwiązaniu problemu.

1. Spróbuj usunąć lub wyłączyć reguły alertów przeszukiwania dzienników, które nie są już używane.
1. Spróbuj użyć [podziału alertów według wymiarów](alerts-unified-log.md#split-by-alert-dimensions) , aby zmniejszyć liczbę reguł. Te reguły mogą monitorować wiele zasobów i przypadków wykrywania.
1. Aby zwiększyć limit przydziału, Kontynuuj otwieranie żądania obsługi i podaj następujące informacje:

    - Identyfikatory subskrypcji i identyfikatory zasobów, dla których należy zwiększyć limit przydziału.
    - Przyczyna zwiększenia limitu przydziału.
    - Typ zasobu dla zwiększenia przydziału: **log Analytics**, **Application Insights**i tak dalej.
    - Żądany limit przydziału.


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Aby sprawdzić bieżące użycie nowych reguł alertów dziennika
    
#### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

1. Otwórz ekran *alerty* i wybierz pozycję *Zarządzaj regułami alertów*
2. Filtruj do odpowiedniej subskrypcji przy użyciu kontrolki listy rozwijanej *Subskrypcja*
3. Upewnij się, że nie Przefiltruj do określonej grupy zasobów, typu zasobu lub zasobu
4. Przy użyciu kontrolki listy rozwijanej *Typ sygnału* wybierz pozycję „Przeszukiwanie dzienników”
5. Sprawdź, czy kontrolka listy rozwijanej *Stan* ma wartość „Włączono”
6. Łączna liczba reguł alertów dotyczących przeszukiwania dzienników zostanie wyświetlona powyżej listy reguł

#### <a name="from-api"></a>Za pomocą interfejsu API

- PowerShell- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- Interfejs API REST — [lista według subskrypcji](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>Przykład dziennika aktywności, gdy reguła jest wyłączona

Jeśli zapytanie kończy się niepowodzeniem przez siedem dni, Azure Monitor spowoduje wyłączenie alertu dziennika i zatrzymanie rozliczenia reguły. Możesz dowiedzieć się, kiedy Azure Monitor wyłączyć alert dziennika w [dzienniku aktywności platformy Azure](../../azure-resource-manager/management/view-activity-logs.md). Zobacz ten przykład:

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat alertów dziennika na platformie Azure](./alerts-unified-log.md).
- Dowiedz się więcej o [konfigurowaniu alertów dzienników](../log-query/log-query-overview.md).
- Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md).
