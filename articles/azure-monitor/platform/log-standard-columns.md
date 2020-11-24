---
title: Kolumny standardowe w Azure Monitor rekordy dziennika | Microsoft Docs
description: Opisuje kolumny, które są wspólne dla wielu typów danych w dziennikach Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 2370f76bacb8645f1b343da4f056c8bcf06a26dd
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95796720"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Standardowe kolumny w dziennikach Azure Monitor
Dane w dziennikach Azure Monitor są [przechowywane jako zestaw rekordów w obszarze roboczym log Analytics lub w aplikacji Application Insights](./data-platform-logs.md), z których każdy ma określony typ danych, który ma unikatowy zestaw kolumn. Wiele typów danych będzie zawierać standardowe kolumny, które są wspólne dla wielu typów. W tym artykule opisano te kolumny i przedstawiono przykłady korzystania z nich w zapytaniach.

Aplikacje oparte na obszarze roboczym w Application Insights przechowują swoje dane w obszarze roboczym Log Analytics i używają tych samych standardowych kolumn, jak inne tabele w obszarze roboczym. Aplikacje klasyczne przechowują swoje dane oddzielnie i mają różne kolumny standardowe zgodnie z opisem w tym artykule.

> [!NOTE]
> Niektóre kolumny standardowe nie będą wyświetlane w widoku schematu ani IntelliSense w Log Analytics i nie będą wyświetlane w wynikach zapytania, chyba że jawnie określisz kolumnę w danych wyjściowych.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated i znacznik czasu
Kolumny **TimeGenerated** (obszar roboczy log Analytics) i **sygnatura czasowa** (aplikacja Application Insights) zawierają datę i godzinę utworzenia rekordu przez źródło danych. Aby uzyskać więcej informacji, zobacz czas pozyskiwania [danych dziennika w Azure monitor](data-ingestion-time.md) .

**TimeGenerated** i **sygnatura czasowa** zapewniają wspólną kolumnę do użycia na potrzeby filtrowania lub podsumowywania według czasu. Po wybraniu przedziału czasu dla widoku lub pulpitu nawigacyjnego w Azure Portal do filtrowania wyników służy TimeGenerated lub znacznik czasu. 

### <a name="examples"></a>Przykłady

Następujące zapytanie zwraca liczbę zdarzeń błędów utworzonych dla każdego dnia w poprzednim tygodniu.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

Następujące zapytanie zwraca liczbę wyjątków utworzonych dla każdego dnia w poprzednim tygodniu.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
Kolumna **\_ TimeReceived** zawiera datę i godzinę odebrania rekordu przez punkt pozyskiwania Azure monitor w chmurze platformy Azure. Może to być przydatne do identyfikowania problemów opóźnienia między źródłem danych i chmurą. Przykładem może być problem z siecią, powodujący opóźnienie przesyłania danych z agenta. Aby uzyskać więcej informacji, zobacz czas pozyskiwania [danych dziennika w Azure monitor](data-ingestion-time.md) .

Następujące zapytanie zwraca średni czas oczekiwania (według godziny) dla rekordów zdarzeń z agenta. Obejmuje to czas od agenta do chmury oraz łączny czas dla rekordu, który będzie dostępny dla zapytań dzienników.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Typ i itemType
Kolumny **Typ** (log Analytics obszar roboczy) i **ItemType** (Application Insights Application) zawierają nazwę tabeli, z której został pobrany rekord, który może być również uważany za typ rekordu. Ta kolumna jest przydatna w zapytaniach, które łączą rekordy z wielu tabel, takich jak te, które używają `search` operatora, aby rozróżnić rekordy różnych typów. **$Table** można używać zamiast **typu** w niektórych miejscach.

### <a name="examples"></a>Przykłady
Następujące zapytanie zwraca liczbę rekordów według typu zebranych w ciągu ostatniej godziny.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_Elementów
Kolumna **\_ ItemId** zawiera unikatowy identyfikator dla rekordu.


## <a name="_resourceid"></a>\_ResourceId
Kolumna **\_ ResourceID** zawiera unikatowy identyfikator zasobu, z którym jest skojarzony rekord. Zapewnia to standardową kolumnę, która ma być używana do określania zakresu zapytania tylko do rekordów z określonego zasobu lub do łączenia się z danymi w wielu tabelach.

W przypadku zasobów platformy Azure wartość **_ResourceId** jest [adresem URL identyfikatora zasobu platformy Azure](../../azure-resource-manager/templates/template-functions-resource.md). Kolumna jest ograniczona do zasobów platformy Azure, w tym zasobów [usługi Azure Arc](../../azure-arc/overview.md) , lub do dzienników niestandardowych, które wskazywały identyfikator zasobu podczas pozyskiwania.

