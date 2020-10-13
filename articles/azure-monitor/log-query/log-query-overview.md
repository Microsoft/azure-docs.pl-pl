---
title: Przegląd zapytań dzienników w Azure Monitor | Microsoft Docs
description: Odpowiedzi na często zadawane pytania dotyczące zapytań dzienników i ułatwiają rozpoczęcie korzystania z nich.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 55463f6af47ef8eda712b1787a89a710c08c1fe6
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995203"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Przegląd zapytań dzienników w Azure Monitor
Zapytania dzienników ułatwiają całkowite wykorzystanie wartości danych zebranych w [dziennikach Azure monitor](../platform/data-platform-logs.md). Zaawansowany język zapytań umożliwia sprzęganie danych z wielu tabel, agregowanie dużych zestawów danych i wykonywanie złożonych operacji przy minimalnym kodzie. Niemal każde pytanie może być odpowiedzią i analizą wykonywaną, o ile dane pomocnicze zostały zebrane, i zrozumieć, jak utworzyć odpowiednie zapytanie.

Niektóre funkcje w Azure Monitor, takie jak [szczegółowe informacje](../insights/insights-overview.md) i [rozwiązania](../monitor-reference.md) , przetwarzają dane dziennika bez udostępniania Cię do podstawowych zapytań. Aby w pełni wykorzystać inne funkcje Azure Monitor, należy zrozumieć, jak są konstruowane zapytania i jak można ich używać do interaktywnego analizowania danych w dziennikach Azure Monitor.

Skorzystaj z tego artykułu jako punktu wyjścia do uczenia się na temat zapytań dzienników w Azure Monitor. Odpowiedzi na często zadawane pytania i udostępniają linki do innej dokumentacji, która zapewnia dalsze szczegóły i lekcje.

## <a name="how-can-i-learn-how-to-write-queries"></a>Jak można dowiedzieć się, jak pisać zapytania?
Jeśli chcesz przejść bezpośrednio do elementów, możesz zacząć od następujących samouczków:

- [Rozpocznij pracę z log Analytics w Azure monitor](get-started-portal.md).
- [Rozpocznij pracę z kwerendami dzienników w Azure monitor](get-started-queries.md).

Po ustaleniu podstaw możesz zapoznać się z wieloma lekcjami przy użyciu własnych danych lub danych z naszego środowiska demonstracyjnego, zaczynając od: 

