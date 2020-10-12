---
title: Korzystanie z pętli T-SQL
description: Porady dotyczące korzystania z pętli T-SQL, zastępowania kursorów i opracowywania powiązanych rozwiązań z pulą SQL w Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 33e1ebc2269ef1db6bb0646f845b09be1a01c724
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289059"
---
# <a name="use-t-sql-loops-in-synapse-sql"></a>Używanie pętli T-SQL w programie SQL Synapse
Ten artykuł zawiera istotne porady dotyczące korzystania z pętli T-SQL, zastępowania kursorów i tworzenia powiązanych rozwiązań z pulą SQL w Synapse SQL.

## <a name="purpose-of-while-loops"></a>Przeznaczenie pętli WHILE

Synapse SQL obsługuje pętlę [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15&preserve-view=true) do wielokrotnego wykonywania bloków instrukcji. Ta Pętla WHILE działa tak długo, jak określone warunki są prawdziwe lub do momentu, aż kod zakończy pętlę przy użyciu słowa kluczowego BREAK. 

Pętle w puli SQL są przydatne do zastępowania kursorów zdefiniowanych w kodzie SQL. Na szczęście prawie wszystkie kursory, które są zapisywane w kodzie SQL, są do przodu, tylko do odczytu. Tak więc, podczas gdy pętle są świetną alternatywą dla zastępowania kursorów.

## <a name="replace-cursors-in-sql-pool"></a>Zastąp kursory w puli SQL

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