> [!NOTE]
> Niektóre typy danych mają już pola zawierające identyfikator zasobu platformy Azure lub co najmniej te elementy, takie jak identyfikator subskrypcji. Chociaż te pola są utrzymywane w celu zapewnienia zgodności z poprzednimi wersjami, zaleca się używanie _ResourceId do wykonywania wzajemnej korelacji, ponieważ będzie ona bardziej spójna.

### <a name="examples"></a>Przykłady
Poniższe zapytanie dołącza dane dotyczące wydajności i zdarzeń dla każdego komputera. Pokazuje wszystkie zdarzenia o IDENTYFIKATORze _101_ i użycia procesora przekraczającym 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Następujące zapytanie sprzęga rekordy _platformy Azure_ z rekordami _SecurityEvent_ . Pokazuje wszystkie operacje działania z użytkownikami, którzy zostali zarejestrowani na tych komputerach.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

Poniższe zapytanie analizuje **_ResourceId** i agreguje rozliczane woluminy danych na grupę zasobów platformy Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

Te `union withsource = tt *` zapytania są oszczędnie zależą od tego, jak skanowanie między typami danych jest kosztowne.

Użycie kolumny identyfikatora subskrypcji jest zawsze wydajniejsze \_ niż wyodrębnianie jej przez analizowanie \_ kolumny ResourceID.

## <a name="_substriptionid"></a>\_SubstriptionId
Kolumna **\_ subskrypcji** zawiera identyfikator subskrypcji zasobu, z którym jest skojarzony rekord. Zapewnia to standardową kolumnę, która ma być używana do określania zakresu zapytania tylko do rekordów z określonej subskrypcji lub do porównywania różnych subskrypcji.

W przypadku zasobów platformy Azure wartość **__SubscriptionId** jest częścią subskrypcji [adresu URL identyfikatora zasobu platformy Azure](../../azure-resource-manager/templates/template-functions-resource.md). Kolumna jest ograniczona do zasobów platformy Azure, w tym zasobów [usługi Azure Arc](../../azure-arc/overview.md) , lub do dzienników niestandardowych, które wskazywały identyfikator zasobu podczas pozyskiwania.

> [!NOTE]
> Niektóre typy danych mają już pola zawierające Identyfikator subskrypcji platformy Azure. Chociaż te pola są przechowywane na potrzeby zgodności z poprzednimi wersjami, zaleca się użycie \_ kolumny identyfikatora subskrypcji w celu przeprowadzenia wzajemnej korelacji, ponieważ będzie ona bardziej spójna.
### <a name="examples"></a>Przykłady
Poniższe zapytanie bada dane wydajności dla komputerów z określoną subskrypcją. 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "57366bcb3-7fde-4caf-8629-41dc15e3b352"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

Poniższe zapytanie analizuje **_ResourceId** i agreguje rozliczane woluminy danych na subskrypcję platformy Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

Te `union withsource = tt *` zapytania są oszczędnie zależą od tego, jak skanowanie między typami danych jest kosztowne.


## <a name="_isbillable"></a>\_Ismiliard
Kolumna **\_ isbilld** określa, czy są naliczane opłaty za pozyskiwane dane. Dane z **\_** niepłatną opłatą `false` są zbierane bezpłatnie i nie są naliczane za Twoje konto platformy Azure.

### <a name="examples"></a>Przykłady
Aby uzyskać listę komputerów wysyłających typy danych, należy użyć następującej kwerendy:

> [!NOTE]
> Używaj zapytań z `union withsource = tt *` nieoszczędnymi możliwościami w miarę wykonywania skanowania między typami danych. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Można to rozszerzyć, aby zwrócić liczbę komputerów na godzinę, które wysyłają typy danych rozliczanych:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
Kolumna **\_ BilledSize** określa rozmiar w bajtach danych, które będą rozliczane na konto platformy Azure, jeśli jest **\_** to wartość true.


### <a name="examples"></a>Przykłady
Aby wyświetlić wielkość rozliczania zdarzeń pobieranych na komputer, użyj kolumny, `_BilledSize` która zapewnia rozmiar w bajtach:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Aby wyświetlić wielkość rozliczania zdarzeń pobieranych na subskrypcję, użyj następującego zapytania:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

Aby wyświetlić rozmiar pozyskanych zdarzeń rozliczanych według grupy zasobów, należy użyć następującego zapytania:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Aby wyświetlić liczbę zdarzeń pozyskanych na komputer, użyj następującego zapytania:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Aby zobaczyć liczbę zdarzeń rozliczanych pobieranych na komputer, użyj następującego zapytania: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Aby wyświetlić liczbę typów danych rozliczanych z określonego komputera, użyj następującego zapytania:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat sposobu [przechowywania danych dziennika Azure monitor](../log-query/log-query-overview.md).
- Zapoznaj się z lekcjami dotyczącymi [pisania zapytań dzienników](../log-query/get-started-queries.md).
- Zapoznaj się z lekcji na [sprzęganie tabel w zapytaniach dziennika](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
