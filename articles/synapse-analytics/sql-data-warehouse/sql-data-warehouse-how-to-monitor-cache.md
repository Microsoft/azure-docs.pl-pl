---
title: Optymalizowanie pamięci podręcznej Gen2
description: Dowiedz się, jak monitorować pamięć podręczną Gen2 przy użyciu Azure Portal.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: fed3ed2c87342d557872e97bfc2a6c4d142b5a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585625"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>Jak monitorować adaptacyjną pamięć podręczną

W tym artykule opisano sposób monitorowania i rozwiązywania problemów z niską wydajnością zapytań przez określenie, czy obciążenie jest optymalnie używane w ramach adaptacyjnej pamięci podręcznej dla dedykowanych pul SQL.

Dedykowana architektura magazynu puli SQL automatycznie warstwuje najczęściej badane segmenty magazynu kolumn w pamięci podręcznej na podstawie dysków SSD. Jeśli zapytania pobierają segmenty znajdujące się w pamięci podręcznej, będzie można zwiększyć wydajność.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Rozwiązywanie problemów przy użyciu Azure Portal

Za pomocą Azure Monitor można wyświetlać metryki pamięci podręcznej w celu rozwiązywania problemów z wydajnością zapytań. Najpierw przejdź do Azure Portal i kliknij pozycję **monitor**, **metryki** i **Wybierz zakres**:

![Zrzut ekranu przedstawia wybór zakresu wybranego z metryk w Azure Portal.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Użyj słupków wyszukiwania i listy rozwijanej, aby zlokalizować dedykowaną pulę SQL. Następnie wybierz pozycję Zastosuj.

![Zrzut ekranu przedstawia okienko wybierz zakres, w którym można wybrać magazyn danych.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Kluczowymi metrykami dotyczącymi rozwiązywania problemów z pamięcią podręczną są **Procent trafień pamięci podręcznej** i **procent wykorzystania pamięci** Wybierz pozycję **Procent trafień pamięci podręcznej** , a następnie użyj przycisku **Dodaj metrykę** , aby dodać **procent użycia pamięci podręcznej** 

![Metryki pamięci podręcznej](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Procent trafień i użycia pamięci podręcznej

W poniższej macierzy opisano scenariusze oparte na wartościach metryk pamięci podręcznej:

|                                | **Wysoki procent trafień w pamięci podręcznej** | **Procent trafień niskiego poziomu pamięci podręcznej** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Procent użycia dużej pamięci podręcznej** |          Scenariusz 1           |          Scenariusz 2          |
| **Procent użycia niskiej pamięci podręcznej**  |          Scenariusz 3           |          Scenariusz 4          |

**Scenariusz 1:** Optymalnie korzystasz z pamięci podręcznej. [Rozwiązywanie problemów z](sql-data-warehouse-manage-monitor.md) innymi obszarami, które mogą spowalniać zapytania.

**Scenariusz 2:** Bieżący roboczy zestaw danych nie mieści się w pamięci podręcznej, co powoduje niską wartość procentową trafień pamięci podręcznej z powodu fizycznych operacji odczytu. Rozważ skalowanie w górę poziomu wydajności i ponowne uruchomienie obciążenia w celu wypełnienia pamięci podręcznej.

**Scenariusz 3:** Prawdopodobnie zapytanie działa wolno ze względu na przyczyny niezwiązane z pamięcią podręczną. [Rozwiązywanie problemów z](sql-data-warehouse-manage-monitor.md) innymi obszarami, które mogą spowalniać zapytania. Możesz również rozważyć [skalowanie wystąpienia](sql-data-warehouse-manage-monitor.md) , aby zmniejszyć rozmiar pamięci podręcznej w celu oszczędności kosztów. 

**Scenariusz 4:** Masz zimną pamięć podręczną, która może być przyczyną powolnego działania zapytania. Rozważ ponowne uruchomienie zapytania, ponieważ roboczy zestaw danych powinien teraz znajdować się w pamięci podręcznej. 

> [!IMPORTANT]
> Jeśli procent trafień pamięci podręcznej lub procent użycia pamięci podręcznej nie jest aktualizowany po rozpoczęciu obciążenia, zestaw roboczy może już znajdować się w pamięci. Tylko klastrowane tabele magazynu kolumn są buforowane.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat ogólnego dostrajania wydajności zapytań, zobacz [monitorowanie wykonywania zapytań](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
