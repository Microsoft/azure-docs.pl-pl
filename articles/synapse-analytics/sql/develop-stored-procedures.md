---
title: Korzystanie z procedur składowanych
description: Porady dotyczące wdrażania procedur przechowywanych w puli sql synapse (magazyn danych) do tworzenia rozwiązań.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430983"
---
# <a name="using-stored-procedures-in-sql-pool"></a>Korzystanie z procedur przechowywanych w puli SQL

Porady dotyczące wdrażania procedur przechowywanych w puli sql synapse (magazyn danych) do tworzenia rozwiązań.

## <a name="what-to-expect"></a>Czego się spodziewać

Pula SQL obsługuje wiele funkcji T-SQL, które są używane w programie SQL Server. Co ważniejsze, istnieją funkcje specyficzne skalowaj w poziomie, których można użyć, aby zmaksymalizować wydajność rozwiązania.

Jednak aby zachować skalę i wydajność puli SQL istnieją również pewne funkcje i funkcje, które mają różnice w zachowaniu i inne, które nie są obsługiwane.

## <a name="introducing-stored-procedures"></a>Wprowadzenie procedur składowanych

Procedury przechowywane są doskonałym sposobem hermetyzacji kodu SQL; przechowywanie go w pobliżu danych w magazynie danych. Procedury przechowywane pomagają deweloperom modularyzacji swoich rozwiązań, hermetyzując kod w jednostkach, którymi można zarządzać; ułatwienie ponownego wykorzystania kodu. Każda procedura składowana może również akceptować parametry, aby uczynić je jeszcze bardziej elastycznymi.

Pula SQL zapewnia uproszczoną i usprawnione implementacji procedury składowanej. Największą różnicą w porównaniu do programu SQL Server jest to, że procedura składowana nie jest wstępnie skompilowanym kodem. W magazynach danych czas kompilacji jest mały w porównaniu do czasu, jaki zajmuje uruchamianie zapytań względem dużych woluminów danych. Jest ważniejsze, aby upewnić się, że kod procedury składowanej jest poprawnie zoptymalizowany pod kątem dużych zapytań. Celem jest zapisanie godzin, minut i sekund, a nie milisekund. Dlatego bardziej pomocne jest myślenie o procedurach przechowywanych jako kontenerach dla logiki SQL.

Gdy pula SQL wykonuje procedurę składowaną, instrukcje SQL są analizowane, tłumaczone i optymalizowane w czasie wykonywania. Podczas tego procesu każda instrukcja jest konwertowana na zapytania rozproszone. Kod SQL, który jest wykonywany względem danych jest inny niż przesłane zapytanie.

## <a name="nesting-stored-procedures"></a>Procedury przechowywane zagnieżdżania

Gdy procedury składowane wywołać inne procedury składowane lub wykonać dynamiczny SQL, a następnie wewnętrznej procedury składowanej lub wywołania kodu mówi się, że zagnieżdżone.

Pula SQL obsługuje maksymalnie osiem poziomów zagnieżdżenia. To jest nieco inny niż SQL Server. Poziom gniazda w programie SQL Server wynosi 32.

Wywołanie procedury składowanej najwyższego poziomu jest równoznaczne z poziomem zagnieżdżenia 1.

```sql
EXEC prc_nesting
```

Jeśli procedura składowana również wykonuje inne wywołanie EXEC, poziom gniazda zwiększa się do dwóch.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Jeśli druga procedura następnie wykonuje niektóre dynamiczne SQL, poziom gniazda zwiększa się do trzech.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

> [!NOTE]
> Pula SQL nie obsługuje obecnie [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Musisz śledzić poziom gniazda. Jest mało prawdopodobne, aby przekroczyć limit poziomu ośmiu gniazd, ale jeśli to zrobisz, należy przerobić kod, aby dopasować poziomy zagnieżdżenia w tym limicie.

## <a name="insertexecute"></a>Wstawić.. Wykonać

Pula SQL nie pozwala na korzystanie z zestawu wyników procedury składowanej z insert instrukcji. Istnieje jednak alternatywne podejście, którego można użyć. Na przykład zobacz artykuł dotyczący [tabel tymczasowych](develop-tables-temporary.md).

## <a name="limitations"></a>Ograniczenia

Istnieją pewne aspekty procedur przechowywanych transact-SQL, które nie są implementowane w puli SQL.

Oto one:

* tymczasowe procedury składowane
* policzone procedury składowane
* rozszerzone procedury składowane
* Procedury przechowywane w clr
* opcja szyfrowania
* opcja replikacji
* parametry wycenione w tabeli
* Parametry tylko do odczytu
* parametry domyślne
* konteksty wykonania
* return, instrukcja

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](develop-overview.md).
