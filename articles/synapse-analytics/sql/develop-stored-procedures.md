---
title: Korzystanie z procedur składowanych
description: Wskazówki dotyczące implementowania procedur składowanych w puli SQL Synapse (magazyn danych) do tworzenia rozwiązań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a431df1ff4ef0984d1197933e7ca78979fa23089
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430983"
---
# <a name="using-stored-procedures-in-sql-pool"></a>Korzystanie z procedur składowanych w puli SQL

Wskazówki dotyczące implementowania procedur składowanych w puli SQL Synapse (magazyn danych) do tworzenia rozwiązań.

## <a name="what-to-expect"></a>Czego oczekiwać

Pula SQL obsługuje wiele funkcji T-SQL, które są używane w SQL Server. Co ważniejsze, dostępne są funkcje skalowania w poziomie, których można użyć w celu zmaksymalizowania wydajności rozwiązania.

Jednak aby zachować skalę i wydajność puli SQL, istnieją także pewne funkcje i funkcje, które mają różnice zachowania i inne, które nie są obsługiwane.

## <a name="introducing-stored-procedures"></a>Wprowadzenie procedur składowanych

Procedury składowane to świetny sposób hermetyzowania kodu SQL; przechowywanie go blisko danych w magazynie danych. Procedury składowane ułatwiają deweloperom modularyzacji swoich rozwiązań poprzez hermetyzację kodu w jednostkach do zarządzania; zwiększenie możliwości ponownego wykorzystania kodu. Każda procedura składowana może również akceptować parametry, aby zapewnić im jeszcze większą elastyczność.

Pula SQL stanowi uproszczoną i udoskonaloną implementację procedury składowanej. Największą różnicą w porównaniu do SQL Server jest to, że procedura składowana nie jest wstępnie kompilowanym kodem. W przypadku magazynów danych czas kompilacji jest mały w porównaniu do czasu potrzebnego do uruchamiania zapytań dotyczących dużych ilości danych. Należy upewnić się, że kod procedury składowanej jest poprawnie zoptymalizowany pod kątem dużych zapytań. Celem jest oszczędność godzin, minut i sekund, a nie milisekund. W związku z tym bardziej pomocne jest, aby traktować procedury składowane jako kontenery dla logiki SQL.

Gdy pula SQL wykonuje procedurę przechowywaną, instrukcje SQL są analizowane, tłumaczone i optymalizowane w czasie wykonywania. W trakcie tego procesu każda instrukcja jest konwertowana na zapytania rozproszone. Kod SQL, który jest wykonywany względem danych, różni się od przesłanego zapytania.

## <a name="nesting-stored-procedures"></a>Zagnieżdżanie procedur składowanych

Gdy procedury składowane wywołują inne procedury składowane lub wykonują dynamiczną instrukcję SQL, wewnętrzna procedura składowana lub wywołanie kodu są określane jako zagnieżdżone.

Pula SQL obsługuje maksymalnie osiem poziomów zagnieżdżenia. Jest to nieco inne SQL Server. Poziom zagnieżdżenia w SQL Server to 32.

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

> [!NOTE]
> Pula SQL nie obsługuje obecnie programu [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Musisz śledzić poziom zagnieżdżenia. Jest mało prawdopodobne, aby przekroczyć limit ośmiu poziomów zagnieżdżenia, ale jeśli to zrobisz, musisz ponownie obsłużyć swój kod, aby dopasować poziomy zagnieżdżenia w ramach tego limitu.

## <a name="insertexecute"></a>Wstaw.. WYKONANA

Pula SQL nie zezwala na używanie zestawu wyników procedury składowanej z instrukcją INSERT. Istnieje jednak alternatywna metoda, której można użyć. Aby zapoznać się z przykładem, zapoznaj się z artykułem dotyczącym [tabel tymczasowych](develop-tables-temporary.md).

## <a name="limitations"></a>Ograniczenia

Istnieją pewne aspekty procedur składowanych Transact-SQL, które nie są zaimplementowane w puli SQL.

Oto one:

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

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](develop-overview.md).
