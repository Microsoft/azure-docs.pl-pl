---
title: Akcje elementu webhook dla alertów dziennika w usłudze Azure Alerts
description: Opisuje sposób konfigurowania alertów dziennika wypychanych za pomocą akcji elementu webhook i dostępnych dostosowań
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 9a074be9bcc62d8c20635400f462f52fb796d2fe
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012326"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akcje elementów webhook dla reguł alertów dziennika

[Alert dotyczący rejestrowania](alerts-log.md) obsługuje [Konfigurowanie grup akcji elementu webhook](action-groups.md#webhook). W tym artykule opisano, jakie właściwości są dostępne i jak skonfigurować niestandardowy element webhook JSON.

> [!NOTE]
> Niestandardowy element webhook oparty na notacji JSON nie jest obecnie obsługiwany w wersji interfejsu API `2020-05-01-preview`

> [!NOTE]
> Zalecane jest używanie [typowych schematów alertów](alerts-common-schema.md) dla integracji elementów webhook. Typowy schemat alertów umożliwia korzystanie z jednego rozszerzalnego i ujednoliconego ładunku alertów dla wszystkich usług alertów w Azure Monitor. Dla reguł alertów dziennika, które mają zdefiniowany niestandardowy ładunek JSON, włączenie wspólnego schematu powoduje przywrócenie schematu ładunku do opisanego w [tym miejscu](alerts-common-schema-definitions.md#log-alerts). Alerty z włączonym wspólnym schematem mają górny limit rozmiaru 256 KB na alert, jednak większy alert nie będzie zawierał wyników wyszukiwania. Gdy wyniki wyszukiwania nie zostaną uwzględnione, należy użyć `LinkToFilteredSearchResultsAPI` lub `LinkToSearchResultsAPI` uzyskać dostęp do wyników zapytania za pośrednictwem interfejsu API log Analytics.

## <a name="webhook-payload-properties"></a>Właściwości ładunku elementu webhook

Akcje elementu webhook umożliwiają wywołanie pojedynczego żądania HTTP POST. Wywołana usługa powinna obsługiwać elementy webhook i wiedzieć, jak używać ładunku, który odbiera.

Domyślne właściwości akcji elementu webhook i ich niestandardowe nazwy parametrów JSON:

| Parametr | Zmienna | Opis |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nazwa reguły alertu. |
| *Ważność* |#severity |Ważność ustawiona dla alertu wygenerowanego dziennika. |
| *AlertThresholdOperator* |#thresholdoperator |Operator progu dla reguły alertu. |
| *AlertThresholdValue* |#thresholdvalue |Wartość progowa dla reguły alertu. |
| *LinkToSearchResults* |#linktosearchresults |Połącz się z portalem analizy, który zwraca rekordy z zapytania, które spowodowało utworzenie alertu. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Połącz się z interfejsem API analizy, który zwraca rekordy z zapytania, które spowodowało utworzenie alertu. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Połącz się z portalem analizy, który zwraca rekordy z przefiltrowanych kombinacji wartości wymiarów, które utworzyły alert. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Połącz się z interfejsem API analizy, który zwraca z kwerendy filtrowanie według kombinacji wartości wymiarów, które utworzyły alert. |
| *Właściwości resultcount dla* |#searchresultcount |Liczba rekordów w wynikach wyszukiwania. |
| *Czas zakończenia interwału wyszukiwania* |#searchintervalendtimeutc |Czas zakończenia zapytania w formacie UTC z formatem mm/dd/rrrr HH: mm: ss AM/PM. |
| *Interwał wyszukiwania* |#searchinterval |Przedział czasu dla reguły alertu w formacie gg: mm: SS. |
| *Interwał wyszukiwania czas rozpoczęcia* |#searchintervalstarttimeutc |Godzina rozpoczęcia zapytania w formacie UTC z formatem mm/dd/rrrr HH: mm: ss AM/PM. 
| *SearchQuery* |#searchquery |Zapytanie wyszukiwania w dzienniku używane przez regułę alertu. |
| *SearchResults* |"IncludeSearchResults": true|Rekordy zwracane przez zapytanie jako tabela JSON, które są ograniczone do pierwszych 1 000 rekordów. "IncludeSearchResults": wartość true jest dodawana do niestandardowej definicji elementu webhook JSON jako Właściwość najwyższego poziomu. |
| *Wymiary* |"IncludeDimensions": true|Kombinacje wartości wymiarów, które wyzwalają ten alert jako sekcję JSON. "IncludeDimensions": wartość true jest dodawana do niestandardowej definicji elementu webhook JSON jako Właściwość najwyższego poziomu. |
| *Typ alertu*| #alerttype | Typ reguły alertu dziennika skonfigurowanej jako [pomiar metryki lub liczba wyników](alerts-unified-log.md#measure).|
| *Identyfikator obszaru roboczego* |#workspaceid |Identyfikator obszaru roboczego Log Analytics. |
| *Identyfikator aplikacji* |#applicationid |Identyfikator aplikacji Application Insights. |
| *Identyfikator subskrypcji* |#subscriptionid |Identyfikator używanej subskrypcji platformy Azure. |

## <a name="custom-webhook-payload-definition"></a>Niestandardowa definicja ładunku elementu webhook

Można użyć **dołączania niestandardowego ładunku JSON dla elementu webhook** w celu pobrania NIESTANDARDOWEGO ładunku JSON przy użyciu powyższych parametrów. Możesz również generować dodatkowe właściwości.
Na przykład można określić następujący ładunek niestandardowy, który zawiera pojedynczy parametr o nazwie *Text*. Usługa, którą wywołuje ten parametr elementu webhook:

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Ten przykładowy ładunek jest rozpoznawany jako podobny do poniższego, gdy jest wysyłany do elementu webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Zmienne w niestandardowym elemencie webhook muszą być określone w obrębie obudowy JSON. Na przykład odwołanie do "#searchresultcount" w powyższym przykładzie elementu webhook spowoduje wyjście na podstawie wyników alertu.

Aby uwzględnić wyniki wyszukiwania, Dodaj **IncludeSearchResults** jako Właściwość najwyższego poziomu w niestandardowym formacie JSON. Wyniki wyszukiwania są uwzględniane jako struktura JSON, dlatego nie można odwoływać się do wyników w niestandardowych polach zdefiniowanych. 

> [!NOTE]
> Przycisk **Wyświetl element webhook** obok opcji **Uwzględnij niestandardowy ładunek JSON dla elementu webhook** zawiera wersję zapoznawczą tego, co zostało dostarczone. Nie zawiera rzeczywiste dane, ale jest reprezentatywne dla schematu JSON, który zostanie użyty. 

## <a name="sample-payloads"></a>Przykładowe ładunki
W tej sekcji przedstawiono przykładowe ładunki dla elementów webhook dla alertów dziennika. Przykładowe ładunki zawierają przykłady, gdy ładunek jest standardowy i gdy jest niestandardowy.

### <a name="log-alert-for-log-analytics"></a>Alert dziennika dla Log Analytics
Następujący przykładowy ładunek dotyczy akcji standardowego elementu webhook, która jest używana na potrzeby alertów na podstawie Log Analytics:

> [!NOTE]
> Wartość pola "ważność" zmienia się w przypadku [przełączenia się do bieżącego interfejsu API scheduledQueryRules](alerts-log-api-switch.md) przy użyciu [starszego interfejsu api alertu log Analytics](api-alerts.md).

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights"></a>Alert dziennika dla Application Insights
Następujący przykładowy ładunek dotyczy standardowego elementu webhook, gdy jest używany do obsługi alertów dziennika na podstawie Application Insights zasobów:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>Alert dziennika dla innych dzienników zasobów (z wersji interfejsu API `2020-05-01-preview` )

> [!NOTE]
> Obecnie nie ma dodatkowych opłat za wersję interfejsu API `2020-05-01-preview` i alerty dzienników zorientowanych na zasoby.  Cennik funkcji, które są w wersji zapoznawczej, zostanie ogłoszony w przyszłości, a powiadomienie podane przed rozpoczęciem rozliczania. W przypadku wybrania opcji kontynuowania korzystania z nowej wersji interfejsu API i zdarzeń dzienników skoncentrowanych na zasobach po upływie okresu wypowiedzenia zostanie naliczona stawka mająca zastosowanie.

Następujący przykładowy ładunek dotyczy standardowego elementu webhook, gdy jest używany do rejestrowania alertów na podstawie innych dzienników zasobów (z wyjątkiem obszarów roboczych i Application Insights):

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>Rejestruj alert z niestandardowym ładunkiem JSON
Na przykład, aby utworzyć niestandardowy ładunek, który zawiera tylko nazwę alertu i wyniki wyszukiwania, Użyj tej konfiguracji: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Następujący przykładowy ładunek dotyczy akcji niestandardowej elementu webhook dla dowolnego alertu dziennika:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej [na temat alertów dziennika w usłudze Azure Alerts](alerts-unified-log.md).
- Zapoznaj się z tematem jak [zarządzać alertami dziennika na platformie Azure](alerts-log.md).
- Tworzenie grup akcji i zarządzanie nimi [na platformie Azure](action-groups.md).
- Dowiedz się więcej o [Application Insights](../log-query/log-query-overview.md).
- Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md). 
