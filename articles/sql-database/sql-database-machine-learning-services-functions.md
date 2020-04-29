---
title: Pisanie zaawansowanych funkcji języka R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Dowiedz się, jak napisać funkcję języka R na potrzeby zaawansowanego obliczania statystycznego w Azure SQL Database przy użyciu Machine Learning Services (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ba78267b1c6dc8f0e1bd25bb8ecdb1d8d344d03e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453118"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Zapisuj zaawansowane funkcje języka R w Azure SQL Database przy użyciu Machine Learning Services (wersja zapoznawcza)

W tym artykule opisano sposób osadzania funkcji matematycznych języka R w procedurze składowanej SQL. Zaawansowane funkcje statystyczne, które są skomplikowane do implementacji w języku T-SQL, można wykonać w R tylko za pomocą jednego wiersza kodu.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/).

- Aby uruchomić przykładowy kod w tych ćwiczeniach, musisz najpierw mieć [Azure SQL Database z włączoną Machine Learning Services (z R)](sql-database-machine-learning-services-overview.md) .

- Upewnij się, że zainstalowano najnowszą [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Skrypty języka R można uruchamiać przy użyciu innych narzędzi do zarządzania bazami danych lub zapytań, ale w tym przewodniku szybki start będziesz używać programu SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Utwórz procedurę przechowywaną, aby generować liczby losowe

Dla uproszczenia używamy pakietu języka R `stats` , który jest instalowany i ładowany domyślnie z Azure SQL Database przy użyciu Machine Learning Services (wersja zapoznawcza). Pakiet zawiera setki funkcji dla wspólnych zadań statystycznych, między nimi `rnorm` . Ta funkcja generuje określoną liczbę losowych liczb przy użyciu rozkładu normalnego, uwzględniając odchylenie standardowe i oznacza.

Na przykład następujący kod R zwraca 100 cyfr w średniej 50, z uwzględnieniem standardowego odchylenia 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Aby wywołać ten wiersz języka R z T-SQL, uruchom `sp_execute_external_script` i Dodaj funkcję r w parametrze skryptu języka r, tak jak to:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Co zrobić, jeśli chcesz ułatwić generowanie innego zestawu losowych liczb?

Jest to łatwe w połączeniu z programem SQL. Należy zdefiniować procedurę składowaną, która pobiera argumenty od użytkownika, a następnie przekazać te argumenty do skryptu języka R jako zmienne.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- Pierwszy wiersz definiuje każdy z parametrów wejściowych SQL, które są wymagane podczas wykonywania procedury składowanej.

- Wiersz zaczynający się `@params` od definiuje wszystkie zmienne używane przez kod języka R oraz odpowiednie typy danych SQL.

- Wiersze, które bezpośrednio obserwują Mapowanie nazw parametrów SQL do odpowiednich nazw zmiennych języka R.

Po zapakowaniu funkcji języka R w procedurze składowanej można łatwo wywołać funkcję i przekazać różne wartości w następujący sposób:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Korzystanie z funkcji narzędzia R do rozwiązywania problemów

`utils` Pakiet instalowany domyślnie udostępnia wiele funkcji narzędzi do badania bieżącego środowiska R. Te funkcje mogą być przydatne, jeśli występują rozbieżności w sposobie wykonywania kodu języka R w języku SQL i w środowiskach zewnętrznych. Na przykład można użyć funkcji R `memory.limit()` , aby uzyskać pamięć dla bieżącego środowiska R.

Ponieważ `utils` pakiet jest zainstalowany, ale nie został załadowany domyślnie, należy użyć `library()` funkcji, aby załadować ją w pierwszej kolejności.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Wielu użytkowników chce użyć funkcji chronometrażu systemu w języku R, takich jak `system.time` i `proc.time`, aby przechwycić czas używany przez procesy języka r i analizować problemy z wydajnością.
