---
title: Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure Monitor | Microsoft Docs
description: Użyj Azure Monitor, aby tworzyć, wyświetlać i zarządzać regułami alertów dziennika na platformie Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 85aaefa12f0cef21e3a367700d1a4899a75e8a90
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298475"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure Monitor

## <a name="overview"></a>Omówienie
W tym artykule pokazano, jak tworzyć alerty dzienników i zarządzać nimi za pomocą interfejsu alertów w ramach Azure Portal. Reguły alertów są definiowane przez trzy składniki:
- Cel: określony zasób platformy Azure do monitorowania
- Kryteria: warunek lub logika do oszacowania dla prawdy. W przypadku wartości true alert zostanie wyzwolony.  
- Akcja: określone wywołanie wysyłane do odbiorcy powiadomienia, wiadomości e-mail, elementu webhook itp.

Termin **alert dziennika** zawiera informacje o alertach, w których są oceniane zapytania dziennika w [obszarze roboczym log Analytics](../learn/tutorial-viewdata.md) lub [Application Insights](../app/analytics.md) , a także alert, który jest uruchamiany, jeśli wynik ma wartość true. Dowiedz się więcej o funkcjach, terminologii i typach z [alertów dziennika — przegląd](alerts-unified-log.md).

> [!NOTE]
> Dane dziennika z [obszaru roboczego log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) mogą być również kierowane do bazy danych metryk Azure monitor. Alerty metryk mają [różne zachowanie](alerts-metric-overview.md), co może być bardziej odpowiednie w zależności od danych, z którymi pracujesz.   Aby uzyskać informacje na temat tego, co i jak można kierować dzienniki do metryk, zobacz [alert metryki dla dzienników](alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Tworzenie reguły alertu dziennika przy użyciu Azure Portal

1. W [portalu](https://portal.azure.com/)wybierz pozycję **monitorowanie**. W tej sekcji Wybierz pozycję **alerty**.

    ![Monitorowanie](media/alerts-log/AlertsPreviewMenu.png)

1. Kliknij pozycję **Nowa reguła alertu**. 

    ![Dodawanie alertu](media/alerts-log/AlertsPreviewOption.png)

1. Zostanie wyświetlone okienko **Tworzenie alertu** . Ma cztery części: 
    - Zasób, którego dotyczy alert
    - Warunek do sprawdzenia
    - Akcja do wykonania, jeśli warunek ma wartość true
    - Szczegóły do nazwy i opisujące alert. 

    ![Tworzenie reguły](media/alerts-log/AlertsPreviewAdd.png)

1. Zdefiniuj warunek alertu za pomocą linku **Wybierz zasób** i określając element docelowy, wybierając zasób. Filtruj, wybierając opcję *subskrypcja*, *Typ zasobu*i wymagany *zasób*. 

   ![Wybierz zasób](media/alerts-log/Alert-SelectResourceLog.png)

1. Upewnij się, że **Typ zasobu** to źródło analityczne, takie jak *log Analytics* lub *Application Insights* i typ sygnału jako *Dziennik*. Kliknij pozycję **Gotowe**. Następnie użyj przycisku **Dodaj kryteria** , aby wyświetlić listę opcji sygnałów dostępnych dla zasobu. Znajdź i wybierz opcję **niestandardowej opcji przeszukiwania dzienników** dla *log Analytics* lub *Application Insights*, w zależności od tego, gdzie znajdują się dane alertów dziennika.

   ![Wybierz zasób — wyszukiwanie w dzienniku niestandardowym](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Listy alertów mogą zaimportować zapytanie analityczne jako typ sygnału **log (zapisane zapytanie)**, jak pokazano na powyższej ilustracji. Dzięki temu użytkownicy mogą w analizie poprawić swoje zapytania, a następnie zapisywać je do użytku w przyszłości. Aby uzyskać więcej informacji na temat korzystania z zapisanych zapytań, zobacz [using log Query in Azure monitor](../log-query/log-query-overview.md) i [shared Query in Application Insights Analytics](../app/app-insights-overview.md).

1. Po wybraniu tego pola Utwórz kwerendę alertów w polu **zapytania wyszukiwania** . Jeśli Składnia zapytania jest niepoprawna, pole jest wyświetlane i pojawia się na czerwono. 

1. Jeśli Składnia zapytania jest poprawna, dane historyczne zapytania są wyświetlane jako Graf z opcją dostosowania przedziału czasu z ostatnich sześciu godzin do ostatniego tygodnia.

    ![Konfigurowanie reguły alertu](media/alerts-log/AlertsPreviewAlertLog.png)

   Wizualizacja danych historycznych jest wyświetlana tylko wtedy, gdy wyniki zapytania zawierają szczegóły czasu. Jeśli zapytanie powoduje wyświetlenie podsumowanych danych lub określonych wartości kolumn, wyświetlany jest pojedynczy wykres.
  
   W przypadku pomiarów metryk przy użyciu Application Insights lub [interfejsu API log Analytics](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)można określić, która zmienna ma grupować dane przy użyciu opcji **Agreguj na** ; jak pokazano poniżej: 
  
   ![Agreguj dla opcji](media/alerts-log/aggregate-on.png)



1. Następnie wybierz warunek **logiki alertu** , agregację i próg. 

1. Wybierz przedział czasu, który ma ocenić określony warunek, przy użyciu opcji **okres** . 

1. Wybierz **częstotliwość uruchamiania alertu.** 

    **Alerty dzienników** mogą opierać się na:
    - [Liczba rekordów](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): tworzony jest alert, jeśli liczba rekordów zwróconych przez zapytanie jest większa lub mniejsza od podanej wartości.
    - [Pomiar metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): tworzony jest alert, jeśli każda *wartość zagregowana* w wynikach przekroczy podaną wartość progową i zostanie *pogrupowana według* wybranej wartości. Liczba naruszeń alertu to liczba przypadków przekroczenia progu w wybranym okresie. Możesz określić całkowite naruszenia dla każdej kombinacji naruszeń w zestawie wyników lub kolejne naruszenia, aby wymagać, aby naruszenia mogły wystąpić w kolejnych próbkach.


1. Kliknij pozycję **Gotowe**. 

1. Zdefiniuj nazwę alertu w polu **Nazwa reguły alertu** wraz z **opisem** szczegółowym dotyczącym alertu i wartości **ważności** z dostępnych opcji. Te szczegóły są ponownie używane we wszystkich wiadomościach e-mail z alertami, powiadomieniami lub wypchnięciami wykonywanymi przez Azure Monitor. Ponadto można zdecydować, aby natychmiast aktywować regułę alertu przy tworzeniu przez kliknięcie przycisku **Włącz regułę podczas tworzenia**.

1. Wybierz, czy chcesz **pomijać alerty** w danym okresie czasu.  Po włączeniu pomijania dla reguły alertu akcje dla reguły są wyłączone przez zdefiniowany czas po utworzeniu nowego alertu. Reguła nadal działa i tworzy rekordy alertów, pod warunkiem że kryteria są spełnione. To ustawienie umożliwia czas na rozwiązanie problemu bez uruchamiania zduplikowanych akcji.

   ![Pomijaj alerty dla alertów dziennika](media/alerts-log/AlertsPreviewSuppress.png)

    > [!TIP]
    > Określ wartość alertu pomijania większą niż częstotliwość alertów w celu zapewnienia, że powiadomienia są zatrzymane bez nakładania się

1. Jako trzeci i ostatni krok Określ, czy reguła alertów powinna wyzwalać co najmniej jedną **grupę akcji** po spełnieniu warunku alertu. Możesz wybrać dowolną istniejącą grupę akcji lub utworzyć nową. Za pomocą grup akcji można wysłać wykonywanie wielu akcji, takich jak wysyłanie wiadomości e-mail, wysyłanie wiadomości SMS, wywoływanie elementów webhook, korygowanie przy użyciu funkcji Runbook platformy Azure, wypychanie do narzędzia Narzędzia ITSM i inne. Dowiedz się więcej na temat [grup akcji](action-groups.md).

    > [!NOTE]
    > Ograniczenia dotyczące działań, które można wykonać, zapoznaj się z [limitami usługi subskrypcji platformy Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) .  

    Dostępne są pewne dodatkowe funkcje, które przesłonią domyślne akcje:

    - **Powiadomienie e-mail**: zastępuje *temat wiadomości e-mail* w wiadomości E-mail wysyłanej przez grupę akcji. Nie można zmodyfikować treści poczty, a to pole **nie** jest przeznaczone dla adresu e-mail.
    - **Uwzględnij niestandardowy ładunek JSON**: przesłania kod JSON elementu webhook używany przez grupy akcji, przy założeniu, że grupa akcji zawiera typ elementu webhook. Aby uzyskać więcej informacji na temat formatów elementu webhook, zobacz [Akcja elementu webhook dla alertów dziennika](../../azure-monitor/platform/alerts-log-webhook.md). Wyświetl opcję elementu webhook w celu sprawdzenia formatu przy użyciu przykładowych danych JSON.

        ![Zastąpienia akcji dla alertów dziennika](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Jeśli wszystkie pola są prawidłowe i z zielonym znacznikiem, można kliknąć przycisk **Utwórz regułę alertu** i utworzyć alert w Azure monitor-alertach. Wszystkie alerty można wyświetlić na pulpicie nawigacyjnym alertów.

     ![Tworzenie reguły](media/alerts-log/AlertsPreviewCreate.png)

     W ciągu kilku minut alert jest aktywny i wyzwalacze opisane wcześniej.

Użytkownicy mogą również sfinalizować swoje zapytanie analityczne w usłudze [log Analytics](../log-query/portals.md) , a następnie wypchnąć je w celu utworzenia alertu za pomocą przycisku "Ustaw Alert", a następnie wykonać instrukcje opisane w kroku 6 lub nowszym w powyższym samouczku.

 ![Alert Log Analytics — Ustawianie](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Wyświetlanie & Zarządzanie alertami dzienników w programie Azure Portal

1. W [portalu](https://portal.azure.com/)wybierz pozycję **monitorowanie** i w sekcji Monitor wybierz pozycję **alerty**.

1. Zostanie wyświetlony **pulpit nawigacyjny alerty** — wszystkie alerty platformy Azure (w tym alerty dzienników) są wyświetlane na pojedynczej tablicy; Dołączanie każdego wystąpienia, gdy reguła alertu dziennika została uruchomiona. Aby dowiedzieć się więcej, zobacz [alert Management](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Reguły alertów dziennika składają się z niestandardowej logiki opartej na zapytaniach, która jest dostarczana przez użytkowników, a tym samym bez rozwiązanego stanu. Z tego powodu, gdy spełnione są warunki określone w regule alertu dziennika, jest on uruchamiany.

1. Wybierz przycisk **Zarządzaj regułami** na górnym pasku, aby przejść do sekcji Zarządzanie regułami, gdzie są wymienione wszystkie reguły alertów. uwzględnienie alertów, które zostały wyłączone.
    ![Zarządzaj regułami alertów](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Zarządzanie alertami dzienników przy użyciu szablonu zasobów platformy Azure

Alerty dzienników w Azure Monitor są skojarzone z typem zasobu `Microsoft.Insights/scheduledQueryRules/` . Aby uzyskać więcej informacji na temat tego typu zasobu, zobacz temat [zaplanowano informacje o interfejsie API reguł zapytań Azure monitor](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Alerty dzienników dla Application Insights lub Log Analytics można utworzyć za pomocą [interfejsu API harmonogramu zaplanowanych zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Alerty dzienników dla Log Analytics mogą być również zarządzane przy użyciu starszego [interfejsu API alertów log Analytics](api-alerts.md) i starszych szablonów [log Analytics zapisanych wyszukiwań i alertów](../insights/solutions-resources-searches-alerts.md) . Aby uzyskać więcej informacji na temat domyślnego użycia nowego interfejsu API ScheduledQueryRules, zobacz [przełączanie do nowego interfejsu API na potrzeby alertów log Analytics](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Przykładowy dziennik tworzenia alertów przy użyciu szablonu zasobów platformy Azure

Poniżej znajduje się Struktura szablonu zasobów opartego na [tworzeniu reguł zaplanowanych zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) przy użyciu standardowego zapytania wyszukiwania w dziennikach o [liczbie alertów](alerts-unified-log.md#number-of-results-alert-rules), z przykładowymi danymi ustawionymi jako zmienne.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

Przykładowy plik JSON powyżej można zapisać jako plik (powiedzieć) sampleScheduledQueryRule. JSON na potrzeby tego przewodnika i można go wdrożyć przy użyciu [Azure Resource Manager w Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Rejestruj alert za pomocą zapytania między zasobami przy użyciu szablonu zasobów platformy Azure

Poniżej znajduje się struktura szablonów zasobów opartych na [tworzeniu zaplanowanych reguł zapytania](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) przy użyciu [kwerendy przeszukiwania dzienników wielu zasobów](../../azure-monitor/log-query/cross-workspace-query.md) [, z przykładowymi](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)danymi ustawionymi jako zmienne.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> W przypadku korzystania z zapytania między zasobami w alercie dziennika użycie [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) jest obowiązkowe, a użytkownik musi mieć dostęp do listy podanych zasobów

Przykładowy plik JSON powyżej można zapisać jako plik (powiedzieć) sampleScheduledQueryRule. JSON na potrzeby tego przewodnika i można go wdrożyć przy użyciu [Azure Resource Manager w Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Zarządzanie alertami dzienników przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor — [interfejs API reguł zaplanowanych zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) jest interfejsem API REST i w pełni zgodny z interfejsem api REST Azure Resource Manager. Polecenia cmdlet programu PowerShell wymienione poniżej są dostępne do korzystania z [interfejsu API zaplanowanych reguł zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

- [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : polecenie cmdlet programu PowerShell, aby utworzyć nową regułę alertu dziennika.
- [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : polecenie cmdlet programu PowerShell służące do aktualizowania istniejącej reguły alertu dziennika.
- [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry źródłowe alertu dziennika. Używane jako dane wejściowe za pomocą polecenia cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): polecenie cmdlet programu PowerShell do tworzenia lub aktualizowania obiektu określającego parametry harmonogramu dla alertu dziennika. Używane jako dane wejściowe za pomocą polecenia cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry akcji dla alertu dziennika. Używane jako dane wejściowe za pomocą polecenia cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry grup akcji dla alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) .
- [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry warunku wyzwalacza dla alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) .
- [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry warunku wyzwalacza metryki dla [alertu dziennika typu pomiaru metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) .
- [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : polecenie cmdlet programu PowerShell, aby wyświetlić listę istniejących reguł alertów dziennika lub konkretnej reguły alertu dziennika
- [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : polecenie cmdlet programu PowerShell do włączania lub wyłączania reguły alertów dziennika
- [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): polecenie cmdlet programu PowerShell, aby usunąć istniejącą regułę alertu dziennika

> [!NOTE]
> Polecenia cmdlet programu PowerShell w programie ScheduledQueryRules mogą zarządzać tylko tymi regułami, które zostały utworzone za pomocą [interfejsu API reguł zapytań Zaplanowanych](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)Azure monitor. Reguły alertów dziennika utworzone przy użyciu starszego [interfejsu API alertów log Analytics](api-alerts.md) i starsze szablony [log Analytics zapisane wyszukiwania i alerty](../insights/solutions-resources-searches-alerts.md) mogą być zarządzane za pomocą poleceń cmdlet programu PowerShell ScheduledQueryRules tylko po ustawieniu [preferencji interfejsu API dla alertów log Analytics](alerts-log-api-switch.md).

Poniżej przedstawiono procedurę tworzenia przykładowej reguły alertu dziennika przy użyciu poleceń cmdlet programu PowerShell scheduledQueryRules.

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Zarządzanie alertami dzienników przy użyciu interfejsu wiersza polecenia lub API

Azure Monitor — [interfejs API reguł zaplanowanych zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) jest interfejsem API REST i w pełni zgodny z interfejsem api REST Azure Resource Manager. W związku z tym można go używać za pośrednictwem programu PowerShell przy użyciu poleceń Menedżer zasobów dla interfejsu wiersza polecenia platformy Azure.


> [!NOTE]
> Alerty dzienników dla Log Analytics mogą być również zarządzane przy użyciu starszego [interfejsu API alertów log Analytics](api-alerts.md) i starszych szablonów [log Analytics zapisanych wyszukiwań i alertów](../insights/solutions-resources-searches-alerts.md) . Aby uzyskać więcej informacji na temat domyślnego użycia nowego interfejsu API ScheduledQueryRules, zobacz [przełączanie do nowego interfejsu API na potrzeby alertów log Analytics](alerts-log-api-switch.md).

Alerty dzienników nie mają obecnie dedykowanych poleceń interfejsu wiersza polecenia. ale jak pokazano poniżej, można użyć polecenia Azure Resource Manager CLI dla przykładowego szablonu zasobu pokazanego wcześniej (sampleScheduledQueryRule. JSON) w sekcji szablonu zasobu:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Po pomyślnym wykonaniu operacji 201 zostanie zwrócona wartość stanu Nowa reguła alertu, a w przypadku zmodyfikowania istniejącej reguły alertu zostanie zwrócona wartość 200.

## <a name="next-steps"></a>Następne kroki

* Informacje o [alertach dzienników w usłudze Azure alertach](../../azure-monitor/platform/alerts-unified-log.md)
* Informacje [o akcjach elementu webhook dla alertów dziennika](../../azure-monitor/platform/alerts-log-webhook.md)
* Dowiedz się więcej o [Application Insights](../../azure-monitor/app/analytics.md)
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md).
