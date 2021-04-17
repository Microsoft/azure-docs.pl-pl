---
title: Optymalizowanie pamięci podręcznej Gen2
description: Dowiedz się, jak monitorować pamięć podręczną Gen2 przy użyciu Azure Portal.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9de795c54f55295fa69ed7fcb5dd894e2963385b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566634"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>Jak monitorować adaptacyjną pamięć podręczną

W tym artykule opisano sposób monitorowania i rozwiązywania problemów z powolną wydajnością zapytań przez określenie, czy obciążenie optymalnie wykorzystujące adaptacyjną pamięć podręczną dla dedykowanych pul SQL.

Dedykowana architektura magazynu puli SQL automatycznie tworzy warstwy najczęściej wyszukiwanych segmentów magazynu kolumn w pamięci podręcznej przechowywanej na dyskach SSD opartych na technologii NVMe. Będziesz mieć większą wydajność, gdy zapytania pobierają segmenty, które znajdują się w pamięci podręcznej.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Rozwiązywanie problemów przy użyciu Azure Portal

Metryki pamięci podręcznej można Azure Monitor do rozwiązywania problemów z wydajnością zapytań. Najpierw przejdź do menu Azure Portal kliknij pozycję **Monitor,** **Metryki** i **+ Wybierz zakres:**

![Zrzut ekranu przedstawia wybieranie zakresu wybranego z metryk w Azure Portal.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Użyj pasków wyszukiwania i listy rozwijanej, aby zlokalizować dedykowaną pulę SQL. Następnie wybierz pozycję Zastosuj.

![Zrzut ekranu przedstawia okienko Wybierz zakres, w którym można wybrać magazyn danych.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Kluczowe metryki rozwiązywania problemów z pamięcią podręczną to Procent **trafień** w pamięci podręcznej i **Procent użycia pamięci podręcznej.** Wybierz pozycję **Procent trafień pamięci podręcznej,** a następnie użyj **przycisku dodawania metryki,** aby dodać **wartość procentową użycia pamięci podręcznej.** 

![Metryki pamięci podręcznej](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Procent trafień i użytych w pamięci podręcznej

W poniższej macierzy opisano scenariusze oparte na wartościach metryk pamięci podręcznej:

|                                | **Wysoki procent trafień pamięci podręcznej** | **Procent niskiej pamięci podręcznej** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Procent wysokiego procentu używanej pamięci podręcznej** |          Scenariusz 1           |          Scenariusz 2          |
| **Procent małej wartości używanej pamięci podręcznej**  |          Scenariusz 3           |          Scenariusz 4          |

**Scenariusz 1:** Optymalnie używasz pamięci podręcznej. [Rozwiązywanie](sql-data-warehouse-manage-monitor.md) problemów z innymi obszarami, które mogą spowalniać zapytania.

**Scenariusz 2:** Bieżący roboczy zestaw danych nie mieści się w pamięci podręcznej, co powoduje niską wartość procentową trafień pamięci podręcznej z powodu odczytów fizycznych. Rozważ skalowanie w górę poziomu wydajności i ponowne uruchomić obciążenie, aby wypełnić pamięć podręczną.

**Scenariusz 3.** Prawdopodobnie zapytanie działa wolno z przyczyn niezwiązanych z pamięcią podręczną. [Rozwiązywanie](sql-data-warehouse-manage-monitor.md) problemów z innymi obszarami, które mogą spowalniać zapytania. Możesz również rozważyć [skalowanie wystąpienia](sql-data-warehouse-manage-monitor.md) w dół, aby zmniejszyć rozmiar pamięci podręcznej w celu obniżenia kosztów. 

**Scenariusz 4.** Masz zimną pamięć podręczną, co może być przyczyną powolnego wykonywania zapytania. Rozważ ponowne uruchomić zapytanie, ponieważ roboczy zestaw danych powinien być teraz w pamięci podręcznej. 

> [!IMPORTANT]
> Jeśli wartość procentowa użycia pamięci podręcznej lub pamięci podręcznej nie jest aktualizowana po ponownej aktualizacji obciążenia, zestaw roboczy może już być w pamięci. Buforowane są tylko klastrowane tabele magazynu kolumn.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat ogólnego dostrajania wydajności zapytań, zobacz [Monitorowanie wykonywania zapytań.](sql-data-warehouse-manage-monitor.md#monitor-query-execution)
