---
title: Używanie procedur składowanych
description: Wskazówki dotyczące opracowywania rozwiązań przez zaimplementowanie procedur składowanych dla dedykowanych pul SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: cc6a58b4ef78aca60d2a26870980e032c0b11a52
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463219"
---
# <a name="using-stored-procedures-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Korzystanie z procedur składowanych dla dedykowanych pul SQL w usłudze Azure Synapse Analytics

Ten artykuł zawiera wskazówki dotyczące tworzenia dedykowanych rozwiązań puli SQL przez implementację procedur składowanych.

## <a name="what-to-expect"></a>Czego oczekiwać

Dedykowana Pula SQL obsługuje wiele funkcji T-SQL, które są używane w SQL Server. Co ważniejsze, dostępne są funkcje skalowania w poziomie, których można użyć w celu zmaksymalizowania wydajności rozwiązania.

Ponadto, aby ułatwić zachowanie skalowalności i wydajności dedykowanej puli SQL, dostępne są dodatkowe funkcje, które mają różnice behawioralne.

## <a name="introducing-stored-procedures"></a>Wprowadzenie procedur składowanych

Procedury składowane to świetny sposób hermetyzowania kodu SQL, który jest przechowywany blisko dedykowanych danych puli SQL. Procedury składowane ułatwiają deweloperom modularyzacji swoich rozwiązań poprzez Hermetyzowanie kodu w jednostki zarządzane, co ułatwia lepsze wielokrotne wykorzystywanie kodu. Każda procedura składowana może również akceptować parametry, aby zapewnić im jeszcze większą elastyczność.

Dedykowana Pula SQL zawiera uproszczoną i udoskonaloną implementację procedury składowanej. Największą różnicą w porównaniu do SQL Server jest to, że procedura składowana nie jest wstępnie skompilowanym kodem.

Ogólnie rzecz biorąc, w przypadku magazynów danych czas kompilacji jest mały w porównaniu do czasu potrzebnego do uruchamiania zapytań dotyczących dużych ilości danych. Należy upewnić się, że kod procedury składowanej jest poprawnie zoptymalizowany pod kątem dużych zapytań.

> [!TIP]
> Celem jest oszczędność godzin, minut i sekund, a nie milisekund. Dlatego warto traktować procedury składowane jako kontenery dla logiki SQL.

Gdy dedykowana Pula SQL wykonuje procedurę przechowywaną, instrukcje SQL są analizowane, tłumaczone i optymalizowane w czasie wykonywania. W trakcie tego procesu każda instrukcja jest konwertowana na zapytania rozproszone. Kod SQL, który jest wykonywany względem danych, różni się od przesłanego zapytania.

## <a name="nesting-stored-procedures"></a>Zagnieżdżanie procedur składowanych

Gdy procedury składowane wywołują inne procedury składowane lub wykonują dynamiczną instrukcję SQL, wewnętrzna procedura składowana lub wywołanie kodu są określane jako zagnieżdżone.

Dedykowana Pula SQL obsługuje maksymalnie osiem poziomów zagnieżdżenia. Natomiast poziom zagnieżdżenia w SQL Server to 32.

Wywołanie procedury składowanej najwyższego poziomu jest równe zagnieżdżeniu poziomu 1.

```sql
EXEC prc_nesting
```

Jeśli procedura składowana wykonuje również inne wywołanie programu EXEC, poziom zagnieżdżenia zwiększy się do dwóch.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Jeśli druga procedura wykonuje następnie dynamiczny SQL, poziom zagnieżdżenia rośnie do trzech.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Dedykowana Pula SQL nie obsługuje obecnie programu [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). W związku z tym należy śledzić poziom zagnieżdżenia. Jest mało prawdopodobne, że zostanie przekroczony limit ośmiu poziomów zagnieżdżenia. Ale jeśli to zrobisz, musisz ponownie obsłużyć swój kod, aby dopasować poziomy zagnieżdżenia w ramach tego limitu.

## <a name="insertexecute"></a>INSERT..EXEUROCZE

Dedykowana Pula SQL nie zezwala na używanie zestawu wyników procedury składowanej z instrukcją INSERT. Istnieje jednak alternatywna metoda, której można użyć. Aby zapoznać się z przykładem, zapoznaj się z artykułem dotyczącym [tabel tymczasowych](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Ograniczenia

Istnieją pewne aspekty procedur składowanych Transact-SQL, które nie są zaimplementowane w dedykowanej puli SQL, co jest następujące:

* tymczasowe procedury składowane
* numerowane procedury składowane
* rozszerzone procedury składowane
* Procedury składowane CLR
* Opcja szyfrowania
* Opcja replikacji
* parametry z wartościami przechowywanymi w tabeli
* parametry tylko do odczytu
* parametry domyślne
* konteksty wykonywania
* return, instrukcja

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).