- [Pracuj z ciągami w zapytaniach dziennika Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Jakiego języka używają zapytania dzienników?
Dzienniki Azure Monitor opierają się na [usłudze Azure Eksplorator danych](/azure/data-explorer), a zapytania dzienników są zapisywane przy użyciu tego samego języka zapytań Kusto (KQL). Jest to bogaty język zaprojektowany, aby można było go łatwo odczytać i utworzyć, i powinien być w stanie rozpocząć korzystanie z niego z minimalnymi wskazówkami.

Zapoznaj się z [dokumentacją usługi Azure Eksplorator danych KQL](/azure/kusto/query) , aby uzyskać pełną dokumentację dotyczącą KQL i informacje o różnych dostępnych funkcjach.<br>
Zobacz Rozpoczynanie [pracy z dziennikami w Azure monitor](get-started-queries.md) , aby szybko zapoznać się z językiem przy użyciu danych z dzienników Azure monitor.
Zapoznaj się z tematem [różnice w języku zapytań dzienników Azure monitor](data-explorer-difference.md) , aby uzyskać drobne różnice w wersji KQL używanej przez Azure monitor.

## <a name="what-data-is-available-to-log-queries"></a>Jakie dane są dostępne do rejestrowania zapytań?
Wszystkie dane zbierane w dziennikach Azure Monitor są dostępne do pobierania i analizowania zapytań dzienników. Różne źródła danych zapisują swoje dane w różnych tabelach, ale można dołączyć wiele tabel w jednym zapytaniu, aby analizować dane w wielu źródłach. Podczas tworzenia zapytania należy zacząć od określenia, które tabele mają szukane dane. Zobacz [strukturę dzienników Azure monitor](../platform/data-platform-logs.md) , aby dowiedzieć się, jak dane są strukturalne.

## <a name="what-does-a-log-query-look-like"></a>Jak wygląda zapytanie dziennika?
Zapytanie może być proste jako nazwa pojedynczej tabeli do pobrania wszystkich rekordów z tej tabeli:

```Kusto
Syslog
```

Można też odfiltrować określone rekordy, podsumowywać je i wizualizować wyniki na wykresie:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Aby uzyskać bardziej złożoną analizę, można pobrać dane z wielu tabel przy użyciu sprzężenia, aby analizować wyniki jednocześnie.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Nawet jeśli nie znasz KQL, powinna być możliwa co najmniej ilustracja podstawowa Logika używana przez te zapytania. Zaczynają się one od nazwy tabeli, a następnie dodają wiele poleceń do filtrowania i przetwarzania tych danych. Zapytanie może korzystać z dowolnej liczby poleceń i można napisać bardziej skomplikowane zapytania, aby zapoznać się z różnymi poleceniami KQL.

Zobacz Rozpoczynanie [pracy z dziennikami zapytań w Azure monitor](get-started-queries.md) , aby zapoznać się z samouczkiem dotyczącym zapytań dziennika, które wprowadzają język i typowe funkcje.<br>


## <a name="what-is-log-analytics"></a>Co to jest usługa Log Analytics?
Usługa Log Analytics to podstawowe narzędzie w witrynie Azure Portal do pisania zapytań dzienników i interaktywnego analizowania ich wyników. Nawet jeśli zapytanie dziennika jest używane w innym miejscu usługi Azure Monitor, zwykle jest ono pisane i testowane przy użyciu usługi Log Analytics.

Log Analytics można uruchomić z kilku miejsc w Azure Portal. Zakres danych dostępnych do Log Analytics jest określany na podstawie sposobu jego uruchomienia. Aby uzyskać więcej informacji, zobacz [zakres zapytań](scope.md) .

- Wybierz pozycję **dzienniki** z menu **Azure monitor** lub **log Analytics obszary robocze** .
- Wybierz pozycję **dzienniki** na stronie **Przegląd** aplikacji Application Insights.
- Wybierz pozycję **dzienniki** z menu zasobu platformy Azure.

![Usługa Log Analytics](media/log-query-overview/log-analytics.png)

Zobacz artykuł Wprowadzenie do [log Analytics w Azure monitor](get-started-portal.md) , aby zapoznać się z samouczkiem dotyczącym log Analytics, które wprowadzają kilka funkcji.

## <a name="where-else-are-log-queries-used"></a>Gdzie są używane zapytania dziennika?
Oprócz interakcyjnej pracy z kwerendami dzienników i ich wyników w Log Analytics, obszary w Azure Monitor, w których będą używane zapytania, obejmują następujące elementy:

- **Reguły alertów.** [Reguły alertów](../platform/alerts-overview.md) aktywnie identyfikują problemy z danych w obszarze roboczym.  Każda reguła alertu jest oparta na przeszukiwaniu dzienników, który jest automatycznie uruchamiany w regularnych odstępach czasu.  Wyniki są sprawdzane w celu określenia, czy ma zostać utworzony alert.
- **Pulpitów nawigacyjnych.** Możesz przypiąć wyniki dowolnego zapytania do [pulpitu nawigacyjnego platformy Azure](../learn/tutorial-logs-dashboards.md) , który umożliwia wspólne wizualizację danych dzienników i metryk, a opcjonalnie udostępnianie innym użytkownikom platformy Azure.
- **Widoki.**  Wizualizacje danych, które mają być dołączone do pulpitów nawigacyjnych użytkowników, można tworzyć za pomocą [projektanta widoków](../platform/view-designer.md).  Zapytania dziennika zapewniają dane używane przez [kafelki](../platform/view-designer-tiles.md) i [części wizualizacji](../platform/view-designer-parts.md) w każdym widoku.  
- **Wywozu.**  Podczas importowania danych dziennika z Azure Monitor do programu Excel lub [Power BI](../platform/powerbi.md), należy utworzyć zapytanie dziennika w celu zdefiniowania danych do wyeksportowania.
- **Narzędzia.** Skrypt programu PowerShell można uruchomić z poziomu wiersza polecenia lub Azure Automation elementu Runbook, który używa [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) , aby pobrać dane dziennika z Azure monitor.  To polecenie cmdlet wymaga zapytania, aby określić dane do pobrania.
- **Interfejs API dzienników Azure Monitor.**  [Interfejs API dzienników Azure monitor](https://dev.loganalytics.io) umożliwia wszystkim klientom interfejsu API REST pobieranie danych dziennika z obszaru roboczego.  Żądanie interfejsu API zawiera zapytanie, które jest uruchamiane względem Azure Monitor, aby określić dane do pobrania.


## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z [samouczkiem dotyczącym korzystania z log Analytics w Azure Portal](get-started-portal.md).
- Zapoznaj się z [samouczkiem dotyczącym pisania zapytań](get-started-queries.md).