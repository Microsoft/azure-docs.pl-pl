---
title: Korzystanie z pętli T-SQL
description: Porady dotyczące korzystania z pętli T-SQL, zastępowania kursorów i opracowywania powiązanych rozwiązań za pomocą puli SQL w synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429956"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>Korzystanie z pętli T-SQL w synapse SQL
W tym artykule przedstawiono podstawowe wskazówki dotyczące korzystania z pętli T-SQL, zastępowania kursorów i opracowywania powiązanych rozwiązań za pomocą puli SQL w języku Synapse SQL.

## <a name="purpose-of-while-loops"></a>Przeznaczenie pętli WHILE

Synapse SQL obsługuje [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) pętli wielokrotnie wykonywania bloków instrukcji. Ta pętla WHILE jest kontynuowana tak długo, jak długo określone warunki są prawdziwe lub dopóki kod specjalnie kończy pętlę przy użyciu break słowa kluczowego. 

Pętle w puli SQL są przydatne do zastępowania kursorów zdefiniowanych w kodzie SQL. Na szczęście prawie wszystkie kursory, które są napisane w kodzie SQL są szybko do przodu, tylko do odczytu odmiany. Tak więc pętle [WHILE] są świetną alternatywą dla wymiany kursorów.

## <a name="replacing-cursors-in-sql-pool"></a>Zastępowanie kursorów w puli SQL

Przed nurkowaniem należy rozważyć następujące pytanie: "Czy ten kursor może zostać przepisany w celu użycia operacji opartych na zestawach?" W wielu przypadkach odpowiedź brzmi tak i często jest najlepszym podejściem. Operacja oparta na zestawie często wykonuje szybciej niż iteracyjne podejście wiersz po wierszu.

Kursory tylko do odczytu do przodu można łatwo zastąpić konstrukcją pętli. Poniższy kod jest prostym przykładem. W tym przykładzie kodu aktualizuje statystyki dla każdej tabeli w bazie danych. Przez iteracji nad tabelami w pętli, każde polecenie wykonuje w sekwencji.

Najpierw utwórz tabelę tymczasową zawierającą unikatowy numer wiersza używany do identyfikowania poszczególnych instrukcji:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Po drugie, zainicjować zmienne wymagane do wykonania pętli:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Teraz pętla nad instrukcjami wykonującymi je po jednym na raz:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Na koniec upuść tabelę tymczasową utworzoną w pierwszym kroku

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](develop-overview.md).
