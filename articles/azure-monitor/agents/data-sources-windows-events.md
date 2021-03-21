---
title: Zbierz źródła danych dziennika zdarzeń systemu Windows za pomocą agenta Log Analytics w Azure Monitor
description: Opisuje sposób konfigurowania kolekcji dzienników zdarzeń systemu Windows przez Azure Monitor i szczegóły tworzonych przez nie rekordów.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 0eaa73fa7e0a9896a875af7e3a3aab22db2a37d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657063"
---
# <a name="collect-windows-event-log-data-sources-with-log-analytics-agent"></a>Zbieranie źródeł danych dziennika zdarzeń systemu Windows za pomocą agenta Log Analytics
Dzienniki zdarzeń systemu Windows to jedno z najpopularniejszych [źródeł danych](../agents/agent-data-sources.md) dla agentów log Analytics na maszynach wirtualnych z systemem Windows, ponieważ wiele aplikacji zapisuje je w dzienniku zdarzeń systemu Windows.  Można zbierać zdarzenia z dzienników standardowych, takich jak system i aplikacja, oprócz określania dzienników niestandardowych utworzonych przez aplikacje, które mają być monitorowane.

> [!IMPORTANT]
> W tym artykule opisano zbieranie zdarzeń systemu Windows za pomocą [agenta log Analytics](./log-analytics-agent.md) , który jest jednym z agentów używanych przez Azure monitor. Inni Agenci zbierają różne dane i są skonfigurowani inaczej. Zobacz [Omówienie agentów Azure monitor](../agents/agents-overview.md) , aby uzyskać listę dostępnych agentów oraz dane, które mogą zbierać.

![Zdarzenia systemu Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurowanie dzienników zdarzeń systemu Windows
Skonfiguruj dzienniki zdarzeń systemu Windows z [menu konfiguracji agentów](../agents/agent-data-sources.md#configuring-data-sources) dla obszaru roboczego log Analytics.

Azure Monitor zbiera tylko zdarzenia z dzienników zdarzeń systemu Windows, które są określone w ustawieniach.  Możesz dodać dziennik zdarzeń, wpisując nazwę dziennika, a następnie klikając pozycję **+** .  Dla każdego dziennika zbierane są tylko zdarzenia z wybranymi serwerami.  Sprawdź informacje o wykorzystaniu poszczególnych dzienników, które chcesz zebrać.  Nie można podać żadnych dodatkowych kryteriów filtrowania zdarzeń.

Podczas wpisywania nazwy dziennika zdarzeń Azure Monitor zawiera sugestie typowych nazw dzienników zdarzeń. Jeśli dziennik, który chcesz dodać, nie znajduje się na liście, możesz dodać go, wpisując pełną nazwę dziennika. Pełną nazwę dziennika można znaleźć za pomocą podglądu zdarzeń. W Podglądzie zdarzeń Otwórz stronę *Właściwości* dziennika i skopiuj ciąg z pola *pełna nazwa* .

[![Konfigurowanie zdarzeń systemu Windows](media/data-sources-windows-events/configure.png)](media/data-sources-windows-events/configure.png#lightbox)

> [!IMPORTANT]
> Nie można skonfigurować kolekcji zdarzeń zabezpieczeń z obszaru roboczego. Aby zbierać zdarzenia zabezpieczeń, należy użyć [Azure Security Center](../../security-center/security-center-enable-data-collection.md) lub punktów kontrolnych [platformy Azure](../../sentinel/connect-windows-security-events.md) .


> [!NOTE]
> Krytyczne zdarzenia w dzienniku zdarzeń systemu Windows będą mieć ważność "błąd" w dziennikach Azure Monitor.

## <a name="data-collection"></a>Zbieranie danych
Azure Monitor zbiera każde zdarzenie, które jest zgodne z wybraną ważnością z monitorowanego dziennika zdarzeń w miarę tworzenia zdarzenia.  Agent rejestruje swoje miejsce w dzienniku zdarzeń, z którego zbiera dane.  Jeśli Agent przejdzie w tryb offline przez pewien czas, zbiera zdarzenia z miejsca, w którym został on ostatnio pozostawiony, nawet jeśli te zdarzenia zostały utworzone, gdy agent był w trybie offline.  Istnieje możliwość, że te zdarzenia nie są zbierane, jeśli dziennik zdarzeń zostanie zawinięty przez zastępowanie niezebranych zdarzeń, gdy Agent jest w trybie offline.

>[!NOTE]
>Azure Monitor nie zbiera zdarzeń inspekcji utworzonych przez SQL Server ze źródłowego *MSSQLSERVER* z identyfikatorem zdarzenia 18453, który zawiera słowa kluczowe — *klasyczne* lub *inspekcji sukces* i *0xa0000000000000* słów kluczowych.
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
| Zdarzenie |Wszystkie zdarzenia systemu Windows. |
| &#124; zdarzeń, gdzie EventLevelName = = "Error" |Wszystkie zdarzenia systemu Windows o ważności błędu. |
| Licznik podsumowania &#124; zdarzeń () według źródła |Liczba zdarzeń systemu Windows według źródła. |
| &#124; zdarzeń, gdzie EventLevelName = = "Error" &#124; Sumuj liczbę () według źródła |Liczba zdarzeń błędów systemu Windows według źródła. |


## <a name="next-steps"></a>Następne kroki
* Skonfiguruj Log Analytics, aby zbierać inne [źródła danych](../agents/agent-data-sources.md) na potrzeby analizy.
* Informacje na temat [zapytań dzienników](../logs/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.  
* Skonfiguruj [kolekcję liczników wydajności](data-sources-performance-counters.md) z poziomu agentów systemu Windows.
