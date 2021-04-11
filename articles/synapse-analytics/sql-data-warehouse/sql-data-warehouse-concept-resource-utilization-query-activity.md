---
title: Zarządzanie i monitorowanie — aktywność zapytań, wykorzystanie zasobów
description: Dowiedz się, jakie funkcje są dostępne, aby zarządzać usługą Azure Synapse Analytics i monitorować ją. Użyj Azure Portal i dynamicznych widoków zarządzania (widoków DMV), aby zrozumieć aktywność zapytań i wykorzystanie zasobów magazynu danych.
services: synapse-analytics
author: gaursa
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e7f093860e9962db055a7551f5cdb608ecad9b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585676"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitorowanie działania użycia zasobów i zapytań w usłudze Azure Synapse Analytics

Usługa Azure Synapse Analytics zapewnia rozbudowane środowisko monitorowania w ramach Azure Portal, aby uzyskać wgląd w dane dotyczące obciążenia magazynu danych. Azure Portal jest zalecanym narzędziem do monitorowania magazynu danych, ponieważ umożliwia on Konfigurowanie okresów przechowywania, alertów, zaleceń oraz dostosowywalnych wykresów i pulpitów nawigacyjnych na potrzeby metryk i dzienników. Portal umożliwia także integrację z innymi usługami monitorowania platformy Azure, takimi jak Azure Monitor (dzienniki) z usługą log Analytics, aby zapewnić kompleksowe środowisko monitorowania nie tylko dla magazynu danych, ale również całą platformę analityczną platformy Azure na potrzeby zintegrowanego środowiska monitorowania. W tej dokumentacji opisano, jakie funkcje monitorowania są dostępne, aby zoptymalizować platformę analityczną i zarządzać nią za pomocą języka SQL Synapse.

## <a name="resource-utilization"></a>Wykorzystanie zasobów

Następujące metryki są dostępne w Azure Portal dla programu SQL Synapse. Te metryki są [naAzure Monitor](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics).

| Nazwa metryki             | Opis                                                  | Typ agregacji |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Procent użycia procesora CPU          | Użycie procesora CPU we wszystkich węzłach dla hurtowni danych      | Średnia, minimum, maksimum    |
| Procent użycia operacji we/wy na danych      | Użycie we/wy we wszystkich węzłach dla hurtowni danych       | Średnia, minimum, maksimum    |
| Procent pamięci       | Użycie pamięci (SQL Server) we wszystkich węzłach dla hurtowni danych | Średnia, minimum, maksimum   |
| Aktywne zapytania          | Liczba aktywnych zapytań wykonywanych w systemie             | Sum              |
| Zakolejkowane zapytania          | Liczba zapytań umieszczonych w kolejce oczekujących na rozpoczęcie wykonywania          | Sum              |
| Udane połączenia  | Liczba pomyślnych połączeń (logowań) z bazą danych | Sum, Count       |
| Połączenia zakończone niepowodzeniem      | Liczba nieudanych połączeń (logowań) z bazą danych | Sum, Count       |
| Zablokowany przez zaporę     | Liczba logowań do magazynu danych, który został zablokowany     | Sum, Count       |
| Limit jednostek dwu               | Cel poziomu usługi hurtowni danych                | Średnia, minimum, maksimum    |
| JEDNOSTEK dwu procent          | Maksimum między wartością procentową procesora CPU a wartością procentową operacji we/wy        | Średnia, minimum, maksimum    |
| JEDNOSTEK dwu używane                | Limit jednostek dwu * jednostek dwu procent                                   | Średnia, minimum, maksimum    |
| Procent trafień w pamięci podręcznej    | (trafienia w pamięci podręcznej/chybień w pamięci podręcznej) * 100, gdzie trafienia pamięci podręcznej jest sumą wszystkich trafień segmentów magazynu kolumn w lokalnej pamięci podręcznej dysków SSD i Chybienia pamięci podręcznej to segmenty magazynu kolumn chybień w lokalnej pamięci | Średnia, minimum, maksimum    |
| Procent użycia pamięci podręcznej   | (użyta pamięć podręczna/pojemność pamięci podręcznej) * 100 gdzie używana pamięć podręczna jest sumą wszystkich bajtów w lokalnej pamięci podręcznej SSD między wszystkimi węzłami i pojemnością pamięci podręcznej jest sumą pojemności magazynu lokalnej pamięci podręcznej SSD dla wszystkich węzłów | Średnia, minimum, maksimum    |
| Procent lokalnej bazy danych tempdb | Użycie lokalnej bazy danych tempdb we wszystkich węzłach obliczeniowych — wartości są emitowane co pięć minut. | Średnia, minimum, maksimum    |

Zagadnienia, które należy wziąć pod uwagę podczas przeglądania metryk i ustawień alertów:

- JEDNOSTEK dwu używany reprezentuje tylko **reprezentację poziomu użycia** w ramach puli SQL i nie jest to kompleksowy wskaźnik użycia. Aby określić, czy skalować w górę, czy w dół, należy wziąć pod uwagę wszystkie czynniki, na które może mieć wpływ jednostek dwu, takie jak współbieżność, pamięć, baza danych tempdb i adaptacyjna pojemność pamięci podręcznej. Zalecamy [Uruchamianie obciążeń w różnych ustawieniach jednostek dwu](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) , aby określić, co najlepiej sprawdza się w celu spełnienia celów firmy.
- Nie powiodło się i pomyślne połączenia są raportowane dla określonego magazynu danych — nie dla samego serwera.
- Procent pamięci odzwierciedla użycie, nawet jeśli magazyn danych jest w stanie bezczynności — nie odzwierciedla aktywnego zużycia pamięci obciążeń. Użyj i śledź tę metrykę wraz z innymi (tempdb, Gen2 cache), aby wykonać całościową decyzję, czy skalowanie dla dodatkowej pojemności pamięci podręcznej spowoduje zwiększenie wydajności obciążeń w celu spełnienia wymagań.

## <a name="query-activity"></a>Działanie zapytania

W przypadku programistycznego środowiska monitorowania Synapse SQL za pośrednictwem T-SQL Usługa udostępnia zestaw dynamicznych widoków zarządzania (widoków DMV). Te widoki są przydatne podczas aktywnego rozwiązywania problemów i identyfikowania wąskich gardeł wydajności w obciążeniu.

Aby wyświetlić listę widoków DMV, które mają zastosowanie do Synapse SQL, zapoznaj się z tą [dokumentacją](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Rejestrowanie metryk i informacji diagnostycznych 

Wszystkie metryki i dzienniki można eksportować do Azure Monitor, w odniesieniu do składnika [dzienników Azure monitor](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) , i można programowo uzyskać do nich dostęp za pomocą [zapytań dzienników](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json). Opóźnienie dziennika Synapse SQL to około 10-15 minut. Więcej informacji o czynnikach wpływających na opóźnienia można znaleźć w następującej dokumentacji.

## <a name="next-steps"></a>Następne kroki

W poniższych przewodnikach opisano typowe scenariusze i przypadki użycia podczas monitorowania magazynu danych i zarządzania nim:

- [Monitorowanie obciążenia magazynu danych za pomocą widoków DMV](sql-data-warehouse-manage-monitor.md)