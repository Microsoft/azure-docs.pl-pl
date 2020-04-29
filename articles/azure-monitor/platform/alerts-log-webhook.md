---
title: Akcje elementu webhook dla alertów dziennika w usłudze Azure Alerts
description: W tym artykule opisano sposób tworzenia reguły alertu dziennika przy użyciu obszaru roboczego Log Analytics lub Application Insights, sposobu wypchnięcia danych jako elementu webhook protokołu HTTP i szczegółowych informacji o różnych możliwych dostosowaniach.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77667707"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Akcje elementu webhook dla reguł alertów dziennika
Po [utworzeniu alertu dziennika na platformie Azure](alerts-log.md)można [skonfigurować go za pomocą grup akcji](action-groups.md) , aby wykonać jedną lub więcej akcji. W tym artykule opisano różne akcje elementu webhook, które są dostępne i przedstawiono sposób konfigurowania niestandardowego elementu webhook opartego na notacji JSON.

> [!NOTE]
> Można również użyć [wspólnego schematu alertów](https://aka.ms/commonAlertSchemaDocs) dla integracji elementów webhook. Typowy schemat alertów umożliwia korzystanie z jednego rozszerzalnego i ujednoliconego ładunku alertów dla wszystkich usług alertów w Azure Monitor. należy pamiętać, że wspólny schemat alertów nie ma opcji niestandardowy kod JSON dla alertów dziennika. Jest ona stosowana do typowego ładunku schematu alertu, jeśli jest zaznaczone niezależnie od dostosowania, które można wykonać na poziomie reguły alertu. [Dowiedz się więcej na temat typowych definicji schematu alertów.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Akcje elementów webhook

Za pomocą akcji elementu webhook można wywołać proces zewnętrzny poprzez pojedyncze żądanie HTTP POST. Wywołana usługa powinna obsługiwać elementy webhook i określać, jak używać dowolnego otrzymanego ładunku.

Akcje elementu webhook wymagają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| **Adres URL elementu webhook** |Adres URL elementu webhook. |
| **Niestandardowy ładunek JSON** |Niestandardowy ładunek do wysłania wraz z elementem webhook w przypadku wybrania tej opcji podczas tworzenia alertu. Aby uzyskać więcej informacji, zobacz [Zarządzanie alertami dzienników](alerts-log.md).|

> [!NOTE]
> Przycisk **Wyświetl element webhook** obok opcji **Uwzględnij niestandardowy ładunek JSON dla elementu webhook** dla alertu dziennika Wyświetla przykładowy ładunek elementu webhook dla podanego dostosowania. Nie zawiera ona rzeczywistych danych, ale jest reprezentatywna dla schematu JSON, który jest używany do obsługi alertów dzienników. 

Elementy webhook zawierają adres URL i ładunek sformatowany w formacie JSON, który dane są wysyłane do usługi zewnętrznej. Domyślnie ładunek zawiera wartości z poniższej tabeli. Możesz zamienić ten ładunek na niestandardowy. W takim przypadku należy użyć zmiennych w tabeli dla każdego z parametrów, aby uwzględnić ich wartości w niestandardowym ładunku.


| Parametr | Zmienna | Opis |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nazwa reguły alertu. |
| *Ważność* |#severity |Ważność ustawiona dla alertu wygenerowanego dziennika. |
| *AlertThresholdOperator* |#thresholdoperator |Operator progu dla reguły alertu, który używa wartości większej niż lub mniejszej niż. |
| *AlertThresholdValue* |#thresholdvalue |Wartość progowa dla reguły alertu. |
| *LinkToSearchResults* |#linktosearchresults |Połącz się z portalem analizy, który zwraca rekordy z zapytania, które spowodowało utworzenie alertu. |
| *Właściwości resultcount dla* |#searchresultcount |Liczba rekordów w wynikach wyszukiwania. |
| *Czas zakończenia interwału wyszukiwania* |#searchintervalendtimeutc |Czas zakończenia zapytania w formacie UTC z formatem mm/dd/rrrr HH: mm: ss AM/PM. |
| *Interwał wyszukiwania* |#searchinterval |Przedział czasu dla reguły alertu w formacie gg: mm: SS. |
| *Interwał wyszukiwania czas rozpoczęcia* |#searchintervalstarttimeutc |Godzina rozpoczęcia zapytania w formacie UTC z formatem mm/dd/rrrr HH: mm: ss AM/PM. 
| *SearchQuery* |#searchquery |Zapytanie wyszukiwania w dzienniku używane przez regułę alertu. |
| *SearchResults* |"IncludeSearchResults": true|Rekordy zwracane przez zapytanie jako tabela JSON, które są ograniczone do pierwszych 1 000 rekordów, jeśli "IncludeSearchResults": true jest dodawana do niestandardowej definicji elementu webhook JSON jako Właściwość najwyższego poziomu. |
| *Typ alertu*| #alerttype | Typ reguły alertu dziennika skonfigurowanej jako [pomiar metryki](alerts-unified-log.md#metric-measurement-alert-rules) lub [liczba wyników](alerts-unified-log.md#number-of-results-alert-rules).|
| *Identyfikator obszaru roboczego* |#workspaceid |Identyfikator obszaru roboczego Log Analytics. |
| *Identyfikator aplikacji* |#applicationid |Identyfikator aplikacji Application Insights. |
| *Subscription ID (Identyfikator subskrypcji)* |#subscriptionid |Identyfikator używanej subskrypcji platformy Azure. 

> [!NOTE]
> *LinkToSearchResults* przekazuje parametry, takie jak *SearchQuery*, *Interwał wyszukiwania StartTime*oraz *czas zakończenia interwału wyszukiwania* w adresie URL do Azure Portal do wyświetlania w sekcji Analiza. Azure Portal ma limit rozmiaru identyfikatora URI wynoszący około 2 000 znaków. W portalu *nie* będą otwierane linki podane w alertach, jeśli wartości parametrów przekraczają limit. Możesz ręcznie wprowadzić szczegóły, aby wyświetlić wyniki w portalu analizy. Można też użyć [interfejsu API REST usługi Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) lub [interfejsu API REST log Analytics](/rest/api/loganalytics/) , aby programowo pobrać wyniki. 

Na przykład można określić następujący ładunek niestandardowy, który zawiera pojedynczy parametr o nazwie *Text*. Usługa, którą wywołuje to wywołanie elementu webhook, oczekuje tego parametru.

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
Ponieważ wszystkie zmienne w niestandardowym elemencie webhook muszą być określone w obrębie obudowy JSON, np. "#searchinterval, wynikowy element webhook również zawiera zmienne dane wewnątrz obudów, takie jak" 00:05:00 ".

Aby uwzględnić wyniki wyszukiwania w niestandardowym ładunku, upewnij się, że **IncludeSearchResults** jest ustawiony jako Właściwość najwyższego poziomu w ładunku JSON. 

## <a name="sample-payloads"></a>Przykładowe ładunki
W tej sekcji przedstawiono przykładowe ładunki dla elementów webhook dla alertów dziennika. Przykładowe ładunki zawierają przykłady, gdy ładunek jest standardowy i gdy jest niestandardowy.

### <a name="standard-webhook-for-log-alerts"></a>Standardowy element webhook dla alertów dziennika 
Oba te przykłady mają fikcyjny ładunek z tylko dwiema kolumnami i dwoma wierszami.

#### <a name="log-alert-for-log-analytics"></a>Alert dziennika dla Log Analytics
Następujący przykładowy ładunek dotyczy akcji standardowego elementu webhook *bez niestandardowej opcji JSON* , która jest używana na potrzeby alertów na podstawie log Analytics:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
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
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> Wartość pola "ważność" może ulec zmianie, jeśli [preferencja interfejsu API](alerts-log-api-switch.md) dla alertów dziennika została przełączona na log Analytics.


#### <a name="log-alert-for-application-insights"></a>Alert dziennika dla Application Insights
Następujący przykładowy ładunek dotyczy standardowego elementu webhook *bez opcji NIESTANDARDOWEGO JSON,* gdy jest używany do rejestrowania alertów na podstawie Application Insights:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
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
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Zgłoś alert z niestandardowym ładunkiem JSON
Na przykład, aby utworzyć niestandardowy ładunek, który zawiera tylko nazwę alertu i wyniki wyszukiwania, można użyć następujących czynności: 

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
- Dowiedz się więcej o [Application Insights](../../azure-monitor/app/analytics.md).
- Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md). 

