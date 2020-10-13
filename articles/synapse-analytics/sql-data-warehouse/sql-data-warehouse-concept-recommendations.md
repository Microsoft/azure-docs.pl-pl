---
title: Synapse zalecenia dotyczące języka SQL
description: Poznaj zalecenia dotyczące Synapse SQL i sposobu ich generowania
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e4564005e3b9cc9673cc20596d4114d102174b9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85482857"
---
# <a name="synapse-sql-recommendations"></a>Synapse zalecenia dotyczące języka SQL

W tym artykule opisano zalecenia dotyczące programu Synapse SQL obsługiwane za pomocą Azure Advisor.  

Synapse SQL zawiera zalecenia, aby zapewnić, że obciążenie magazynu danych jest stale zoptymalizowane pod kątem wydajności. Zalecenia są ściśle zintegrowane z [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) , aby zapewnić najlepszym praktykom bezpośrednio w ramach [Azure Portal](https://aka.ms/Azureadvisor). Synapse SQL zbiera zalecenia dotyczące danych telemetrycznych i powierzchni dla aktywnego obciążenia w codziennej erze. Obsługiwane scenariusze rekomendacji opisano poniżej wraz z sposobem zastosowania zalecanych akcji.

Możesz [sprawdzić swoje rekomendacje](https://aka.ms/Azureadvisor) już dzisiaj! 

## <a name="data-skew"></a>Pochylenie danych

Pochylenie danych może spowodować dodatkowe wąskie gardła lub zasoby związane z zasobami podczas uruchamiania obciążenia. Poniższa dokumentacja zawiera opis pochylenia danych i uniemożliwia jego zachowanie, wybierając optymalny klucz dystrybucji.

- [Zidentyfikuj i Usuń pochylenie](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Nieaktualne dane statystyczne

Posiadanie nieoptymalnych statystyk może poważnie wpływać na wydajność zapytań, ponieważ może to spowodować, że optymalizator zapytań SQL generuje nieoptymalne plany zapytań. W poniższej dokumentacji opisano najlepsze rozwiązania dotyczące tworzenia i aktualizowania statystyk:

- [Tworzenie i aktualizowanie statystyk tabeli](sql-data-warehouse-tables-statistics.md)

Aby wyświetlić listę tabel, których dotyczy problem, należy uruchomić następujący  [skrypt T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Program Advisor ciągle uruchamia ten sam skrypt T-SQL w celu wygenerowania tych zaleceń.

## <a name="replicate-tables"></a>Replikowanie tabel

W przypadku zaleceń dotyczących zreplikowanej tabeli program Advisor wykrywa kandydatów tabeli na podstawie następujących cech fizycznych:

- Rozmiar zreplikowanej tabeli
- Liczba kolumn
- Typ dystrybucji tabeli
- Liczba partycji

Doradca ciągle korzysta z algorytmów heurystycznych opartych na obciążeniu, takich jak częstotliwość dostępu do tabel, wiersze zwrócone na średnim i progi wokół rozmiaru i aktywności magazynu danych w celu zapewnienia, że generowane są zalecenia o wysokiej jakości.

W poniższej sekcji opisano algorytmy heurystyczne oparte na obciążeniu, które mogą znajdować się w Azure Portal dla każdego zalecenia zreplikowanej tabeli:

- Skanowanie Średni — Średni procent wierszy zwróconych z tabeli dla każdego dostępu do tabeli w ciągu ostatnich siedmiu dni
- Często odczytywane, brak aktualizacji — wskazuje, że tabela nie została zaktualizowana w ciągu ostatnich siedmiu dni podczas wyświetlania działania dostępu
- Współczynnik odczytu/aktualizacji — stosunek częstotliwości uzyskiwania dostępu do tabeli względem czasu, gdy zostanie on zaktualizowany w ciągu ostatnich siedmiu dni
- Działanie — mierzy użycie na podstawie działania dostępu. To działanie porównuje operacje dostępu do tabel względem średniej aktywności dostępu do tabeli w magazynie danych w ciągu ostatnich siedmiu dni.

Obecnie w usłudze Advisor będą widoczne tylko cztery kandydujące zreplikowane tabele jednocześnie z klastrowanymi indeksami magazynu kolumn o priorytetach o najwyższej aktywności.

> [!IMPORTANT]
> Zalecenie zreplikowanej tabeli nie jest pełną próbą i nie uwzględnia operacji przenoszenia danych. Pracujemy nad dodaniem tego elementu jako algorytmu heurystycznego, ale w międzyczasie należy zawsze sprawdzić poprawność obciążenia po zastosowaniu zalecenia. Aby dowiedzieć się więcej na temat zreplikowanych tabel, zapoznaj się z poniższą [dokumentacją](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).


## <a name="adaptive-gen2-cache-utilization"></a>Adaptacyjne (Gen2) użycie pamięci podręcznej
W przypadku dużego zestawu roboczego można obsłużyć procent trafień w pamięci podręcznej i duże użycie pamięci podręcznej. W tym scenariuszu należy skalować w górę, aby zwiększyć pojemność pamięci podręcznej i ponownie uruchamiać obciążenie. Aby uzyskać więcej informacji, zapoznaj się z poniższą [dokumentacją](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache). 

## <a name="tempdb-contention"></a>Rywalizacja o bazę danych tempdb

Wydajność zapytań może wzniżyć, gdy istnieje duża rywalizacja o bazę danych tempdb.  Rywalizacja o bazę danych tempdb może odbywać się za pośrednictwem tabel tymczasowych zdefiniowanych przez użytkownika lub w przypadku dużej ilości ruchu. W tym scenariuszu można skalować więcej alokacji tempdb i [konfigurować klasy zasobów i zarządzanie obciążeniami](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management) w celu zapewnienia większej ilości pamięci dla zapytań. 

## <a name="data-loading-misconfiguration"></a>Niekonfiguracja ładowania danych

Należy zawsze ładować dane z konta magazynu w tym samym regionie, w którym znajduje się pula SQL, aby zminimalizować opóźnienia. Użyj [instrukcji Copy do pozyskiwania danych o wysokiej przepływności](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) i Podziel pliki przemieszczane na koncie magazynu, aby zmaksymalizować przepływność. Jeśli nie można użyć instrukcji COPY, można użyć interfejsu API SqlBulkCopy lub BCP o dużej wielkości partii, aby zapewnić lepszą przepływność. Aby uzyskać dodatkowe wskazówki dotyczące ładowania danych, zapoznaj się z poniższą [dokumentacją](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data). 
