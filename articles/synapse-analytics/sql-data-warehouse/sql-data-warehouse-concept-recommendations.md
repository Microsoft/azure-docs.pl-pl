---
title: Zalecenia dotyczące dedykowanych pul SQL Azure Advisor SQL
description: Dowiedz się więcej Synapse SQL i sposobu ich generowania
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b418b46199c524ca92d60dece6031073938e159b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568424"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Advisor zaleceń dotyczących dedykowanej puli SQL w programie Azure Synapse Analytics

W tym artykule opisano zalecenia dotyczące dedykowanej puli SQL dostępne w Azure Advisor.  

Dedykowana pula SQL udostępnia rekomendacje, które zapewniają stałe optymalizowanie obciążenia magazynu danych pod kątem wydajności. Zalecenia są ściśle zintegrowane z [Azure Advisor,](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) aby zapewnić najlepsze rozwiązania bezpośrednio w Azure Portal [.](https://aka.ms/Azureadvisor) Dedykowana pula SQL zbiera dane telemetryczne i codziennie rozpowszechnia zalecenia dotyczące aktywnego obciążenia. Obsługiwane scenariusze zaleceń zostały opisane poniżej wraz ze instrukcjami stosowania zalecanych akcji.

Już dziś [możesz sprawdzić rekomendacje!](https://aka.ms/Azureadvisor) 

## <a name="data-skew"></a>Niesyć danych

Niesyć danych może powodować dodatkowe ruchy danych lub wąskie gardła zasobów podczas uruchamiania obciążenia. W poniższej dokumentacji opisano sposób identyfikowania niesyłania danych i zapobiegania im przez wybranie optymalnego klucza dystrybucji.

- [Identyfikowanie i usuwanie niesyłania](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Brak lub nieaktualne statystyki

Nieoptymalna statystyka może poważnie wpłynąć na wydajność zapytań, ponieważ może spowodować, że optymalizator zapytań SQL wygeneruje nieoptymalna wydajność planów zapytań. W poniższej dokumentacji opisano najlepsze rozwiązania dotyczące tworzenia i aktualizowania statystyk:

- [Tworzenie i aktualizowanie statystyk tabeli](sql-data-warehouse-tables-statistics.md)

Aby wyświetlić listę tabel, których te zalecenia mają wpływ, uruchom następujący skrypt [T-SQL.](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) Usługa Advisor stale uruchamia ten sam skrypt języka T-SQL w celu wygenerowania tych zaleceń.

## <a name="replicate-tables"></a>Replikowanie tabel

W przypadku zaleceń dotyczących zreplikowanych tabel program Advisor wykrywa kandydatów do tabeli na podstawie następujących cech fizycznych:

- Rozmiar zreplikowanej tabeli
- Liczba kolumn
- Typ dystrybucji tabeli
- Liczba partycji

Doradca stale wykorzystuje heurystyczne oparte na obciążeniach, takie jak częstotliwość dostępu do tabel, wiersze zwracane średnio oraz progi dotyczące rozmiaru i aktywności magazynu danych w celu zapewnienia generowania rekomendacji wysokiej jakości.

W poniższej sekcji opisano heurystyczne oparte na obciążeniach, które można znaleźć w Azure Portal dla każdego zalecenia replikowanej tabeli:

- Skanowanie średnio — średni procent wierszy zwróconych z tabeli dla każdego dostępu do tabeli w ciągu ostatnich siedmiu dni
- Częste odczytywanie, brak aktualizacji — wskazuje, że tabela nie została zaktualizowana w ciągu ostatnich siedmiu dni podczas wyświetlania aktywności dostępu
- Współczynnik odczytu/aktualizacji — stosunek częstotliwości dostępu do tabeli względem liczby aktualizacji w ciągu ostatnich siedmiu dni
- Działanie — mierzy użycie na podstawie działania dostępu. To działanie porównuje aktywność dostępu do tabel względem średniej aktywności dostępu do tabel w magazynie danych w ciągu ostatnich siedmiu dni.

Obecnie usługa Advisor będzie wyświetlać tylko cztery zreplikowane tabeli kandydatów jednocześnie z indeksami klastrowanego magazynu kolumn priorytetów najwyższy działania.

> [!IMPORTANT]
> Zalecenie dotyczące replikowanej tabeli nie jest pełnej weryfikacji i nie uwzględnia operacji przemieszczania danych. Pracujemy nad dodaniem tego jako heurystycznego, ale w międzyczasie należy zawsze zweryfikować obciążenie po zastosowaniu zalecenia. Aby dowiedzieć się więcej na temat replikowanych tabel, zapoznaj się z następującą [dokumentacją.](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)


## <a name="adaptive-gen2-cache-utilization"></a>Adaptacyjne wykorzystanie pamięci podręcznej (Gen2)
Jeśli masz duży zestaw roboczy, możesz doświadczyć niskiej wartości procentowej trafień w pamięci podręcznej i wysokiego wykorzystania pamięci podręcznej. W tym scenariuszu należy skalować w górę, aby zwiększyć pojemność pamięci podręcznej i ponownie uruchomić obciążenie. Aby uzyskać więcej informacji, odwiedź następującą [dokumentację.](./sql-data-warehouse-how-to-monitor-cache.md) 

## <a name="tempdb-contention"></a>Pogotowa w bazie danych Tempdb

Wydajność zapytań może się zmniejszyć, jeśli istnieje wysoki poziom pogody z bazą danych tempdb.  Pojedyńka bazy danych tempdb może odbywać się za pośrednictwem tabel tymczasowych zdefiniowanych przez użytkownika lub w przypadku dużego ruchu danych. W tym scenariuszu można skalować w [](./sql-data-warehouse-workload-management.md) celu zapewnienia większej ilości alokacji bazy danych tempdb oraz skonfigurować klasy zasobów i zarządzanie obciążeniami w celu zapewnienia większej ilości pamięci dla zapytań. 

## <a name="data-loading-misconfiguration"></a>Błędna konfiguracja ładowania danych

Zawsze należy ładować dane z konta magazynu w tym samym regionie co dedykowana pula SQL, aby zminimalizować opóźnienie. Użyj instrukcji [COPY do pozyskiwania](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) danych o wysokiej przepływności i podziel pliki etapowe na koncie magazynu, aby zmaksymalizować przepływność. Jeśli nie możesz użyć instrukcji COPY, możesz użyć interfejsu API SqlBulkCopy lub narzędzia bcp o dużym rozmiarze partii w celu zwiększenia przepływności. Aby uzyskać dodatkowe wskazówki dotyczące ładowania danych, odwiedź następującą [dokumentację.](./guidance-for-loading-data.md)