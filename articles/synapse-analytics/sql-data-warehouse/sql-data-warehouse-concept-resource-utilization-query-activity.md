---
title: Możliwości zarządzania i monitorowania — aktywność zapytań, wykorzystanie zasobów
description: Dowiedz się, jakie funkcje są dostępne do monitorowania Azure Synapse Analytics. Użyj widoków Azure Portal i dynamicznych widoków zarządzania (DMV), aby zrozumieć aktywność zapytań i wykorzystanie zasobów magazynu danych.
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7b103991e22ffab8ed5bd2b3c10400330e1d09b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568407"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitorowanie wykorzystania zasobów i działań zapytań w Azure Synapse Analytics

Azure Synapse Analytics zapewnia rozbudowane środowisko monitorowania w Azure Portal, aby uzyskać szczegółowe informacje dotyczące obciążenia magazynu danych. Ta Azure Portal jest zalecanym narzędziem do monitorowania magazynu danych, ponieważ zapewnia konfigurowalne okresy przechowywania, alerty, zalecenia oraz dostosowywalne wykresy i pulpity nawigacyjne dla metryk i dzienników. Portal umożliwia również integrację z innymi usługami monitorowania platformy Azure, takimi jak Azure Monitor (dzienniki) z usługą Log Analytics, w celu zapewnienia całościowego środowiska monitorowania nie tylko dla magazynu danych, ale także całej platformy analizy Platformy Azure w celu zapewnienia zintegrowanego środowiska monitorowania. W tej dokumentacji opisano dostępne możliwości monitorowania w celu optymalizacji platformy analitycznej i zarządzania nimi za pomocą Synapse SQL.

## <a name="resource-utilization"></a>Wykorzystanie zasobów

Następujące metryki są dostępne w Azure Portal dla Synapse SQL. Te metryki są dostępne za pośrednictwem [Azure Monitor](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics).

| Nazwa metryki             | Opis                                                  | Typ agregacji |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Procent użycia procesora CPU          | Wykorzystanie procesora CPU we wszystkich węzłach magazynu danych      | Średnia, Minimalna, Maksymalna    |
| Procent użycia operacji we/wy na danych      | Wykorzystanie we/wy we/wy we wszystkich węzłach magazynu danych       | Średnia, Minimalna, Maksymalna    |
| Procent pamięci       | Wykorzystanie pamięci (SQL Server) we wszystkich węzłach magazynu danych | Średnia, Minimalna, Maksymalna   |
| Aktywne zapytania          | Liczba aktywnych zapytań wykonywanych w systemie             | Sum              |
| Zapytania w kolejce          | Liczba zapytań w kolejce oczekujących na rozpoczęcie wykonywania          | Sum              |
| Pomyślne połączenia  | Liczba pomyślnych połączeń (identyfikatorów logowania) z bazą danych | Suma, Liczba       |
| Połączenia zakończone niepowodzeniem      | Liczba nieudanych połączeń (identyfikatorów logowania) z bazą danych | Suma, Liczba       |
| Zablokowany przez zaporę     | Liczba logowań do magazynu danych, który został zablokowany     | Suma, Liczba       |
| Limit jednostek DWU               | Cel poziomu usług magazynu danych                | Średnia, Minimalna, Maksymalna    |
| Procent jednostek DWU          | Maksymalna wartość między procentem użycia procesora CPU a procentem liczby we/wy danych        | Średnia, Minimalna, Maksymalna    |
| Używane jednostek DWU                | Limit jednostek DWU * Procent jednostek DWU                                   | Średnia, Minimalna, Maksymalna    |
| Procent trafień w pamięci podręcznej    | (trafienia pamięci podręcznej / chybienia pamięci podręcznej) * 100, gdzie trafienia pamięci podręcznej to suma trafień wszystkich segmentów magazynu kolumn w lokalnej pamięci podręcznej SSD, a chybienia pamięci podręcznej to chybienia segmentów magazynu kolumn w lokalnej pamięci podręcznej SSD zsumowane we wszystkich węzłach | Średnia, Minimalna, Maksymalna    |
| Procent użytej pamięci podręcznej   | (używana pamięć podręczna/ pojemność pamięci podręcznej) * 100, gdzie używana pamięć podręczna to suma wszystkich bajtów w lokalnej pamięci podręcznej dysków SSD we wszystkich węzłach, a pojemność pamięci podręcznej to suma pojemności magazynu lokalnej pamięci podręcznej ssd we wszystkich węzłach | Średnia, Minimalna, Maks.    |
| Procent lokalnej bazy danych tempdb | Wykorzystanie lokalnej bazy danych tempdb we wszystkich węzłach obliczeniowych — wartości są emitowane co pięć minut | Średnia, Minimalna, Maks.    |

Co należy wziąć pod uwagę podczas wyświetlania metryk i ustawiania alertów:

- Używana jednostka DWU reprezentuje **tylko** reprezentację wysokiego poziomu użycia w puli SQL i nie jest kompleksowym wskaźnikiem wykorzystania. Aby określić, czy skalować w górę, czy w dół, należy wziąć pod uwagę wszystkie czynniki, na które mogą mieć wpływ jednostka DWU, takie jak współbieżność, pamięć, baza danych tempdb i adaptacyjna pojemność pamięci podręcznej. Zalecamy uruchamianie [obciążenia w różnych ustawieniach jednostek DWU,](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) aby określić, co najlepiej spełnia cele biznesowe.
- Nieudane i pomyślne połączenia są zgłaszane dla określonego magazynu danych, a nie dla samego serwera.
- Wartość procentowa pamięci odzwierciedla wykorzystanie, nawet jeśli magazyn danych jest w stanie bezczynności — nie odzwierciedla aktywnego użycia pamięci obciążenia. Użyj tej metryki i śledź jej wraz z innymi (tempdb, gen2 cache), aby podjąć całościową decyzję, czy skalowanie w celu zwiększenia pojemności pamięci podręcznej zwiększy wydajność obciążenia zgodnie z wymaganiami.

## <a name="query-activity"></a>Działanie zapytania

Aby programowo monitorować Synapse SQL za pomocą języka T-SQL, usługa udostępnia zestaw dynamicznych widoków zarządzania (DMV). Te widoki są przydatne podczas aktywnego rozwiązywania problemów z obciążeniem i identyfikowania wąskich gardeł wydajności.

Aby wyświetlić listę widoków DMV, które mają zastosowanie do Synapse SQL, zapoznaj się z tą [dokumentacją.](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs) 

## <a name="metrics-and-diagnostics-logging"></a>Rejestrowanie metryk i informacji diagnostycznych 

Zarówno metryki, jak i dzienniki można wyeksportować do usługi Azure Monitor, w szczególności do składnika dzienników usługi [Azure Monitor,](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i można uzyskać do nich dostęp programowo za [pośrednictwem zapytań dziennika.](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) Opóźnienie dziennika dla Synapse SQL wynosi około 10–15 minut. Aby uzyskać więcej informacji na temat czynników wpływających na opóźnienie, odwiedź następującą dokumentację.

## <a name="next-steps"></a>Następne kroki

W poniższym przewodniku opisano typowe scenariusze i przypadki użycia podczas monitorowania magazynu danych i zarządzania nim:

- [Monitorowanie obciążenia magazynu danych przy użyciu widoków DMV](sql-data-warehouse-manage-monitor.md)