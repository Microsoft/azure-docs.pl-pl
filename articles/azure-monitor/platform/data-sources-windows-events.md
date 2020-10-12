---
title: Zbieranie i analizowanie dzienników zdarzeń systemu Windows w Azure Monitor | Microsoft Docs
description: Opisuje sposób konfigurowania kolekcji dzienników zdarzeń systemu Windows przez Azure Monitor i szczegóły tworzonych przez nie rekordów.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa34196233ce4037ef6fa49b782b9aa958f7632d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87075260"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Źródła danych dziennika zdarzeń systemu Windows w Azure Monitor
Dzienniki zdarzeń systemu Windows to jedno z najpopularniejszych [źródeł danych](agent-data-sources.md) do zbierania danych przy użyciu agentów systemu Windows, ponieważ wiele aplikacji zapisuje w dzienniku zdarzeń systemu Windows.  Można zbierać zdarzenia z dzienników standardowych, takich jak system i aplikacja, oprócz określania dzienników niestandardowych utworzonych przez aplikacje, które mają być monitorowane.

![Zdarzenia systemu Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurowanie dzienników zdarzeń systemu Windows
Skonfiguruj dzienniki zdarzeń systemu Windows z [menu dane w oknie Ustawienia zaawansowane](agent-data-sources.md#configuring-data-sources).

Azure Monitor zbiera tylko zdarzenia z dzienników zdarzeń systemu Windows, które są określone w ustawieniach.  Możesz dodać dziennik zdarzeń, wpisując nazwę dziennika, a następnie klikając pozycję **+** .  Dla każdego dziennika zbierane są tylko zdarzenia z wybranymi serwerami.  Sprawdź informacje o wykorzystaniu poszczególnych dzienników, które chcesz zebrać.  Nie można podać żadnych dodatkowych kryteriów filtrowania zdarzeń.

Podczas wpisywania nazwy dziennika zdarzeń Azure Monitor zawiera sugestie typowych nazw dzienników zdarzeń. Jeśli dziennik, który chcesz dodać, nie znajduje się na liście, możesz dodać go, wpisując pełną nazwę dziennika. Pełną nazwę dziennika można znaleźć za pomocą podglądu zdarzeń. W Podglądzie zdarzeń Otwórz stronę *Właściwości* dziennika i skopiuj ciąg z pola *pełna nazwa* .

![Konfigurowanie zdarzeń systemu Windows](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Krytyczne zdarzenia w dzienniku zdarzeń systemu Windows będą mieć ważność "błąd" w dziennikach Azure Monitor.

## <a name="data-collection"></a>Zbieranie danych
Azure Monitor zbiera każde zdarzenie, które jest zgodne z wybraną ważnością z monitorowanego dziennika zdarzeń w miarę tworzenia zdarzenia.  Agent rejestruje swoje miejsce w dzienniku zdarzeń, z którego zbiera dane.  Jeśli Agent przejdzie w tryb offline przez pewien czas, zbiera zdarzenia z miejsca, w którym został on ostatnio pozostawiony, nawet jeśli te zdarzenia zostały utworzone, gdy agent był w trybie offline.  Istnieje możliwość, że te zdarzenia nie są zbierane, jeśli dziennik zdarzeń zostanie zawinięty przez zastępowanie niezebranych zdarzeń, gdy Agent jest w trybie offline.

>[!NOTE]
>Azure Monitor nie zbiera zdarzeń inspekcji utworzonych przez SQL Server ze źródłowego *MSSQLSERVER* z identyfikatorem zdarzenia 18453, który zawiera słowa kluczowe — *klasyczne* lub *inspekcji sukces* i *0xa0000000000000*słów kluczowych.
>

## <a name="windows-event-records-properties"></a>Właściwości rekordów zdarzeń systemu Windows
Rekordy zdarzeń systemu Windows mają typ **zdarzenia** i mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Nazwa komputera, z którego został zebrany zdarzenie. |
| EventCategory |Kategoria zdarzenia. |
| EventData |Wszystkie dane zdarzeń w formacie nieprzetworzonym. |
| Identyfikator zdarzenia |Liczba zdarzeń. |
| EventLevel |Ważność zdarzenia w postaci numerycznej. |
| EventLevelName |Ważność zdarzenia w postaci tekstu. |
| Elemencie |Nazwa dziennika zdarzeń, z którego zostały zebrane zdarzenia. |
| ParameterXml |Wartości parametrów zdarzenia w formacie XML. |
| ManagementGroupName |Nazwa grupy zarządzania dla agentów System Center Operations Manager.  W przypadku innych agentów ta wartość jest `AOI-<workspace ID>` |
| RenderedDescription |Opis zdarzenia z wartościami parametrów |
| Element źródłowy |Źródło zdarzenia. |
| SourceSystem |Typ agenta, z którego zostały zebrane zdarzenia. <br> OpsManager — Agent systemu Windows, bezpośrednie połączenie lub Operations Manager zarządzany <br> Linux — Wszyscy agenci systemu Linux  <br> AzureStorage — Diagnostyka Azure |
| TimeGenerated |Data i godzina utworzenia zdarzenia w systemie Windows. |
| Nazwa użytkownika |Nazwa użytkownika konta, które zarejestrowało zdarzenie. |

## <a name="log-queries-with-windows-events"></a>Rejestruj zapytania ze zdarzeniami systemu Windows
W poniższej tabeli przedstawiono różne przykłady zapytań dzienników, które pobierają rekordy zdarzeń systemu Windows.

| Zapytanie | Opis |
|:---|:---|
| Wydarzenie |Wszystkie zdarzenia systemu Windows. |
| &#124; zdarzeń, gdzie EventLevelName = = "Error" |Wszystkie zdarzenia systemu Windows o ważności błędu. |
| Licznik podsumowania &#124; zdarzeń () według źródła |Liczba zdarzeń systemu Windows według źródła. |
| &#124; zdarzeń, gdzie EventLevelName = = "Error" &#124; Sumuj liczbę () według źródła |Liczba zdarzeń błędów systemu Windows według źródła. |


## <a name="next-steps"></a>Następne kroki
* Skonfiguruj Log Analytics, aby zbierać inne [źródła danych](agent-data-sources.md) na potrzeby analizy.
* Informacje na temat [zapytań dzienników](../log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.  
* Skonfiguruj [kolekcję liczników wydajności](data-sources-performance-counters.md) z poziomu agentów systemu Windows.
