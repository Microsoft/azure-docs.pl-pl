---
title: Korzystanie z pętli T-SQL
description: Porady dotyczące korzystania z pętli T-SQL, zastępowania kursorów i opracowywania powiązanych rozwiązań z pulą SQL w Synapse SQL.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429956"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>Używanie pętli T-SQL w programie SQL Synapse
Ten artykuł zawiera istotne porady dotyczące korzystania z pętli T-SQL, zastępowania kursorów i tworzenia powiązanych rozwiązań z pulą SQL w Synapse SQL.

## <a name="purpose-of-while-loops"></a>Przeznaczenie pętli WHILE

Synapse SQL obsługuje pętlę [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) do wielokrotnego wykonywania bloków instrukcji. Ta Pętla WHILE działa tak długo, jak określone warunki są prawdziwe lub do momentu, aż kod zakończy pętlę przy użyciu słowa kluczowego BREAK. 

Pętle w puli SQL są przydatne do zastępowania kursorów zdefiniowanych w kodzie SQL. Na szczęście prawie wszystkie kursory, które są zapisywane w kodzie SQL, są do przodu, tylko do odczytu. Tak więc pętle [WHILE] są świetną alternatywą dla zastępowania kursorów.

## <a name="replacing-cursors-in-sql-pool"></a>Zastępowanie kursorów w puli SQL

Przed napisaniem w programie należy wziąć pod uwagę następujące kwestie: "czy można ponownie napisać ten kursor, aby użyć operacji ustawionych?". W wielu przypadkach odpowiedź ma wartość yes i jest często najlepszym rozwiązaniem. Operacja oparta na zestawie często wykonuje się szybciej niż iteracyjne, sekwencyjne wiersz po wierszu.

Szybkie przewijanie kursorów tylko do odczytu jest łatwo zastępowane przez konstrukcję pętli. Poniższy kod jest prostym przykładem. Ten przykład kodu aktualizuje statystyki dla każdej tabeli w bazie danych. Przez iterację w tabelach w pętli, każde polecenie wykonuje sekwencję.

Najpierw utwórz tabelę tymczasową zawierającą unikatowy numer wiersza służący do identyfikowania poszczególnych instrukcji:

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

Następnie zainicjuj zmienne wymagane do wykonania pętli:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Teraz instrukcje pętli over wykonujeją je pojedynczo:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Na koniec Usuń tabelę tymczasową utworzoną w pierwszym kroku

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](develop-overview.md).
