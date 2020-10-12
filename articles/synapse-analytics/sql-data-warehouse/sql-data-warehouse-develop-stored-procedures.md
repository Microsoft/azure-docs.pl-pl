---
title: Używanie procedur składowanych
description: Wskazówki dotyczące opracowywania rozwiązań przez zaimplementowanie procedur składowanych w puli SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 87c7eaa57f9da87bd83f89953afc09632d42b1f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213401"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Korzystanie z procedur składowanych w puli Synapse SQL

Ten artykuł zawiera wskazówki dotyczące opracowywania rozwiązań puli SQL przez zaimplementowanie procedur składowanych.

## <a name="what-to-expect"></a>Czego oczekiwać

Pula SQL obsługuje wiele funkcji T-SQL, które są używane w SQL Server. Co ważniejsze, dostępne są funkcje skalowania w poziomie, których można użyć w celu zmaksymalizowania wydajności rozwiązania.

Ponadto, aby ułatwić zachowanie skalowalności i wydajności puli SQL, dostępne są dodatkowe funkcje, które mają różnice behawioralne.

## <a name="introducing-stored-procedures"></a>Wprowadzenie procedur składowanych

Procedury składowane to świetny sposób hermetyzowania kodu SQL, który jest przechowywany blisko danych puli SQL. Procedury składowane ułatwiają deweloperom modularyzacji swoich rozwiązań poprzez Hermetyzowanie kodu w jednostki zarządzane, co ułatwia lepsze wielokrotne wykorzystywanie kodu. Każda procedura składowana może również akceptować parametry, aby zapewnić im jeszcze większą elastyczność.

Pula SQL stanowi uproszczoną i udoskonaloną implementację procedury składowanej. Największą różnicą w porównaniu do SQL Server jest to, że procedura składowana nie jest wstępnie skompilowanym kodem.

Ogólnie rzecz biorąc, w przypadku magazynów danych czas kompilacji jest mały w porównaniu do czasu potrzebnego do uruchamiania zapytań dotyczących dużych ilości danych. Należy upewnić się, że kod procedury składowanej jest poprawnie zoptymalizowany pod kątem dużych zapytań.

> [!TIP]
> Celem jest oszczędność godzin, minut i sekund, a nie milisekund. Dlatego warto traktować procedury składowane jako kontenery dla logiki SQL.

Gdy pula SQL wykonuje procedurę przechowywaną, instrukcje SQL są analizowane, tłumaczone i optymalizowane w czasie wykonywania. W trakcie tego procesu każda instrukcja jest konwertowana na zapytania rozproszone. Kod SQL, który jest wykonywany względem danych, różni się od przesłanego zapytania.

## <a name="nesting-stored-procedures"></a>Zagnieżdżanie procedur składowanych

Gdy procedury składowane wywołują inne procedury składowane lub wykonują dynamiczną instrukcję SQL, wewnętrzna procedura składowana lub wywołanie kodu są określane jako zagnieżdżone.

Pula SQL obsługuje maksymalnie osiem poziomów zagnieżdżenia. Natomiast poziom zagnieżdżenia w SQL Server to 32.

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

Pula SQL nie obsługuje obecnie [programu @NESTLEVEL @](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). W związku z tym należy śledzić poziom zagnieżdżenia. Jest mało prawdopodobne, że zostanie przekroczony limit ośmiu poziomów zagnieżdżenia. Ale jeśli to zrobisz, musisz ponownie obsłużyć swój kod, aby dopasować poziomy zagnieżdżenia w ramach tego limitu.

## <a name="insertexecute"></a>INSERT..EXEUROCZE

Pula SQL nie zezwala na używanie zestawu wyników procedury składowanej z instrukcją INSERT. Istnieje jednak alternatywna metoda, której można użyć. Aby zapoznać się z przykładem, zapoznaj się z artykułem dotyczącym [tabel tymczasowych](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Ograniczenia

Istnieją pewne aspekty procedur składowanych Transact-SQL, które nie są zaimplementowane w puli SQL, które są następujące:

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
