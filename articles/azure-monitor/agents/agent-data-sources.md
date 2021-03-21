---
title: Źródła danych agenta Log Analytics w Azure Monitor
description: Źródła danych definiują dane dziennika, które Azure Monitor zbierać z agentów i innych podłączonych źródeł.  W tym artykule opisano sposób, w jaki Azure Monitor korzystają ze źródeł danych, wyjaśniono szczegółowe informacje na temat sposobu ich konfigurowania i zawiera podsumowanie różnych dostępnych źródeł danych.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 51cdee9c899feeb003a7d6301d2da0749fad65e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041942"
---
# <a name="log-analytics-agent-data-sources-in-azure-monitor"></a>Źródła danych agenta Log Analytics w Azure Monitor
Dane, które Azure Monitor zbiera z maszyn wirtualnych z agentem [log Analytics](./log-analytics-agent.md) , są definiowane przez źródła danych skonfigurowane w [obszarze roboczym log Analytics](../logs/data-platform-logs.md).   Każde źródło danych tworzy rekordy określonego typu z każdym typem, który ma swój własny zestaw właściwości.

> [!IMPORTANT]
> W tym artykule opisano źródła danych dla [agenta log Analytics](./log-analytics-agent.md) , który jest jednym z agentów używanych przez Azure monitor. Inni Agenci zbierają różne dane i są skonfigurowani inaczej. Zobacz [Omówienie agentów Azure monitor](agents-overview.md) , aby uzyskać listę dostępnych agentów oraz dane, które mogą zbierać.

![Zbieranie danych dziennika](media/agent-data-sources/overview.png)

> [!IMPORTANT]
> Źródła danych opisane w tym artykule mają zastosowanie tylko do maszyn wirtualnych z uruchomionym agentem Log Analytics. 

## <a name="summary-of-data-sources"></a>Podsumowanie źródeł danych
W poniższej tabeli wymieniono źródła danych agentów, które są obecnie dostępne dla agenta Log Analytics.  Każdy z nich zawiera link do oddzielnego artykułu z szczegółowymi informacjami dla tego źródła danych.   Zawiera również informacje o ich metodzie i częstotliwości zbierania. 


| Źródło danych | Platforma | Agent usługi log Analytics | Agent programu Operations Manager | Azure Storage | Operations Manager jest wymagane? | Dane agenta Operations Manager wysyłane przez grupę zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Niestandardowe dzienniki](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | przy nadejściu |
| [Niestandardowe dzienniki](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | przy nadejściu |
| [Dzienniki usług IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |zależy od ustawienia przerzucania pliku dziennika |
| [Liczniki wydajności](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |zgodnie z harmonogramem, co najmniej 10 sekund |
| [Liczniki wydajności](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |zgodnie z harmonogramem, co najmniej 10 sekund |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |z usługi Azure Storage: 10 minut; z agenta: przy nadejściu |
| [Dzienniki zdarzeń systemu Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | przy nadejściu |


## <a name="configuring-data-sources"></a>Konfigurowanie źródeł danych
Aby skonfigurować źródła danych dla agentów Log Analytics, przejdź do menu **log Analytics obszary robocze** w Azure Portal i wybierz obszar roboczy. Kliknij pozycję **Konfiguracja agentów**. Wybierz kartę dla źródła danych, które chcesz skonfigurować. Możesz użyć linków w powyższej tabeli, aby zapoznać się z dokumentacją dla każdego źródła danych i szczegółowe informacje dotyczące ich konfiguracji.

Każda konfiguracja jest dostarczana do wszystkich agentów połączonych z tym obszarem roboczym.  Nie można wykluczyć żadnych podłączonych agentów z tej konfiguracji.

[![Konfigurowanie zdarzeń systemu Windows](media/agent-data-sources/configure-events.png)](media/agent-data-sources/configure-events.png#lightbox)



## <a name="data-collection"></a>Zbieranie danych
Konfiguracje źródła danych są dostarczane do agentów, które są bezpośrednio połączone z Azure Monitor w ciągu kilku minut.  Określone dane są zbierane od agenta i dostarczane bezpośrednio do Azure Monitor w odstępach czasu specyficznych dla każdego źródła danych.  Zapoznaj się z dokumentacją dla każdego źródła danych dla tych konkretnych celów.

W przypadku agentów System Center Operations Manager w podłączonej grupie zarządzania konfiguracje źródeł danych są tłumaczone na pakiety administracyjne i domyślnie dostarczane do grupy zarządzania co 5 minut.  Agent pobiera pakiet administracyjny, podobnie jak inne i zbiera określone dane. W zależności od źródła danych dane zostaną wysłane do serwera zarządzania, który przekazuje dane do Azure Monitor, lub Agent wyśle dane do Azure Monitor bez przechodzenia przez serwer zarządzania. Aby uzyskać szczegółowe informacje, zobacz [szczegóły zbierania danych dotyczących rozwiązań do monitorowania na platformie Azure](../monitor-reference.md) .  Informacje o szczegółach łączenia Operations Manager i Azure Monitor i modyfikowania częstotliwości dostarczania konfiguracji można znaleźć w artykule [Konfigurowanie integracji z System Center Operations Manager](./om-agents.md).

Jeśli Agent nie może nawiązać połączenia z Azure Monitor lub Operations Manager, będzie nadal zbierać dane, które będą dostarczane podczas ustanawiania połączenia.  Dane mogą zostać utracone, jeśli ilość danych osiągnie maksymalny rozmiar pamięci podręcznej dla klienta lub jeśli Agent nie może nawiązać połączenia w ciągu 24 godzin.

## <a name="log-records"></a>Rejestruj rekordy
Wszystkie dane dzienników zbierane przez Azure Monitor są przechowywane w obszarze roboczym jako rekordy.  Rekordy zbierane przez różne źródła danych będą mieć własny zestaw właściwości i być identyfikowane przez ich Właściwość **Type** .  Zapoznaj się z dokumentacją dla każdego źródła danych i rozwiązania, aby uzyskać szczegółowe informacje dotyczące poszczególnych typów rekordów.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [rozwiązaniach do monitorowania](../insights/solutions.md) , które dodają funkcje do Azure monitor, a także Zbieraj dane w obszarze roboczym.
* Informacje na temat [zapytań dzienników](../logs/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań do monitorowania.  
* Skonfiguruj [alerty](../alerts/alerts-overview.md) , aby aktywnie powiadamiać o najważniejszych danych zebranych ze źródeł danych i rozwiązań monitorowania.