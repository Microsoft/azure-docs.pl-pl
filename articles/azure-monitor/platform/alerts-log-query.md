---
title: Rejestruj zapytania alertów w Azure Monitor | Microsoft Docs
description: Zawiera zalecenia dotyczące tworzenia wydajnych zapytań dotyczących alertów dziennika w ramach aktualizacji Azure Monitor i procesu konwertowania istniejących zapytań.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77667792"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Rejestruj zapytania alertów w Azure Monitor
[Reguły alertów na podstawie dzienników Azure monitor](alerts-unified-log.md) są uruchamiane w regularnych odstępach czasu, dlatego należy upewnić się, że są one zapisane w celu zminimalizowania obciążenia i opóźnienia. Ten artykuł zawiera zalecenia dotyczące tworzenia wydajnych zapytań dotyczących alertów dziennika oraz procesu konwertowania istniejących zapytań. 

## <a name="types-of-log-queries"></a>Typy zapytań dziennika
[Zapytania dzienników w Azure monitor](../log-query/log-query-overview.md) zaczynają się od tabeli lub operatora [wyszukiwania](/azure/kusto/query/searchoperator) lub [Union](/azure/kusto/query/unionoperator) .

Na przykład następujące zapytanie jest objęte zakresem tabeli _SecurityEvent_ i wyszukuje określony identyfikator zdarzenia. Jest to jedyna tabela, którą musi przetworzyć zapytanie.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Zapytania, które zaczynają się od `search` lub `union` umożliwiają przeszukiwanie wielu kolumn w tabeli lub nawet wielu tabel. W poniższych przykładach przedstawiono wiele metod przeszukiwania okresu ważności _pamięci_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Chociaż `search` i `union` są przydatne podczas eksploracji danych, wyszukiwanie w całym modelu danych jest mniej wydajne niż użycie tabeli, ponieważ muszą one być skanowane w wielu tabelach. Ponieważ zapytania w regułach alertów są uruchamiane w regularnych odstępach czasu, może to spowodować nadmierne obciążenie Dodawanie opóźnień do alertu. Z powodu tego kosztu zapytania dotyczące reguł alertów dziennika na platformie Azure powinny zawsze rozpoczynać się od tabeli w celu zdefiniowania jasnego zakresu, co poprawia wydajność zapytań i istotność wyników.

## <a name="unsupported-queries"></a>Nieobsługiwane zapytania
Od 11 stycznia 2019 roku Tworzenie lub modyfikowanie reguł alertów dziennika, które używają `search` lub `union` Operatory nie będzie obsługiwane w Azure Portal. Użycie tych operatorów w regule alertu zwróci komunikat o błędzie. Ta zmiana nie wpłynie na istniejące reguły alertów i reguły alertów utworzone i edytowane za pomocą interfejsu API Log Analytics. Mimo to należy rozważyć zmianę wszelkich reguł alertów korzystających z tych typów zapytań, aby zwiększyć ich wydajność.  

Ta zmiana nie ma wpływ na reguły alertów dziennika używające [zapytań obejmujących wiele zasobów](../log-query/cross-workspace-query.md) `union` , które ograniczają zakres zapytania do określonych zasobów. Nie jest to odpowiednik `union *` , którego nie można użyć.  Poniższy przykład będzie prawidłowy w regule alertu dziennika:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Zapytanie między zasobami](../log-query/cross-workspace-query.md) w ramach alertów dziennika jest obsługiwane w nowym [interfejsie API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Domyślnie Azure Monitor używa [starszego interfejsu API alertów log Analytics](api-alerts.md) na potrzeby tworzenia nowych reguł alertów dziennika z Azure Portal, chyba że zostanie przełączony w [STARSZEJ wersji interfejsu API alertów dziennika](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po przełączeniu nowy interfejs API zostanie ustawiony jako domyślny dla nowych reguł alertów w Azure Portal i umożliwia tworzenie reguł alertów dziennika zapytań dla wielu zasobów. Można tworzyć reguły alertów dziennika [zapytań dla wielu zasobów](../log-query/cross-workspace-query.md) bez przełączenia przy użyciu [szablonu ARM dla interfejsu API scheduledQueryRules](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , ale ta reguła alertu jest możliwa do zarządzania, chociaż [interfejs API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , a nie z Azure Portal.

## <a name="examples"></a>Przykłady
Poniższe przykłady obejmują zapytania dziennika, które używają `search` i `union` i udostępniają kroki, których można użyć do modyfikacji tych zapytań do użycia z regułami alertów.

### <a name="example-1"></a>Przykład 1
Chcesz utworzyć regułę alertu dziennika za pomocą następującego zapytania, które pobiera informacje o wydajności przy użyciu `search` : 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Aby zmodyfikować to zapytanie, Zacznij od następującego zapytania, aby zidentyfikować tabelę, do której należą właściwości:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Wynik tego zapytania pokazuje, że właściwość _CounterName_ pochodzi z tabeli _perf_ . 

Ten wynik służy do tworzenia następującego zapytania, którego można użyć dla reguły alertu:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Przykład 2
Chcesz utworzyć regułę alertu dziennika za pomocą następującego zapytania, które pobiera informacje o wydajności przy użyciu `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Aby zmodyfikować to zapytanie, Zacznij od następującego zapytania, aby zidentyfikować tabelę, do której należą właściwości:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Wynik tego zapytania pokazuje, że właściwość _ObjectName_ i _CounterName_ pochodzi z tabeli _perf_ . 

Ten wynik służy do tworzenia następującego zapytania, którego można użyć dla reguły alertu:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Przykład 3

Chcesz utworzyć regułę alertu dziennika przy użyciu następującego zapytania, które używa obu `search` i `union` do pobrania informacji o wydajności: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Aby zmodyfikować to zapytanie, Zacznij od następującego zapytania, aby zidentyfikować tabelę, do której należą właściwości w pierwszej części zapytania: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Wynik tego zapytania pokazuje, że wszystkie te właściwości pochodzą z tabeli _wydajności_ . 

Teraz Użyj `union` `withsource` polecenia with, aby zidentyfikować tabelę źródłową, która ma udział w każdym wierszu.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Wynik tego zapytania pokazuje, że te właściwości również pochodzą z tabeli _wydajności_ . 

Za pomocą tych wyników można utworzyć następujące zapytanie, którego można użyć dla reguły alertu: 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
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
)  
on Hour 
| count 
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

 
Wynik wskazuje, że właściwości po prawej stronie sprzężenia należą do tabeli pulsu. 

Za pomocą tych wyników można utworzyć następujące zapytanie, którego można użyć dla reguły alertu: 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Następne kroki
- Informacje o [alertach dzienników](alerts-log.md) w Azure monitor.
- Dowiedz się więcej na temat [zapytań dzienników](../log-query/log-query-overview.md).

