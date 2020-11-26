---
title: Optymalizowanie zapytań alertów dziennika | Microsoft Docs
description: Zalecenia dotyczące pisania wydajnych zapytań dotyczących alertów
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: e7c9c76816b5d1ee2eedfb7e54645e056906feef
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186630"
---
# <a name="optimizing-log-alert-queries"></a>Optymalizowanie zapytań dotyczących alertów dziennika
W tym artykule opisano sposób pisania i konwertowania zapytań [alertów dziennika](alerts-unified-log.md) w celu uzyskania optymalnej wydajności. Zoptymalizowane zapytania zmniejszają opóźnienia i obciążenia alertów, które często uruchamiają się.

## <a name="how-to-start-writing-an-alert-log-query"></a>Jak rozpocząć pisanie zapytania dziennika alertu

Zapytania alertów zaczynają [wysyłać zapytania o dane dziennika w log Analytics](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) wskazujące problem. Możesz użyć [przykładu zapytania alertu](../log-query/example-queries.md) , aby zrozumieć, co można odnaleźć. Możesz również [rozpocząć tworzenie własnych zapytań](../log-query/log-analytics-tutorial.md). 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>Zapytania wskazujące problem, a nie alert

Przepływ alertów został utworzony w celu przekształcenia wyników wskazujących na problem z alertem. Na przykład w przypadku zapytania, takiego jak:

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

### <a name="avoid-limit-and-take-operators"></a>Unikanie `limit` i `take` Operatory

Użycie `limit` i `take` w zapytaniach może zwiększyć opóźnienia i obciążenie alertów, ponieważ wyniki nie są spójne w czasie. Preferowana jest ich użycie tylko w razie konieczności.

## <a name="log-query-constraints"></a>Ograniczenia zapytania dziennika
[Zapytania dzienników w Azure monitor](../log-query/log-query-overview.md) zaczynają się od tabeli, [`search`](/azure/kusto/query/searchoperator) lub [`union`](/azure/kusto/query/unionoperator) operatora.

Zapytania dotyczące reguł alertów dziennika powinny zawsze rozpoczynać się od tabeli w celu zdefiniowania jasnego zakresu, co poprawia wydajność zapytań i istotność wyników. Zapytania w regułach alertów działają często, dlatego korzystanie z `search` `union` nich może spowodować nadmierne obciążenie, dodając opóźnienie do alertu, ponieważ wymaga ono skanowania między wieloma tabelami. Te operatory zmniejszają również możliwość optymalizacji zapytania przez usługę alertów.

Nie obsługujemy tworzenia lub modyfikowania reguł alertów dziennika, które są używane przez operatory OR i nie są `search` `union` oczekiwane w przypadku zapytań między zasobami.

Na przykład następujące zapytanie o alerty jest objęte zakresem tabeli _SecurityEvent_ i wyszukuje określony identyfikator zdarzenia. Jest to jedyna tabela, która musi być przetwarzana przez zapytanie.

``` Kusto
SecurityEvent
| where EventID == 4624
```

Ta zmiana nie ma wpływ na reguły alertów dziennika, ponieważ [zapytania między zasobami](../log-query/cross-workspace-query.md) używają typu `union` , który ogranicza zakres zapytania do określonych zasobów. Poniższy przykład będzie prawidłowym zapytaniem alertu dziennika:

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> [Zapytania między zasobami](../log-query/cross-workspace-query.md) są obsługiwane w nowym [interfejsie API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules). Jeśli nadal używasz [starszego interfejsu API log Analytics alertów](api-alerts.md) do tworzenia alertów dziennika, możesz dowiedzieć się więcej o przełączaniu w [tym miejscu](alerts-log-api-switch.md).

## <a name="examples"></a>Przykłady
Poniższe przykłady obejmują zapytania dziennika, które używają `search` i `union` i udostępniają kroki, których można użyć do modyfikacji tych zapytań do użycia w regułach alertów.

### <a name="example-1"></a>Przykład 1
Chcesz utworzyć regułę alertu dziennika przy użyciu następującego zapytania pobierającego informacje o wydajności przy użyciu `search` : 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

Aby zmodyfikować to zapytanie, Zacznij od następującego zapytania, aby zidentyfikować tabelę, do której należą właściwości:

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

Wynik tego zapytania pokazuje, że właściwość _CounterName_ pochodzi z tabeli _perf_ .

Za pomocą tego wyniku można utworzyć następujące zapytanie, które będzie używane dla reguły alertu:

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>Przykład 2
Chcesz utworzyć regułę alertu dziennika przy użyciu następującego zapytania pobierającego informacje o wydajności przy użyciu `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

Aby zmodyfikować to zapytanie, Zacznij od następującego zapytania, aby zidentyfikować tabelę, do której należą właściwości:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

Wynik tego zapytania pokazuje, że właściwość _ObjectName_ i _CounterName_ pochodzi z tabeli _perf_ .

Za pomocą tego wyniku można utworzyć następujące zapytanie, które będzie używane dla reguły alertu:

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>Przykład 3

Chcesz utworzyć regułę alertu dziennika przy użyciu następującego zapytania, które używa obu `search` i `union` do pobrania informacji o wydajności: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

Aby zmodyfikować to zapytanie, Zacznij od następującego zapytania, aby zidentyfikować tabelę, do której należą właściwości w pierwszej części zapytania: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

Wynik tego zapytania pokazuje, że wszystkie te właściwości pochodzą z tabeli _wydajności_ .

Teraz Użyj `union` `withsource` polecenia with, aby zidentyfikować tabelę źródłową, która ma udział w każdym wierszu.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

Wynik tego zapytania pokazuje, że te właściwości również pochodzą z tabeli _wydajności_ .

Za pomocą tych wyników można utworzyć następujące zapytanie, które będzie używane dla reguły alertu: 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>Przykład 4
Chcesz utworzyć regułę alertu dziennika przy użyciu następującego zapytania, które łączy wyniki dwóch `search` zapytań:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

Aby zmodyfikować zapytanie, Zacznij od następującego zapytania, aby zidentyfikować tabelę zawierającą właściwości z lewej strony sprzężenia: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

Wynik wskazuje, że właściwości z lewej strony sprzężenia należą do tabeli _SecurityEvent_ . 

Teraz użyj następującego zapytania, aby zidentyfikować tabelę zawierającą właściwości znajdujące się po prawej stronie sprzężenia: 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
Wynik wskazuje, że właściwości po prawej stronie sprzężenia należą do tabeli _pulsu_ .

Za pomocą tych wyników można utworzyć następujące zapytanie, które będzie używane dla reguły alertu: 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>Następne kroki
- Informacje o [alertach dzienników](alerts-log.md) w Azure monitor.
- Dowiedz się więcej na temat [zapytań dzienników](../log-query/log-query-overview.md).