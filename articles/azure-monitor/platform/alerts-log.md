---
title: Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure Monitor | Microsoft Docs
description: Użyj Azure Monitor, aby tworzyć, wyświetlać i zarządzać regułami alertów dziennika na platformie Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 96b1bd86576f8cf34428eb60e2d3f476312311c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249427"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure Monitor

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób konfigurowania alertów dziennika przy użyciu interfejsu alertów w programie Azure Portal. Definicja reguły alertu składa się z trzech części:
- Cel: określony zasób platformy Azure, który ma być monitorowany
- Kryteria: określony warunek lub logika, która pojawia się w sygnale, powinna wyzwalać akcję
- Akcja: określone wywołanie wysyłane do odbiorcy powiadomienia, wiadomości e-mail, elementu webhook itp.

Termin **alerty dziennika** do opisywania alertów, w których sygnał jest wysyłany zapytanie dziennika w [obszarze roboczym log Analytics](../learn/tutorial-viewdata.md) lub [Application Insights](../app/analytics.md). Dowiedz się więcej o funkcjach, terminologii i typach z [alertów dziennika — przegląd](alerts-unified-log.md).

> [!NOTE]
> Popularne dane dziennika z [obszaru roboczego log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) są teraz również dostępne na platformie metryk w Azure monitor. Aby wyświetlić szczegóły, [alert dotyczący metryk dzienników](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Zarządzanie alertami dziennika z Azure Portal

Szczegółowe instrukcje zawiera przewodnik krok po kroku dotyczący używania alertów dziennika przy użyciu interfejsu Azure Portal.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Tworzenie reguły alertu dziennika przy użyciu Azure Portal

1. W [portalu](https://portal.azure.com/)wybierz pozycję **monitorowanie** i w sekcji Monitor wybierz pozycję **alerty**.

    ![Monitorowanie](media/alerts-log/AlertsPreviewMenu.png)

1. Wybierz przycisk **Nowa reguła alertu** , aby utworzyć nowy Alert na platformie Azure.

    ![Dodawanie alertu](media/alerts-log/AlertsPreviewOption.png)

1. Sekcja Tworzenie alertu jest wyświetlana z trzema częściami składającymi się z: *Zdefiniuj warunek alertu*, *Zdefiniuj szczegóły alertu*i *Zdefiniuj grupę akcji*.

    ![Tworzenie reguły](media/alerts-log/AlertsPreviewAdd.png)

1. Zdefiniuj warunek alertu za pomocą linku **Wybierz zasób** i określając element docelowy, wybierając zasób. Filtruj, wybierając opcję _subskrypcja_, _Typ zasobu_i wymagany _zasób_.

   > [!NOTE]
   > W celu utworzenia alertu dziennika — przed kontynuowaniem sprawdź, czy jest dostępny sygnał **dziennika** dla wybranego zasobu.
   >  ![Wybierz zasób](media/alerts-log/Alert-SelectResourceLog.png)

1. *Alerty dziennika*: Upewnij się, że **Typ zasobu** to źródło analityczne, takie jak *log Analytics* lub *Application Insights* i typ sygnału jako **Dziennik**, po wybraniu odpowiedniego **zasobu** kliknij przycisk *gotowe*. Następnie użyj przycisku **Dodaj kryteria** , aby wyświetlić listę opcji sygnałów dostępnych dla zasobu oraz z opcji **wyszukiwania niestandardowego dziennika** listy sygnałów dla wybranej usługi monitora dzienników, takiej jak *log Analytics* lub *Application Insights*.

   ![Wybierz zasób — wyszukiwanie w dzienniku niestandardowym](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Listy alertów mogą zaimportować zapytanie analityczne jako typ sygnału **log (zapisane zapytanie)**, jak pokazano na powyższej ilustracji. Dzięki temu użytkownicy mogą uzyskać doskonałe wyniki zapytania w analizie, a następnie zapisać je do użycia w przyszłości w alertach — więcej szczegółowych informacji na temat używania zapytania w [dzienniku w Azure monitor](../log-query/log-query-overview.md) lub [udostępnione zapytanie w usłudze Application Insights Analytics](../app/app-insights-overview.md).

1. *Alerty dziennika*: po wybraniu zapytania o alerty można określić w polu **zapytania wyszukiwania** . Jeśli Składnia zapytania jest niepoprawna, w polu czerwony zostanie wyświetlony komunikat o błędzie. Jeśli Składnia zapytania jest poprawna — w przypadku referencyjnych danych historycznych zapytania jest wyświetlany jako wykres z opcją dostosowywania przedziału czasu z ostatnich sześciu godzin do ostatniego tygodnia.

    ![Konfigurowanie reguły alertu](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Wizualizację danych historycznych można wyświetlić tylko wtedy, gdy wyniki zapytania zawierają szczegóły czasu. Jeśli zapytanie prowadzi do podsumowania danych lub określonych wartości kolumn — ta sama wartość jest pokazywana jako pojedyncze Kreślenie.
   > Aby uzyskać informacje o typie pomiaru metryki alertów dziennika przy użyciu Application Insights lub [przełączeć do nowego interfejsu API](alerts-log-api-switch.md), można określić, która zmienna ma grupować dane przy użyciu opcji **Agreguj na** ; jak pokazano poniżej:
   > 
   > ![Agreguj dla opcji](media/alerts-log/aggregate-on.png)

1. *Alerty dziennika*: w miejscu wizualizacji można wybrać **logikę alertów** spośród pokazywanych opcji warunku, agregacji i wartości progowej finally. Na koniec Określ w logice, czas do oceny dla określonego warunku, przy użyciu opcji **okres** . Niezależnie od tego, jak często ma być uruchamiany alert, wybierając pozycję **częstotliwość**. **Alerty dzienników** mogą opierać się na:
    - [Liczba rekordów](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): tworzony jest alert, jeśli liczba rekordów zwróconych przez zapytanie jest większa lub mniejsza od podanej wartości.
    - [Pomiar metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): tworzony jest alert, jeśli każda *wartość zagregowana* w wynikach przekroczy podaną wartość progową i zostanie *pogrupowana według* wybranej wartości. Liczba naruszeń alertu to liczba przypadków przekroczenia progu w wybranym okresie. Możesz określić całkowite naruszenia dla każdej kombinacji naruszeń w zestawie wyników lub kolejne naruszenia, aby wymagać, aby naruszenia mogły wystąpić w kolejnych próbkach.


1. W drugim kroku Zdefiniuj nazwę alertu w polu **Nazwa reguły alertu** wraz z **opisem** szczegółowym dotyczącym alertu i wartości **ważności** z dostępnych opcji. Te szczegóły są ponownie używane we wszystkich wiadomościach e-mail z alertami, powiadomieniami lub wypychaniu wykonywanym przez Azure Monitor. Ponadto użytkownik może zdecydować się na natychmiastowe aktywowanie reguły alertu przy tworzeniu przez odpowiednie przełączenie **reguły włączania przy tworzeniu** .

    Tylko w przypadku **alertów dziennika** niektóre dodatkowe funkcje są dostępne w szczegółach alertu:

    - **Pomijaj alerty**: po włączeniu pomijania dla reguły alertu akcje dla reguły są wyłączone przez zdefiniowany czas po utworzeniu nowego alertu. Zasada jest nadal uruchomiona i tworzy rekordy alertów z warunkiem spełnienia kryteriów. Umożliwienie poprawienia problemu bez uruchamiania zduplikowanych akcji.

        ![Pomijaj alerty dla alertów dziennika](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Określ wartość pomijania alertu większą niż częstotliwość alertu, aby powiadomienia zostały zatrzymane bez nakładania się

1. Jako trzeci i ostatni krok Określ, czy w przypadku spełnienia warunku alertu ma być wyzwalana jakakolwiek **Grupa akcji** dla reguły alertu. Możesz wybrać dowolną istniejącą grupę akcji z alertem lub utworzyć nową grupę akcji. Zgodnie z wybraną grupą akcji, gdy wyzwalany jest alert Azure: Wysyłaj wiadomości e-mail, wysyłaj wiadomości SMS, wywołaj elementy webhook, Koryguj przy użyciu elementów Runbook platformy Azure, wypchnij do narzędzia Narzędzia ITSM itp. Dowiedz się więcej na temat [grup akcji](action-groups.md).

    > [!NOTE]
    > Zapoznaj się z [limitami usługi subskrypcji platformy Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) , aby uzyskać limity dla ładunków elementów Runbook wyzwalanych dla alertów dziennika za pośrednictwem grup akcji platformy Azure

    W przypadku **alertów dotyczących dziennika** niektóre dodatkowe funkcje są dostępne w celu zastąpienia domyślnych akcji:

    - **Powiadomienie e-mail**: przesłania wiadomość *e-mail podmiotu* w wiadomości e-mail, która jest wysyłana za pośrednictwem grupy akcji; Jeśli co najmniej jedna akcja poczty e-mail istnieje w tej grupie akcji. Nie można zmodyfikować treści poczty, a to pole **nie** jest przeznaczone dla adresu e-mail.
    - **Uwzględnij niestandardowy ładunek JSON**: przesłania kod JSON elementu webhook używany przez grupy akcji; Jeśli co najmniej jedno działanie elementu webhook istnieje w tej grupie akcji. Użytkownik może określić format JSON, który ma być używany dla wszystkich elementów webhook skonfigurowanych w skojarzonej grupie akcji; Aby uzyskać więcej informacji na temat formatów elementu webhook, zobacz [Akcja elementu webhook dla alertów dziennika](../../azure-monitor/platform/alerts-log-webhook.md). Wyświetl opcję elementu webhook w celu sprawdzenia formatu przy użyciu przykładowych danych JSON.

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

Alerty dzienników w Azure Monitor są skojarzone z typem `Microsoft.Insights/scheduledQueryRules/`zasobu. Aby uzyskać więcej informacji na temat tego typu zasobu, zobacz temat [zaplanowano informacje o interfejsie API reguł zapytań Azure monitor](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Alerty dzienników dla Application Insights lub Log Analytics można utworzyć za pomocą [interfejsu API harmonogramu zaplanowanych zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

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

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : polecenie cmdlet programu PowerShell, aby utworzyć nową regułę alertu dziennika.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : polecenie cmdlet programu PowerShell służące do aktualizowania istniejącej reguły alertu dziennika.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry źródłowe alertu dziennika. Używane jako dane wejściowe za pomocą polecenia cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): polecenie cmdlet programu PowerShell do tworzenia lub aktualizowania obiektu określającego parametry harmonogramu dla alertu dziennika. Używane jako dane wejściowe za pomocą polecenia cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry akcji dla alertu dziennika. Używane jako dane wejściowe za pomocą polecenia cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) i [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry grup akcji dla alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) .
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry warunku wyzwalacza dla alertu dziennika. Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) .
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : polecenie cmdlet programu PowerShell służące do tworzenia lub aktualizowania obiektu określającego parametry warunku wyzwalacza metryki dla [alertu dziennika typu pomiaru metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Używane jako dane wejściowe przez polecenie cmdlet [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) .
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : polecenie cmdlet programu PowerShell, aby wyświetlić listę istniejących reguł alertów dziennika lub konkretnej reguły alertu dziennika
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : polecenie cmdlet programu PowerShell do włączania lub wyłączania reguły alertów dziennika
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): polecenie cmdlet programu PowerShell, aby usunąć istniejącą regułę alertu dziennika

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
